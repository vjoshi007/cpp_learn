# C++ Learning Project - AI Agent Instructions

## Project Overview
This is a C++ learning codebase organized by chapters. Each chapter contains simple programs demonstrating basic C++ concepts.

## Architecture
- **Structure**: Root contains chapter directories (e.g., `ch_1/`)
- **Per Chapter**: `main.cpp` source file, compiled `main` executable, and debug symbols (`main.dSYM/` on macOS)
- **No complex components**: Single-file programs with minimal dependencies

## Build & Run Workflow
- **Compile**: `g++ main.cpp -o main` (run in chapter directory)
- **Execute**: `./main`
- **Debug**: `lldb ./main` (macOS debugger)
- **Clean**: Remove `main` and `main.dSYM/` manually

## Code Patterns
- **Entry Point**: Standard `int main()` function
- **Output**: Uses `std::cout` with `'\n'` instead of `std::endl`
- **Includes**: Minimal, only `<iostream>` for basic I/O
- **Variables**: Simple declarations and assignments

## Key Files
- `ch_1/main.cpp`: Example basic program with variable assignment and output
- `.gitignore`: Excludes build artifacts, IDE files, and test directories

## Development Notes
- No automated build system (Makefile, CMake)
- No testing framework
- Focus on fundamental C++ syntax and concepts
- Each chapter is self-contained