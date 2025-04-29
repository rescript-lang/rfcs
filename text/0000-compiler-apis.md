---
Feature Name: compiler-apis
Start Date: (fill me in with today's date, YYYY-MM-DD)
RFC PR: (leave this empty)
ReScript Issue: (leave this empty)
---

## Summary

Define and standardize a programmatic API surface for the ReScript compiler to enable consistent and reliable integration across tools and environments.

## Motivation

Currently, ReScript lacks a stable, documented programmatic interface.

As a result, most tooling around ReScript—such as editors, external build systems, and web-based tools—depend on reverse-engineered knowledge or tight coupling to the compiler's internal implementation details. This approach is fragile, difficult to maintain, and prone to breakage with compiler updates.

For instance:

- Some projects vendor large parts of the compiler source to extract basic capabilities like parsing or type-checking.
- Some projects implement custom CLI wrappers to invoke the compiler in subprocesses and then parse its output—an error-prone strategy that limits interactivity and flexibility.
- Building browser-based tools like the ReScript Playground is particularly challenging, since the compiler is not designed for use in non-Node.js environments. This forces projects to either patch or rebuild the compiler entirely.

A stable, documented, and minimal programmatic API would simplify these use cases, encourage ecosystem innovation, and reduce duplication of effort across the community.

## Detailed design

### Metadata

`rescript` entry provides locations of executable binaries.

```resi
let version: string
```

### Compiler entry

`rescript/compiler` entry provides interfaces for the standalone compiler instance.

### Parser entry

`rescript/parser` entry provides interfaces for the standalone parser instance.

### The LSP entry

`rescript/lsp` entry provides interfaces for the standalone parser instance.
