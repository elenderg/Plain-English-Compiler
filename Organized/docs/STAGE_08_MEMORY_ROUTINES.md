# Stage 08: Generation of Built-in Memory Routines

**Stage Number**: `[08/16]`  
**Routine Header**: `To compile a directory (add the built-in memory routines):`  
**Location**: [PlainEnglishCompiler.plain: lines 2648–2838](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L2648-L2838)

---

## 1. Overview & Purpose

Stage 08 generates automatic memory management routines for every custom type and composite record in the project. It synthesizes `Allocate memory for a [type]`, `Deallocate a [type]`, `~Finalize a [type]`, and `Destroy [type]` routines directly into the compiler's routine database.

---

## 2. Step-by-Step Code Walkthrough

```plain
To add the allocate and deallocate and finalize and destroy routines:
  Loop.
    Get a type from the types.
    If the type is nil, exit.
    Add the finalize routine for the type.
    Add the allocate routine for the type.
    Add the deallocate routine for the type.
    Add the destroy routine for the type.
  Repeat.
```
*(Lines 2657–2666)*

### 1. `Allocate memory for a [type]`
```plain
To add the allocate routine for a type:
  ...
  Append "Allocate memory for a " then the type's name to the routine's header string.
  Append "Assign the " then the type's name then " given " to the routine's body string.
  Append the type's target type's length then "." to the routine's body string.
```
*(Lines 2724–2736)*
- Emits code that calls the low-level memory allocator (`Assign`), passing the byte length computed in Stage 07.

### 2. `~Finalize a [type]`
```plain
To add the finalize routine for a type (record):
  ...
  Append "Intel $50. " to the routine's body string. \ push eax
  Loop.
    Get a field from the type's fields.
    If the field is nil, break.
    If the field's type should not be finalized, repeat.
    Append "~Finalize the " then the type's name then "'s " then the field's name then ". " to the routine's body string.
  Repeat.
  Append "Intel $58." to the routine's body string. \ pop eax
```
*(Lines 2690–2709)*
- Finalizers clean up dynamic resources held by an object without freeing the object's outer memory.
- If a record contains `string` fields or nested dynamic records, `~Finalize` recursively finalizes each field.
- Wraps execution in `PUSH EAX` / `POP EAX` to preserve return registers across recursive invocations.

### 3. `Deallocate a [type]`
- Checks if the pointer is `nil`.
- If the target type has dynamic members, it invokes `~Finalize`.
- Calls `Unassign` to return the buffer to the OS heap.

### 4. `Destroy [type]`
- For simple pointers: Invokes `Deallocate`.
- For `things` (linked lists): Iterates through every element in the list, unlinking nodes, finalizing contents, and deallocating memory.

---

## 3. Technical Rationale & Architectural Decisions

### Compiler-Generated RAII and Ownership
Unlike C (which requires manual `malloc`/`free` calls paired with custom cleanup functions) or C++ (which requires template smart pointers and copy/move constructors), Plain English generates type-specific lifecycle routines automatically based on struct introspection.

### Prefix Tildes (`~`) for Compiler-Internal Routines
Routines synthesized by the compiler (such as `~Finalize a monster`) are prefixed with a tilde `~`. This avoids naming collisions with user-defined methods while signaling to the compiler engine that these routines handle internal resource teardown.
