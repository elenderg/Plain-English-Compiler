# Stage 01: Compiler Initialization & Built-in Types

**Stage Number**: `[01/16]`  
**Routine Header**: `To compile a directory (start):`  
**Location**: [PlainEnglishCompiler.plain: lines 425–520](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L425-L520)

---

## 1. Overview & Purpose

Stage 01 prepares the compiler state for a clean build cycle. It clears any leftover data structures from previous compilation tasks, starts performance profiling timers, sets up prime-sized hash indexes for fast O(1) symbol lookups, and registers the foundational built-in types upon which all Plain English programs depend.

---

## 2. Step-by-Step Code Walkthrough

### Step 1: State Finalization & Cleanup
```plain
To compile a directory (start):
  Write in strong blue " [01/16]" to StdOut.
  Write "  Starting compilation…" to Stdout.
  Finalize the compiler.
  Start the compiler's timer.
  Initialize the compiler given the directory.
  Add the built-in types.
```
- Calls `Finalize the compiler`, which systematically traverses and deallocates every linked list and hash index:
  ```plain
  To finalize the compiler:
    Destroy the utility index.
    Destroy the routine index.
    Destroy the literal index.
    Destroy the global index.
    Destroy the type index.
    Destroy the imports.
    Destroy the routines.
    Destroy the literals.
    Destroy the globals.
    Destroy the types.
    Destroy the source files.
  ```
  *(Lines 433–445)*

### Step 2: Hash Table Initialization with Prime Moduli
In `To initialize the compiler given a directory:` ([lines 446–459](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L446-L459)):
- The target compilation directory path is stored.
- Error flags (`abort flag`, `abort message`, `abort path`, `abort row#`) are zeroed out.
- Symbol indexes are allocated using prime numbers of buckets:
  - **Type Index**: 4,027 buckets
  - **Global Index**: 4,027 buckets
  - **Literal Index**: 4,027 buckets
  - **Routine Index**: 7,919 buckets
  - **Utility Index**: 4,027 buckets

### Step 3: Registering Primitive Root Types
In `To add the built-in types:` ([lines 460–469](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L460-L469)):
```plain
To add the built-in types:
  Add a built-in type given "byte" and "bytes" and 1.
  Add another built-in type given "record" and "records" and 0.
```
- `byte`: Allocated with length 1 byte. Base type points to itself.
- `record`: Allocated with length 0 bytes. Base type points to itself. Serves as the structural root of all compound types and objects.
- Both types are entered into the `type index` with their singular and plural forms.

### Step 4: Diagnostic & Locus Binding Setup
In `To abort with a string and a byte pointer:` ([lines 503–533](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L503-L533)):
- Whenever any phase detects a syntax or semantic error, it provides an abort string and a `locus` (raw byte pointer pointing to the current token inside loaded memory).
- `find a path and a row# given a byte pointer` loops through the source files' memory buffers to locate which file contains that pointer address, then scans newline delimiters (`0x0D` / return byte) to convert the raw address into a human-readable 1-indexed line number.

---

## 3. Technical Rationale & Architectural Decisions

### Why Prime Bucket Counts (4027 & 7919)?
Plain English routine names are full natural language sentences (e.g., `To write a string with a font and a size and a color and a spot:`). Conventional power-of-two hash tables can suffer from clustering when hashing natural-language phrases that share common prefixes (`To draw...`, `To write...`). Using large prime moduli significantly reduces collision rates across open-addressing / linked-bucket chains without needing expensive runtime rehashing.

### Why Minimal Built-in Types?
Rather than predefining dozens of primitive types (such as `int8`, `uint32`, `float64`, `bool`, `char*`), the compiler provides only:
1. `byte` (atomic unit of memory)
2. `record` (structural grouping mechanism)

All other high-level concepts in Plain English (`number`, `string`, `pointer`, `box`, `color`, `rider`, etc.) are defined in `StandardLibrary.plain` as derivations or records built on top of these two root types. This keeps the compiler core compact, minimal, and fully self-bootstrapping.

### Zero-Copy Error Reporting
Instead of storing line and column metadata on every AST node and token (which would significantly inflate memory consumption), the compiler simply records a `locus` (a 32-bit memory pointer into the source buffer). If and only if an error occurs, the compiler maps the pointer back to file and line number on demand via `find a row# given a source file and a byte pointer`.
