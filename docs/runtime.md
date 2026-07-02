# Runtime Structure

The simulator runs a small assembly program through a software CPU model and renders the internal state with Pygame.

## Execution Flow

1. The assembly program string is parsed by `parse_program()`.
2. Instructions are encoded into RAM.
3. `CPU(program)` initializes RAM, registers, buses, flags, and D flip-flop state.
4. `real_time_computer_simulation()` starts the Pygame event loop.
5. Each simulated clock cycle alternates between fetch and execute phases.
6. The screen is redrawn at 60 FPS to show the current CPU state.

```text
Assembly Program
      |
      v
parse_program()
      |
      v
RAM -> PC -> MAR -> IR
      |
      v
decode_execute()
      |
      +--> ALU -> A Register -> OUT Register
      |
      +--> RAM read/write
      |
      +--> Flags: Carry, Zero, Greater, Less
```

## Fetch Phase

The fetch phase loads the next instruction from RAM.

| Register | Action |
| --- | --- |
| `PC` | Holds the current instruction address. |
| `MAR` | Receives the address from `PC`. |
| `RAM` | Outputs the instruction at the selected address. |
| `Bus` | Carries the instruction bits. |
| `IR` | Latches the fetched instruction. |
| `PC` | Increments for the next instruction. |

```text
PC -> MAR -> RAM -> Bus -> IR
PC = PC + 1
```

## Execute Phase

The execute phase decodes the current instruction and updates CPU state.

| Instruction family | Main effect |
| --- | --- |
| Load/store | Move values between RAM and registers. |
| Arithmetic | Move RAM into B, run the ALU, latch the result into A. |
| Compare | Run ALU comparison and update flags. |
| Branch | Change the program counter. |
| Output | Copy A into the output register. |
| Halt | Stop the simulation loop. |

## Clock Behavior

The simulator does not update register state continuously. Registers use rising-edge D flip-flop updates:

```text
if clock rises:
    Q = D
```

This makes the simulator behave more like a sequential hardware circuit than a direct high-level emulator.

## Pygame Loop

The Pygame loop performs three jobs:

1. Process keyboard input.
2. Advance the simulated clock and CPU phase.
3. Draw LEDs, RAM state, flags, output display, and status text.

Controls:

- `p`: pause or resume
- `r`: reset CPU state
- `q`: quit
- `+`: slow the clock
- `-`: speed up the clock
- `Up` / `Down`: change memory page in the graphic simulator
