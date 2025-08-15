# Plain English Compiler
[<img src="https://raw.githubusercontent.com/elenderg/Plain-English-Compiler/refs/heads/main/Compilation.png">](https://github.com/elenderg/Plain-English-Compiler/)

A compiler for the **Plain English Programming Language**, designed to make programming feel like writing ordinary English.  
The goal is to provide a natural, beginner-friendly syntax while still generating efficient native code.

## Features

- **Readable syntax** that uses common English words (`A`, `AN`, `THE`, etc.).
- Compiles Plain English source code into native machine code.
- Minimal learning curve for non-programmers.
- Extensible architecture for adding new keywords and rules.

## Example

A simple Plain English program might look like:

```
To run:  
  Write "Hello, world!" to the console.
  Shut down.  
```

And produces output:

```
Hello, world!
```

## Installation

### Prerequisites
- **Windows** (32-bit or 64-bit) — main target platform.

### Philosophy

The Plain English Compiler is built on the belief that:
***If you can explain it in English, you should be able to make a computer do it.***  
Instead of cryptic symbols and boilerplate, the syntax mirrors natural English sentences, making it more intuitive for beginners, writers, and non-technical audiences.

### Contributing

Contributions are welcome!
If you have ideas for:

- New language features
- Improved parsing and error handling
- Better examples and documentation

Please submit a pull request or open an issue.


# Documentation

# Language Reference

## 1. Keywords and Reserved Words

**Definition keywords:**
```
A
AN
SOME
THE
TO
```

**Control flow:**
```
IF
LOOP
BREAK
EXIT
REPEAT
```

**Special definition words:**
```
CALLED
EQUAL
```

**Infix operators:**
```
PLUS
MINUS
TIMES
DIVIDED BY
THEN
```

**Imperative verbs (built-in commands):**
```
ADD
SUBTRACT
MULTIPLY
DIVIDE
ROUND
DE-SIGN
REVERSE
REDUCE
CLEAR
PUT
SWAP
COPY
CONVERT
APPEND
ALLOCATE
DEALLOCATE
FINALIZE
DESTROY
UNASSIGN
```

**Prepositions (used in parsing and monikettes):**
```
IN
INTO
TO
FROM
GIVEN
WITH
USING
AT
ON
AROUND
OVER
UNDER
THRU
```

**Conjunctions:**
```
AND
OR
```

**Literals:**
- Numbers (`123`, `0`, `-45`)
- Strings (`"Hello, World!"`)
- Ratios (`3/4`)

---

### Example — Defining and Using a Routine
```plaintext
A point is a record with
    A number called x,
    A number called y.

The origin is a point.

To move the origin to coordinates:
    Put 0 into the origin's x.
    Put 0 into the origin's y.
    Write in strong green "Origin reset" to StdOut.

If the origin's x is greater than 100, exit.
```

---

## 2. Built-in Types

From `Compiler Source Code.txt` and `Standard Library.txt`:

**Primitive types:**
```
byte         — 1-byte unsigned integer
number       — general integer type
string       — variable-length sequence of bytes
pointer      — generic memory reference
ratio        — fractional number
thing        — generic record container
record       — structured fields
```

**Common record types:**
```
position
pair
box
color
cursor
buffer
mask
alignment
pdf
pdf object
pdf state
pdf outline entry
paintstruct
pchar (pointer to byte)
addrinfo
```

**Pointer types:**
```
A point pointer is a pointer to a point.
A pdf pointer is a pointer to a pdf.
An addrinfoptr is a pointer to an addrinfo.
```

---

### Example — Using Built-in Types
```plaintext
A pair is a record with
    A number called x,
    A number called y.

The mypair is a pair.

Put 5 into the mypair's x.
Put 10 into the mypair's y.
```

---

## 3. Built-in Constants

From `Standard Library.txt`:

### ASCII Bytes
```
The backslash byte is a byte equal to 92.
The big-a byte is a byte equal to 65.
The percent-sign byte is a byte equal to 37.
The period byte is a byte equal to 46.
...
```

(These cover printable and control characters; each constant is defined as “The <name> byte is a byte equal to <ASCII value>.”)

### Keyboard Keys
```
The a-key is a key equal to 65.
The p-key is a key equal to 80.
The page-up key is a key equal to 33.
The backspace key is a key equal to 8.
The alt key is a key equal to 18.
...
```

### Colors
```
A pastel color is a color.
A pen is a color.
The pen size is a number.
```

### Special Symbols
```
The at-sign byte is a byte equal to 64.
The ampersand byte is a byte equal to 38.
The paragraph byte is a byte equal to 182.
The per-mille-sign byte is a byte equal to 137.
```

### Units & Numbers
```
A billion is 1000 millions.
A percent is a number.
An amount is a number.
An angle is some precise degrees [0 to 3599].
```

---

### Example — Using Constants
```plaintext
Write "The backslash is: " to StdOut.
Write the backslash byte to StdOut.
Write the CRLF string to StdOut.

If the a-key is equal to 65,
    Write "The 'A' key constant works!" to StdOut.
```

---

## 4. Standard Library Routines

From `Standard Library.txt`:

**Console Output with Colors:**
```
Write in strong red "Error!" to StdOut.
Write in strong green "Success!" to StdOut.
Write with underscore "Underlined text" to StdOut.
Write on the background using strong yellow "Warning" to StdOut.
```

**Example:**
```plaintext
Write in strong yellow "Compiling project..." to StdOut.
```

**File Operations:**
```
Append a buffer to a file.
Append a string to some string things.
```

**Example:**
```plaintext
Append "Hello" to the output string.
Append the output string to the log file.
```

**Math and Geometry:**
```
Add a number to another number.
Add a pair to another pair.
Add some horizontal twips and some vertical twips to the current spot.
```

**Example:**
```plaintext
Add 5 to the counter.
Add the first pair to the second pair.
```

**PDF Utilities:**
```
Add a pdf object given a kind.
Add a pdf outline entry.
```

**Example:**
```plaintext
Add a pdf object given page.
Add a pdf outline entry with "Introduction".
```

---

## 5. Memory Management Routines

Automatically generated by compiler:
```
Allocate memory for a <type>.
Deallocate a <type>.
Finalize a <type>.
Destroy a <type>.
```

**Example:**
```plaintext
Allocate memory for a point.
Put 5 into the point's x.
Put 6 into the point's y.
Destroy the point.
```
