# 06: Memory Management, Strings & Buffers

Plain English delivers the speed and memory efficiency of low-level systems languages without the burden of manual deallocation or garbage collection pauses.

---

## 1. The Memory Model

### 1.1 Automatic Synthesis of Lifecycle Routines
Whenever you declare a custom record, pointer, or `thing`, the compiler automatically generates four memory routines for that type:
- `Allocate memory for a [type]`
- `Deallocate a [type]`
- `~Finalize a [type]`
- `Destroy [type]`

When a record containing strings or dynamic sub-objects is deallocated, its `~Finalize` routine automatically walks all fields and releases nested dynamic buffers.

### 1.2 The `privatize` Keyword
When a routine receives a pointer or string and needs to modify it locally without changing the caller's copy, use `privatize`:
```plain
To reverse a string:
  Privatize the string.
  ...
```
`Privatize` allocates a local copy of the data on the current stack or local heap frame, ensuring parameter isolation.

---

## 2. Strings & Substrings

In Plain English, strings are not simple null-terminated arrays of characters (like C `char*`). A string is represented by two pointers:

```plain
A string is a record with
  A first byte pointer and
  A last byte pointer.
```

### 2.1 Benefits of Two-Pointer Strings
1. **O(1) Length Calculation**: Length is computed instantly as:
   $$\text{Length} = \text{last byte pointer} - \text{first byte pointer} + 1$$
2. **Zero-Copy Substrings (`substring`)**: Slicing or taking a substring does not allocate memory or copy characters. It simply creates a new `first`/`last` pointer pair referencing a segment of the original buffer.
3. **Binary Safe**: Strings can contain embedded null bytes (`0x00`) without truncation.

### 2.2 Common String Operations

#### Creating and Appending
```plain
Put "Plain" into a string.
Append " English" to the string.
\ Result: "Plain English"
```

#### Concatenation with `then`
```plain
Write "Score: " then the player's score then " points." to the console.
```

#### Trimming Whitespace
```plain
Trim the input string.
Strip leading noise from the input string.
```

#### Splitting Words
```plain
Slap a substring on the sentence.
Loop.
  If the substring is blank, exit.
  Get a word from the substring.
  Write the word to the console.
Repeat.
```

---

## 3. Buffers and Binary Data

A `buffer` in Plain English represents raw contiguous memory:
- **Loading Files**: `Load the path into the buffer.`
- **Writing Files**: `Write the buffer to the path.`
- **Hexadecimal Conversions**:
  ```plain
  Convert the number to a hex string.
  Convert the hex string to a binary buffer.
  ```

---

## 4. The Rider Abstraction

When parsing text, scanning tokens, or reading structured buffers, Plain English uses a **Rider**:

```plain
A rider is a record with
  A source substring and
  A token substring.
```

A rider allows you to walk through text while maintaining zero-copy views of both the current token and the remaining source.

Example:
```plain
Slap a rider on the document buffer.
Move the rider.
Loop.
  If the rider's token substring is blank, break.
  Write "Token: " then the rider's token substring to the console.
  Move the rider.
Repeat.
```

---

## Next Guide
Continue to [07: Standard Library, Graphics, and Windows API](file:///c:/Plain-English-Compiler/Compiler/language-guide/07_STANDARD_LIBRARY_AND_INTEROP.md).
