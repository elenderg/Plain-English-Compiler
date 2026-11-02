# Stage 16: Executable Output & Pipeline Finalization

**Stage Number**: `[16/16]`  
**Routine Header**: `To compile a directory (write the exe):` & `To compile a directory (stop):`  
**Location**: [PlainEnglishCompiler.plain: lines 5428–5456](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L5428-L5456)

---

## 1. Overview & Purpose

Stage 16 writes the linked PE executable buffer from memory onto disk at the destination path, checks for file I/O errors, stops performance profiling timers, and reports final process metrics.

---

## 2. Step-by-Step Code Walkthrough

```plain
To compile a directory (write the exe):
  ...
  Start the writing timer.
  Write the compiler's exe to the compiler's exe path.
  If the i/o error is not blank,
    Abort with the i/o error.
  Stop the writing timer.
```
*(Lines 5428–5439)*

### Step 1: Destination Path Resolution
```plain
To put the compiler's exe path into a path:
  Extract a designator from the compiler's directory.
  Remove any trailing backslash from the designator.
  Put the compiler's directory then the designator then ".exe" into the path.
```
*(Lines 5440–5444)*
- Identifies the folder name of the target project directory.
- Appends `.exe` to create the output binary name directly inside the target directory (e.g., `D:\MyProject\` -> `D:\MyProject\MyProject.exe`).

### Step 2: Binary File Flush
- `Write the compiler's exe to the compiler's exe path`:
  - Invokes Win32 `CreateFileA` (`GENERIC_WRITE`, `CREATE_ALWAYS`).
  - Calls `WriteFile` with the exact size calculated in `To put the compiler's exe size into a size`.
  - Closes the file handle with `CloseHandle`.

### Step 3: Pipeline Completion & Metrics Reporting
```plain
To compile a directory (stop):
  Stop the compiler's timer.
  Write the CRLF string then "          Process finished." to Stdout.
```
*(Lines 5451–5456)*
- Stops the master performance counter.
- In `compile the project` ([lines 350](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L350)), logs total compilation time:
  `Write "Compilation time: " then the compiler's timer's string then " ms." to StdOut.`

---

## 3. Technical Rationale & Architectural Decisions

### Atomic Write-Only-on-Success
The executable file on disk is only created or overwritten at the very end of the pipeline after all 15 preceding analysis, addressing, and code generation stages succeed. If an error occurs at any point (such as a syntax mistake or missing routine), the compiler aborts early, leaving any existing binary uncorrupted.

### Transparent Profiling
The compiler instruments every individual phase with dedicated microsecond timers (`loading timer`, `scanning timer`, `compiling routine bodies timer`, `transmogrifying timer`, `linking timer`). This gives language maintainers granular insight into which compilation phases require optimization.
