Bare-Metal STM32G0 Toolchain from Scratch

This project is a complete, professional bare-metal development environment for the STM32G071RB (Nucleo-G0) board, 
built entirely from the ground up without relying on vendor IDEs like Keil or STM32CubeIDE.

This repository demonstrates a deep, low-level understanding of the embedded software build process, from the first line of assembly 
to a fully debuggable C application.

Key Features & Skills Demonstrated

This project isn't just a "blinky" program; it's the engine used to build the program. 
The key components are:
Custom Toolchain Setup: Configured a complete cross-compilation toolchain using ARM GCC, MSYS2, and Make.

Advanced Makefile: A fully-featured Makefile that handles:

Automatic dependency generation (.o from .c).

Multi-stage builds (compile, link).

Separate targets for preprocessing (make preprocess), assembly (make assembly), and disassembly (make disasm).

Easy cleanup (make clean).

C-Based Startup File (startup.c): Replaced the vendor's assembly startup code with a custom, readable C implementation. 
This file correctly sets up the vector table and initializes RAM before calling main().

Custom Linker Script (Linker.ld): A from-scratch linker script that precisely maps all code (.text), data (.data), 
and uninitialized variables (.bss) to the STM32G0's specific FLASH and RAM addresses.

System Call Implementation (syscalls.c): Provided stubs for C library functions (like _sbrk, _write, etc.) to satisfy the linker in a bare-metal environment.

VS Code Integration: A fully configured setup for professional development and debugging:
tasks.json: Provides a seamless build experience (via Ctrl+Shift+B).

launch.json: Enables one-click hardware debugging (F5) using OpenOCD and GDB.

c_cpp_properties.json: Configured for perfect IntelliSense and code navigation.

Hardware Required
Board: Nucleo-G071RB

Toolchain & Setup (How to Use)
To clone and build this project, you will need the following tools installed and available in your system's PATH:
ARM GCC Compiler: (arm-none-eabi-gcc)
MSYS2: To provide make and other build utilities.
OpenOCD: For debugging.
VS Code: With the C/C++ and Cortex-Debug extensions.

Once the environment is set up, you can use the following commands from the terminal:

# Build the final firmware.elf
make

# Clean the project directory
make clean

# Generate a human-readable disassembly of the final program
make disasm

How to Debug
Connect the Nucleo board via USB.
In VS Code, press F5 to start a debug session.