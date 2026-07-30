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
