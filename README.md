
<img align="left" src="https://github.com/timmy0811/Monty08/blob/Projects/Img/logo.png" width="200" height="200">

# The Monty08 Microprocessor
An 8 Bit Accumulator Processor featuring a Microcode-ALU. First built in LogiSim -> then on Breadboard! The file ```Monty08.xlsx``` features the ISA and all microcode programming.

## How to use
This project uses the latest (but old) version of default Logisim to simulate all functinalities. To open the project yourself: open the ```CPU.circ``` file inside Logisim.

## What's next?
The microprocessor is in early developementand changes periodically so images and more documentation will follow soon...

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

This CPU does not support Pipelining! (Maybe a future version will)

## Insights
Abstract Diagram of the Control Unit without further insights
<img src="https://github.com/timmy0811/Monty08/blob/Projects/Img/img.png">
### Main Control Unit State Machine
<img src="https://github.com/timmy0811/Monty08/blob/Projects/Img/img1.png">

## Memory
<img src="https://github.com/timmy0811/Monty08/blob/Projects/Img/img2.png" width=200>
