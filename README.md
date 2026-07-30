# HolyC Programming Language & holycc Transpiler: Complete Reference

**HolyC** is the systems programming language created by **Terry A. Davis**
(1969–2018) for the TempleOS operating system. `holycc` is a clean-room
transpiler that converts HolyC source code (`.HC`) into human-readable C17,
then compiles it with GCC or Clang into a native executable.

This document is the complete reference manual for the HolyC language as
implemented by `holycc`. It covers the language syntax, type system, runtime
library, compiler architecture, and includes dozens of worked examples.

## Table of Contents

### Part I: Language Fundamentals

1. [Introduction](#1-introduction)
2. [Quick Start](#2-quick-start)
3. [Type System](#3-type-system)
4. [Variables and Storage](#4-variables-and-storage)
5. [Operators](#5-operators)
6. [Control Flow](#6-control-flow)
7. [Functions](#7-functions)
8. [Classes, Structs, Unions, Enums](#8-classes-structs-unions-enums)
9. [Memory Management](#9-memory-management)

### Part II: Standard Library

10. [Runtime Library — Complete API Reference](#10-runtime-library--complete-api-reference)
11. [HolyC-Specific Features](#11-holyc-specific-features)
12. [Preprocessor and Modules](#12-preprocessor-and-modules)

### Part III: Compiler Internals

13. [Compiler Architecture](#13-compiler-architecture)
14. [HolyC to C17 Transformation Matrix](#14-holyc-to-c17-transformation-matrix)

### Part IV: Examples and Reference

15. [Comprehensive Examples](#15-comprehensive-examples)
16. [Error Handling and Diagnostics](#16-error-handling-and-diagnostics)
17. [CLI Reference](#17-cli-reference)
18. [Differences from C](#18-differences-from-c)
19. [Building and Contributing](#19-building-and-contributing)
20. [Implementation Limits and Notes](#20-implementation-limits-and-notes)
21. [Advanced Topics](#21-advanced-topics)
22. [API Index](#22-api-index-quick-reference)
23. [Version History and References](#23-version-history)
10. [Runtime Library — Complete API Reference](#10-runtime-library--complete-api-reference)
    - 10.1 I/O Functions
    - 10.2 Memory Functions
    - 10.3 String Functions
    - 10.4 Control Functions
    - 10.5 Math Library
    - 10.6 Math Constants
11. [HolyC-Specific Features](#11-holyc-specific-features)
    - 11.1 Top-Level Code
    - 11.2 String Auto-Print
    - 11.3 Bare Function Call
    - 11.4 Chained Comparisons
    - 11.5 Switch Extensions
    - 11.6 The U0 Type
    - 11.7 argc/argv Variadic
    - 11.8 Class Methods
    - 11.9 try / catch / throw
    - 11.10 The reg and noreg Keywords
    - 11.11 The public and private Keywords
    - 11.12 The no_warn Keyword
    - 11.13 The offset Keyword
    - 11.14 The has Keyword
    - 11.15 Color Literals in Strings
    - 11.16 Inline Assembly
    - 11.17 The Power Operator
    - 11.18 The NULL, TRUE, FALSE Constants
12. [Preprocessor and Modules](#12-preprocessor-and-modules)
    - 12.1 #define
    - 12.2 #include
    - 12.3 import
    - 12.4 #if / #else / #endif
    - 12.5 #ifdef / #ifndef / #elif

### Part III: Compiler Internals

13. [Compiler Architecture](#13-compiler-architecture)
    - 13.1 Pipeline Overview
    - 13.2 Lexer
    - 13.3 Parser
    - 13.4 Abstract Syntax Tree (AST)
    - 13.5 Semantic Analysis
    - 13.6 Symbol Table
    - 13.7 Type System Internals
    - 13.8 Code Generator
    - 13.9 CLI Driver
    - 13.10 Diagnostics System
    - 13.11 Runtime Library
    - 13.12 File-by-File Layout
14. [HolyC to C17 Transformation Matrix](#14-holyc-to-c17-transformation-matrix)

### Part IV: Examples and Reference

15. [Comprehensive Examples](#15-comprehensive-examples)
    - 15.1 Hello World
    - 15.2 Arithmetic and Variables
    - 15.3 Control Flow Demonstrator
    - 15.4 Functions and Variadics
    - 15.5 Classes and Methods (OOP)
    - 15.6 Dynamic Memory: Linked List
    - 15.7 Math Library in Action
    - 15.8 Exception Handling
    - 15.9 Array Operations
    - 15.10 String Operations
    - 15.11 Switch Statement
    - 15.12 Recursive Functions
    - 15.13 Function Pointers
    - 15.14 Preprocessor and Modules
    - 15.15 Interactive CLI Program
    - 15.16 Type System Demo
    - 15.17 Complete Class-Based Program
16. [Error Handling and Diagnostics](#16-error-handling-and-diagnostics)
    - 16.1 Error Levels
    - 16.2 Common Error Messages
    - 16.3 Diagnostic Output Format
17. [CLI Reference](#17-cli-reference)
    - 17.1 All Flags and Options
    - 17.2 Usage Examples
18. [Differences from C](#18-differences-from-c)
19. [Building and Contributing](#19-building-and-contributing)
    - 19.1 Build Requirements
    - 19.2 Build Instructions
    - 19.3 Testing
    - 19.4 Contributing Guidelines
20. [Implementation Limits and Notes](#20-implementation-limits-and-notes)
    - 20.1 Limits Table
    - 20.2 Behavioral Notes

---

# Part I: Language Fundamentals

---

## 1. Introduction

### 1.1 What is HolyC?

HolyC is a systems programming language designed by Terry A. Davis for use
in the TempleOS operating system. It is a C-like language that strips away
boilerplate while preserving low-level power. Key characteristics include:

- **No mandatory #include** — runtime functions are always available
- **No mandatory main()** — top-level code executes directly
- **Fixed-width integer types** (I8, I16, I32, I64, U8, U16, U32, U64)
- **Single floating-point type** (F64 = double)
- **Built-in variadic argument handling** via argc / argv
- **String literals as statements** auto-print to stdout
- **Chained comparisons** like `0 <= x < 10`
- **Extended switch** with implicit and range cases
- **Class-based OOP** using the class keyword
- **Exception handling** via try / catch / throw
- **Power operator** using backtick
- **No C-style struct** — only class

### 1.2 Terry A. Davis and TempleOS

Terry A. Davis (1969–2018) was an American programmer who single-handedly
created TempleOS, a 64-bit operating system written entirely in HolyC.
TempleOS featured its own kernel, compiler, graphics system, file system,
and networking stack — all written in HolyC by one person over the course
of a decade.

HolyC was designed to be the ultimate C replacement: retaining C's power
and speed while eliminating what Davis saw as unnecessary bureaucratic
syntax. The language was never standardized outside of TempleOS.

### 1.3 The holycc Transpiler

holycc is a clean-room implementation of a HolyC-to-C17 transpiler. It:

1. Reads HolyC source files (.HC)
2. Parses them into an Abstract Syntax Tree
3. Performs semantic analysis and type checking
4. Generates human-readable C17 code
5. Invokes GCC or Clang to produce a native executable

The transpiler is written in C17 and has no dependencies beyond a C compiler
and standard library. It is released under the GPL v3 license.

### 1.4 Design Philosophy

HolyC's design philosophy can be summarized as:

- **Power over safety** — the programmer is trusted to know what they are doing
- **Minimal boilerplate** — no #include for builtins, no typedef struct
- **Everything is 64-bit by default** — the native word size is always I64
- **No magic** — the generated C17 code is straightforward and readable
- **Do what I mean** — string literals print, bare identifiers call functions

---

## 2. Quick Start

### 2.1 Building from Source

```bash
# Clone the repository
git clone https://github.com/Voctl/holyc.git
cd holyc

# Build with CMake
cmake -B build
cmake --build build

# Run the built-in test suite
./build/tests/test_lexer

# Install system-wide
sudo ./install.sh
```

Build requirements:
- C17 compiler (GCC 8+ or Clang 10+)
- CMake 3.16+
- Make or Ninja

### 2.2 Installation

The install.sh script copies the holycc binary to /usr/local/bin/
and the runtime library to /usr/local/lib/. After installation:

```bash
holycc --version
```

### 2.3 Hello, World — Three Ways

#### Method 1: Top-level code (no main)

```c
// hello.HC
Print("Hello, world!\n");
return 0;
```

#### Method 2: Explicit main function

```c
// hello_main.HC
I64 main()
{
    Print("Hello, world!\n");
    return 0;
}
```

#### Method 3: String auto-print

```c
// hello_auto.HC
"Hello, world!\n";
return 0;
```

All three produce identical output: Hello, world!

### 2.4 Compilation and Execution

```bash
# Compile to executable (produces ./hello)
holycc hello.HC

# Compile and run immediately
holycc hello.HC --run

# Specify output file
holycc hello.HC -o my_program

# Generate C17 only (no compilation)
holycc hello.HC --emit-c

# Keep the generated .c file
holycc hello.HC --keep-c

# View the token stream
holycc hello.HC --tokens

# View the AST tree
holycc hello.HC --ast
```

---

## 3. Type System

### 3.1 Primitive Types

HolyC uses fixed-width integer types instead of C's platform-dependent
int, long, short. Every type has a guaranteed size across all platforms.

| HolyC Type | Size    | Signed | C17 Equivalent    | Range (min)        | Range (max)        |
|------------|---------|--------|--------------------|--------------------|--------------------|
| I8       | 1 byte  | Yes    | int8_t           | -128               | 127                |
| I16      | 2 bytes | Yes    | int16_t          | -32,768            | 32,767             |
| I32      | 4 bytes | Yes    | int32_t          | -2,147,483,648     | 2,147,483,647      |
| I64      | 8 bytes | Yes    | int64_t          | -9.22e18           | 9.22e18            |
| U8       | 1 byte  | No     | uint8_t          | 0                  | 255                |
| U16      | 2 bytes | No     | uint16_t         | 0                  | 65,535             |
| U32      | 4 bytes | No     | uint32_t         | 0                  | 4,294,967,295      |
| U64      | 8 bytes | No     | uint64_t         | 0                  | 1.84e19            |
| F64      | 8 bytes | Yes    | double           | ~2.2e-308          | ~1.8e308           |
| Bool     | 1 byte  | No     | bool             | FALSE (0)          | TRUE (1)           |
| Char     | 1 byte  | N/A    | char             | -128 / 0           | 127 / 255          |
| void     | 0 bytes | N/A    | void             | —                  | —                  |
| U0       | 0 bytes | N/A    | void             | —                  | —                  |

### 3.2 HolyC to C17 Type Mapping

| HolyC      | C17              | Printf Format |
|------------|------------------|---------------|
| I8       | int8_t         | %hhd           |
| I16      | int16_t        | %hd            |
| I32      | int32_t        | %d             |
| I64      | int64_t        | %lld           |
| U8       | uint8_t        | %hhu           |
| U16      | uint16_t       | %hu            |
| U32      | uint32_t       | %u             |
| U64      | uint64_t       | %llu           |
| F64      | double         | %f / %g        |
| Bool     | bool           | %d             |
| Char     | char           | %c             |
| void     | void           | —              |

### 3.3 The U0 Procedure Type

U0 is a zero-sized type used for procedures that return nothing. It is
functionally identical to void but carries semantic meaning in HolyC:
a U0 function is a procedure (no return value).

```c
U0 PrintMessage(Char *msg)
{
    Print("Message: %s\n", msg);
}

U0 LogError()
{
    "ERROR occurred\n";
}
```

In C17, both U0 and void map to void.

### 3.4 Derived Types

| Syntax                 | Description                | C17 Equivalent              |
|------------------------|----------------------------|-----------------------------|
| Type*                | Pointer to Type            | Type*                     |
| Type[size]           | Array of Type              | Type[size]                |
| Type (*)(args)       | Function pointer           | Type (*)(args)            |
| class Name { ... }   | Structured type            | typedef struct { ... } Name |
| union Name { ... }   | Union type                 | typedef union { ... } Name |
| enum Name { ... }    | Enumerated type            | typedef enum { ... } Name |

```c
I64 x = 42;
I64 *ptr = &x;
*ptr = 100;

I64 arr[10];
I64 matrix[5][5];
arr[0] = 42;
I64 values[4] = {1, 2, 3, 4};

I64 (*op)(I64, I64);
I64 Add(I64 a, I64 b) { return a + b; }
op = Add;
I64 result = op(10, 20);
```

### 3.5 Type Literals

```c
I64  decimal    = 42;
I64  hex        = 0xFF;
I64  binary     = 0b1010;
F64  float_lit  = 3.14159;
F64  scientific = 2.718e0;
Bool flag       = TRUE;
Bool nope       = FALSE;
Char c          = 'A';
Char nl         = '\\n';
Char nul        = '\\0';
```

Integer literals are I64 by default. Floating-point literals are F64.

#### Escape Sequences in Character Literals

| Sequence | Value  | Description     |
|----------|--------|-----------------|
| \\n     | 10     | Newline         |
| \\t     | 9      | Tab             |
| \\0     | 0      | Null character  |
| \\\\     | 92     | Backslash       |
| \\'     | 39     | Single quote    |
| \\"     | 34     | Double quote    |

### 3.6 Type Queries

#### sizeof Operator

```c
I64 sz1 = sizeof(I64);          // 8
I64 sz2 = sizeof(F64);          // 8
I64 sz3 = sizeof(Char);         // 1
I64 sz4 = sizeof(U0);           // 0
I64 sz5 = sizeof(I64*);         // 8
I64 sz6 = sizeof(I64[10]);      // 80
I64 x = 42;
I64 sz7 = sizeof(x);            // 8
```

#### offset Operator

Returns the byte offset of a field within a class/struct:

```c
class Vec2 { F64 x; F64 y; };
I64 off = offset(Vec2.y);       // 8
```

### 3.7 Type Predicates

The type system classifies types into categories:

- **Integer types**: I8, I16, I32, I64, U8, U16, U32, U64, Char
- **Floating type**: F64
- **Numeric**: integer or floating
- **Scalar**: numeric, pointer, or Bool
- **Aggregate**: struct, union, or array
- **Void/U0**: zero-sized non-value types

Type checking rules:
- Arithmetic between two integers produces an integer type
- If either operand is F64, the result is F64
- Comparison operators produce Bool
- Logical operators (&&, ||, !) require Bool operands
- Assignment requires type compatibility

---

## 4. Variables and Storage

### 4.1 Declaration and Initialization

Variables are declared with a type followed by a name:

```c
I64 count;             // uninitialized (undefined value)
I64 total = 0;         // initialized
F64 pi = 3.14159;
Bool flag = TRUE;
Char letter = 'A';
I64 *ptr = NULL;

// Multiple on one line
I64 a, b, c;
I64 x = 10, y = 20, z = 30;
```

### 4.2 Storage Class Modifiers

| Modifier  | Description                                      | C17 Equivalent |
|-----------|--------------------------------------------------|----------------|
| static  | File-internal linkage                             | static       |
| extern  | External linkage                                  | extern       |
| public  | Public API symbol (informational)                 | (none)       |
| private | File-internal linkage                             | static       |
| const   | Read-only                                         | const        |
| reg     | Hint to store in a CPU register                   | (hint)       |
| noreg   | Hint NOT to use a CPU register                    | (hint)       |
| no_warn | Suppress compiler warnings                        | (absorbed)   |

```c
static I64 internal_counter = 0;
extern I64 global_state;
public I64 API_VERSION = 2;
private I64 internal_flag = 0;
const F64 TAX_RATE = 0.18;
reg I64 R15 loop_counter;
noreg I64 temp_result;
no_warn I64 unused;
```

### 4.3 Scope and Lifetime

HolyC uses lexical (static) scoping:

```c
I64 global = 100;               // GLOBAL scope

U0 DemoScope()
{
    I64 func_var = 200;         // FUNCTION scope
    {
        I64 block_var = 300;    // BLOCK scope
    }
    // block_var is NOT accessible here
}
```

Scope hierarchy: GLOBAL -> FUNCTION -> BLOCK -> STRUCT
Inner scopes can shadow outer names:

```c
I64 x = 10;
U0 ShadowDemo() {
    I64 x = 20;                // shadows global x
    Print("%lld\n", x);         // prints 20
}
```

### 4.4 Constants

```c
Bool running = TRUE;            // TRUE = 1, FALSE = 0
void *ptr = NULL;               // null pointer

enum Color { RED, GREEN, BLUE };
I64 c = GREEN;                  // 1

F64 circumference = 2.0 * HC_PI * radius;
```

### 4.5 Variable Naming Rules

- Must begin with a letter or underscore
- Subsequent characters: letters, digits, or underscores
- Case-sensitive (count, Count, COUNT are different)
- No length limit
- Keywords cannot be used as names

---

## 5. Operators

### 5.1 Arithmetic Operators

| Operator | Description   | Example   | Result |
|----------|---------------|-----------|--------|
| +      | Addition      | 10 + 3  | 13   |
| -      | Subtraction   | 10 - 3  | 7    |
| *      | Multiplication| 10 * 3  | 30   |
| /      | Division      | 10 / 3  | 3 (integer) |
| %      | Modulo        | 10 % 3  | 1    |
| ++     | Increment     | x++     | x then x+1 |
| --     | Decrement     | x--     | x then x-1 |

```c
I64 a = 10, b = 3;
I64 sum = a + b;           // 13
I64 diff = a - b;          // 7
I64 prod = a * b;          // 30
I64 quot = a / b;          // 3
I64 rem = a % b;           // 1

I64 c = 5;
c++;                       // c = 6
I64 d = ++c;               // c = 7, d = 7
I64 e = c++;               // e = 7, c = 8
```

### 5.2 Bitwise Operators

| Operator | Description   | Example       | Result     |
|----------|---------------|---------------|------------|
| &      | AND           | 0xFF & 0x0F | 0x0F     |
| |      | OR            | 0xF0 | 0x0F | 0xFF     |
| ^      | XOR           | 0xFF ^ 0x0F | 0xF0     |
| ~      | NOT           | ~0xFF       | platform-dependent |
| <<     | Left shift    | 1 << 4      | 16       |
| >>     | Right shift   | 16 >> 2     | 4        |

```c
I64 flags = 0b0011;
I64 mask   = 0b0101;
I64 and    = flags & mask;     // 0b0001
I64 or     = flags | mask;     // 0b0111
I64 xor    = flags ^ mask;     // 0b0110
I64 not    = ~flags;           // bitwise NOT
I64 shl    = 1 << 3;          // 8
I64 shr    = 16 >> 2;         // 4
```

### 5.3 Logical Operators

| Operator | Description | Example           | Result  |
|----------|-------------|-------------------|---------|
| &&     | AND         | TRUE && FALSE   | FALSE |
| ||     | OR          | TRUE || FALSE   | TRUE  |
| !      | NOT         | !FALSE          | TRUE  |

```c
Bool a = TRUE;
Bool b = FALSE;
Bool r1 = a && b;       // FALSE
Bool r2 = a || b;       // TRUE
Bool r3 = !a;           // FALSE
Bool r4 = !b;           // TRUE
```

Logical operators short-circuit.

### 5.4 Comparison Operators

| Operator | Description        | Example    | Result  |
|----------|--------------------|------------|---------|
| ==     | Equal to           | 5 == 5   | TRUE  |
| !=     | Not equal to       | 5 != 3   | TRUE  |
| <      | Less than          | 3 < 5    | TRUE  |
| >      | Greater than       | 5 > 3    | TRUE  |
| <=     | Less than or equal | 5 <= 5   | TRUE  |
| >=     | Greater or equal   | 5 >= 3   | TRUE  |

### 5.5 Chained Comparisons

HolyC supports chained comparisons (invalid in C):

```c
if (0 <= x < 10) { }          // desugared to (0 <= x) && (x < 10)
if (10 <= x < 100) { }        // (10 <= x) && (x < 100)
if (1 <= x && x < 10 <= y) { } // valid chain
```

### 5.6 Assignment Operators

| Operator | Example    | Equivalent to |
|----------|------------|---------------|
| =      | x = 5    | x = 5       |
| +=     | x += 3   | x = x + 3   |
| -=     | x -= 3   | x = x - 3   |
| *=     | x *= 3   | x = x * 3   |
| /=     | x /= 3   | x = x / 3   |
| %=     | x %= 3   | x = x % 3   |
| &=     | x &= 3   | x = x & 3   |
| |=     | x |= 3   | x = x | 3   |
| ^=     | x ^= 3   | x = x ^ 3   |
| <<=    | x <<= 3  | x = x << 3  |
| >>=    | x >>= 3  | x = x >> 3  |

### 5.7 Member Access Operators

```c
class Point { I64 x; I64 y; };
Point p;
p.x = 10;                  // direct access
Point *ptr = &p;
ptr->x = 30;               // pointer access
I64 arr[5];
arr[2] = 42;               // array subscript
```

### 5.8 The Power Operator (Backtick)

The backtick character is a binary power operator, mapping to pow():

```c
F64 squared = 5.0 ` 2.0;    // 25.0
F64 cubed   = 2.0 ` 3.0;    // 8.0
F64 root    = 27.0 ` (1.0/3.0); // 3.0
F64 big     = 10.0 ` 6.0;   // 1,000,000.0
```

### 5.9 The offset Operator

Returns byte offset of a field:

```c
class Data { I8 flags; I64 value; F64 ratio; };
I64 off1 = offset(Data.flags);  // 0
I64 off2 = offset(Data.value);  // typically 8
I64 off3 = offset(Data.ratio);  // typically 16
```

### 5.10 The sizeof Operator

```c
I64 s1 = sizeof(I8);          // 1
I64 s2 = sizeof(I64);         // 8
I64 s3 = sizeof(F64);         // 8
I64 s4 = sizeof(Char);        // 1
I64 s5 = sizeof(Bool);        // 1
I64 s6 = sizeof(U0);          // 0
I64 arr[20];
I64 s7 = sizeof(arr);         // 160
class Vec3 { F64 x; F64 y; F64 z; };
I64 s8 = sizeof(Vec3);        // 24
```

### 5.11 The Cast Operator

```c
F64 pi = 3.14159;
I64 truncated = (I64)pi;      // 3
I64 addr = 0x7FFF0000;
void *ptr = (void*)addr;
F64 ratio = (F64)10 / 3;      // 3.333...
```

### 5.12 The Ternary Conditional Operator

```c
I64 age = 20;
Char *status = (age >= 18) ? "adult" : "minor";
I64 max = (a > b) ? a : b;
```

### 5.13 Operator Precedence Table

| Level | Operators                      | Assoc          |
|-------|--------------------------------|----------------|
| 14    | `                             | Left-to-right  |
| 13    | * / %                        | Left-to-right  |
| 12    | + -                          | Left-to-right  |
| 11    | << >>                        | Left-to-right  |
| 10    | < <= > >=                     | Left-to-right  |
| 9     | == !=                        | Left-to-right  |
| 8     | &                            | Left-to-right  |
| 7     | ^                            | Left-to-right  |
| 6     | |                            | Left-to-right  |
| 5     | &&                           | Left-to-right  |
| 4     | ||                           | Left-to-right  |
| 3     | ?:                           | Right-to-left  |
| 2     | = += -= *= /= %= &= |= ^= <<= >>= | Right-to-left |

Prefix unary operators bind tighter than binary. Postfix binds tightest.

---

## 6. Control Flow

### 6.1 if / else if / else

```c
I64 score = 85;
if (score >= 90) {
    Print("Grade: A\n");
} else if (score >= 80) {
    Print("Grade: B\n");
} else if (score >= 70) {
    Print("Grade: C\n");
} else {
    Print("Grade: F\n");
}
```

### 6.2 Chained Comparisons in Conditions

```c
I64 age = 17;
if (13 <= age < 20) {
    Print("Teenager\n");
}
if (0 < age < 150) {
    Print("Valid age\n");
}
```

### 6.3 switch Statement

```c
switch (x) {
    case 0:
        Print("zero\n"); break;
    case 1:
        Print("one\n"); break;
    default:
        Print("other\n"); break;
}
```

### 6.4 Implicit Case Labels

Cases without values auto-increment from 0:

```c
switch (mode) {
    case:            // 0
        Print("Idle\n"); break;
    case:            // 1
        Print("Active\n"); break;
    case:            // 2
        Print("Debug\n"); break;
}
```

### 6.5 Range Case Labels

A range `case X...Y:` matches any value from X to Y inclusive:

```c
switch (score) {
    case 90...100:
        Print("A\n"); break;
    case 80...89:
        Print("B\n"); break;
    case 70...79:
        Print("C\n"); break;
    default:
        Print("Invalid\n"); break;
}
```

### 6.6 for Loop

```c
for (I64 i = 0; i < 10; i++) {
    Print("i = %lld\n", i);
}

I64 j;
for (j = 0; j < 10; j++) { }

for ( ; ; ) { break; }  // infinite loop
```

### 6.7 while Loop

```c
I64 i = 0;
while (i < 5) {
    Print("i = %lld\n", i); i++;
}

while (TRUE) {           // infinite loop
    if (exit) break;
}
```

### 6.8 do-while Loop

```c
I64 i = 0;
do {
    Print("i = %lld\n", i); i++;
} while (i < 5);
```

### 6.9 goto Statement

HolyC encourages goto for control flow:

```c
I64 i = 0;
start_loop:
    if (i >= 5) goto end_loop;
    Print("i = %lld\n", i);
    i++;
    goto start_loop;
end_loop:

// Error handling with goto
U0 Process() {
    if (!step1()) goto err;
    if (!step2()) goto err;
    return;
err:
    Print("Error\n");
}
```

### 6.10 break Statement

Exits innermost loop or switch:

```c
for (I64 i = 0; i < 100; i++) {
    if (i == 50) break;
}
```

### 6.11 continue Statement

Skips to next iteration. Note: continue is not standard HolyC —
the transpiler emits a warning suggesting goto instead.

```c
for (I64 i = 0; i < 10; i++) {
    if (i % 2 == 0) continue;   // warning emitted
    Print("%lld ", i);            // prints odd numbers
}
```

### 6.12 return Statement

```c
I64 Add(I64 a, I64 b) { return a + b; }
U0 Log(Char *msg) { Print("[LOG] %s\n", msg); return; }

// Top-level return exits program
Print("Hello\n");
return 0;
```

---

## 7. Functions

### 7.1 Function Definition

```c
I64 Add(I64 a, I64 b)
{
    return a + b;
}
```

No forward declarations needed — functions are visible file-wide.

### 7.2 Return Types

```c
I64  Compute(I64 x)  { return x * x; }
F64  GetPI()          { return HC_PI; }
Bool IsPositive(I64 x){ return x > 0; }
Char GetGrade(F64 pct){ return pct >= 90 ? 'A' : 'B'; }
U0   Log(Char *msg)   { Print("[LOG] %s\n", msg); }
void LogError(Char *e){ Print("[ERR] %s\n", e); }
```

### 7.3 Parameters

```c
U0 NoParams() { Print("No params\n"); }
I64 Sum(I64 a, I64 b, I64 c) { return a + b + c; }
```

### 7.4 Variadic Functions (argc/argv)

HolyC provides built-in argc and argv for variadic functions:

```c
I64 SumAll(I64 count, ...)
{
    I64 total = 0;
    for (I64 i = 0; i < count; i++) {
        total += argv[i];
    }
    return total;
}

I64 result = SumAll(5, 10, 20, 30, 40, 50);  // 150
```

- argc: number of variadic arguments (built-in)
- argv: array of I64 containing the variadic arguments (built-in)
- Maximum 64 variadic arguments
- Function must have at least one named parameter before ...

### 7.5 Bare Function Calls

Zero-argument functions can be called without parentheses:

```c
U0 ClearScreen() { Print("\\033[2J\\033[H"); }

U0 ShowBanner() {
    ClearScreen;        // same as ClearScreen();
    Print("Banner\n");
}
```

### 7.6 The main() Entry Point

```c
I64 main()
{
    Print("Started\n");
    return 0;
}
```

Return value = exit code (0 = success).

### 7.7 Top-Level Code Execution

Statements outside any function become the entry point:

```c
"Starting...\n";
I64 x = 42;
U0 Init() { Print("Init\n"); }
Init;
return 0;
```

The transpiler wraps top-level statements in int main().

### 7.8 Function Pointers

```c
I64 Add(I64 a, I64 b) { return a + b; }
I64 Sub(I64 a, I64 b) { return a - b; }

I64 Compute(I64 a, I64 b, I64 (*op)(I64, I64)) {
    return op(a, b);
}

I64 (*fp)(I64, I64) = Add;
I64 r = fp(10, 5);                    // 15
I64 r2 = Compute(10, 5, Sub);         // 5
```

### 7.9 Callbacks

```c
U0 Map(I64 *arr, I64 count, I64 (*op)(I64)) {
    for (I64 i = 0; i < count; i++) arr[i] = op(arr[i]);
}

I64 Double(I64 x) { return x * 2; }
I64 data[3] = {1, 2, 3};
Map(data, 3, Double);              // data = {2, 4, 6}
```

### 7.10 Recursion

```c
I64 Factorial(I64 n) {
    if (n <= 1) return 1;
    return n * Factorial(n - 1);
}

I64 Fibonacci(I64 n) {
    if (n <= 1) return n;
    return Fibonacci(n-1) + Fibonacci(n-2);
}

I64 f5 = Factorial(5);    // 120
I64 f7 = Fibonacci(7);    // 13
```

---

## 8. Classes, Structs, Unions, Enums

### 8.1 The class Keyword

HolyC uses class instead of C's typedef struct:

```c
class Vec2 {
    F64 x;
    F64 y;
};
```

Generates:
```c
typedef struct { double x; double y; } Vec2;
```

### 8.2 Fields

```c
class Player {
    I64  id;
    Char name[64];
    I64  health;
    I64  level;
    Bool is_alive;
};

Player p;
p.id = 1;
p.health = 100;
Player *ptr = &p;
ptr->level = 5;
```

### 8.3 Methods

Functions defined inside a class become methods:

```c
class Vec2 {
    F64 x;
    F64 y;

    F64 Length() {
        return Sqrt(x * x + y * y);
    }

    U0 Scale(F64 factor) {
        x *= factor;
        y *= factor;
    }
};

Vec2 v; v.x = 3; v.y = 4;
F64 len = v.Length();      // 5.0
v.Scale(2);                // v = (6, 8)
```

### 8.4 The this Pointer

Inside a method, this refers to the current instance:

```c
class Vec2 {
    F64 x; F64 y;
    U0 Set(F64 x, F64 y) { this->x = x; this->y = y; }
};
```

### 8.5 C17 Code Generation for Class Methods

Methods transpile to ClassName_MethodName(ClassName *this):

```c
// HolyC:
v.Length();

// Generated C17:
Vec2_Length(&v);
```

### 8.6 union

```c
union Data {
    I64 integer;
    F64 floating;
    Char bytes[8];
};

union Data d;
d.floating = 3.14159;
I64 i = d.integer;          // read same bytes as int
```

### 8.7 enum

```c
enum Color { RED, GREEN, BLUE };
enum HttpStatus { OK = 200, NOT_FOUND = 404 };
enum Weekday { MON = 1, TUE, WED, THU, FRI, SAT, SUN };

enum Color c = GREEN;      // 1
I64 status = NOT_FOUND;    // 404
```

### 8.8 Memory Layout and Alignment

| Type | Alignment | Size  |
|------|-----------|-------|
| I8/U8/Char/Bool | 1 byte  | 1 byte  |
| I16/U16 | 2 bytes | 2 bytes |
| I32/U32 | 4 bytes | 4 bytes |
| I64/U64/F64 | 8 bytes | 8 bytes |

```c
class Example {
    I8  a;      // offset 0, size 1
    // 7 bytes padding
    I64 b;      // offset 8, size 8
    // Total: 16 bytes
};
```

---

## 9. Memory Management

### 9.1 Stack vs Heap

Stack: local variables, auto-allocated/freed, fast but limited (~8MB).
Heap: manual allocation, persists until freed, larger capacity.

### 9.2 Dynamic Allocation

```c
I64 *ptr = MAlloc(sizeof(I64));
*ptr = 42;
Free(ptr);

I64 *arr = MAlloc(100 * sizeof(I64));
for (I64 i = 0; i < 100; i++) arr[i] = i;
Free(arr);

class Point { I64 x; I64 y; };
Point *p = MAlloc(sizeof(Point));
p->x = 10; p->y = 20;
Free(p);
```

### 9.3 Memory Operations

```c
I64 buf[256];
MemSet((U8*)buf, 0, sizeof(buf));          // zero
MemCpy((U8*)dest, (U8*)buf, sizeof(buf));  // copy
I64 diff = MemCompare((U8*)a, (U8*)b, 32); // compare
void *block = MAlloc(1024);
U64 size = MSize(block);                    // >= 1024
```

### 9.4 Best Practices

1. Always free what you allocate
2. Free(NULL) is safe (no-op)
3. Use MemSet with 0 to initialize memory
4. Use sizeof() for correct allocation sizes
5. Stack for small fixed-size, heap for large/variable

---

# Part II: Standard Library

---

## 10. Runtime Library — Complete API Reference

All runtime functions are built-in. No includes or declarations needed.

### 10.1 I/O Functions

#### Print

```c
void Print(const char *fmt, ...);
```

Formatted output to stdout.

```c
Print("Hello, world!\n");
Print("Value: %lld\n", 42);
Print("Name: %s, Age: %lld\n", "Alice", 30);
Print("PI = %.6f\n", 3.14159);
```

Format specifiers: %lld (I64), %llu (U64), %d (I32), %f (F64),
%s (string), %c (char), %x (hex), %p (pointer)

#### PrintLn

```c
void PrintLn(const char *fmt, ...);
```

Same as Print but appends newline:

```c
PrintLn("Hello, world!");      // "Hello, world!\n"
```

#### PutChar

```c
void PutChar(char c);
```

Outputs a single character:

```c
PutChar('H'); PutChar('i'); PutChar('!'); PutChar('\\n');
// Output: Hi!
```

#### GetCh

```c
int GetCh(void);
```

Reads a single character from stdin (blocking):

```c
Print("Press a key: ");
I64 ch = GetCh();
Print("You pressed: %c (code %lld)\n", ch, ch);
```

#### SPrint

```c
int SPrint(char *buf, const char *fmt, ...);
```

Formats into a string buffer:

```c
Char buffer[256];
I64 n = SPrint(buffer, "Value = %lld", 42);
Print("Buffer: %s (%lld chars)\n", buffer, n);
```

### 10.2 Memory Functions

#### MAlloc

```c
void *MAlloc(uint64_t size);
```

Allocates size bytes from the heap. Returns NULL on failure.
Memory is NOT zero-initialized.

```c
I64 *arr = MAlloc(10 * sizeof(I64));
if (arr == NULL) { Print("Allocation failed!\n"); return 1; }
Free(arr);
```

#### Free

```c
void Free(void *ptr);
```

Deallocates memory. NULL-safe. Double-free is undefined.

#### MSize

```c
uint64_t MSize(void *ptr);
```

Returns usable size of allocated block:

```c
void *block = MAlloc(100);
U64 actual = MSize(block);              // >= 100
```

#### MemSet

```c
void MemSet(uint8_t *dst, uint8_t value, uint64_t count);
```

Sets count bytes at dst to value:

```c
I64 buffer[256];
MemSet((U8*)buffer, 0, sizeof(buffer));       // zero
MemSet((U8*)buffer, 0xFF, sizeof(buffer));    // all 1s
```

#### MemCpy

```c
void MemCpy(uint8_t *dst, const uint8_t *src, uint64_t count);
```

Copies count bytes from src to dst.

#### MemCompare

```c
int64_t MemCompare(const uint8_t *a, const uint8_t *b, uint64_t count);
```

Returns 0 if equal, negative if a < b, positive if a > b.

### 10.3 String Functions

#### StrLen

```c
uint64_t StrLen(const char *str);
```

Returns string length (excluding null terminator):

```c
U64 len = StrLen("Hello");      // 5
```

#### StrCompare

```c
bool StrCompare(const char *a, const char *b);
```

Equality check (returns TRUE if strings are identical):

```c
Bool eq = StrCompare("abc", "abc");  // TRUE
```

#### AtoI

```c
int64_t AtoI(const char *str);
```

String to I64:

```c
I64 val = AtoI("42");            // 42
I64 neg = AtoI("-100");         // -100
```

#### AtoF

```c
double AtoF(const char *str);
```

String to F64:

```c
F64 f = AtoF("3.14159");         // 3.14159
```

### 10.4 Control Functions

#### CDelay

```c
void CDelay(uint64_t ms);
```

Pauses execution for ms milliseconds:

```c
Print("Waiting...\n");
CDelay(1000);
Print("Done!\n");
```

#### Exit

```c
void Exit(int64_t code);
```

Terminates program immediately:

```c
if (fatal_error) { Print("Fatal error\n"); Exit(1); }
```

### 10.5 Math Library

All math functions operate on F64 (double) and return F64.

#### Trigonometric

```c
double Sin(double x);     // sine (radians)
double Cos(double x);     // cosine (radians)
double Tan(double x);     // tangent (radians)
```

#### Inverse Trigonometric

```c
double ASin(double x);    // arc sine, returns [-pi/2, pi/2]
double ACos(double x);    // arc cosine, returns [0, pi]
double ATan(double x);    // arc tangent, returns (-pi/2, pi/2)
double ATan2(double y, double x); // quadrant-aware arc tangent
```

#### Hyperbolic

```c
double SinH(double x);    // hyperbolic sine
double CosH(double x);    // hyperbolic cosine
double TanH(double x);    // hyperbolic tangent
```

#### Exponential, Power, Roots

```c
double Exp(double x);            // e^x
double Pow(double base, double exp); // base^exp
double Sqrt(double x);           // square root
double Cbrt(double x);           // cube root
double Hypot(double a, double b); // sqrt(a^2 + b^2)
```

#### Logarithm

```c
double Log(double x);     // natural logarithm (ln)
double Log2(double x);    // base-2 logarithm
double Log10(double x);   // base-10 logarithm
```

#### Rounding

```c
double Floor(double x);   // round down (toward -inf)
double Ceil(double x);    // round up (toward +inf)
double Round(double x);   // round nearest (ties away from zero)
double Trunc(double x);   // round toward zero
```

```c
F64 v = 3.7;
F64 f = Floor(v);    // 3.0
F64 c = Ceil(v);     // 4.0
F64 r = Round(v);    // 4.0
F64 t = Trunc(v);    // 3.0
```

#### Absolute Value

```c
double FAbs(double x);     // F64 absolute value
int64_t Abs(int64_t x);    // I64 absolute value
```

#### Min / Max

```c
double FMin(double a, double b);
double FMax(double a, double b);
int64_t Min(int64_t a, int64_t b);
int64_t Max(int64_t a, int64_t b);
```

#### FMod

```c
double FMod(double x, double y);  // floating remainder
```

### 10.6 Math Constants

```c
#define HC_PI    3.14159265358979323846
#define HC_E     2.71828182845904523536
#define HC_TAU   6.28318530717958647692
#define HC_SQRT2 1.41421356237309504880
```

```c
F64 circumference = 2.0 * HC_PI * radius;
F64 area = HC_PI * radius * radius;
F64 full_turn = HC_TAU;
F64 diagonal = HC_SQRT2 * side;
```

---

## 11. HolyC-Specific Features

### 11.1 Top-Level Code

Statements outside any function execute at startup:

```c
"Starting...\n";
I64 x = 42;
return 0;
```

### 11.2 String Auto-Print

Bare string literals auto-print:

```c
"Hello\n";               // equivalent to Print("Hello\n");
```

### 11.3 Bare Function Call

Zero-arg functions can be called by name alone:

```c
MyFunc;                   // same as MyFunc();
```

### 11.4 Chained Comparisons

```c
if (0 <= x < 10) { }     // (0 <= x) && (x < 10)
if (a < b <= c < d) { }  // chains of any length
```

### 11.5 Switch Extensions

Implicit cases (auto-numbered from 0) and range cases:

```c
switch (x) {
    case:             // 0
    case 4...10:      // 4 through 10
}
```

### 11.6 The U0 Type

Zero-sized procedure type:

```c
U0 Log() { "logged\n"; }   // maps to void
```

### 11.7 argc/argv Variadic

Built-in variadic support:

```c
I64 Sum(I64 n, ...) {
    I64 total = 0;
    for (I64 i = 0; i < argc; i++) total += argv[i];
    return total;
}
```

### 11.8 Class Methods

Methods transpile to ClassName_MethodName(ClassName *this):

```c
class Vec2 {
    F64 x, y;
    F64 Length() { return Sqrt(x*x + y*y); }
};
// v.Length() -> Vec2_Length(&v)
```

### 11.9 try / catch / throw

Exception handling via setjmp/longjmp:

```c
try {
    if (error) throw;
} catch {
    "Error!\n";
}
```

### 11.10 The reg and noreg Keywords

Register allocation hints:

```c
reg I64 R15 fast;         // register hint
noreg I64 slow;           // no register hint
```

### 11.11 The public and private Keywords

Visibility control:

```c
public I64 api;           // documentation only
private I64 internal;     // maps to static
```

### 11.12 The no_warn Keyword

Suppress warnings:

```c
no_warn I64 temp;         // no unused var warning
```

### 11.13 The offset Keyword

Field byte offset:

```c
offset(Vec2.y);           // -> offsetof(Vec2, y)
```

### 11.14 The has Keyword

Compile-time member existence test (token only):

```c
has Vec2.y                // member exists?
```

### 11.15 Color Literals in Strings

TempleOS color codes are stripped:

```c
"$FF$red text$FG$ normal\n";
// $FF$ = foreground color, $FG$ = reset
// $RRGGBB$ = true color (stripped)
// $$ = literal dollar sign
```

### 11.16 Inline Assembly

```c
asm {
    "mov eax, 42";
};
// Generated: /* inline assembly */ /* mov eax, 42 */;
```

### 11.17 The Power Operator

Backtick is power operator:

```c
F64 square = 5.0 ` 2.0;   // 25.0 (pow(5.0, 2.0))
```

### 11.18 The NULL, TRUE, FALSE Constants

```c
void *ptr = NULL;         // null pointer
Bool running = TRUE;      // true = 1
Bool done = FALSE;        // false = 0
```

---

## 12. Preprocessor and Modules

### 12.1 #define

Simple macro definitions (function-like macros NOT supported):

```c
#define WIDTH  800
#define HEIGHT 600
#define DEBUG  1
#define TITLE  "HolyC"

I64 area = WIDTH * HEIGHT;   // 480000
```

### 12.2 #include

Only double-quote form (relative to source directory):

```c
#include "math.HC"
#include "defines.HC"
```

### 12.3 import

Similar to #include, but without .HC extension:

```c
import "mylib";           // searches mylib.HC
```

### 12.4 #if / #else / #endif

```c
#define PLATFORM_LINUX 1
#if PLATFORM_LINUX
    "Running on Linux\n";
#else
    "Unknown platform\n";
#endif
```

### 12.5 #ifdef / #ifndef / #elif

```c
#ifdef FEATURE_X
    I64 feature_x = 1;
#endif

#ifndef DISABLE_LOGGING
    U0 Log(Char *msg) { Print("[LOG] %s\n", msg); }
#elif LOG_LEVEL >= 2
    U0 Log(Char *msg) { Print("[VERBOSE] %s\n", msg); }
#endif
```

---

# Part III: Compiler Internals

---

## 13. Compiler Architecture

### 13.1 Pipeline Overview

The holycc transpiler is a multi-pass pipeline:

```
Source (.HC)
    |
    v
+-----------------+
|     Lexer       |  Characters -> Tokens (114 token kinds)
|  src/lexer/     |  Keyword matching, literal parsing
+--------+--------+
         | Token stream
         v
+-----------------+
|    Parser       |  Tokens -> AST (45+ node kinds)
|  src/parser/    |  Recursive descent + precedence climbing
+--------+--------+
         | AST tree
         v
+-----------------+
|   Semantic      |  Type checking, symbol resolution
|  src/semantic/  |  Scope management, error detection
+--------+--------+
         | Verified AST
         v
+-----------------+
|   CodeGen       |  AST -> C17 source text
|  src/codegen/   |  Type mapping, special case handling
+--------+--------+
         | Generated C17 (.c)
         v
+-----------------+
|  GCC / Clang    |  Compilation + linking
|  (via Driver)   |  libholyc_runtime.a + -lm
+--------+--------+
         |
         v
   Native Executable
```

### 13.2 Lexer

Location: src/lexer/lexer.c (473 lines)

The lexer converts source text into a token stream, operating
character-by-character through the source buffer.

Key functions:
- lexer_next_token() — consume and return the next token
- lexer_peek_token() — return next token without consuming
- lexer_read_number() — decimal, hex (0x), binary (0b), float
- lexer_read_string() — strings with escape sequences
- lexer_read_identifier() — identifier/keyword dispatch
- lexer_read_preprocessor() — #if, #else, #endif, etc.
- lexer_skip_whitespace() — spaces, tabs, // and /* */ comments

Token kinds (114+):
- Literals: integer, float, string, char, bool
- Keywords: 53 HolyC keywords (all types, control flow, storage, etc.)
- Operators: all arithmetic, bitwise, logical, comparison, assignment
- Punctuation: {} () [] ; : , . -> ... #

Comment handling:
- // to end of line
- /* */ block comments (nested not supported)

### 13.3 Parser

Location: src/parser/parser.c (1159 lines)

Algorithm: Recursive descent with precedence climbing for expressions.

Precedence levels (14=highest, 2=lowest):
14: Power (`)
13: Mult/Div/Mod (* / %)
12: Add/Sub (+ -)
11: Shift (<< >>)
10: Relational (< <= > >=)
9: Equality (== !=)
8: Bitwise AND (&)
7: Bitwise XOR (^)
6: Bitwise OR (|)
5: Logical AND (&&)
4: Logical OR (||)
3: Ternary (?:)
2: Assignment (= += -= etc.)

Parser layers:
1. parser_parse_primary() — literals, identifiers, (...), {...}
2. parser_parse_postfix() — [], (), ., ->, ++, --
3. parser_parse_prefix() — unary -, !, ~, *, &, ++, --, sizeof, offset, casts
4. parser_parse_binary() — precedence climbing with chained comparison
5. parser_parse_expr() — entry point

Statement parsing dispatches on token kind:
- if/for/while/do/switch — control flow
- return/break/continue/goto — jumps
- try/catch/throw — exceptions
- asm — inline assembly
- { — block
- Type keyword — declaration

Chained comparisons: a < b <= c desugared to (a < b) && (b <= c)
via AST cloning.

### 13.4 Abstract Syntax Tree (AST)

Location: src/ast/ast.c (186 lines)

Each AstNode has:
- kind — 45+ node kinds
- first_child, last_child — child list anchors
- next — sibling pointer
- parent — parent pointer
- data — polymorphic union (int, float, string, token_kind)
- flags — boolean attributes
- type — resolved Type* (attached by semantic analysis)

AST node categories:
- Literals: INTEGER, FLOAT, STRING, CHAR, BOOL, NULL
- Identifiers: IDENTIFIER
- Types: NAMED_TYPE, POINTER_TYPE, ARRAY_TYPE, FUNC_POINTER_TYPE
- Expressions: BINARY, UNARY, CONDITIONAL, CALL, INDEX, MEMBER, etc.
- Statements: IF, FOR, WHILE, DO_WHILE, SWITCH, CASE, RETURN, etc.
- Declarations: FUNC_DECL, VAR_DECL, STRUCT_DECL, ENUM_DECL, etc.
- Top-level: TRANSLATION_UNIT, INCLUDE, PP_IF, PP_ELSE, etc.

Key operations:
- ast_node_create(kind)
- ast_add_child(parent, child)
- ast_visit(node, pre_cb, post_cb) — pre/post-order traversal
- ast_clone_node(node) — deep copy subtree
- ast_node_destroy_tree(node) — recursive cleanup

### 13.5 Semantic Analysis

Location: src/semantic/semantic.c (536 lines)

The semantic analyzer performs:

Symbol resolution:
- Looks up identifiers in symbol table
- Resolves type names to internal Type* objects
- Checks for undeclared variables/functions

Type checking:
- Dereference (*) requires pointer operand
- Address-of (&) produces pointer type
- Comparisons produce Bool
- Arithmetic promotes to F64 if either operand is float
- Function calls use symbol's return type

Scope management:
- Push scope on entering functions/blocks/aggregates
- Pop scope on leaving
- Detect duplicate definitions in current scope

HolyC-specific checks:
- continue outside loop -> warning
- break outside loop/switch -> error
- NULL, TRUE, FALSE recognized without declarations
- Class methods registered with ClassName_MethodName naming

### 13.6 Symbol Table

Location: src/symbol/symbol.c (157 lines)

Scope kinds: GLOBAL, FUNCTION, BLOCK, STRUCT, UNION
Symbol kinds: VARIABLE, FUNCTION, STRUCT, UNION, ENUM, CONSTANT, LABEL, TYPEDEF
Storage classes: NONE, STATIC, EXTERN

Operations:
- symbol_table_create() / destroy()
- scope_push(kind) / scope_pop()
- symbol_add(name, ...) — with duplicate detection
- symbol_lookup(name) — walks scope chain
- symbol_lookup_current_scope(name)

### 13.7 Type System Internals

Location: src/types/types.c (307 lines)

Type kinds: VOID, U0, BOOL, CHAR, I8-I64, U8-U64, F64, POINTER, ARRAY, STRUCT, UNION, ENUM, FUNCTION, ERROR, UNRESOLVED

Type factories:
type_void(), type_bool(), type_i8(), ..., type_f64(),
type_pointer(base), type_array(base, length), type_function(ret, params, variadic)

Type queries:
type_kind_name(), type_c_name(), type_size(), type_alignment()
type_is_integer(), type_is_floating(), type_is_numeric()
type_is_scalar(), type_is_aggregate()
type_equals() — structural equality

HolyC -> C17 name mapping:
I8->int8_t, I16->int16_t, I32->int32_t, I64->int64_t,
U8->uint8_t, U16->uint16_t, U32->uint32_t, U64->uint64_t,
F64->double, Bool->bool, Char->char, void->void, U0->void

### 13.8 Code Generator

Location: src/codegen/codegen.c (1173 lines)

The code generator walks the AST and emits C17 source text via StringBuffer.
It is the most complex module (1173 lines) because it must handle every
AST node kind and produce valid, idiomatic C17 output.

#### CodeGen State Structure

```c
struct CodeGen {
    SymbolTable *symtab;       // symbol table reference
    int indent_level;          // current indentation (4 spaces per level)
    StringBuffer buf;          // accumulating C17 output
    FuncNameNode *func_names;  // linked list of declared function names
};
```

The `func_names` linked list is built during a pre-scan of the translation
unit. It stores every function name found in declarations, which enables
the bare-call syntax (identifiers matching function names get appended
with `()` automatically).

#### Translation Unit Emission

1. **Header emission**: Includes stdint, stdbool, stddef, stdio, stdlib,
   string, math, stdarg, setjmp.
2. **Runtime prototypes**: All built-in function declarations.
3. **Math wrappers**: Thin wrapper functions around libm.
4. **setjmp buffer**: `jmp_buf __holyc_jmp_buf;`
5. **Function pre-scan**: Collect function names for bare-call support.
6. **Main detection**: Scan for explicit `main()`. If absent and top-level
   statements exist, create a synthetic `int main() { ... }`.
7. **Declaration-first ordering**: Function/type declarations are emitted
   before top-level statements in the synthetic main.

#### Expression Emission (codegen_emit_expr)

Handles all expression node kinds:

- **Literals**: Integer, float, string, char, bool, NULL
- **Identifiers**: Emitted directly by name
- **Unary**: -, !, ~ prefix; * and & with parentheses; ++/-- prefix
- **Binary**: Wrapped in `(left op right)` except power operator which
  becomes `pow(left, right)`
- **Ternary**: `(cond ? then : else)`
- **Calls**: `func(arg1, arg2, ...)`
- **Index**: `base[index]`
- **Member**: `obj.field` and `ptr->field`
- **Cast**: `((type)expr)`
- **sizeof**: `sizeof(expr)`
- **offset**: `offsetof(type, field)`
- **Array init**: `{ val1, val2, ... }`

String literals undergo color-code stripping: `$XX$` and `$RRGGBB$`
sequences are removed, `$$` becomes literal `$`.

Side-effect-free expressions (literals, identifiers, non-assignment
binaries) are wrapped in `(void)(expr)` to suppress GCC warnings.

#### Statement Emission (codegen_emit_stmt)

The main dispatcher handles 35+ statement kinds:

- **TranslationUnit**: Top-level orchestrator (header + declarations + main)
- **FuncDecl**: Return type mapping, `main()` gets `int`, variadic setup
- **VarDecl**: Optional static/extern, type mapping, array syntax, init
- **Block**: `{ indent children dedent }`
- **If/While/DoWhile/For**: Standard C control flow
- **Switch**: Emits switch body with case/default/range children
- **Case**: Single value `case N:`
- **CaseRange**: Expands to `case N: case N+1: ... case M:`
- **Return/Break/Continue/Goto/Label**: Simple C statements
- **ExprStmt**: String literals -> Print(), bare identifiers -> func(),
  side-effect-free -> (void)(expr)
- **Struct/Union**: typedef struct/union with field listing + method emission
- **Enum**: typedef enum with enumerator values
- **Try/Catch/Throw**: setjmp/longjmp with global jmp_buf
- **Asm**: /* inline assembly */ comments
- **Define**: #define pass-through
- **Preprocessor**: #if/#else/#endif/#ifdef/#ifndef/#elif pass-through
- **Include**: Child statements (from included file) emitted inline

### 13.9 CLI Driver

Location: src/driver/driver.c (396 lines)

The driver orchestrates the pipeline:

1. Parse CLI arguments
2. Read source file
3. Init diagnostics
4. Lexer (--tokens dump)
5. Parser (--ast dump)
6. Semantic analysis
7. Code generation
8. GCC invocation:
   gcc -std=c17 -O2 -Wall -Wextra -Wpedantic /tmp/prog.c -o prog -L/usr/local/lib -lholyc_runtime -lm
9. If --run, execute binary
10. Cleanup

### 13.10 Diagnostics System

Location: src/diag.c (171 lines)

Error levels: ERROR, WARNING, NOTE, ICE (Internal Compiler Error)

Output format:
file:line:col: level: message
  |
  source line
  ^

Pipeline error flow:
Read file -> fail -> exit 1
Lexer -> errors -> skip parse
Parser -> errors -> skip semantic
Semantic -> errors -> skip codegen
CodeGen -> errors -> skip GCC
GCC -> fail -> exit 1

### 13.11 Runtime Library

Location: runtime/holyc_runtime.c (168 lines), holyc_runtime.h (92 lines)

A static library (libholyc_runtime.a) providing all built-in functions.

Implementation notes:
- Print/PrintLn -> vprintf wrapper
- MAlloc -> malloc, Free -> free
- StrLen -> manual loop (no string.h)
- StrCompare -> equality check (not strcmp!)
- AtoI -> strtoll, AtoF -> strtod
- MemSet/MemCpy/MemCompare -> byte-by-byte loops
- MSize -> malloc_usable_size
- CDelay -> usleep(ms * 1000)
- GetCh -> getchar, PutChar -> putchar
- Exit -> exit
- SPrint -> vsprintf
- Math -> thin wrappers around libm

### 13.12 File-by-File Layout

```
include/holyc/          # Public API headers
  token.h               # TokenKind, SourceLocation, Token
  lexer.h               # Lexer opaque type
  parser.h              # Parser opaque type
  ast.h                 # AstKind, AstNode, visitor
  semantic.h            # Semantic opaque type
  symbol.h              # Scope, Symbol, SymbolTable
  types.h               # TypeKind, Type, StructField
  codegen.h             # CodeGen opaque type
  driver.h              # DriverOptions struct
  diag.h                # Diagnostics, Diagnostic
  utils.h               # StringBuffer, file I/O

src/                    # Compiler implementation
  main.c                # Entry point (6 lines)
  token.c               # Token lookup table (140 lines)
  diag.c                # Diagnostics (171 lines)
  utils.c               # StringBuffer, file I/O (125 lines)
  lexer/lexer.c         # Tokenizer (473 lines)
  parser/parser.c       # Recursive descent parser (1159 lines)
  ast/ast.c             # AST lifecycle (186 lines)
  semantic/semantic.c   # Type checking (536 lines)
  symbol/symbol.c       # Symbol table (157 lines)
  types/types.c         # Type system (307 lines)
  codegen/codegen.c     # C17 emitter (1173 lines)
  driver/driver.c       # CLI orchestration (396 lines)

runtime/                # HolyC runtime library
  holyc_runtime.h       # Declarations (92 lines)
  holyc_runtime.c       # Implementations (168 lines)

tests/                  # Test suite
  test_lexer.c          # 161 lexer tests (213 lines)

docs/                   # Documentation
  ARCHITECTURE.md       # Architecture (378 lines)
  LANGUAGE.md           # Language reference (1059 lines)

examples/               # Example .HC programs
  hello.HC, simple.HC, greeting.HC, arrays.HC,
  complex.HC, switch.HC, math.HC, mylib.HC,
  use_import.HC, with_include.HC
```

---

## 14. HolyC to C17 Transformation Matrix

| HolyC Construct                  | Generated C17 Code                        |
|----------------------------------|-------------------------------------------|
| "Hello\n";                     | Print("Hello\n");                       |
| I64 x = 42;                    | int64_t x = 42;                         |
| U0 Proc() {}                   | void Proc() {}                          |
| F64 Calc(F64 x)                | double Calc(double x)                   |
| Bool done = TRUE;              | bool done = true;                       |
| Char c = 'A';                  | char c = 'A';                           |
| class Vec { F64 x; };          | typedef struct { double x; } Vec;        |
| v.Method();                    | Vec_Method(&v);                         |
| I64 *ptr = NULL;               | int64_t *ptr = NULL;                    |
| a ` b                          | pow(a, b)                               |
| if (0 <= x < 10)               | if (((0 <= x) && (x < 10)))             |
| offset(Vec.x)                  | offsetof(Vec, x)                        |
| sizeof(I64)                    | sizeof(int64_t)                         |
| (F64)x                         | ((double)x)                             |
| try { } catch { }              | if (setjmp(buf) == 0) { } else { }      |
| throw;                         | longjmp(buf, 1);                        |
| throw val;                     | longjmp(buf, 1 + (int)(val));            |
| case 4...10:                   | case 4: case 5: ... case 10:            |
| case:                          | case 0: (or next implicit)               |
| Func; (bare call)              | Func();                                  |
| asm { "code"; };               | /* inline assembly */ /* code */;        |
| I64 Sum(I64 n, ...)            | int64_t Sum(int64_t n, ...) (+ va_list setup) |
| #define WIDTH 800              | #define WIDTH 800 (pass-through)         |
| Top-level statements            | Wrapped in int main() { ... }            |
| void / U0                       | void                                     |
| Print(fmt, ...)                | Print(fmt, ...) (prototype in preamble)  |
| MAlloc(size)                   | MAlloc(size) (prototype in preamble)     |
| Sin(x) / Sqrt(x)               | sin(x) / sqrt(x) (wrapper functions)     |
| I64 arr[5] = {1,2,3};          | int64_t arr[5] = {1,2,3};                |
| return 0; (top-level)          | return 0; (inside auto-generated main)   |
| I64 (*fp)(I64);                | int64_t (*fp)(int64_t);                  |

---

# Part IV: Examples and Reference

---

## 15. Comprehensive Examples

### 15.1 Example: Hello World

```c
// ex01_hello.HC — Three ways to say hello
Print("Hello from Print()!\n");
"Hello from auto-print!\n";

I64 main() {
    Print("Hello from main()!\n");
    return 0;
}
```

### 15.2 Example: Arithmetic and Variables

```c
// ex02_arithmetic.HC
I64 a = 25, b = 7;
Print("a+b=%lld, a-b=%lld, a*b=%lld, a/b=%lld, a%%b=%lld\n",
       a+b, a-b, a*b, a/b, a%b);

F64 fa=25.0, fb=7.0;
Print("fa/fb=%.4f\n", fa/fb);

I64 x = 10; x += 5; x *= 3; x >>= 2;
Print("x = %lld\n", x);

I64 y = 5;
Print("y++=%lld, ++y=%lld, y--=%lld, --y=%lld\n", y++, ++y, y--, --y);
return 0;
```

### 15.3 Example: Control Flow

```c
// ex03_control.HC
I64 score = 83;
if (score >= 90)      { Print("A\n"); }
else if (score >= 80) { Print("B\n"); }
else if (score >= 70) { Print("C\n"); }
else                  { Print("F\n"); }

I64 age = 17;
if (13 <= age < 20) { Print("Teenager\n"); }

for (I64 i = 0; i < 5; i++) { Print("%lld ", i); }
Print("\n");

I64 i = 0; while (i < 5) { Print("%lld ", i); i++; }
Print("\n");

i = 0;
loop:
    if (i >= 5) goto end;
    Print("%lld ", i); i++; goto loop;
end:
Print("\n");
return 0;
```

### 15.4 Example: Functions and Variadics

```c
// ex04_functions.HC
I64 Add(I64 a, I64 b) { return a + b; }
I64 Factorial(I64 n) { if (n <= 1) return 1; return n * Factorial(n-1); }

I64 SumAll(I64 count, ...) {
    I64 total = 0;
    for (I64 i = 0; i < argc; i++) total += argv[i];
    return total;
}

Print("Add(10,20)=%lld\n", Add(10, 20));
Print("5!=%lld\n", Factorial(5));
Print("SumAll(5,10,20,30,40,50)=%lld\n", SumAll(5, 10, 20, 30, 40, 50));
return 0;
```

### 15.5 Example: Classes and Methods (OOP)

```c
// ex05_oop.HC
class Vec2 {
    F64 x; F64 y;
    F64 Length() { return Sqrt(x*x + y*y); }
    U0 Scale(F64 f) { x *= f; y *= f; }
    U0 Print(Char *l) { Print("%s: (%.2f,%.2f)\n", l, x, y); }
};

Vec2 v; v.x = 3; v.y = 4;
v.Print("v");
v.Scale(2);
v.Print("v*2");
Print("Length=%.2f\n", v.Length());
return 0;
```

### 15.6 Example: Dynamic Memory — Linked List

```c
// ex06_list.HC
class Node { I64 data; Node *next; };
class List {
    Node *head; I64 count;
    U0 Init() { head = NULL; count = 0; }
    U0 Push(I64 v) {
        Node *n = MAlloc(sizeof(Node));
        n->data = v; n->next = head; head = n; count++;
    }
    U0 Print() {
        Node *c = head;
        while (c) { Print("%lld ", c->data); c = c->next; }
        Print("\n");
    }
    U0 Destroy() {
        Node *c = head;
        while (c) { Node *t = c; c = c->next; Free(t); }
    }
};

List list; list.Init;
list.Push(30); list.Push(20); list.Push(10);
list.Print(); list.Destroy();
return 0;
```

### 15.7 Example: Math Library in Action

```c
// ex07_math.HC
F64 deg45 = HC_PI / 4.0;
Print("Sin(45)=%.4f\n", Sin(deg45));
Print("Cos(45)=%.4f\n", Cos(deg45));
Print("Tan(45)=%.4f\n", Tan(deg45));
Print("Sqrt(144)=%.0f\n", Sqrt(144.0));
Print("2^10=%.0f\n", 2.0`10.0);
Print("Cbrt(27)=%.0f\n", Cbrt(27.0));
Print("Log2(256)=%.0f\n", Log2(256.0));
Print("Floor(3.7)=%.0f, Ceil(3.7)=%.0f\n", Floor(3.7), Ceil(3.7));
Print("Hypot(3,4)=%.0f\n", Hypot(3.0, 4.0));
Print("Abs(-42)=%lld\n", Abs(-42));
Print("Min(10,20)=%lld, Max(10,20)=%lld\n", Min(10,20), Max(10,20));
return 0;
```

### 15.8 Example: Exception Handling

```c
// ex08_trycatch.HC
U0 Validate(I64 age) {
    if (age < 0) { Print("Throwing\n"); throw -1; }
    Print("Age %lld is valid\n", age);
}

U0 Test(I64 v) {
    try { Validate(v); } catch { Print("Caught for %lld\n", v); }
}

Test(25); Test(-5); Test(30);

try { Print("In try\n"); throw 42; } catch { Print("In catch\n"); }
return 0;
```

### 15.9 Example: Array Operations

```c
// ex09_arrays.HC
I64 arr[5] = {10, 20, 30, 40, 50};
for (I64 i = 0; i < 5; i++) arr[i] *= 2;
for (I64 i = 0; i < 5; i++) Print("%lld ", arr[i]);
Print("\n");

I64 mat[3][4];
I64 val = 0;
for (I64 r=0; r<3; r++) for (I64 c=0; c<4; c++) mat[r][c] = val++;
for (I64 r=0; r<3; r++) {
    for (I64 c=0; c<4; c++) Print("%3lld ", mat[r][c]);
    Print("\n");
}

class Point { I64 x; I64 y; };
Point pts[3];
for (I64 i=0; i<3; i++) { pts[i].x = i*3; pts[i].y = i*7; }
Print("sizeof=%lld bytes, count=%lld\n", sizeof(arr), sizeof(arr)/sizeof(arr[0]));
return 0;
```

### 15.10 Example: String Operations

```c
// ex10_strings.HC
Char *msg = "Hello, HolyC!";
Print("StrLen(\"%s\")=%llu\n", msg, StrLen(msg));
Print("StrCompare: %s\n", StrCompare("abc","abc") ? "TRUE":"FALSE");
I64 num = AtoI("12345");
Print("AtoI= %lld\n", num);
F64 pi = AtoF("3.14159");
Print("AtoF= %.5f\n", pi);
Char buf[256];
I64 n = SPrint(buf, "Answer=%lld", 42);
Print("SPrint: \"%s\" (%lld chars)\n", buf, n);
"This auto-prints!\n";
return 0;
```

### 15.11 Example: Switch Statement

```c
// ex11_switch.HC
I64 mode = 1;
switch (mode) {
    case:  Print("Mode 0\n"); break;
    case:  Print("Mode 1\n"); break;
    case:  Print("Mode 2\n"); break;
    default: Print("Other\n"); break;
}

I64 score = 73;
switch (score) {
    case 90...100: Print("A\n"); break;
    case 80...89:  Print("B\n"); break;
    case 70...79:  Print("C\n"); break;
    case 0...69:   Print("D/F\n"); break;
    default: Print("Invalid\n"); break;
}
return 0;
```

### 15.12 Example: Recursive Functions

```c
// ex12_recursive.HC
I64 Factorial(I64 n) {
    if (n <= 1) return 1;
    return n * Factorial(n - 1);
}

I64 GCD(I64 a, I64 b) {
    if (b == 0) return a;
    return GCD(b, a % b);
}

I64 Fibonacci(I64 n) {
    if (n <= 1) return n;
    return Fibonacci(n-1) + Fibonacci(n-2);
}

Print("Factorial(10)=%lld\n", Factorial(10));
Print("GCD(48,18)=%lld\n", GCD(48, 18));
Print("Fibonacci(10)=%lld\n", Fibonacci(10));
return 0;
```

### 15.13 Example: Function Pointers

```c
// ex13_funptr.HC
I64 Add(I64 a, I64 b) { return a + b; }
I64 Sub(I64 a, I64 b) { return a - b; }
I64 Mul(I64 a, I64 b) { return a * b; }

I64 Compute(I64 a, I64 b, I64 (*op)(I64,I64)) { return op(a,b); }
