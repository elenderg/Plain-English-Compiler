# Stage 10: Routine Body Semantic Compilation & Fragment Generation

**Stage Number**: `[10/16]`  
**Routine Header**: `To compile a directory (compile the bodies of the routines):`  
**Location**: [PlainEnglishCompiler.plain: lines 3005–4470](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L3005-L4470)

---

## 1. Overview & Purpose

Stage 10 is the largest semantic analysis engine in the compiler. It starts at the program entry points (`Initialize before run`, `Run`, `Finalize after run`) and compiles statements by matching natural-language clauses to indexed routines. It builds an Intermediate Representation (IR) consisting of **Fragments** (prologs, parameter pushes, calls, branch jumps, returns, and raw intel instructions).

---

## 2. Step-by-Step Code Walkthrough

### Step 1: Reachability-Driven Compilation (Dead-Code Pruning)
```plain
To compile the bodies of the routines:
  Compile the body of the routine called "Initialize before run".
  Compile the body of the routine called "Run".
  Compile the body of the routine called "Finalize after run".
```
*(Lines 3014–3018)*

- Only routines reachable from `Run` or the startup lifecycle are compiled.
- If a routine body calls another routine, the compiler resolves and recursively marks that routine for body compilation (`compiled flag`).
- Uncalled routines remain uncompiled and are excluded from the output binary.

### Step 2: Moniker Matching & "Bubbling"
```plain
To find a routine given a moniker and a monikette and an index:
  ...
  Loop.
    Append the monikette to the moniker (while bubbling).
    Find a refer given the moniker and the index.
    If the refer is not nil,
      Find the routine given the moniker and the monikette's next monikette and the index.
    If the routine is not nil, break.
    Bubble the monikette.
    If the monikette is bubbled out, break.
  Repeat.
```
*(Lines 3161–3179)*

- As statements are parsed, expressions are converted into monikettes.
- **Bubbling Algorithm**: If the exact concrete type does not match a registered routine moniker, `Bubble the monikette` promotes the parameter type to its `base type` and re-checks the routine index.
- If no routine matches after bubbling to root types, the compiler falls back to the `utility index` (Stage 09).

### Step 3: Statement Dispatch
```plain
To compile the next statement given a rider:
  ...
  If the rider's token substring is "if",
    Compile the next statement given the rider (if); Exit.
  If the rider's token substring is "loop",
    Compile the next statement given the rider (loop); Exit.
  If the rider's token substring is "privatize",
    Compile the next statement given the rider (privatize); Exit.
  Compile the next statement given the rider (other).
```
*(Lines 3229–3248)*

1. **`if` Conditionals**:
   - Compiles decider expressions (e.g., `If the string is blank, exit.`).
   - Emits a `jump false` fragment with a deferred backpatch label.
2. **`loop` / `repeat`**:
   - Emits `loop` marker fragment.
   - Statements inside loop compile until `repeat`, emitting an unconditional jump back to the loop start.
   - `exit` or `break` emits jump fragments targeting the loop exit point.
3. **`privatize`**:
   - Clones a variable locally on the stack to prevent mutation of the caller's parameter.
4. **General Calls (`other`)**:
   - Parses arguments, pushes addresses/values, and emits `call internal` or `call external`.

### Step 4: Emitting Fragments (IR)
Each routine accumulates an intrusive linked list of `fragment` records:
- `prolog` / `epilog`: Stack frame lifecycle.
- `push address` / `push value`: Argument passing.
- `call internal`: Inter-routine function call.
- `call external`: Win32 DLL import invocation.
- `jump false` / `repeat` / `break`: Control flow.
- `load eax` / `save eax`: Return value propagation.
- `intel`: Raw machine byte injection (`Intel $90.`).

---

## 3. Technical Rationale & Architectural Decisions

### Why Tree Traversal via Reachability?
Traditional C compilers compile every function found in every included translation unit, delegating dead-code elimination to the linker. Plain English compiles only what is strictly referenced from `Run`. This dramatically reduces compilation time and produces tiny, self-contained executables.

### The "Bubbling" Mechanism
Bubbling allows the developer to write natural English without explicit type casting. If a routine expects a `string` and the developer passes a `file path` (which derives from `string`), the bubbling mechanism automatically promotes the argument type and successfully binds the call without programmer friction.
