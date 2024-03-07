# Mips-Using-One-Atmega32
Is it possible to simulate the mips architecture using an atmega32 ?

- [Mips](#mips)
- [Instruction Set](#instruction_set)
- [Instruction Formats](#instruction_formats)
- [Approach to Implement push-pop](#approach-to-implement-the-push-and-pop-instructions)
- [Tools](#tools)
- [How to run](#how-to-run)
- [Register Selection Bits](#register-map)

## Mips
MIPS (Microprocessor without Interlocked Pipelined Stages) is a family of reduced instruction set computer (RISC) instruction set architectures (ISA) developed by MIPS Computer Systems, now MIPS Technologies.

## Instruction Set

| Decimal (Opcode) | Instruction ID | Category            | Type | Instruction |
|------------------|----------------|---------------------|------|-------------|
| 0 (0000)         | E              | Logic               | R    | and         |
| 1 (0001)         | F              | Logic               | I    | andi        |
| 2 (0010)         | N              | Conditional jump    | I    | beq         |
| 3 (0011)         | P              | Unconditional jump  | J    | j           |
| 4 (0100)         | O              | Conditional jump    | I    | bneq        |
| 5 (0101)         | C              | Arithmetic          | R    | sub         |
| 6 (0110)         | D              | Arithmetic          | I    | subi        |
| 7 (0111)         | B              | Arithmetic          | I    | addi        |
| 8 (1000)         | I              | Logic               | S    | sll         |
| 9 (1001)         | G              | Logic               | R    | or          |
| 10 (1010)        | A              | Arithmetic          | R    | add         |
| 11 (1011)        | H              | Logic               | I    | ori         |
| 12 (1100)        | M              | Memory              | I    | lw          |
| 13 (1101)        | J              | Logic               | S    | srl         |
| 14 (1110)        | K              | Logic               | R    | nor         |
| 15 (1111)        | L              | Memory              | I    | sw          |

## Instruction Formats
Our MIPS Instructions will be 16-bits long with the following 4 formats.

1. R-type Instruction:

| Opcode (4 bits) | Src Reg-1 (4 bits) | Src Reg-2 (4 bits) | Dst Reg (4 bits) |
|-----------------|---------------------|---------------------|-------------------|
|    15-12        |   11-8              |       7-4           |      3-0          |

2. I-type Instruction:

| Opcode (4 bits) | Src Reg-1 (4 bits)  | Src Reg-2/Dst Reg   | Address/Immediate (4 bits) |
|-----------------|---------------------|---------------------|----------------------------|
|    15-12        |   11-8              |       7-4           |      3-0                   |

3. S-type Instruction:

| Opcode (4 bits) | Src Reg-1 (4 bits) | Dst Reg (4 bits) | Shamt (4 bits) |
|-----------------|---------------------|---------------------|---------------------|
|    15-12        |   11-8              |       7-4           |      3-0                   |

4. J-type Instruction:

| Opcode (4 bits) | Target Jump Address (8 bits) | 0 (4bits)|
|-----------------|-------------------------------|-|
|        15-12         |       11-4                        | 3-0 |

## Approach to Implement the push and pop Instructions

We maintained a stack memory in the Data Memory Unit and a stack pointer in the register file. MIPS follows the RISC architecture. So, there were no direct instructions for push and pop. However, we used pseudo instructions to implement push, push offset, and pop instructions as follows:

| Instructions          | Pseudo Instructions          | 
|-----------------------|------------------------------| 
| push \$t1             | sw \$t1, 0(\$sp) <br> subi \$sp, \$sp, 1           | 
| push 3(\$t1)          | lw \$t5, 3(\$t1) <br> sw \$t5, 0(\$sp) <br>subi \$sp, \$sp, 1           | 
| pop \$t1              | addi \$sp, \$sp, 1 <br>lw \$t1, 0(\$sp)             | 

## Tools
1. Atmel Studio 7.0 . Download [link](https://www.microchip.com/en-us/tools-resources/archives/avr-sam-mcus)
2. Proteus Version 8.15. Download [link](https://engineeringsoftware.net/electronics/proteus-8-15-full-crack/).

## How To Run
1. Go to the [compiler](/Codes/) directory and open a .asm file with the desired mips assembly.
2. Run the [asm_to_hex.py](/Codes/asm_to_hex.py).
```
python asm_to_hex.py <input>.asm
```
3. A file named `atmega32_instruction.txt` will be formed. It simulates the instruction memeory of our mips architecture and has the current instruction loaded in it. Copy it.
4. Open the [main.c](/Simulation/Simulation/main.cpp) in atmel studio and navigate to the **unsigned int instruction[256]** array. Paste the instruction as R.H.S of this array.

5. Build the solution and load the new hex file formed into the atmega32.(For proteus simulation, it will be loaded automatically).

## Register map
| $S_{2}$ | $S_{1}$ | $S_{0}$ | Register |
|---------|---------|---------|----------|
|    0     |    0     |    0     |     \$zero    |
|    0     |    0     |    1     |     \$t0     |
|    0     |    1     |    0     |    \$t1      |
|    0     |    1     |    1     |    \$t2      |
|    1     |    0     |    0     |    \$t3      |
|    1     |    0     |    1     |    \$t4      |
|    1     |    1     |    0     |    \$t5*      |
|    1     |    1     |    1     |    \$sp      |
\* reserved for push pop operations

