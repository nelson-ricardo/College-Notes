

# MIPS Assembly Language

```nasm
# a = b + c + d + e
add a, b, c # a = b + c
add a, a, d # a = b + c + d
add a, a, e # a = b + c + d + e
```

Comments are denoted by the _#_ symbol. Each line can only contain one instruction. Each instruction needs a destination variable and two sources

```nasm
# a = b + c // C code to add b + c and store it in variable a
# d = a - e // C code to subtract e from a and store it in d

add a, b, c
sub d, a, e
```

We can have temporary variables like the following code:

```nasm
# f = (g + h) - (i + j)
add t0, g, h # temporary variable t0 = g + h
add t1, i, j # temporary variable t1 = i + j
sub f, t0, t1 # f = t0 - t1
```

**Temporary** variables are useful to keep intermediate return values from operations. **Saved** variables are useful to keep values between function calls

```nasm
# C to MIPS
# f = (g + h) - (i + j);
# f = $s0 g = $s1 h = $s2 i = $s3 j = $s4

add $t0, $s1, $s2 # temp t0 = g + h
add $t1, $s3, $s4 # temp t1 = i + j
sub $s0, $t0, $t1 # f = t0 - t1
```

## Memory Operands

To use data from main memory:

1. Load values from memory into registers
2. Store result from register to memory

- Memory is byte addressed
    - Each address identifies an 8-bit byte
- Words are aligned in memory
    - Address must be a multiple of 4

### Load

Copies data from memory to a register

```nasm
lw destination, constant(register)
```

The “load word” instruction takes the sum of the constant and the register to determine a memory address. The data at this address is placed in the destination register.

**Example:**
```asm6502
# From C to MIPS
# A is an array of 100 words
# g is a variable in $s1
# h is a variable in $s2
# base address of A is in $s3
# g = h + A[8]

lw $t0, 32($s3) # t0 = A[8]
add $s1, $s2, $t0 # g = h + A[8]
```
$s3 is the base address and 32 is the offset (addresses are multiples of 4)
### Store

Copies data from a register to memory

`sw data, constant(register)`
The "store word" instruction takes the sum of the constant and the register to determine a memory address. The data in the first register operand will be placed at this address.

From C to MIPS
- A is an array of 100 words
- h is a variable in $s2
- base address of A is in $s3
`A[12] = h + A[8]`
```
lw $t0, 32($s3) # load A[8] into the register $t0
add $t0, $s2, $t0 # add h and the value stored in $t0 and store it in $t0
sw $t0, 48($s3) # store t0 in A[12]
```

### Constant Operands
We have only been able to load things into a register and then operate in them, instead of being able to use constant values to add to a register
- We load a word
- Add
"Add immediate" instruction allows us to use a constant instead of one of the register operands
`addi = $s0, $s0, 4 # In C this equals to s = s + 4

## Representing Instructions
- Format
	- The layout of an instruction
	- formed by pieces of the instruction called fields

| op     | rs     | rt     | rd     | shamt  | funct  |
| ------ | ------ | ------ | ------ | ------ | ------ |
| 6 bits | 5 bits | 5 bits | 5 bits | 5 bits | 6 bits |
Fields:
1. op: operation code (opcode)
2. rs: first source register number
3. rt: second source register number
4. rd: destination register number
5. shamt: shift amount (00000 for now)
6. funct: function code (extends opcode)

### Instruction Formats
R-Type

| op     | rs     | rt     | rd     | shamt  | funct  |
| ------ | ------ | ------ | ------ | ------ | ------ |
| 6 bits | 5 bits | 5 bits | 5 bits | 5 bits | 6 bits |
I-Type

| op     | rs     | rt     | constant or address |
| ------ | ------ | ------ | ------------------- |
| 6 bits | 5 bits | 5 bits | 16 bits             |
J-Type

| op     | address |     |
| ------ | ------- | --- |
| 6 bits | 26 bits |     |
## Logical Operations
### Shift Operations
Shift left: `sll $t2 $s0, 4 #t2 = s0 << 4`
Uses shamt field.

### AND OR NOT

| Logical Operation | C Operator | MIPS Instruction |
| ----------------- | ---------- | ---------------- |
| Bitwise AND       | &          | and, andi        |
| Bitwise OR        | \|         | or, ori          |
| Bitwise NOT       | ~          | nor              |

## Branches
1. `beq rs, rt, L1` equality between rs and rt. (rs == rt), if true jumps to L1
2. `bne rs, rt, L1` inequality between rs and rt. (rs != rt), if true jumps to L1
3. `j L1` unconditional jump to instruction labeled L1

```c
if(i == j) {
	f = g + h;
} else {
	f = g - h;
}

/*
i is $s0
j is $s1
f is $s2
g is $s3
h is $s4

MIPS CODE
	bne $s0, $s1, Else # if i != j then jump to Else, if not continue
	add $s2, $s3, $s4 # f = g + h
	j Exit
Else: sub $s2, $s3, $s4 # f = g - h
Exit:
*/
```

## Additional Conditional Operators
`slt rd, rs, rt # if (rs < rt) set rd = 1, else rd = 0`
`slti rt, rs, constant # if rs < constant, set rt = 1, else rt = 0`

Used in combination with `beq, bne`
```
slt $t0, $s1, $s2 # if ($s1 < $s2)
bne $t0, $zero, L # branch to L
```

## For Loop
```c
for(int i = 0; i < 10; i++) {
	sum += i;
}

// i is $s1
// sum is $s3

/*
	addi $s1, $zero, 0 # initialize i with 0
Loop: slti $t0, $s1, 10 # if(s1 < 10) t0 = 1, else t0 = 0
	  beq $t0, $zero, Exit # if t0 is 0 then go to Exit
	  add $s3, $s3, $s1 # add s1 to sum
	  addi $s1, $s1, 1 # add 1 to s1
	  j Loop
Exit:
*/
```

## Signed vs Unsigned
- Signed comparison `slt, slti`
- Unsigned comparison `sltu, sltui`

## Switch Statement
```c
switch(selection) {
	case 1:
	// code
	case 2:
	// code
	default:
	// code
}

// this can be translated to
if(selection == 1) {
	// code
} else if(selection == 2) {
	// code
} else {
	// code
}
```

Jump address table
- selection is an index to the table
- the table contains addresses
`jr $s0` - copies $s0 to PC 

## Procedures
A function or method in the high level languages we know
A procedure is a stored subroutine that performs a specific task based on the parameters it is provided
### Steps for Calling a Procedure
1. Place parameters in registers
2. Transfer control to procedure
3. Acquire storage to procedure
4. Perform procedure's operations
5. Place result in register for caller
6. Return to place of call

#### Registers
- `$a0-$a3` four argument registers
- `$v0-$v1` two return value registers
- `$ra` one return address register
#### Procedure Call Instructions
- Procedure call: jump and link
	- `jal ProcedureLabel` address of following instruction put in `$ra`
	- Jumps to target address
- Procedure return: jump register
	- `jr $ra`
	- copies $ra to program counter

#### Procedure
1. The parent program (caller) places parameters in `$a0-$a3`
2. The caller uses `jal` to jump to the location of the function being called (callee) and store the return address
3. The callee completes its task and stores the result in `$v0-$v1`
4. The callee returns control with `jr $ra`

### Stacks
If we need more words as input for our function we can use stacks in main memory

How it works:
1. At the beginning of a procedure the contents of `$s0-$s7` can be saved in main memory
2. The procedure can then use `$s0-$s7` normally
3. At the end, the previous values of `$s0-$s7` are retrieved from main memory

In MIPS, the stack pointer is a register `$sp` that grows from top (highest address) to bottom (lowest address)
- Push: decrement `$sp` - write to main memory (at `$sp`)
- Pop: increment `$sp` - read from main memory (at `$sp`)

#### Example - Leaf Procedure
```c
int leaf_example(int g, int h, int i, int j) {
	int f;
	f = (g + h) - (i + j);
	return f;
}

/*
g - h are stored in $a0-$a3
f in $s0 (hence, need to save $s0 on stack)
Result in $v0
*/

/*
leaf_example:
	addi $sp, $sp, -12
	sw $t1, 8($sp)
	sw $t0, 4($sp)
	sw $s0, 0($sp)
	add $t0, $a0, $a1
	add $t1, $a2, $a3
	sub $s0, $t0, $t1
	add $v0, $s0, $zero
	lw $s0, 0($sp)
	lw $t0, 4($sp)
	lw $t1, 8($sp)
	addi $sp, $sp, 12
	jr $ra
*/

// Another option is not saving and restoring temporary registers
/*
leaf_example:
	addi $sp, $sp, -4
	sw $s0, 0($sp)
	add $t0, $a0, $a1
	add $t1, $a2, $a3
	sub $s0, $t0, $t1
	add $v0, $s0, $zero
	lw $s0, 0($sp)
	addi $sp, $sp, 12
	jr $ra
*/
```

### Procedures Calling Other Procedures (Or Themselves)
For nested calls, called needs to save on the stack
- Its return address
- Any arguments and temporaries needed after the call
- Restore from the stack after the call

```c
int fact(int n) {
	if(n < 1) return 1;
	else return n * fact(n - 1);
}

// argument n in $a0
// result in $v0

/*
fact: 
	addi $sp, $sp, -8        # adjust stack for 2 items
	sw $ra, 4($sp)           # push return address to the stack
	sw $a0, 0($sp)           # push argument to the stack
	slti $t0, $a0, 1         # test for n < 1
	beq $t0, $zero, L1
	addi $v0, $zero, 1       # if so, result is 1
	addi $sp, $sp, 9         #    pop 2 items from stack
	jr $ra                   #    and return
L1:
	addi $a0, $a0, -1        # else decrement n
	jal fact                 # (recursive call)
	lw $a0, 0($sp)           # restore original n
	lw $ra, 4($sp)           #     and return address
	addi $sp, $sp, 8         # pop 2 items from stack
	mult $v0, $a0            # multiply to get result
	mflo $v0                 # copy result into v0
	jr $ra                   # and return
*/
```

## Characters
Characters are represented in 8-bit bytes
- ASCII 128 characters: 95 graphics, 33 control
- Latin-1 256 characters: ASCII characters + 96 more graphic characters
- Unicode
	- Variable length: 8 bits (UTF-8), 16 bits (UTF-16), 32 bits
	- Contains most of the world's alphabets, plus symbols

To load a character:
- Use load word to retrieve the correct 32 bit words
- Use logical instructions to extract the correct byte
MIPS byte instruction:
- load byte
- store byte

### Byte Operations
- `lb rt, offset(rs)` loads a byte from rs+offset, placing it in the rightmost 8 bits of rt
- `sb rt, offset(rs)` stores the rightmost 8 bites of rt in rs+offset

```
lb $t0, 0($sp) # read byte from source
sb $t0, 0($sp) # write byte to destination
```

### Strings
Representing a string:
1. The first position of the string is reserved to give the length of a string (Java)
2. An accompanying variable has the length of the string
3. The last position of a string is indicated by a character used to mark the end of a string (C)

Example:
```c
void strcpy(char x[], char y[]) {
	int i;
	i = 0;
	while((x[i] = y[i]) != '\0') i += 1;
}

// addresses of x, y in $a0 and $a1
// i in $s0

/*
strcpy:
	addi $sp, $sp, -4        # adjust stack for 1 item
	sw $s0, 0($sp)           # save $s0
	add $s0, $zero, $zero    # i = 0
L1:
	add $t1, $s0, $a1        # addr of y[1] in $t1
	lb $t2, 0($t1)           # $t2 = y[1]
	add $t3, $s0, $a0        # addr of x[i] in $t3
	sb $t2, 0($t3)           # x[i] = y[i]
	beq $t2, $zero, L2       # exit loop if y[i] == 0
	addi $s0, $s0, 1         # i = i + 1
	j L1                     # next iteration of the loop
L2:
	lw $s0, 0($sp)           # restore saved $s0
	addi $sp, $sp, 4         # pop 1 item from stack
	jr $ra                   # and return

*/
```

- We can store i in a temporary register to avoid having to save and restore from the stack
- Temp. registers are registers that the callee should use whenever convenient

### Halfword Operations
- load halfword `lh rt, offset(rs)` - loads a halfword from rs+offset, placing it in the rightmost 16 bits of rt
- store halfword `sh rt, offset(rs)` - stores the rightmost 16 bits of rt in rs+offset

## Constants
- Immediate-type instructions have 16 bits for constant values
	- 50% to 60% of constants fit within 8 bits
	- 75% to 80% of constants fit within 16 bits
- The occasional 32-bit constant must be loaded into a register before it can be sured
	- Set the upper half of the register (load upper immediate)
	- Set the lower half of the register (or immediate)

Example:
```
Given: 
0000 0000 0011 1101 0000 1001 0000 0000
lui $s0, 61        # 61 = 0000 0000 0011 1101
                   # $s0 = 0000 0000 0011 1101 0000 0000 0000 0000
ori $s0, $s0, 2304 # 2304 = 0000 1001 0000 0000
                   # $s0 = 0000 0000 0011 1101 0000 1001 0000 0000
```

The MIPS assemble must break larger constants into pieces and reassemble them into a register
- This is why there is one register reserved for the assemble: `$at`

## Addressing Modes
**Addressing Modes** - the term addressing modes refers to the way in which the operand of an instruction is specified. **The addressing mode specifies a rule for interpreting or modifying the address field of the instruction before the operand is actually executed**
- Register Addressing
- Immediate Addressing
- Base Addressing
- PC-Relative Addressing
- Pseudodirect addressing

### Register Addressing
- Destination and source operands are specified by register
	- R-Type Instructions
### Immediate Addressing
- Source operand is specified by an immediate value
	- Arithmetic I-Types like addi, andi, ori, and slti
### Base Addressing
- Source is determined by register + branch address
	- I-types lw, lh, lb (load word, load halfword, load byte)
- Destination is determined by register + branch address
	- I-Types sw, sh, sb (save word, save halfword, save byte)
### PC-relative Addressing
- Destination is determined by PC + Address x 4
	- I-types beq and bne (binary equals/not equals)
- PC (program counter) is the location of our next address
- Address field is 16 bits
- If all addresses had to fit in 16 bits, programs could only be $2^{16}$ btes or 16,382 words long
- Conditional branches tend to branch to nearby instructions
	- Some studies suggest half of all branches go less than 16 instructions away
- If we use PC as the register to be added to the address, our branch range will be $2^{15}$ words in either direction
### Pseudodirect Addressing
- Jump instructions specify a 26 bit address

| 2      | 10000   |
| ------ | ------- |
| 6 bits | 26 bits |
PC = $\text{PC}_{31...28}$: (address x 4)
PC = $\text{PC}_{31...28}$: 40,000
- The 26-bit address field is shifted to the left twice
	- Multiplies by 4 and creates a 28 bit field
	- Four most significant bits are copied from the current Program Counter (PC)

## Decoding Machine Language
- The first 6 bits are the opcode
- R-type 

| op     | rs     | rt     | rd     | shamt  | funct  |
| ------ | ------ | ------ | ------ | ------ | ------ |
| 6 bits | 5 bits | 5 bits | 5 bits | 5 bits | 6 bits |
- I-Type

| op     | rs     | rt     | constant or address |
| ------ | ------ | ------ | ------------------- |
| 6 bits | 5 bits | 5 bits | 15 bits             |
- J-Type

| op     | address |
| ------ | ------- |
| 6 bits | 26 bits |
Based on the opcode:
- Bits 31 to 29 specify the row
- Bits 28 to 26 specify the column

- R-type instructions have an opcode of `000000`
	- Instruction is determined by funct field
	- bits 5-3 specify row
	- bits 2-0 specify column

