# 01: Introduction to Plain English

Welcome to **Plain English**, a unique, general-purpose programming language designed to look and read like natural English prose. Unlike conventional programming languages filled with curly braces (`{}`), parentheses, semicolons, and symbolic sigils, Plain English programs are written in clean, full sentences.

---

## 1. The Core Philosophy

### 1.1 Readability First
Code is read far more often than it is written. Plain English is designed so that a non-programmer, domain expert, or casual observer can read a program and immediately understand its intent without needing to decipher esoteric syntax.

Compare this typical procedural code in C/JavaScript:
```c
int count = 0;
for (int i = 0; i < len; i++) {
    if (items[i].active && items[i].price > 100) {
        count++;
    }
}
```
With Plain English:
```plain
Put 0 into a count.
Loop.
  Get an item from the items.
  If the item is nil, break.
  If the item is not active, repeat.
  If the item's price is greater than 100, add 1 to the count.
Repeat.
```

### 1.2 Multi-Paradigm & Self-Hosting
Plain English is not a toy scripting layer or a macro preprocessor; it is a **fully self-hosting, compiled systems programming language**. The Plain English compiler itself is written entirely in Plain English and compiles directly to native 32-bit x86 Windows machine instructions without external toolchains or linkers.

---

## 2. Key Language Characteristics

1. **Sentence-Based Grammar**:
   - Statements end with a period (`.`).
   - Routine and type headers end with a colon (`:`).
   - Indentation indicates statement grouping inside loops and routines.

2. **Order Independence**:
   - Types, global variables, and routines can be declared anywhere across your source files in any order. There are no header files (`.h`), forward declarations, or `#include` directives.

3. **Natural Plurals and Articles**:
   - The language understands English grammar: indefinite articles (`a`, `an`, `another`, `some`) introduce new instances or parameter slots, while definite articles (`the`) reference existing variables.
   - Plural forms (e.g., `box` $\rightarrow$ `boxes`, `monster` $\rightarrow$ `monsters`) are automatically indexed and connected.

4. **Built-in Collections ("Things")**:
   - Defining a type as `A [name] is a thing` automatically equips it with doubly-linked list nodes (`next`, `previous`) and list header chains (`first`, `last`).

5. **First-Class Graphics and Native Win32 Integration**:
   - The Plain English standard library includes integrated 2D graphics primitives (drawing boxes, ellipses, typography, turtle geometry, color spaces) and seamless Win32 API interoperability.

---

## 3. "Hello World" in Plain English

Every executable Plain English program begins at the `To run:` routine:

```plain
To run:
  Start up.
  Write "Hello, World!" to the console.
  Wait for the enter key.
  Shut down.
```

To draw a colorful box on the screen:
```plain
To run:
  Start up.
  Clear the screen with the black color.
  Make a box 2 inches by 2 inches.
  Center the box on the screen.
  Draw the box with the gold color.
  Refresh the screen.
  Wait for the escape key.
  Shut down.
```

---

## 4. Next Steps

Explore the rest of this user guide to master Plain English:
- [02: Syntax and Fundamental Rules](file:///c:/Plain-English-Compiler/Compiler/language-guide/02_SYNTAX_AND_RULES.md)
- [03: Type System and Data Structures](file:///c:/Plain-English-Compiler/Compiler/language-guide/03_TYPE_SYSTEM.md)
- [04: Routines, Functions, and Deciders](file:///c:/Plain-English-Compiler/Compiler/language-guide/04_ROUTINES_AND_DECIDERS.md)
- [05: Control Flow and Loops](file:///c:/Plain-English-Compiler/Compiler/language-guide/05_CONTROL_FLOW.md)
- [06: Memory Management and Strings](file:///c:/Plain-English-Compiler/Compiler/language-guide/06_MEMORY_AND_STRINGS.md)
- [07: Standard Library, Graphics, and Windows API](file:///c:/Plain-English-Compiler/Compiler/language-guide/07_STANDARD_LIBRARY_AND_INTEROP.md)
