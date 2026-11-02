# 07: Standard Library, Graphics & Win32 Interoperability

One of Plain English's greatest strengths is its built-in runtime and comprehensive standard library implemented in [StandardLibrary.plain](file:///c:/Plain-English-Compiler/Compiler/StandardLibrary.plain). It provides native 2D drawing, turtle graphics, color management, window handling, and seamless Windows API calling.

---

## 1. 2D Graphics and Geometry

The standard library models visual space using high-level geometric primitives:
- **`spot`**: A 2D point with an `x coord` and `y coord`.
- **`box`**: A rectangle with `left`, `top`, `right`, and `bottom` coordinates.
- **`twip`**: The standard unit of measurement (1/20 of a typographical point; 1440 twips = 1 inch).

### 1.1 Drawing Shapes
```plain
To run:
  Start up.
  Clear the screen with the white color.
  
  \ Create a centered 4-inch square
  Make a box 4 inches by 4 inches.
  Center the box on the screen.
  
  \ Draw with a color and border
  Draw the box with the light blue color.
  Stroke the box with the navy color and 3 twips.
  
  \ Draw an ellipse inside the box
  Draw an ellipse in the box with the gold color.
  
  Refresh the screen.
  Wait for the escape key.
  Shut down.
```

### 1.2 Typography & Text Rendering
You can render text with custom fonts, colors, and alignments directly into any box:
```plain
Draw "Welcome to Plain English" in the box with the dark gray color and "Arial" and 24 points.
```

---

## 2. Colors and the HSL Color Space

Rather than forcing developers to memorize RGB hexadecimal values (`#FF8800`), Plain English represents colors with `hue`, `saturation`, and `lightness`:

```plain
A color is a record with
  A hue,
  A saturation,
  A lightness, and
  A brightness at the lightness.
```

Predefined color aliases include:
- `the red color`, `the green color`, `the blue color`
- `the light blue color`, `the dark red color`, `the pastel green color`
- `the gold color`, `the silver color`, `the charcoal color`

You can blend or adjust colors naturally:
```plain
Lighten the color by 10 percent.
Darken the color by 20 percent.
Mix the red color with the blue color into a purple color.
```

---

## 3. Turtle Geometry

Plain English has native turtle graphics for algorithmic art, fractal generation, and diagramming:
```plain
Start in the middle of the screen.
Face north.
Loop 4 times.
  Move 2 inches.
  Turn right 90 degrees.
Repeat.
```

---

## 4. File I/O & Disk Operations

File operations are straightforward:
```plain
\ Read an entire file into a buffer
Load "C:\data\input.txt" into a buffer.

\ Write text to disk
Put "Log entry: OK" into a string.
Write the string to "C:\data\log.txt".

\ Check if a file or directory exists
If "C:\MyFolder\" is in the file system,
  Write "Folder exists!" to the console.
```

---

## 5. Native Windows Win32 API Interoperability

Plain English programs can call any Windows DLL directly using the `Call [dll] [function]` syntax.

### 5.1 Calling a Win32 API
```plain
Call "kernel32.dll" "GetTickCount" returning a number called ticks.

Call "user32.dll" "MessageBoxA" with
  0 and
  "Operation completed successfully." and
  "Notice" and
  $00000040 [MB_ICONINFORMATION]
  returning a number called result.
```

### 5.2 How the Compiler Handles External Calls
1. **Import Borough Resolution**: The compiler automatically registers the DLL name and exported function in the binary's Import Directory (`.idata`).
2. **IAT Generation**: It creates an Import Address Table entry for the external function.
3. **Indirect Call Emission**: The call statement compiles into a direct x86 `CALL DWORD PTR [IAT_Entry]` instruction (`FF 15 <address>`).

---

## 6. Inline Assembly (`Intel`)

For hardware-level programming, SIMD operations, or high-performance byte manipulation, Plain English allows inline x86 machine opcodes:

```plain
To clear register eax:
  Intel $31C0. \ xor eax, eax

To halt the processor:
  Intel $F4.   \ hlt
```

---

## Summary of the User Guide

Congratulations! You now have a comprehensive understanding of Plain English:
1. **Sentence-based structure** with intuitive articles and possessives.
2. **Clean type modeling** with records, units, and automatic doubly-linked lists (`things`).
3. **Expressive routines**, deciders, and synonym headers.
4. **Fast, self-contained compilation** into native 32-bit Windows executables.
