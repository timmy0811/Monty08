
<img align="left" src="https://github.com/timmy0811/Monty08/blob/Projects/Img/logo.png" width="200" height="200">

# The Monty08 Microprocessor
An 8 Bit Accumulator Processor featuring a Microcode-ALU. First built in LogiSim -> then on Breadboard! The file ```Monty08.xlsx``` features the ISA and all microcode programming. There is also a non-microcode ALU-Version that is more efficient in some aspects and easyier to build.

## How to use
This project uses the latest (but old) version of default Logisim to simulate all functionalities. To open the project yourself: open the ```CPU.circ``` file inside Logisim. -> Future versions of the .circ files will use this fork to add additional components: [Logisim ITA](https://github.com/Logisim-Ita/Logisim)

## What's next?
The logic design and fundamental concepts of the architecture have been implemented and fully tested. The next steps will be creating a rudimental documentation highlichting the ISA and usage. The current ISA is not set in stone and will likely change while transfering the conceptual design into a real electric circuit.

## Features
The Monty08 CPU is based on the Von Neumann - Accumulator Architecture with both code and storage inside the same memory. It offers the following features:
- 16 Bit - Addressable memory
- Operand Registers: Accumulator and B
- General Purpose Registers: GPREG0 and GPREG1
- Conditional Jumps using buffered ALU-Flags: Zero, Equal an Carry (negation of flags included)
- 4 kB of Stack storage
- 12 kB of dynamic Heap storage
- indirect Addressing using Registers and Indexing (Index <= 255) for Stack and Heap
- Dynamic Microcode ALU offering a post-build modification of ALU-Instructions
- 2x 8 Bit Ports for reading and writing data to the user
- CALL and RET Instructions for calling Functions
- 2x non-maskable Hardware-Interrupts
- extern 65kB video memory that can be used by additional video controllers
- dedicated V-Buffer Pointer for fast Frambuffer-writing

## Highlighting Problems
This is my first CPU that I have designed and built on my own. Even though it offers the most important functionalities of a fully scaled CPU like the Core i7 or ATMegaX, the efficiency and speed cannot be compared. The part count of the Monty08 is by no means as low as it could be and the ISA is too complicated in certain aspects. Since the CPU offers just two GP-Registers, it depends on many memory accesses which also can slow down the operation. I have learned a lot during this project and would like to throw the bin and rebuid most of the components but I think is is best to realize the current state and make it run.

This CPU does not support Pipelining! (Maybe a future version will)

## Insights
Abstract Diagram of the Control Unit without further insights
<img src="https://github.com/timmy0811/Monty08/blob/Projects/Img/img.png">
### Main Control Unit State Machine (Just to show the internal documentation)
<img src="https://github.com/timmy0811/Monty08/blob/Projects/Img/img1.png">

## Instruction Set Architecture
The Monty08 is based on one- and two-address-instructions. The Opcode-Field consists of 6 bits and carries additional micro-parameters inside the two extra bits defined as the prefix. More parameters can be chained behind the opcode. Different ways of addressing are split into different instructions:
<p align="center">
  <img src="https://github.com/timmy0811/Monty08/blob/Projects/Img/img3.png" width=600>
</p>
The following table shows the ISA mnemonics:

|  Instruction (Pref0 Pref1) (Op0 Op1) | Opcode | Cycles | Description
| ------------- | ------------- | ------------- | ------------- |
| *Arithmetic and Logic (Microcode-Ver)* |
| ADD | 0x00 | 3 | Accumulator = Accumulator + B |
| ADDC | 0x01 | 3 | Accumulator = Accumulator + B + 1|
| SUB | 0x02 | 3 | Accumulator = Accumulator - B |
| SUBB | 0x03 | 3 | Accumulator = Accumulator - B - 1 |
| INC | 0x04 | 3 | Accumulator = Accumulator + 1 |
| DEC | 0x05 | 3 | Accumulator = Accumulator - 1 |
| AND | 0x06 | 3 | Accumulator = Accumulator & B |
| OR | 0x07 | 3 | Accumulator = Accumulator \| B |
| XOR | 0x08 | 3 | Accumulator = Accumulator ^ B |
| ZERO | 0x09 | 3 | Accumulator = Logic Zero |
| CPL | 0x0A | 3 | Accumulator = Not Accumulator |
| ONE | 0x0B | 3 | Accumulator = Logic One |
| RL | 0x0C | NI | Accumulator = Accumulator << 1 |
| RLC | 0x0D | NI | Accumulator = Accumulator << C << 1 |
| CMP | 0x0E | 3 | Accumulator - B (discards result) |
|  |
| *Arithmetic and Logic (None Microcode-Ver)* |
| ADD 0 0 | 0x09 | 3 | Accumulator = Accumulator + B |
| ADDC 1 0 | 0x09 | 3 | Accumulator = Accumulator + B + 1|
| SUB 1 0 | 0x06 | 3 | Accumulator = Accumulator - B |
| SUBB 0 0 | 0x06 | 3 | Accumulator = Accumulator - B - 1 |
| INC 1 0 | 0x00 | 3 | Accumulator = Accumulator + 1 |
| DEC 0 0| 0x0F | 3 | Accumulator = Accumulator - 1 |
| AND 1 0 | 0x0B | 3 | Accumulator = Accumulator & B |
| OR 0 0 | 0x01 | 3 | Accumulator = Accumulator \| B |
| XOR 1 1 | 0x06 | 3 | Accumulator = Accumulator ^ B |
| ZERO  1 0 | 0x03 | 3 | Accumulator = Logic Zero |
| CPL 1 1 | 0x00 | 3 | Accumulator = Not Accumulator |
| ONE 0 0 | 0x03 | 3 | Accumulator = Logic One |
| RL | 0x0C | NI | Accumulator = Accumulator << 1 |
| RLC | 0x0D | NI | Accumulator = Accumulator << C << 1 |
| CMP 1 1 | 0x03 | 3 | Accumulator - B (discards result) |
|  |
| *Transport* |
| XCHOP | 0x10 | 2 | Accumulator = B; B = Accumulator |
| SWP | 0x39 | NI | Accumulator_LOW = Accumulator_HIGH; Accumulator_HIGH = Accumulator_LOW |
| MODA REG - Operation| 0x11 | 4 | Accumulator = *REG* *Operation* Accumulator |
| MODR 0 - Operation| 0x14 | 6| R0 = R0 *Operation* B |
| XCHAR REG - | 0x15 | NI | Accumulator = *REG*; *REG* = Accumulator |
| XCHRR | 0x16 | NI | R1 = R0; R0 = R1 |
| MOVAR REG -| 0x17 | 2 | Accumulator = *REG* |
| MOVRA REG -| 0x18 | 2 | *REG* = Accumulator |
| LDAD - - DIR0 DIR1 | 0x19 | 4 | Accumulator = @DIR |
| LDRD REG - DIR0 DIR1 | 0x1A | 4 | *REG* = @DIR |
| LDASTI REG - | 0x1B | 4 | Accumulator = [BP + *REG*] |
| LDRSTI REG REGD | 0x1C | 4 | *REGD* = [BP + *REG*] |
| LDAHPI REG - | 0x1D | 4 | Accumulator = [heap_adr + *REG*] |
| LDRHPI REG REGD | 0x1E | 4 | *REGD* = [heap_adr + *REG*] |
| LDAV - - DATA | 0x1F | 2 | Accumulator = *DATA* |
| LDRV REG - DATA | 0x20 | 2 | *REG* = *DATA* |
| STOAD - - DIR0 DIR1 | 0x21 | 4 | @DIR = Accumulator |
| STORD REG - DIR0 DIR1 | 0x22 | 4 | @DIR = *REG* |
| STOASTI REG - | 0x23 | 4 | [BP + *REG*] = Accumulator |
| STORSTI REG REGS | 0x24 | 4 | [BP + *REG*] = *REGS* |
| STOAHPI REG - | 0x25 | 4 | [heap_adr + *REG*] = Accumulator |
| STORHPI REG REGS | 0x26 | 4 | [heap_adr + *REG*] = *REGS* |
|  |
| *Branching and Conditionals* |
| JMP - - ADR0 ADR1 | 0x27 | 3 | PC = Accumulator |
| JMPC NEG 1 ADR0 ADR1 | 0x28 | 3 | PC = ADR + 1, if Carry set (*NEG* inverts cond.) |
| JMPZ NEG 1 ADR0 ADR1 | 0x29 | 3 | PC = ADR + 1, if Zero set (*NEG* inverts cond.) |
| JMPE NEG 1 ADR0 ADR1 | 0x2A | 3 | PC = ADR + 1, if Equal set (*NEG* inverts cond.) |
|  |
| *I/O* |
| PORTWRT PORT - | 0x2C | 2 | *PORT* = Accumulator |
| PORTRD PORT - | 0x2D | 2 | Accumulator = *PORT* |
| PSHVB 0 - | 0x30 | 2 | VBUF[VPTR++] = Accumulator (VPTR increments by one afterwards)|
| LDVPV 0 - DATA0 DATA1 | 0x31 | 3 | VPTR = DATA |
| MOVVPA BYTE - | 0x32 | 2 | VPTR_LOWB = Accumulator if *BYTE* == 0, for *BYTE* == 1 VPTR_HIGHB = Accumulator |
| MOVAVP BYTE - | 0x33 | 2 | Accumulator = VPTR_LOWB if *BYTE* == 0, for *BYTE* == 1 Accumulator = VPTR_HIGHB |
|  |
| *Subroutines* |
| CALL - - ADR0 ADR1 | 0x3A | 6 | Creates new frame and jumps to *ADR* (Does not set the BP) |
| RET | 0x3B | 6 | Destroys frame and recreates old PC-state |
|  |
| *Stack* |
| PUSH | 0x3C | 2 | @SP++ = Accumulator (Pushes Accumulator onto the stack) |
| POP | 0x3D | 3 | Accumulator = @--SP (Pops value from the stack into Accumulator) |
|  |
| *Operation* |
| NOP | 0x3E | 2 | Does nothing |
| END | 0x3F | until reset | Infinite looop |

*NI = Not implemented, *@X = MEM[X]
  
## Memory
<img src="https://github.com/timmy0811/Monty08/blob/Projects/Img/img2.png" width=200>

## Resources
During design I read this book and now want to recommend it to you:
[Structured Computer Organization - Andrew S. Tanenbaum](https://csc-knu.github.io/sys-prog/books/Andrew%20S.%20Tanenbaum%20-%20Structured%20Computer%20Organization.pdf)

Another honorable mention:
[Ben Eater: 8 Bit Computer](https://eater.net/8bit/)
