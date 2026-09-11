# 02: Syntax, Lexical Rules, and Conventions

Plain English replaces conventional programming symbols with English grammar. Understanding how the compiler processes words, punctuation, and case is key to writing clean and idiomatic Plain English.

---

## 1. Punctuation and Statements

### 1.1 The Period (`.`)
Every executable statement in Plain English must end with a period:
```plain
Add 1 to the count.
Put "Welcome" into the greeting.
```
If you forget a period, the compiler will alert you:
`"You seem to have forgotten a period around here."`

### 1.2 The Colon (`:`)
A colon is used at the end of definitions:
- **Routine declarations**: `To draw a circle given a radius:`
- **Type definitions**: `A monster is a thing with:`

### 1.3 The Semicolon (`;`) for Alternate Wordings
Plain English allows you to give a routine multiple names or synonym headers using semicolons:
```plain
To write a string with a color;
To stroke a string with a color:
  Put the color into the context's color.
  Stroke the string.
```
Now caller code can say either `Write "Hello" with the red color.` or `Stroke "Hello" with the red color.`

---

## 2. Comments and Remarks

### 2.1 Single-Line Comments (`\`)
The backslash character (`\`) begins a comment that runs to the end of the line:
```plain
\ This is a single-line comment. The compiler ignores everything after the backslash.
Put 10 into the score. \ Set initial player score
```

### 2.2 Inline Remarks (`[...]`)
Square brackets enclose remarks or annotations that can be placed anywhere, even in the middle of statements:
```plain
The image base is an address equal to 4194304. [$00400000]
A degree is a number [0 to 359].
```

---

## 3. Articles: Definite vs. Indefinite

Plain English uses articles to distinguish between **creating** variables and **referencing** existing ones:

| Article | Purpose | Example |
| :--- | :--- | :--- |
| `a`, `an`, `another`, `some` | **Indefinite**: Declares a new variable or parameter slot | `Put 5 into a number.` |
| `the` | **Definite**: Refers to an already existing variable | `Add 1 to the number.` |

Example:
```plain
Put "Alice" into a string called name.       \ Creates a new local variable called 'name'
Append " Smith" to the name.   \ Refers to the existing local variable
```

---

## 4. Possessives and Field Access (`'s`)

To access fields of a record or properties of an object, use the natural English possessive `'s` (or trailing `'` for plurals):
```plain
Put the box's width into a width.
Put the person's first name into a string.
Draw the monster's left eye.
```
This takes the place of dot-notation (`box.width`, `person.firstName`) in other languages.

---

## 5. Literals

Plain English supports several literal data formats:

### Numbers
- Standard decimal: `0`, `42`, `-100`, `1000`
- Fractions: `1/2`, `3/4`, `20/1`

### Hexadecimal
Prefixed with a dollar sign (`$`):
```plain
Put $00400000 into the address.
Put $5A4D into the signature.
```

### Strings
Surrounded by double quotation marks (`"`):
```plain
Put "Plain English" into the title.
```
To include quotes inside a string, escape them with double quotes (`""`):
```plain
Write "He said, ""Hello!""" to the console.
```

### Flags (Booleans)
The literals `yes` and `no` represent boolean truth values:
```plain
Set the flag.        \ Sets to yes (true)
Clear the flag.      \ Sets to no (false)
```

---

## 6. Case Sensitivity and Formatting

- **Case Insensitivity**: Plain English is completely case-insensitive. `The Box`, `the box`, and `THE BOX` refer to the same identifier.
- **Indentation**: Indentation (usually two spaces) is used to indicate code nesting inside routine bodies, loops, and conditional branches, but it is entirely optional.

---

## Next Guide
Continue to [03: Type System and Data Structures](file:///c:/Plain-English-Compiler/Compiler/language-guide/03_TYPE_SYSTEM.md).
