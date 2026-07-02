# 8-bit Computer

> A software implementation of an 8-bit computer built entirely from digital logic components.

![Python](https://img.shields.io/badge/Python-3.13-blue)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Overview

This project recreates a complete 8-bit computer in software using fundamental digital logic principles. Every component, from logic gates to the CPU, is implemented from scratch without relying on existing CPU emulators.

The goal is to understand how modern computers work by constructing each hardware module step by step.

## Features

- Logic gates
- Combinational circuits
- Sequential circuits
- ALU
- Registers
- Program Counter
- RAM
- Instruction Decoder
- Control Unit
- 8-bit CPU
- Assembly execution

## Architecture

```text
                 +----------------------+
                 |      CPU             |
                 |                      |
                 |  +---------------+   |
Input ---------> |  | Registers     |   |
                 |  +---------------+   |
                 |          |           |
                 |  +---------------+   |
                 |  | ALU           |   |
                 |  +---------------+   |
                 |          |           |
                 |  +---------------+   |
                 |  | Control Unit  |   |
                 |  +---------------+   |
                 +----------|-----------+
                            |
                     Address/Data Bus
                            |
                     +--------------+
                     |     RAM      |
                     +--------------+
```

## Project Structure

```
8bit-computer/
├── logic/
├── cpu/
├── memory/
├── assembler/
├── simulator/
└── examples/
```

## Example

```python
MOV A, 5
MOV B, 10
ADD A, B
OUT A
HLT
```

Output

```
15
```

## Motivation

Instead of using an existing emulator, this project builds an entire computer from the ground up to better understand computer architecture and digital logic.

## Future Work

- Interrupt support
- Memory-mapped I/O
- Pipeline CPU
- Cache simulation
- 16-bit architecture

## References

- Computer Organization and Design
- Digital Design and Computer Architecture
- Ben Eater's 8-bit Computer
