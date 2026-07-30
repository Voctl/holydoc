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
