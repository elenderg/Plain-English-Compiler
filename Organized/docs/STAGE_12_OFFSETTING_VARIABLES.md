# Stage 12: Stack Frame & Parameter Offsetting

**Stage Number**: `[12/16]`  
**Routine Header**: `To compile a directory (offset parameters and variables):`  
**Location**: [PlainEnglishCompiler.plain: lines 4515–4607](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L4515-L4607)

---

## 1. Overview & Purpose

Stage 12 establishes the x86 32-bit execution stack layout for every compiled routine. It calculates the exact memory displacements of parameters and local variables relative to the base pointer register (`EBP`) and determines the overall stack allocation size needed for the routine prolog (`SUB ESP, local_size`).

---

## 2. Step-by-Step Code Walkthrough

```plain
To compile a directory (offset parameters and variables):
  ...
  Offset the parameters in the routines.
  Offset the locals in the routines.
```
*(Lines 4515–4523)*

### Step 1: Parameter Offsetting (`[EBP + 8 + ...]`)
```plain
To offset the parameters in a routine:
  ...
  Clear the routine's parameter size.
  Loop.
    Get a parameter from the routine's parameters.
    If the parameter is nil, exit.
    If the routine's callback flag is set,
      Set the parameter's by-value flag.
    If the parameter's previous parameter is nil,
      Put 8 into the parameter's offset. \ skip the return address and saved ebp
    If the parameter's previous parameter is not nil,
      Put the parameter's previous parameter's offset plus 4 into the parameter's offset.
    Add 4 to the routine's parameter size.
  Repeat.
```
*(Lines 4533–4548)*

- On x86 function calls, `CALL` pushes the 4-byte return address, and the function prolog executes `PUSH EBP; MOV EBP, ESP`.
- Therefore:
  - `[EBP + 0]` = Saved previous `EBP`
  - `[EBP + 4]` = 32-bit Return Address (EIP)
  - `[EBP + 8]` = First Parameter
  - `[EBP + 12]` = Second Parameter, etc.
- In Plain English, parameters are passed by reference (4-byte pointers) by default, or as 4-byte values for Win32 callback signatures (`stdcall`). Every parameter occupies 4 bytes on the call stack.

### Step 2: Local Variable Offsetting (`[EBP - ...]`)
```plain
To offset the locals in a routine:
  ...
  Clear the routine's local size.
  Loop.
    Get a local from the routine's locals.
    If the local is nil, exit.
    If the local's previous local is not nil,
      Put the local's previous local's offset into the local's offset.
    Put the local's type's length into a length.
    If the local's kind is "scratch",
      Put a pointer's magnitude into the length.
    Round the length up to the nearest multiple of 4.
    Subtract the length from the local's offset.
    Add the length to the routine's local size.
  Repeat.
```
*(Lines 4558–4574)*

- Locals grow downwards from `EBP`.
- Every local variable's byte size is rounded up to a 4-byte boundary for DWORD alignment.
- Offsets are calculated as negative displacements: `[EBP - 4]`, `[EBP - 8]`, etc.
- The total accumulated size is stored in `routine's local size` to be emitted in the routine's stack allocation instruction (`SUB ESP, local_size`).

---

## 3. Technical Rationale & Architectural Decisions

### Standard x86 Calling Convention Compatibility
Aligning stack parameters at `[EBP + 8]` and locals at negative offsets matches the standard 32-bit `cdecl` and `stdcall` ABIs used by the Windows operating system and modern x86 debuggers (like Visual Studio and x64dbg).

### 4-Byte Stack Alignment
Rounding every local allocation up to the nearest multiple of 4 ensures that 32-bit integer, pointer, and record accesses remain DWORD-aligned, preventing CPU unaligned access penalties on x86 processors.
