# Instruction Set

Instructions are written as semicolon-separated assembly statements:

```asm
LDA 10; CMP 11; JNE 6; OUT; HLT
```

## Encoding

An `opcode` is the operation code inside an instruction. It tells the CPU which operation to run, such as load, store, add, compare, jump, output, or halt.

An `operand` is the value attached to the opcode. In this project, the operand usually means a RAM address.

```text
LDA 10
 |  |
 |  +-- operand: address 10
 +----- opcode: load from RAM into A
```

The base simulator uses one-byte instructions:

```text
bits 7-4: opcode
bits 3-0: operand
```

For example, this assembly statement:

```asm
LDA 10
```

is encoded as:

```text
0001 1010
---- ----
 LDA   10
```

The base simulator supports operands from `0` to `15`. The graphic simulator uses a wider two-byte instruction layout so it can address larger RAM locations such as graphic memory at `224` (`0xE0`).

The opcode values below are shown in the assembly encoding format used by `parse_program()`.

## Opcode Table

| Mnemonic | Opcode | Operand | Supported in | Meaning |
| --- | ---: | --- | --- | --- |
| `NOP` | `0000` | No | Base, Graphic | Do nothing for one execute phase. |
| `LDA` | `0001` | Yes | Base, Graphic | Load RAM at the operand address into the A register. |
| `STA` | `0010` | Yes | Base, Graphic | Store the A register into RAM at the operand address. |
| `ADD` | `0011` | Yes | Base, Graphic | Add RAM at the operand address to A through the ALU, then store the result in A. |
| `SUB` | `0100` | Yes | Base, Graphic | Subtract RAM at the operand address from A through the ALU, then store the result in A. |
| `OUT` | `0101` | No | Base, Graphic | Copy the A register into the output register. |
| `JMP` | `0110` | Yes | Base, Graphic | Set the program counter to the operand address. |
| `HLT` | `0111` | No | Base, Graphic | Stop the CPU simulation. |
| `CMP` | `1000` | Yes | Base, Graphic | Compare A with RAM at the operand address and update flags. |
| `JE` | `1001` | Yes | Base, Graphic | Jump if the zero flag is set. |
| `JNE` | `1010` | Yes | Base, Graphic | Jump if the zero flag is not set. |
| `STG` | `1011` | Yes | Graphic only | Store A into graphic RAM or another RAM address. |

## Instruction Behavior

### LDA

```text
LDA operand
      |
      v
MAR = operand
Bus = RAM[MAR]
A   = Bus
```

### STA

```text
STA operand
      |
      v
MAR = operand
Bus = A
RAM[MAR] = Bus
```

### ADD / SUB

```text
ADD operand
      |
      v
B = RAM[operand]
ALU(A, B, ADD)
A = result
Flags = Carry, Zero, Greater, Less
```

`SUB` follows the same path but selects the subtract operation in the ALU.

### CMP

```text
CMP operand
      |
      v
B = RAM[operand]
ALU compare A and B
Flags = Zero, Greater, Less
```

### JMP / JE / JNE

```text
JNE target
      |
      v
if Zero == 0:
    PC = target
```

`JMP` always changes the program counter. `JE` branches when `Zero == 1`. `JNE` branches when `Zero == 0`.

### OUT

```text
OUT
      |
      v
OUT Register = A Register
```

### STG

`STG` is used by the graphic simulator to write display rows. Each stored byte becomes one row of the 8x8 pixel display.

```text
STG 224
      |
      v
RAM[224] = A
Graphic Display Row 0 = RAM[224]
```

## Base CPU Example

```asm
LDA 10;
CMP 11;
JNE 6;
LDA 12;
OUT;
HLT;
LDA 13;
OUT;
HLT
```

The base simulator preloads:

| Address | Value | Purpose |
| --- | ---: | --- |
| `10` | `5` | First comparison value |
| `11` | `3` | Second comparison value |
| `12` | `8` | Output if values are equal |
| `13` | `9` | Output if values are not equal |

Since `5 != 3`, the program jumps to address `6`, loads `RAM[13]`, and outputs `9`.

## Graphic Display Example

```asm
LDA 40; STG 224;
LDA 41; STG 225;
LDA 42; STG 226;
LDA 43; STG 227;
LDA 44; STG 228;
LDA 43; STG 229;
LDA 42; STG 230;
LDA 41; STG 231;
LDA 40; STG 232;
HLT
```

The graphical simulator preloads RAM addresses `40` through `44` with byte patterns:

| Address | Decimal | Binary |
| --- | ---: | --- |
| `40` | `0` | `00000000` |
| `41` | `24` | `00011000` |
| `42` | `60` | `00111100` |
| `43` | `126` | `01111110` |
| `44` | `255` | `11111111` |

Those values are written into graphic RAM and rendered as pixels:

```text
00000000
00011000
00111100
01111110
11111111
01111110
00111100
00011000
```
