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
