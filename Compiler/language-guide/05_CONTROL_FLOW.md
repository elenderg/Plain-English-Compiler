# 05: Control Flow, Conditionals & Loops

Plain English provides structured, intuitive control-flow constructs without requiring parentheses, braces, or `goto` statements.

---

## 1. Conditionals (`If`)

### 1.1 Single-Line Conditionals
A conditional statement checks an expression or calls a decider:
```plain
If the number is greater than 10, add 1 to the count.
If the flag is set, exit.
```

### 1.2 Multi-Line Conditionals
When multiple actions depend on a condition, separate the actions with semicolons or indent them:
```plain
If the player's health is less than or equal to 0,
  Play the game over sound;
  Show the game over screen;
  Exit.
```

### 1.3 Negative Conditions
You can invert conditions using natural English negative words:
```plain
If the string is not blank,
  Write the string to the console.

If the file does not exist,
  Create the file.
```

---

## 2. Loops (`Loop` / `Repeat`)

In Plain English, loops begin with `Loop.` and conclude with `Repeat.`. Inside the loop, you use `break`, `repeat` (as continue), or `exit` to control execution.

### 2.1 Basic Loop with a Counter
```plain
Put 1 into a counter number.
Loop.
  If the counter is greater than 5, break.
  Write the counter's string then "... " to the console.
  Add 1 to the counter.
Repeat.
```

### 2.2 Looping Through Linked Lists ("Things")
Iterating over a collection of `things` is clean and idiomatically English:
```plain
Loop.
  Get a monster from the monsters.
  If the monster is nil, break.
  If the monster's health is 0, repeat. \ Skips dead monsters (like 'continue' in C)
  Draw the monster.
Repeat.
```

### 2.3 Looping Through Strings Character-by-Character
```plain
Slap a substring on the text.
Loop.
  If the substring is blank, exit.
  Get a byte from the substring.
  Write the byte to the console.
Repeat.
```

---

## 3. Controlling Execution

- **`break`**: Immediately exits the innermost `Loop ... Repeat` block.
- **`repeat`**: Skips the remainder of the current loop iteration and starts the next cycle (identical to `continue` in C/Java/Python).
- **`exit`**: Returns immediately from the current routine.

Example:
```plain
To find a student given a name returning a student:
  Void the student.
  Loop.
    Get a current student from the students.
    If the current student is nil, exit.
    If the current student's name is the name,
      Put the current student into the student;
      Exit.
  Repeat.
```

---

## 4. Guard Clauses & Early Returns

Writing clean Plain English favors guard clauses at the beginning of routines:
```plain
To process an order:
  If the order is nil, exit.
  If the order's items are empty, exit.
  If the customer has not paid,
    Alert "Order cannot be processed until payment is received.";
    Exit.
  
  \ Proceed with order fulfillment...
  Ship the order.
```

---

## Next Guide
Continue to [06: Memory Management and Strings](file:///c:/Plain-English-Compiler/Compiler/language-guide/06_MEMORY_AND_STRINGS.md).
