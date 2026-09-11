# Stage 13: Memory Boroughs & Virtual Addressing

**Stage Number**: `[13/16]`  
**Routine Header**: `To compile a directory (address):`  
**Location**: [PlainEnglishCompiler.plain: lines 4608–4857](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L4608-L4857)

---

## 1. Overview & Purpose

Stage 13 performs the concrete virtual memory layout for the Windows Portable Executable (PE). It partitions program data and code into three 4096-byte page-aligned memory **boroughs** (Import, Data, Code), calculating the exact 32-bit runtime Relative Virtual Address (RVA) and absolute address (`ImageBase + RVA`, starting at `0x00400000`) for every DLL import thunk, global variable, string literal, and machine instruction fragment.

---

## 2. Step-by-Step Code Walkthrough

```plain
To compile a directory (address):
  ...
  Put 4096 into an address. \ Section RVA start at 4KB
  Put the address into the import borough's base address.
  Address the imports given the address.
  Put the address minus the import borough's base address into the import borough's length.
  Round the address up to the nearest multiple of 4096.

  Put the address into the data borough's base address.
  Address the globals given the address.
  Address the literals given the address.
  Put the address minus the data borough's base address into the data borough's length.
  Round the address up to the nearest multiple of 4096.

  Put the address into the code borough's base address.
  Address the routines given the address.
  Put the address minus the code borough's base address into the code borough's length.
```
*(Lines 4608–4627)*

### 1. Import Borough Addressing (`.idata`)
```plain
To address an import:
  ...
  Put the current name address into the import's import header's name memory address.
  ...
  Put the current thunk address into the import's import header's first thunk memory address.
  Address the import's entries.
```
*(Lines 4655–4666)*
- Lays out the PE Import Directory Table:
  - `Import Directory Headers`: Array of 20-byte descriptors.
  - `Import Address Table (IAT) / Thunks`: Array of 4-byte pointers where Windows loader writes resolved API pointers.
  - `Hint/Name Table`: ASCII names of DLLs (e.g., `kernel32.dll`, `user32.dll`) and exported API names (e.g., `ExitProcess`, `WriteFile`).

### 2. Data Borough Addressing (`.data`)
```plain
To address a variable given an address:
  ...
  Put the image base plus the address into the variable's address.
  Add the variable's type's length to the address.
  If the variable's type can be reduced to "string",
    Add the variable's data's length plus 1 to the address.
  Round the address up to the nearest multiple of 4.
```
*(Lines 4690–4699)*
- Allocates fixed global memory slots for all compiled globals and string/data literals.
- Pads each slot to a 4-byte multiple.

### 3. Code Borough & Fragment Length Calculation (`.code`)
```plain
To address a fragment given an address:
  ...
  Put the image base plus the address into the fragment's address.
  If the fragment's tag is the push address tag, Add 7 to the address; Exit.
  If the fragment's tag is the call internal tag, Add 5 to the address; Exit.
  If the fragment's tag is the load address tag, Add 12 to the address; Exit.
  If the fragment's tag is the increment tag, Add 10 to the address; Exit.
  If the fragment's tag is the dereference tag, Add 14 to the address; Exit.
  If the fragment's tag is the jump false tag, Add 9 to the address; Exit.
  ...
```
*(Lines 4726–4750)*
- Assigns target addresses to every routine and instruction fragment.
- Advances the cursor by the exact number of machine instruction bytes that will be produced when transmogrifying that fragment into x86 opcodes.

---

## 3. Technical Rationale & Architectural Decisions

### Ahead-of-Time Byte Length Reservation
Notice that `address a fragment` increments the address by explicit constants (e.g., `Add 5 to the address` for `call internal`, `Add 9` for `jump false`). Because the exact opcode sequence for every IR fragment is known ahead of time, the compiler computes the absolute target address of all jumps, calls, and variables *before* emitting any machine code.

### 4096-Byte Page Alignment
Windows virtual memory management enforces 4KB (`0x1000`) page alignment on PE sections. By rounding the length of each borough up to a multiple of 4096, the compiler guarantees that Section Headers (`.idata`, `.data`, `.code`) can be loaded directly by the Windows NT kernel loader without section collision or memory faults.
