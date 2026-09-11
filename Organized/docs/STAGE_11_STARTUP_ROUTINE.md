# Stage 11: Startup Routine Construction

**Stage Number**: `[11/16]`  
**Routine Header**: `To compile a directory (add and compile the built-in startup routine):`  
**Location**: [PlainEnglishCompiler.plain: lines 4471–4514](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L4471-L4514)

---

## 1. Overview & Purpose

Stage 11 synthesizes the master binary entry point function: `~Initialize before run and Run and Finalize after run`. This wrapper orchestrates the complete runtime execution lifecycle: initializing the runtime environment, setting up global variables, invoking user code in `Run`, executing finalizers, and terminating the operating system process via `ExitProcess`.

---

## 2. Step-by-Step Code Walkthrough

```plain
To add and compile the initialize before run and run and finalize after run routine:
  ...
  Add a routine to the routines given nil.
  Append "~Initialize before run and Run and Finalize after run" to the routine's header string.
  Append "Initialize before run. " to the routine's body string.
  Append the global initializers to the routine's body string.
  Append "Run. " to the routine's body string.
  Append the global finalizers to the routine's body string.
  Append "Finalize after run. " to the routine's body string.
  Append "Call ""kernel32.dll"" ""ExitProcess"" with 0. " to the routine's body string.
  ...
  Compile the header of the routine.
  Compile the body of the routine.
```
*(Lines 4481–4494)*

### Step 1: Pre-Execution Environment Setup
- Appends `"Initialize before run. "`: Initializes standard I/O handles, console code pages, heap management structures, and memory allocators.

### Step 2: Global Initializers Injection
```plain
To append the global initializers to a string:
  Loop.
    Get a global from the globals.
    If the global is nil, exit.
    If the global's literal is nil, repeat.
    If the global's compiled flag is not set, repeat.
    Append the global's initializer string to the string.
  Repeat.
```
*(Lines 4496–4504)*
- Only globals whose `compiled flag` was set during Stage 10 (meaning they are actually used by reachable routines) have initializers generated.

### Step 3: User Entry Point (`Run`)
- Appends `"Run. "`, transferring control to the user application.

### Step 4: Tear-down & Global Finalization
```plain
To append the global finalizers to a string:
  Loop.
    Get a global from the globals.
    If the global is nil, exit.
    If the global's compiled flag is not set, repeat.
    If the global's type should not be finalized, repeat.
    Append "~finalize the " then the global's name then ". " to the string.
  Repeat.
```
*(Lines 4506–4513)*
- Invokes automatic resource cleanup (`~finalize the [global]`) on all active global variables holding dynamic buffers, strings, or records.

### Step 5: Post-Execution Cleanup & OS Exit
- Appends `"Finalize after run. "`: Flushes screen buffers, closes file handles, and tears down the graphics/window subsystems.
- Emits a Win32 system exit: `Call "kernel32.dll" "ExitProcess" with 0.`.

---

## 3. Technical Rationale & Architectural Decisions

### Synthesizing Source Statements Rather Than Emitting Opcodes Directly
Instead of manually generating raw assembly instructions (`CALL`, `PUSH`, `RET`) for the program startup sequence, the compiler builds a natural-language Plain English statement string and routes it directly through `Compile the header` and `Compile the body`. This guarantees that calling conventions, argument staging, stack frames, and error checks remain uniform across all user and compiler-generated routines.

### Pruning Unused Global Initializers
Globals defined in the standard library that are unreferenced by the current application are ignored (`compiled flag is not set`). This keeps the entry point concise and prevents initializing unused subsystems.
