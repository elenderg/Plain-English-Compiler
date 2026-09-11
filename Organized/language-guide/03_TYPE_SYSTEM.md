# 03: Type System & Data Structures

Plain English features a rich, nominal type system with support for custom records, scaled units of measure, automatic doubly-linked lists (`things`), and pointer references.

---

## 1. Defining Types

Types are defined using English declarative sentences:

### 1.1 Type Aliasing & Subtyping
You can derive new domain-specific types from existing ones:
```plain
A path is a string.
A handle is a number.
An address is a number.
A character is a byte.
```
This improves code clarity while allowing routines written for the base type (e.g., routines that operate on `string`) to accept the derived type (`path`) automatically via compile-time utility reduction.

### 1.2 Scaled Units of Measure
You can declare custom physical or mathematical units with explicit scale factors:
```plain
A foot is 12 inches.
A second is 1000 milliseconds.
A grain is 10 milliseconds.
A twip is 1/20 of a point.
A hundred is 100 units.
```
Arithmetic operations automatically respect the unit definitions.

---

## 2. Records (Structures)

A record is a composite data structure containing named fields:

```plain
A point is a record with
  An x coord and
  A y coord.

A person is a record with
  A name,
  An age number, and
  An address.
```

### 2.1 Field Redefinition (Unions / Aliasing)
Fields can redefine existing fields, sharing the exact same memory offset:
```plain
A spot is a pair with
  An x coord,
  A y coord,
  A left at the x coord, and
  A top at the y coord.
```
In this example, accessing `spot's left` accesses the same 32-bit slot as `spot's x coord`.

---

## 3. Doubly Linked Lists ("Things")

In most languages, when you want a collection of items, you instantiate an external list or array container (`List<Item>`). In Plain English, you declare the item as a **`thing`**:

```plain
A monster is a thing with
  A name,
  A health number, and
  A level number.
```

When you write `A [type] is a thing with...`, the compiler automatically generates three distinct elements for you:
1. **The Node Record (`monster record`)**: Contains the fields you declared plus intrinsic `next monster` and `previous monster` pointers.
2. **The Pointer Type (`monster`)**: A 4-byte reference pointing to a `monster record`.
3. **The List Header Chain (`monsters`)**: A header record containing pointers to `first monster` and `last monster`.

### Working with Things
You can now create and manipulate your lists naturally:
```plain
The monsters are some monsters.

To create the goblin:
  Allocate memory for a monster.
  Put "Goblin" into the monster's name.
  Put 50 into the monster's health.
  Append the monster to the monsters.
```

Iterating through a list:
```plain
Loop.
  Get a monster from the monsters.
  If the monster is nil, break.
  Write the monster's name to the console.
Repeat.
```

---

## 4. Pointers & References

Pointers are 32-bit memory addresses that refer to another typed block of memory:
```plain
A monster pointer is a pointer to a monster.
A byte pointer is a pointer to a byte.
```

Special literals and dereferencing:
- `nil`: Represents a null pointer (`0x00000000`).
- Check if valid: `If the pointer is nil, exit.`
- Target access: `Put the pointer's target into a byte.`

---

## 5. Built-in Core Types

The Plain English runtime environment predefines essential system types:
- `byte`: 8-bit unsigned unit of storage (`0` to `255`).
- `number`: 32-bit signed integer (`twos-complement`).
- `flag`: Boolean truth value (`yes` or `no`).
- `string`: Composed of a `first byte pointer` and `last byte pointer`.
- `substring`: Non-owning slice pointing into a string buffer.
- `box`: Geometry structure representing a rectangular region (`left`, `top`, `right`, `bottom`).
- `color`: High-level color representation with `hue`, `saturation`, and `lightness`.

---

## Next Guide
Continue to [04: Routines, Functions, and Deciders](file:///c:/Plain-English-Compiler/Compiler/language-guide/04_ROUTINES_AND_DECIDERS.md).
