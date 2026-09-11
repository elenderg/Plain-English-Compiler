# Plain English Programming Language: Complete User Guide

Welcome to the comprehensive introductory user documentation for the **Plain English** programming language. This guide is organized into dedicated topic modules in the [`language-guide/`](file:///c:/Plain-English-Compiler/Compiler/language-guide) directory.

---

## Documentation Modules

| # | Guide Module | Key Topics Covered |
| :---: | :--- | :--- |
| **01** | [Introduction to Plain English](file:///c:/Plain-English-Compiler/Compiler/language-guide/01_INTRODUCTION.md) | Design philosophy, natural English prose programming, self-hosting compiler, Hello World. |
| **02** | [Syntax, Rules & Conventions](file:///c:/Plain-English-Compiler/Compiler/language-guide/02_SYNTAX_AND_RULES.md) | Periods, colons, remarks (`[...]`), single-line comments (`\`), definite vs indefinite articles, possessives (`'s`), literals, and case insensitivity. |
| **03** | [Type System & Data Structures](file:///c:/Plain-English-Compiler/Compiler/language-guide/03_TYPE_SYSTEM.md) | Custom records, scaled units of measure, automatic doubly-linked lists (`things`), field unions/redefinitions, and pointers. |
| **04** | [Routines, Functions & Deciders](file:///c:/Plain-English-Compiler/Compiler/language-guide/04_ROUTINES_AND_DECIDERS.md) | Sentence-like procedures, deciders (`To decide if...`), function returns, preposition synonyms, and Win32 callbacks (`compatibly`). |
| **05** | [Control Flow, Conditionals & Loops](file:///c:/Plain-English-Compiler/Compiler/language-guide/05_CONTROL_FLOW.md) | `If` conditions, `Loop ... Repeat`, iterating through linked collections (`things`) and strings, `break`, `repeat`, and guard clauses. |
| **06** | [Memory Management, Strings & Buffers](file:///c:/Plain-English-Compiler/Compiler/language-guide/06_MEMORY_AND_STRINGS.md) | Automatic RAII lifecycle synthesis (`Allocate`/`Deallocate`/`~Finalize`), parameter isolation via `privatize`, two-pointer strings, substrings, and the `rider` text cursor. |
| **07** | [Standard Library, Graphics & Win32 Interop](file:///c:/Plain-English-Compiler/Compiler/language-guide/07_STANDARD_LIBRARY_AND_INTEROP.md) | 2D drawing (`spot`, `box`, `twip`), HSL color space, turtle geometry, file I/O, direct Windows DLL calling (`kernel32`, `user32`), and inline assembly (`Intel $...`). |

---

## Quick Example: Creating and Processing Data

```plain
A student is a thing with
  A name,
  A grade number.

The students are some students.

To enroll a student given a name and a grade:
  Allocate memory for the student.
  Put the name into the student's name.
  Put the grade into the student's grade.
  Append the student to the students.

To run:
  Start up.
  Enroll a student given "Alice" and 95.
  Enroll a student given "Bob" and 82.
  Enroll a student given "Charlie" and 90.

  Write "--- Honor Roll Students ---" to the console.
  Loop.
    Get a student from the students.
    If the student is nil, break.
    If the student's grade is greater than or equal to 90,
      Write the student's name then " - Grade: " then the student's grade to the console.
  Repeat.

  Wait for the enter key.
  Shut down.
```
