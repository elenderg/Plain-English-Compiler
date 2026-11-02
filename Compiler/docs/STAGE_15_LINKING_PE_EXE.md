# Stage 15: PE & DOS Header Linking

**Stage Number**: `[15/16]`  
**Routine Header**: `To compile a directory (link):`  
**Location**: [PlainEnglishCompiler.plain: lines 5195–5427](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L5195-L5427)

---

## 1. Overview & Purpose

Stage 15 acts as the compiler's internal linker. Rather than generating an intermediate `.obj` file to be processed by an external linker, Plain English directly constructs the Microsoft Windows Portable Executable (PE) image in an in-memory buffer, including the MS-DOS stub, COFF header, Optional Header, Section Headers, Import Address Table, and Borough data dumps.

---

## 2. Step-by-Step Code Walkthrough

```plain
To link:
  Round up borough sizes.
  Initialize a DOS header.
  Initialize a PE header.
  Fill the compiler's exe with the NULL byte given the compiler's exe size.
  Blurt the DOS header into the compiler's exe.
  Blurt the PE header into the compiler's exe.
  Blurt the imports [DLLs] into the compiler's exe.
  Blurt the globals into the compiler's exe.
  Blurt the literals into the compiler's exe.
  Blurt the routines into the compiler's exe.
```
*(Lines 5204–5214)*

### Step 1: DOS 2.0 Compatibility Stub
```plain
To initialize a DOS header:
  Put 23117 [$5A4D] into the DOS header's signature. \ "MZ"
  Put 64 [$0040] into the DOS header's AddressOfRelocationTableInFile.
  Put 256 [$00000100] into the DOS header's AddressOfPEHeaderInFile.
```
*(Lines 5224–5227)*
- Sets standard `MZ` magic signature.
- Positions the offset to the PE Header at file offset `0x00000100` (256 bytes).

### Step 2: COFF & PE Standard Headers
```plain
To initialize a PE header (standard header):
  Put 17744 [$00004550] into the PE header's Signature. \ "PE\0\0"
  Put 332 [$014C] into the PE header's MachineType. \ Intel 386+
  Put 3 [$0003] into the PE header's NumberOfSections. \ idata, data, code
```
*(Lines 5238–5241)*

### Step 3: Optional Header Configuration
```plain
To initialize a PE header (optional header):
  Put 267 [$010B] into the PE header's MagicNumber. \ PE32 format
  Put the code borough's length into the PE header's SizeOfCodeInFile.
  ...
  Find a routine given "~Initialize before run and Run and Finalize after run" ...
  Put the routine's address minus the image base into the PE header's AddressOfEntryPointInMemory.
  Put the image base into the PE header's ImageBase. \ $00400000
  Put 4096 into the PE header's MemoryAlignment.
  Put 4096 into the PE header's FileAlignment.
  ...
  Put 3 into the PE header's SubSystem. \ Windows CUI (Console)
```
*(Lines 5248–5283)*
- Points `AddressOfEntryPointInMemory` directly to the startup routine compiled in Stage 11.
- Specifies image base as `0x00400000`.
- Configures subsystem `3` (`IMAGE_SUBSYSTEM_WINDOWS_CUI`).

### Step 4: Section Header Descriptors
- **`idata`** (`$C0000040`): Readable, Writable, Initialized Data.
- **`data`** (`$C0000040`): Readable, Writable, Initialized Data.
- **`code`** (`$E0000020`): Executable, Readable, Writable, Code Object.

### Step 5: Serializing Binary Slices ("Blurting")
The compiler "blurts" (raw binary block copy) each memory borough into the output buffer at its pre-calculated file displacement:
- DOS Header at byte 0
- PE Header at byte 256
- Import Borough at file offset `0x1000` (4096)
- Data Borough at `import_base + import_size`
- Code Borough at `data_base + data_size`

---

## 3. Technical Rationale & Architectural Decisions

### Direct Binary Synthesis without External Linkers
Traditional compilers rely on external toolchains (GNU Binutils, LLVM LLD, or MSVC LINK) to turn intermediate object files into an executable. This introduces external version dependencies and slow subprocess overhead. By synthesizing the PE structure natively, the Plain English compiler requires zero external software, achieving link times under 5 milliseconds.

### Code Section Writable Flag (`$E0000020`)
The code section is marked both executable and writable. This permits low-level runtime self-modifying code or runtime hook patches without calling Win32 `VirtualProtect` APIs.
