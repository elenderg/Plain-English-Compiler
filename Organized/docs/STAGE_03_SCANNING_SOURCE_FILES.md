# Stage 03: Lexical Ingestion & Initial Scanning

**Stage Number**: `[03/16]`  
**Routine Header**: `To compile a directory (scan the source files):`  
**Location**: [PlainEnglishCompiler.plain: lines 624–1687](file:///c:/Plain-English-Compiler/Compiler/PlainEnglishCompiler.plain#L624-L1687)

---

## 1. Overview & Purpose

Stage 03 turns raw character buffers into structural definition shells. In standard compilers, lexical tokenization emits a flat token stream. In Plain English, Stage 03 extracts high-level constructs—**Types**, **Globals**, and **Routines**—while deferring the detailed parsing of routine bodies to Stage 10.

---

## 2. Step-by-Step Code Walkthrough

### Step 1: Rider Navigation & Lexical Rules
```plain
To scan a source file:
  Slap a rider on the source file's buffer.
  Move the rider (compiler rules).
  Loop.
    If the compiler's abort flag is set, exit.
    If the rider's token substring is blank, exit.
    If the rider's token substring is the start of any type,
      Scan a type given the rider;
      Repeat.
    If the rider's token substring is the start of any global,
      Scan a global given the rider;
      Repeat.
    If the rider's token substring is the start of any routine,
      Scan a routine given the rider;
      Repeat.
    Abort with "Error #4. Expected the start of a type definition, global variable declaration, or routine declaration..."
  Repeat.
```
*(Lines 641–659)*

The scanner uses cursor abstraction structures called **riders** (`source substring`, `token substring`, and cursor pointer):
1. **Comment Stripping**:
   - Single-line comments starting with `\` advance to the return byte (`0x0D`).
   - Nested remarks enclosed in `[...]` count bracket nesting depth.
2. **Glomming Word Tokens**:
   - `Move the rider (code rules - glom)` gathers alphanumeric sequences, symbols, and hyphens into word tokens.
   - Possessives like `'s` and trailing plural `'` are singled out as dedicated grammatical tokens.
3. **Punctuation Marks**:
   - Colons (`:`), semicolons (`;`), commas (`,`), and periods (`.`) are isolated as clause delimiters.

### Step 2: Scanning Type Shells
```plain
To scan a type given a rider:
  Add the type to the types given the rider's token substring's first byte pointer.
  Move the rider (compiler rules).
  Scan the type's name given the rider.
  Put the type's name into the type's plural name.
  Pluralize the type's plural name.
  ...
```
*(Lines 863–891)*
- Triggered by indefinite articles: `"A"`, `"An"`, `"Some"`.
- Reads the type name (e.g., `box`).
- Automatically computes the plural form via `Pluralize` (e.g., `boxes`).
- Inspects the verb:
  - `"is [unit of measure] [base]"`: Creates a scaled numeric subtype (e.g., `A twip is 1/20 of a point`).
  - `"is a record with [fields]"`: Scans field specifications into the type's `fields` list.
  - `"is a pointer to [target]"`: Configures a pointer type descriptor.

### Step 3: Scanning Global Variable Shells
- Triggered by `"The "` followed by variable and type specifications.
- Example: `The image base is an address equal to 4194304.`
- Scans name, type name, and preserves the initialization substring (`global body`) for Stage 05.

### Step 4: Scanning Routine Shells & Alternate Wordings
```plain
To scan a routine header given a rider:
  ...
  Put the rider's token substring's first byte pointer into the routine header's first byte pointer.
  Loop.
    If the rider's token substring is the semi-colon byte, exit.
    If the rider's token substring is the colon byte, exit.
    ...
```
*(Lines 1637–1650)*
- Routine headers begin with `"To "`.
- The scanner records the byte boundaries of the header (`routine header` substring) up to the `:` or `;` delimiter.
- **Alternate Wordings (Synonyms)**:
  Plain English allows defining multiple natural headers for the same routine:
  ```plain
  To write a string with a color;
  To stroke a string with a color:
    Put the color into the context's color.
    Stroke the string.
  ```
  If a header terminates with `;`, Stage 03 links the routine shell as an alternate wording (`routine given a rider (alternate wording)`), referencing the following implementation body.
- Scans the body boundaries (`routine body` substring) up to the terminating period before the next definition.

---

## 3. Technical Rationale & Architectural Decisions

### Deferred Tokenization of Function Bodies
Rather than deeply parsing the statements within function bodies during the initial scan, Stage 03 only captures their slice pointers (`first byte pointer` and `last byte pointer` in source memory). This architectural decision guarantees:
1. **Order Independence**: Every type, record field, global variable, and routine header across all files is fully known before any statement inside a routine body is validated.
2. **Speed & Low Memory**: Routine bodies remain unparsed substrings until Stage 10. Only reachable routines ever get semantically analyzed and compiled into fragments.

### Natural Syntax Word Classification
Words in Plain English can be names, operators, prepositions, or conjunctions. The scanner embeds natural English grammatical rules (`is any preposition`, `is any conjunction`, `is any verb`) directly into token movement, avoiding rigid grammar tables and supporting fluid prose expressions.
