---
Feature Name: interoperable-exceptions
Start Date: (fill me in with today's date, YYYY-MM-DD)
RFC PR: (leave this empty)
ReScript Issue: (leave this empty)
---

## Summary

Improve exception syntax to ensure exceptions from JavaScript are safely converted and interoperable.

## Motivation

ReScript provides a convenient exception syntax. But it's not really useful.

However, in the ReScript ecosystem, the `result` type is generally preferred over exceptions, so this syntax is rarely used.

On the other hand, since it's not compatible with JavaScript exceptions, there is no way to handle executions that may throw JavaScript exceptions safely with exception syntax.

## Rationale 

When the compiler processes a `catch` statement, it performs a runtime type check to ensure that the caught value is compatible with the special runtime representation of the ReScript exception.

Simply by allowing customization of the runtime type checking, we can make ReScript exceptions interoperable with virtually any JavaScript value.

## Detailed design

### Syntax

There is optional `when` clause after the `exception` declaration.

```
exception Constructor(type_binding) when let_binding
```

An exception constructor with a `when` clause can have only one payload.

```res
exception Valid(t1) when fn
exception Invalid(t1, t2) when fn 
```

And the identifier in the `when` clause must be a valid binding with type `unknown => bool`.

### Semantics

The compiler uses the function in the `when` clause to determine if the caught value can be safely coerced to the expected payload type before entering the specific catch branch.

```res
external isJsError: unknown => bool = "Error.isError"

exception JsError(JsErrot.t) when isJsError

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
  }
  throw exn;
}
```
