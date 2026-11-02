# Stage 02: Loading Source Files

**Stage Number**: `[02/16]`  
**Routine Header**: `To compile a directory (load the source files):`  
**Location**: [PlainEnglishCompiler.plain: lines 580–623](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L580-L623)

---

## 1. Overview & Purpose

Stage 02 discovers, inspects, and buffers all source code files belonging to the project into continuous memory chunks. Plain English operates under an order-agnostic model: code files do not need `#include` or `import` statements to reference routines or types defined in other files in the directory.

---

## 2. Step-by-Step Code Walkthrough

### Step 1: Directory Enumeration
```plain
To load some source files:
  Put ".plain" to a string called file extension.
  Null terminate the file extension.
  Loop.
    If the compiler's abort flag is set, exit.
    Get an item from the compiler's directory.
    If the item is not found, break.
    If the item's kind is not "file", repeat.
    If the item's extension is not the file extension, repeat.
    Add a source file to the source files given the item's path.
    Load the source file.
  Repeat.
```
*(Lines 589–601)*

- Scans the directory using operating system file enumeration primitives (`FindFirstFileA` / `FindNextFileA` in the standard library).
- Ignores subdirectories, temporary files, and files without the `.plain` extension.
- Creates a `source file` object:
  ```plain
  A source file is a thing with
    A path and
    A buffer.
  ```
  *(Lines 190–192)*

### Step 2: In-Memory File Buffering
```plain
To load a source file:
  If the source file is nil, exit.
  Load the source file's path into the source file's buffer.
  If the i/o error is not blank,
    Abort with "Error #3. Could not load the file '" then the source file's path then "'.";
    Exit.
```
*(Lines 611–617)*

- `Load [path] into [buffer]` executes a direct file read into heap memory.
- The file content is loaded intact with its carriage returns (`0x0D`) and linefeeds (`0x0A`).
- The buffer boundaries (`first byte pointer` and `last byte pointer`) are preserved on the source file instance.

---

## 3. Technical Rationale & Architectural Decisions

### Why Monolithic In-Memory Loading?
In conventional C/C++ compilers, disk reads are repeated per compilation unit, requiring extensive precompiled headers to mitigate parsing bottlenecks. The Plain English compiler instead loads the entire codebase (including the standard library) into memory upfront. Because total project size is typically a few megabytes, loading everything takes only a few milliseconds while giving all subsequent passes direct zero-copy pointer access to the text.

### No Header Files / No Import Trees
In Plain English, every `.plain` file in the project folder participates equally in a unified namespace. Stage 02 aggregates all files into the global `source files` linked list. This eliminates header management, include guards, and circular import problems.
