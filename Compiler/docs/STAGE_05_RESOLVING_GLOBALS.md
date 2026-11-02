# Stage 05: Global Variable & Literal Resolution

**Stage Number**: `[05/16]`  
**Routine Header**: `To compile a directory (resolve the globals):`  
**Location**: [PlainEnglishCompiler.plain: lines 1979–2221](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L1979-L2221)

---

## 1. Overview & Purpose

Stage 05 establishes program-level state. It resolves the data type of every global variable against `type index`, parses and validates compile-time literal values (numbers, strings, flags, hex bytes), and assigns nicknames for natural-language dereferencing in statements.

---

## 2. Step-by-Step Code Walkthrough

### Step 1: Global Iteration & Type Lookup
```plain
To resolve a global:
  ...
  If the global's global body is not blank,
    Resolve the global (compile body).
  If the global's type name is blank,
    Abort with "Error #39. Expected a type name for global variable..."
  Resolve the global as a variable.
```
*(Lines 1996–2005)*

- If the global has an initializer (e.g., `equal to 4194304`), it branches to `Resolve the global (compile body)`.
- If no explicit type was specified, the type is inferred from the literal initializer.
- `Resolve the global as a variable` connects the global's `type` reference to the corresponding `type` record in the symbol table.

### Step 2: Parsing & Compiling Literals
```plain
To compile a literal given a rider:
  ...
  If the rider's token substring is any flag literal,
    Compile the literal given the rider (flag);
    Exit.
  If the rider's token substring is any hex literal,
    Compile the literal given the rider (hex);
    Exit.
  If the rider's token substring is any numeric literal,
    Compile the literal given the rider (numeric);
    Exit.
  If the rider's token substring is any pointer literal,
    Compile the literal given the rider (pointer);
    Exit.
  If the rider's token substring is any string literal,
    Compile the literal given the rider (string);
    Exit.
```
*(Lines 2024–2043)*

Literals are parsed into byte sequences stored in `literal's data`:
- **Flag literals**: `yes` (`0x01`) or `no` (`0x00`).
- **Hex literals**: Hexadecimal strings prefixed by `$` (e.g., `$00400000`).
- **Numeric literals**: Integers and fixed-point numbers converted to binary twos-complement 32-bit values.
- **String literals**: Double-quoted strings encoded as length-prefixed and null-terminated character sequences.
- **Pointer literals**: `nil` (evaluated as 0).

### Step 3: Nickname & Suffix Decomposition
```plain
To find a type and a nickname given a name:
  Void the type.
  Clear the nickname.
  Slap a substring on the name.
  Loop.
    If the substring is blank, exit.
    Find the type given the substring and the type index.
    If the type is not nil, break.
    Skip to the next word in the substring.
  Repeat.
```
*(Lines 2071–2087)*

Plain English allows variable names that include the type name as a suffix. For instance, given `The red color`:
- The scanner detects that `color` is a registered type.
- It sets `type` to `color`.
- It sets `nickname` to `red` (or `red color`).
- This enables routines to refer to `the red color`, `the color`, or `the red` interchangeably.

---

## 3. Technical Rationale & Architectural Decisions

### Compile-Time Constant Constraint on Globals
Global variables in Plain English can only be initialized with compile-time literals. The compiler rejects non-literal expressions (such as routine calls or arithmetic formulas) during this stage. Dynamic initialization is deliberately deferred to Stage 11 (Startup Routine), where initialization expressions are generated as runtime statements executed in sequence.

### Nickname Disambiguation
By decomposing multi-word variable identifiers into `(nickname, type)` pairs, the compiler enables English phrasing where adjectives modify nouns naturally. The type system can match variable slots without requiring rigid `type variable` or `variable: type` formatting.
