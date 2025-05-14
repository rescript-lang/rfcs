---
Feature Name: interoperable-exceptions
Start Date: 2025-04-30
RFC PR: https://github.com/rescript-lang/rfcs/pull/4
ReScript Issue: (leave this empty)
---

## Summary

Improve exception syntax to ensure exceptions from JavaScript are safely converted and interoperable.

## Motivation

In the JavaScript/TypeScript world, Exceptions are one of the most akward syntax to use.

- Exception handling is only available in the try-catch statement, which is not an expression.
- Since JavaScript allows to throw any values, TypeScript only allows `any` or `unknown` as its type.
- Users have to hoist the `let` binding themselves and type inference will not work automatically.

### TypeScript example

```ts
function isTypeError(exn: unknown): exn is TypeError {
  return exn instanceof TypeError;
}

// Or using third-party validator library for more complex structs.
import * as S from "sury";

// E.g. https://nodejs.org/api/errors.html#nodejs-error-codes
const AccessDeninedErrorSchema = S.schema({
  code: S.literal("ERR_ACCESS_DENINED"),
  errno: S.number,
  message: S.string,
});

let result: ReturnType<typeof maybeThrow>;
try {
  result = maybeThrow()
} catch (exn: unknown) {
  let result = S.safe(() => S.parseOrThrow(exn, AccessDeninedError));
  if (result.sucess) {
    let error = exn.value;
    // ?^ S.Output<typeof AccessDeninedErrorSchema>
  } else if (isTypeError(exn)) {
    let error = exn;
    // ?^ TypeError
  }
  throw exn;
}
```

ReScript have way better exception syntax.

```res
exception AccessDeninedError(exn)
exception TypeError(exn)

let result = try {
  maybeThrow()
} catch {
| AccessDeninedError(exn) => // ...
| TypeError(exn) => // ...
}

// or in pattern-matching
let result = switch maybeThrow() {
| exception AccessDeninedError(exn) => // ...
| Ok(value) => // ...
| _ => // ...
}
```

But it's not really useful because it's not compatible with JavaScript exceptions, there is no way to handle executions that may throw JavaScript exceptions safely with exception syntax.

## Rationale 

Make the error representation checking behavior consistent and customizable with a new attribute syntax.

When the compiler processes a `catch` statement, it performs a runtime type check to ensure that the caught value is compatible with the special runtime representation of the ReScript exception.

Simply by allowing customization of the runtime type checking, we can make ReScript exceptions interoperable with virtually any JavaScript value.

## Detailed design

### Syntax

There is an optional `@check` attribute in `exception` declarations.

```res
@check(coerce_function)
exception Constructor(type_binding)
```

An exception constructor with a `when` clause can have only one payload.

```res
@check(fn) exception Valid(t1)
@check(fn) exception Invalid(t1, t2)
```

And the identifier in the `@check` attribute must be a valid binding with type `unknown => bool`.

#### Keyword considerations

We could introduce new keyword instead of using the attribute. Any keywords are fine, as it only used in the exception declarations grammar.

Example:

```res
exception Constructor(type_binding) when let_binding
```

- `when`: Highlighting works because we've used it in old syntax.
- `if`: It would be suitable for reducing the number of tokens, but it can be confusing because it looks different from an if expression grammar. 
- `with`
- `using`

### Semantics

The compiler uses the function in the `when` clause to determine if the caught value can be safely coerced to the expected payload type before entering the specific catch branch.

```res
external isJsError: unknown => bool = "Error.isError"

@check(isJsError)
exception JsError(JsError.t)

let throwJsError: unit => string = %raw(`() => {
  throw new Error();
}`)

let result = try {
  throwJsError()
} catch {
| JsError(error) => {
    Console.log2("recover from " + error->JsError.name)
    "recovered"
  }
}
```

```js
let throwJsError = (() => {
  throw new Error();
});

let result;
try {
  result = throwJsError();
} catch (exn) {
  if (Error.isError(exn)) {
    // exn here should safely be coerced to `Error` instance.
    console.log("recover from " + error.name);
    result = "recovered"; 
  } else {
    throw exn;
  }
}
```

## Other considerations

### Canonicalizing exceptions

It is mostly compatible with existing exception representation, except for the payload restrictions.

So this proposal could become the default semantics for exceptions if the existing exception syntax is changed not to allow multiple payloads.

```res
exception ResError // This uses "unit" payload type implicitly
exception ResError(t) // Omit `@check` attribute to use primitive (e.g. `isRescriptException`)
exception ResError(t, t)
                 // ^ Gonna be syntax error 

@check(JsError.isJsError)
exception JsError(JsError.t)

let result = try {
  throwError()
} catch {
| ResError(t) => recover(t)
| JsError(error) => recoverFromError(error)
}
```

```js
import * as Primitive_exceptions from "@rescript/runtime/Primitive_exceptions";
import * as JsError from "./JsError.js";

let result;
try {
  result = throwError();
} catch (exn) {
  if (Primitive_exceptions.isRescriptException(
    exn,
    // Compiler can pass additional arguments for internal usage.
    Symbol.for("Module.ResError"),
  )) {
    result = recover(exn); 
  } else if (JsError.isJsError(exn)) {
    result = recoverFromError(error)
  } else {
    throw exn;
  }
}
```


## Questions

### Could it use untagged variants?

This is similar to the idea of untagged variants match. If we can make the untagged variants are fully customizable, we could leverage the same mechanism like:

```res
@untagged
type t =
  | @check(isResError) ResError(t) 
  | @check(JsError.isJsError) JsError(JsError.t) 

exception ResException(t)
```

However, variant matches must guarantee exhaustiveness; exception matches are not.
