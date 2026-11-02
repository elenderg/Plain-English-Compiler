# Stage 09: Routine Indexing for Utility Use (Subtyping)

**Stage Number**: `[09/16]`  
**Routine Header**: `To compile a directory (index the routines for utility use):`  
**Location**: [PlainEnglishCompiler.plain: lines 2839–3004](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L2839-L3004)

---

## 1. Overview & Purpose

Stage 09 builds the polymorphic dispatch index (`utility index`). Plain English does not use virtual method tables (vtables) or dynamic dispatch overhead. Instead, it enables compile-time subtyping polymorphism by reducing specialized derived parameters down to their base types (`string`, `number`, `pointer`, etc.), allowing generalized routines to accept specific subtypes.

---

## 2. Step-by-Step Code Walkthrough

```plain
To compile a directory (index the routines for utility use):
  ...
  Index the routines for utility use.
  Scrub the utility index.
  Reindex the utility index.
```
*(Lines 2839–2848)*

### Step 1: Type Reduction for Utility Signatures
```plain
To reduce a type to another type for utility use:
  Put the type into the other type.
  Loop.
    If the other type is nil, exit.
    If the other type's name is "hex string", exit.
    If the other type's name is "string", exit.
    If the other type's name is "number", exit.
    If the other type's name is "pointer", exit.
    If the other type's name is "thing", Void the other type; Exit.
    If the other type's base type is the other type, Void the other type; Exit.
    Put the other type's base type into the other type.
  Repeat.
```
*(Lines 2904–2920)*

- Traverses the inheritance tree upward until reaching a fundamental storage representation:
  - `file path` -> `string`
  - `twip` -> `number`
  - `window handle` -> `pointer`
- Parameter types in the routine signature are substituted with these reduced types.

### Step 2: Ambiguity Detection & Invalidation
```plain
To index a routine for utility use:
  ...
  If the moniker is in the routine index, exit.
  Find a refer given the moniker and the utility index.
  If the refer is not nil,
    Put nil into the refer's pointer;
    Exit.
  Index the routine given the moniker and the utility index.
```
*(Lines 2858–2872)*

- If the reduced moniker already exists as an explicit routine in `routine index`, the utility signature is ignored.
- If two different routines reduce to the identical utility moniker (causing an ambiguous dispatch), the compiler sets the entry to `nil`.

### Step 3: Scrubbing Invalided Ambiguities
- `Scrub the utility index` sweeps through all buckets and deletes references where `pointer is nil`.
- `Reindex the utility index` reorganizes the remaining unambiguous polymorphic routines.

---

## 3. Technical Rationale & Architectural Decisions

### Compile-Time Subtyping without VTable Penalties
In object-oriented runtimes, calling a method on a subtype incurs indirect pointer dereferences through a vtable or runtime type check (RTTI). Plain English achieves subtyping at compile time:
- If a developer calls `Clear a file path`, the compiler checks for an exact match in `routine index`.
- If no exact match exists, it consults `utility index`, finding `Clear a string`.
- It generates a direct static call (`CALL rel32`) without any runtime indirection.

### Safe Invalidation of Ambiguous Signatures
When multiple overloaded routines collapse to the same base representation, picking one arbitrarily would cause subtle bugs. By zeroing out colliding entries in Step 2 and scrubbing them in Step 3, the compiler ensures that only completely unambiguous generalized routines are resolved automatically.
