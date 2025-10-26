# Bare-Metal STM32G0 Toolchain

A complete bare-metal development setup for the STM32G071RB (Nucleo-G0) board. No vendor IDEs, no HAL libraries—just ARM GCC, Make, and a text editor.

## Why This Exists

Most STM32 projects start with STM32CubeIDE or Keil, which hide the build process behind layers of abstraction. This project does the opposite: every file is written from scratch to show exactly how embedded software gets built.

**What's included:**
- Custom startup code in C (not assembly)
- Hand-written linker script for memory layout
- Professional Makefile with preprocessing and disassembly targets
- VS Code configuration for debugging with OpenOCD

## Hardware

- **Board:** STM32 Nucleo-G071RB
- **MCU:** STM32G071RB (Cortex-M0+, 128KB Flash, 36KB RAM)
- **Debugger:** ST-LINK/V2-1 (built into the board)

## Requirements

You'll need these tools in your PATH:

- `arm-none-eabi-gcc` - ARM GCC compiler
- `make` - Build automation (use MSYS2 on Windows)
- `openocd` - For flashing and debugging

For VS Code, install the **C/C++** and **Cortex-Debug** extensions.

## Building

```bash
make              # Compile and link everything
make clean        # Remove build artifacts
make disasm       # Generate human-readable disassembly
```

The Makefile also has `preprocess` and `assembly` targets if you want to see what the compiler does at each stage.

## Debugging

Connect the board via USB, open the project in VS Code, and press F5. OpenOCD will flash the firmware and start a debug session. Set breakpoints, step through code, inspect variables—the usual stuff.

## Project Structure

```
main.c          Your application code
startup.c       Vector table and initialization code
syscalls.c      Minimal C library stubs for bare-metal
Linker.ld       Memory map (where code and data go)
Makefile        Build rules
.vscode/        Editor and debugger config
```

## How It Works

### Boot Sequence

```
Power On
   |
   v
[Vector Table @ 0x08000000]
   |
   ├─> Load Stack Pointer
   └─> Jump to Reset_Handler()
          |
          v
   [startup.c: Reset_Handler()]
          |
          ├─> Copy .data from Flash to RAM
          ├─> Zero out .bss in RAM
          └─> Call main()
```

When the STM32 boots, it reads the vector table from the start of flash (0x08000000). The first entry is the initial stack pointer, the second is the reset handler address.

### Memory Layout

```
FLASH (128KB)              RAM (36KB)
┌──────────────────┐       ┌──────────────────┐
│ Vector Table     │ 0x08000000
├──────────────────┤       │                  │
│ .text (code)     │       │                  │
│                  │       ├──────────────────┤
├──────────────────┤       │ .data (init)     │ 0x20000000
│ .rodata (const)  │       ├──────────────────┤
├──────────────────┤       │ .bss (zeroed)    │
│ .data (initial)  │       ├──────────────────┤
└──────────────────┘       │ Heap             │
                            │        ↓         │
                            │        ↑         │
                            │ Stack            │
                            └──────────────────┘ 0x20009000
```

The linker script (`Linker.ld`) tells the linker where to place each section. Code and constants go in flash. Initialized data gets stored in flash but copied to RAM at startup. The BSS section (uninitialized globals) just gets zeroed in RAM.

### Build Process

```
main.c ──┐
         ├─> [Compile] ─> main.o ──┐
startup.c ┤                         ├─> [Link with Linker.ld] ─> firmware.elf
         ├─> [Compile] ─> startup.o ┤
syscalls.c ┘                        │
                                    └─> [objdump -d] ─> firmware.asm
```

The Makefile compiles each `.c` file to a `.o` object file, then links them together using the memory layout defined in `Linker.ld` to produce the final `.elf` executable.

## Customization

To use a different STM32, you'll need to:
1. Update memory sizes in `Linker.ld`
2. Adjust the vector table in `startup.c`
3. Change the MCU flags in the Makefile

The `.vscode/launch.json` file also needs the correct OpenOCD config files for your board.

## References

- [STM32G071 Reference Manual](https://www.st.com/resource/en/reference_manual/rm0444-stm32g0x1-advanced-armbased-32bit-mcus-stmicroelectronics.pdf)
- [GNU Linker Documentation](https://sourceware.org/binutils/docs/ld/)
- [ARM Cortex-M0+ TRM](https://developer.arm.com/documentation/ddi0484/latest/)

---

This project was built to understand embedded toolchains from the ground up. If you're learning embedded systems or want to see how vendor tools work under the hood, this might be useful.
