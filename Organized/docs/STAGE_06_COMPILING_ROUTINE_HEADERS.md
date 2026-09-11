# Stage 06: Routine Header Compilation & Moniker Construction

**Stage Number**: `[06/16]`  
**Routine Header**: `To compile a directory (compile the headers of the routines):`  
**Location**: [PlainEnglishCompiler.plain: lines 2222–2539](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L2222-L2539)

---

## 1. Overview & Purpose

Stage 06 processes every routine header (e.g., `To draw a circle given a radius and a color:`). It identifies parameter slots, categorizes the routine into its functional paradigm (decider, function, callback, or procedure), normalizes grammatical synonyms, and serializes the signature into a canonical search key known as a **moniker**.

---

## 2. Step-by-Step Code Walkthrough

### Step 1: Routine Paradigm Classification
```plain
To compile the header of a routine:
  ...
  Slap a rider on the routine's routine header.
  Move the rider (compiler rules).
  If the rider's token substring is "decide",
    Compile the header of the routine given the rider (decider);
    Exit.
  If the rider is the start of any function,
    Compile the header of the routine given the rider (function);
    Exit.
  If the rider's token substring is "compatibly",
    Compile the header of the routine given the rider (callback);
    Exit.
  Compile the header of the routine given the rider (procedure).
```
*(Lines 2239–2254)*

1. **Decider**: Sets `routine's decider flag`. Deciders represent boolean truth checks that return CPU zero/carry flags (`ZF`/`CF`) directly.
2. **Function**: Returns a value placed in register `EAX`.
3. **Callback**: Functions exported to Windows with standard `stdcall` callee-cleanup calling conventions.
4. **Procedure**: Standard imperative subroutine.

### Step 2: Parameter Extraction & Indefinite Articles
```plain
To compile some monikettes and some parameters given a rider (indefinite article):
  Add a parameter to the parameters given "parameter" and the rider's token substring's first byte pointer.
  ...
  Scan a name given the rider.
  Extend the parameter's name with the name.
  If the rider's token substring is "called",
    Compile the call'd part given the rider and the parameter.
  Resolve the parameter.
  Add a monikette to the monikettes given the parameter's type.
```
*(Lines 2369–2384)*

In Plain English, parameter slots in headers are indicated by indefinite articles:
- `To move a box by a distance`:
  - `"a box"` -> Parameter 1 of type `box`.
  - `"a distance"` -> Parameter 2 of type `distance`.
- If an explicit identifier is needed: `a number called width` -> Type is `number`, name is `width`.

### Step 3: Synonyms and Preposition Normalization
```plain
To add a monikette to some monikettes given a string:
  ...
  If the string is "from", Put "from/given/with/using" into the monikette's string; Exit.
  If the string is "given", Put "from/given/with/using" into the monikette's string; Exit.
  If the string is "with", Put "from/given/with/using" into the monikette's string; Exit.
  If the string is "using", Put "from/given/with/using" into the monikette's string; Exit.
  If the string is "in", Put "in/into/to" into the monikette's string; Exit.
  If the string is "into", Put "in/into/to" into the monikette's string; Exit.
  If the string is "is", Put "is/are" into the monikette's string; Exit.
  If the string is "are", Put "is/are" into the monikette's string; Exit.
  ...
```
*(Lines 2297–2365)*

English allows interchangeable prepositions. The compiler maps these variants into single canonical tokens inside the `monikettes` list.

### Step 4: Moniker Assembly & Routine Indexing
- The sequence of literal words and typed parameter slots is serialized into a single string called the **moniker**.
- Example moniker: `draw <box> from/given/with/using <color>`
- The routine is added to the global `routine index` (7,919-bucket hash table).

---

## 3. Technical Rationale & Architectural Decisions

### What is a "Moniker"?
In languages like C++, name mangling generates symbols like `_Z4draw3Box5Color`. In Plain English, the **moniker** serves as a human-readable, normalized sentence signature with type placeholders. When calling a routine, the compiler transforms the call statement using the same normalization rules and directly queries `routine index`.

### Natural Language Flexibility without Ambiguity
Mapping prepositions (`from`, `given`, `with`, `using`) to a unified canonical token allows programmers to use phrasing that fits grammatical context without writing multiple boilerplate wrapper functions.
