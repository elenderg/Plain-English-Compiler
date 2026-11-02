# Stage 07: Type Size & Field Offset Calculation

**Stage Number**: `[07/16]`  
**Routine Header**: `To compile a directory (calculate lengths and offsets of types):`  
**Location**: [PlainEnglishCompiler.plain: lines 2540–2647](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L2540-L2647)

---

## 1. Overview & Purpose

Stage 07 computes the concrete binary memory layout of every user and library record. It calculates the cumulative byte length of every type, determines the relative byte offset of each record field, and resolves field redefinitions (unions/aliasing).

---

## 2. Step-by-Step Code Walkthrough

```plain
To compile a directory (calculate lengths and offsets of types):
  ...
  Calculate the lengths of the types.
  Calculate the offsets in the types.
  Calculate the redefinition offsets in the types.
```
*(Lines 2540–2550)*

### Step 1: Recursive Length Calculation
```plain
To calculate the length of a type:
  ...
  If the type's length is not -1, exit.
  Calculate the length of the type's base type.
  If the type's fields are empty,
    Put the type's base type's length into the type's length;
    Exit.
  Put 0 into the type's length.
  Loop.
    Get a field from the type's fields.
    If the field is nil, exit.
    If the field's redefinition target name is not blank, repeat.
    Calculate the length of the field's type.
    Add the field's type's length times the field's count to the type's length.
  Repeat.
```
*(Lines 2559–2574)*

- Types start initialized with length `-1` (uncalculated).
- Calculates the base type's length recursively.
- Iterates over all non-redefined fields, accumulating `field's type's length * field's count`.

### Step 2: Sequential Field Offset Assignment
```plain
To calculate the offsets in a type:
  Put 0 into an offset.
  Loop.
    Get a field from the type's fields.
    If the field is nil, exit.
    If the field's redefinition target name is not blank, repeat.
    Put the offset into the field's offset.
    Add the field's type's length times the field's count to the offset.
  Repeat.
```
*(Lines 2584–2594)*

- Each regular field receives a displacement offset relative to the start of the record instance.
- Offsets increment linearly matching packed binary struct layout.

### Step 3: Resolving Field Redefinitions (Unions)
```plain
To calculate the redefinition offsets in a type:
  Loop.
    Get a field from the type's fields.
    If the field is nil, exit.
    If the field's redefinition target name is blank, repeat.
    Find another field given the field's redefinition target name and the type's fields.
    Put the other field's offset into the field's offset.
  Repeat.
```
*(Lines 2604–2616)*

- Plain English allows fields to redefine existing members (equivalent to anonymous C unions):
  ```plain
  A point is a record with
    A number called x,
    A number called y,
    A number called left redefining x,
    A number called top redefining y.
  ```
- Redefined fields are assigned the exact same memory offset as their target.

---

## 3. Technical Rationale & Architectural Decisions

### Deferred Layout Computation
Because Plain English source files can define composite records out-of-order across multiple files, attempting to compute memory sizes during parsing (Stage 03) would require placeholder sizes or dynamic backpatching. Deferring size calculation to Stage 07 ensures all base types and nested fields are fully registered, allowing clean memoized recursive sizing.

### Packed Binary Representation
Plain English records are packed with byte-exact alignments. This simplifies interoperability with Windows Win32 API C-structs (such as `BITMAPINFOHEADER`, `RECT`, and `MSG`), allowing Plain English records to be passed directly to native Windows calls without serialization or marshalling layers.
