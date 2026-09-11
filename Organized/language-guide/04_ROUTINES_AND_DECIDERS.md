# 04: Routines, Functions, and Deciders

In Plain English, functions and procedures are called **Routines**. Rather than requiring rigid parentheses and parameter lists (e.g., `doSomething(a, b)`), routine signatures are written as full sentences.

---

## 1. Defining Procedures

A procedure executes actions and side effects. Its definition begins with `To`:

```plain
To greet a person:
  Write "Hello, " then the person's name then "!" to the console.
```

### Parameter Slots
Parameters are defined in the header using **indefinite articles** (`a`, `an`, `another`, `some`):
```plain
To draw a box given a color and a width:
  Put the color into the context's color.
  Stroke the box given the width.
```

When calling this routine, caller statements can use any preposition synonym recognized by the language:
```plain
Draw the screen box with the blue color and 5.
Draw the screen box given the blue color and 5.
```
Both statements match the exact same routine.

---

## 2. Deciders (Predicate Routines)

A **Decider** is a routine that answers a yes/no question. Decider headers begin with `To decide if`:

```plain
To decide if a number is odd:
  Privatize the number.
  Bitwise AND the number with 1.
  If the number is 0, say no.
  Say yes.

To decide if a number is even:
  If the number is odd, say no.
  Say yes.

To decide if a string is blank:
  \ assume true
  Intel $B801000000. \ mov eax,1
  Intel $8B9D08000000. \ mov ebx,[ebp+8] \ the string
  \ if first is 0, exit
  Intel $833B00. \ cmp [ebx],0
  Intel $0F8410000000. \ je end
  \ if last is less than first, exit
  Intel $8B5304. \ mov edx,[ebx+4] \ last pointer
  Intel $3B13. \ cmp edx,[ebx]
  Intel $0F8C05000000. \ jl end
  \ SAY NO:
  Intel $B800000000. \ mov eax,0
  \ END:
```

### Calling Deciders in Conditionals
Deciders integrate directly into natural `if` statements:
```plain
If the score is even,
  Write "Even score!" to the console.

If the input string is blank,
  Write "Please enter your name." to the console;
  Exit.
```

At the machine level, deciders return truth results directly through the CPU condition flags (`ZF` or `CF`), allowing `JZ`/`JNZ` instructions to branch without allocating intermediate boolean stack variables.

---

## 3. Functions (Returning Values)

Functions return a computed value. In Plain English, functions typically use the pattern `To put [type] into [variable]:` or `To get [type] from [source]:`:

```plain
To put the double of a number into another number:
  Put the number times 2 into the other number.
```

Usage:
```plain
Put 21 into an original number.
Double the original number.
\ Or using functional return:
Put the double of 21 into a result number.
```

---

## 4. Alternate Wordings & Synonyms

Plain English allows a developer to provide multiple headers for a single routine using semicolons (`;`):

```plain
To add a number to another number;
To sum a number with another number;
To increment a number by another number:
  Add the other number to the number.
```

Callers can now use whichever phrasing reads best in context:
- `Add 5 to the total.`
- `Increment the total by 5.`
- `Sum 5 with the total.`

---

## 5. Employ Statements

If you want a routine to alias or delegate entirely to another routine, use the `employ` keyword:

```plain
To print a string:
  Employ write the string to the console.
```

---

## 6. Callback Routines (Windows API Integration)

When Windows requires a callback function (such as a Window Procedure `WndProc` or timer hook), use the keyword `compatibly`:

```plain
To handle a window message compatibly given a window and a message number and a w-param and a l-param:
  ...
```
Routines declared with `compatibly` follow standard Win32 `stdcall` callee-cleanup calling conventions and pass arguments by value.

---

## Next Guide
Continue to [05: Control Flow and Loops](file:///c:/Plain-English-Compiler/Compiler/language-guide/05_CONTROL_FLOW.md).
