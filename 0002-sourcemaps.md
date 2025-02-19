---
Feature Name: Native Sourcemap Support for ReScript
Start Date: 2025-02-19
RFC PR: https://github.com/rescript-lang/rfcs/pull/2
ReScript Issue: https://github.com/rescript-lang/rescript/issues/1699
---

## Summary

This RFC proposes adding native sourcemap support to the ReScript compiler. The goal is to generate sourcemap files during compilation, enabling developers to map the generated JavaScript back to ReScript source code. This feature will greatly enhance debugging capabilities and streamline the developer experience.

## Motivation

### 2.1. Debugging Efficiency

- **Problem:** Without sourcemaps, developers must debug minified or transformed JavaScript code, obscuring the original ReScript source.
- **Benefit:** Sourcemaps enable direct debugging of the original ReScript code, reducing debugging time and improving overall developer productivity.

### 2.2. Developer Experience

- **Problem:** Debugging tools (e.g., Chrome DevTools, VS Code) rely on sourcemaps for accurate breakpoint placement and error reporting.
- **Benefit:** Native sourcemap support improves integration with these tools, providing a seamless debugging experience.

### 2.3. Industry Alignment

- **Observation:** Many modern compilers (TypeScript, Babel, etc.) offer robust sourcemap support.
- **Benefit:** Implementing sourcemaps in ReScript keeps the language competitive and aligned with current industry practices.

## Detailed design

### 3.1. Overview

The compiler will be extended to produce a JSON-based sourcemap file alongside the compiled JavaScript output. The sourcemap will adhere to the [Source Map](https://tc39.es/ecma426/) specification.

### 3.2. Compiler Changes

- **AST and Code Generation:**
  - Extend the compiler’s code generation phase to track source positions (line and column numbers) for each node.
  - Generate mappings between ReScript source files and the emitted JavaScript code.
- **Output:**
  - For each compiled JavaScript file (e.g., `file.res.js`), produce a corresponding `file.res.js.map`.
  - Append a comment at the end of `file.res.js` to reference the sourcemap file:
    ```js
    //# sourceMappingURL=file.js.map
    ```

### 3.3. Configuration Options

Introduce new configuration options in the compiler’s configuration file (`rescript.json`):

- **`--sourcemap` flag:** Boolean flag to enable or disable sourcemap generation.
- **Customization Options:**
  - Specify the output directory for sourcemaps.
  - Choose between inline sourcemaps (embedded in the JavaScript file) and external sourcemap files.

### 3.4. Handling Inline vs. External Sourcemaps

- **Inline Sourcemaps:**
  - **Definition:** Embed the sourcemap data as a base64-encoded string at the end of the JavaScript file.
  - **Pros:** Simplifies deployment by bundling everything in one file.
  - **Cons:** Increases file size.
- **External Sourcemaps:**
  - **Definition:** Generate a separate `.map` file alongside the JavaScript file.
  - **Pros:** Keeps the main JavaScript file lean.
  - **Cons:** Requires managing multiple files.
- **Default Configuration:** External sourcemaps, with an option for inline maps if desired by the user.

### 3.5. Error Handling & Edge Cases

- **Incomplete Mappings:** Ensure every segment of generated code has a corresponding mapping or a defined fallback.
- **Minification:** Ensure compatibility with downstream minification tools by mapping back to the original ReScript code before minification.
- **Non-linear Code Generation:** Accommodate non-sequential mapping entries due to ReScript-to-JS transformation complexities.

## Drawbacks

- **Increased Compilation Time:**
  - _Mitigation:_ Optimize the mapping generation process and offer a flag to disable sourcemaps for production builds.
- **Compiler Complexity:**
  - _Mitigation:_ Keep the mapping logic modular and well-documented; encourage community code reviews and contributions.
- **Maintenance Overhead:**
  - _Mitigation:_ Write comprehensive tests to prevent regressions and ensure long-term stability.

## Rationale and alternatives

- **External Tooling:**  
  Rely on third-party tools to generate sourcemaps post-compilation.
  - **Drawback:** Less integration, higher risk of mismatches, and increased maintenance overhead.
- **Partial Mapping:**  
  Generate sourcemaps only for a subset of language features.
  - **Drawback:** Results in an inconsistent debugging experience and incomplete error mapping.

The chosen approach of native support within the compiler ensures a consistent and reliable debugging experience.

## Prior art

- TypeScript sourcemaps: https://www.typescriptlang.org/tsconfig/#sourceMap

## Adoption strategy

Ideally, people who need them will just enable this feature in `rescript.json` and/or via a compiler flag.

## Unresolved questions

- Find out what preparations are needed in the compiler (and build system/rewatch) that need to be landed before this can be implemented in a way which does not break on subsequent changes?
- Should we introduce a production build mode / production build configuration?

## Future possibilities

Native support for tools like Sentry, Wallabyjs, etc.
