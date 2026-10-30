# WLP4 Compiler

A full compiler written in C++ for WLP4, a simplified C-like language, targeting ARM64 assembly. It covers the whole pipeline from scanning through code generation to linking and loading, with an emphasis on low-level systems and formal language theory.

## Features

- **Scanner:** DFA-based tokenizer using maximal munch, producing a token stream for the parser
- **Parser:** SLR(1) parser written from scratch, driven by the WLP4 context-free grammar, emitting a preorder parse tree in linear time
- **Semantic Analysis:** Type checker that builds symbol tables and procedure signatures, resolves scopes (including recursive procedures), type-checks all statements and expressions, and outputs a type-annotated parse tree
- **Code Generation:** Walks the annotated tree and emits ARM64 assembly:
  - `wain` and non-`wain` procedures with full call support
  - Stack-based variable storage addressed off a frame pointer (`x29`)
  - Pointers, pointer arithmetic, and pointer comparisons
  - Dynamic memory allocation (`new` / `delete`)
  - Control flow (`if`, `while`) and memory-mapped I/O
- **Linking and Loading:** Output assembled into the ARMCOM module format and run through a linker and relocating loader
- **Written in:** C++ (standard library only, no external dependencies)

## Core Concepts

- Finite automata and maximal munch tokenization
- Context-free grammars and SLR(1) parsing
- Symbol tables, scope tracking, and type checking over a parse tree
- ARM64 instruction-level code generation
- Calling conventions, stack frames, and register management
- Relocation, linking, and loading of object modules

## Pipeline

```
WLP4 source -> wlp4scan -> wlp4parse -> wlp4type -> wlp4gen -> ARM64 assembly
                                                                   |
                                          cs241.linkasm -> cs241.linker -> cs241.arm64emu
```

## Example

**Input (WLP4):**
```
int wain(int a, int b) {
  return a + b;
}
```

**Output (ARM64, illustrative and abridged):**
```
// prologue: set up frame pointer, store parameters a and b on the stack
// load a, push it
// load b into x0
// pop a into x1
add x0, x1, x0     // a + b, result returned in x0
// epilogue: restore frame, return to caller
```

## Usage

```
./wlp4scan < prog.wlp4 | ./wlp4parse | ./wlp4type | ./wlp4gen > prog.asm
```

Sample inputs and expected outputs are included in the repository.

## Note

This compiler was developed as part of CS 241: Foundations of Sequential Programs at the University of Waterloo. Because of academic integrity policies, the source code is not public. If you're interested in the implementation, feel free to reach out at [arav.sharma@uwaterloo.ca](mailto:arav.sharma@uwaterloo.ca).
