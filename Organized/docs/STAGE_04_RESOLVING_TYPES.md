# Stage 04: Type Resolution & Extension

**Stage Number**: `[04/16]`  
**Routine Header**: `To compile a directory (resolve the types):`  
**Location**: [PlainEnglishCompiler.plain: lines 1688–1978](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L1688-L1978)

---

## 1. Overview & Purpose

Stage 04 connects raw type names into a typed object model. In Plain English, defining a type as `A [name] is a thing` implicitly constructs a doubly-linked node record, an accompanying list header type, and pointer bindings. Stage 04 expands these linked collections, resolves inheritance hierarchies (`base type`), indexes plural aliases, and verifies that types do not have circular dependency cycles.

---

## 2. Step-by-Step Code Walkthrough

The stage runs four ordered sub-passes:
```plain
To compile a directory (resolve the types):
  ...
  Resolve the types (expand things).
  Resolve the types (index plurals).
  Resolve the types (base types).
  Resolve the types (optional info).
```
*(Lines 1688–1698)*

### Sub-Pass 1: Expanding "Things" into Nodes & Chains
```plain
To resolve a type (expand thing):
  ...
  Find a base type given the type's base name and the type index.
  Put the type's name then " record" into a name.
  Put the type's name then " records" into a plural name.
  Put the base type's name then " record" into a base name.
  Add a record type to the types...
  Add a field to the record type's fields given "next " ...
  Add another field to the record type's fields given "previous " ...
  If the type's fields are not empty,
    Append the type's fields to the record type's fields.
  ...
  Add a chain type to the types given the type's plural name...
  Add a third field to the chain type's fields given "first "...
  Add a fourth field to the chain type's fields given "last "...
```
*(Lines 1708–1759)*

When user code states:
```plain
A monster is a thing with a name and a health number.
```
This sub-pass automatically synthesizes:
1. **`monster record`**: Contains `next monster`, `previous monster`, `name`, and `health number`.
2. **`monster`**: Configured as a pointer targeting `monster record`.
3. **`monsters`**: A header chain record containing pointers to `first monster` and `last monster`.

### Sub-Pass 2: Plural Indexing
- For non-collection types, registers their plural forms in `type index` (e.g., `number` -> `numbers`, `byte` -> `bytes`).
- Allows variable declarations such as `some numbers` or `some bytes` to directly resolve to their corresponding underlying type.

### Sub-Pass 3: Base Type Inheritance & Cycle Detection
```plain
To resolve a type (base type):
  ...
  Find the type's base type given the type's base name and the type index.
  Set the type's cooking flag.
  If the type's base type's cooking flag is set,
    Abort with "Error #34. Recursive type definition involving '..."
  Resolve the type's base type (base type).
  Clear the type's cooking flag.
```
*(Lines 1815–1829)*
- Connects each type to its parent (`base type`).
- Uses a `cooking flag` (cycle-detection marker) to catch recursive dependencies (e.g., `A is a B. B is an A.`).

### Sub-Pass 4: Optional Info (Pointers, Records, Numbers)
- Validates target types for pointers (`A [x] is a pointer to [y]`).
- Traverses record fields to resolve each field's type name against `type index`.
- Validates scale factors on numeric unit types (e.g., `A foot is 12 inches.`).

---

## 3. Technical Rationale & Architectural Decisions

### Automatic Doubly Linked Lists ("Things")
In C++ or Java, developers instantiate standard template collections (`std::list<T>`, `LinkedList<T>`). In Plain English, any domain object declared as a `thing` automatically receives intrinsic intrusive doubly linked list nodes (`next`, `previous`) and a list anchor (`first`, `last`). Intrusive lists eliminate dynamic wrapper node allocations, resulting in faster iteration, predictable memory footprints, and natural phrasing like `Get a monster from the monsters`.

### Two-Color Cycle Detection via `cooking flag`
Because types can be defined in any order across multiple files, recursive descent resolution could easily trigger stack overflows if an inheritance loop exists. The `cooking flag` provides an efficient cycle detector during graph traversal without allocating external set structures.
