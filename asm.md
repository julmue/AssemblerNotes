# X86 Assembler

Notes from [Programming from the Ground Up](http://download-mirror.savannah.gnu.org/releases/pgubook/ProgrammingGroundUp-1-0-booksize.pdf)

## toolchain

GCC set - GNU compiler collection

## basic concepts

linux - the name of the kernel

### what is a kernel?
The kernel is the core part of an operating system that keeps track of everything.
The kernel is both - a fence and a gate.

the kernel as a gate:
allows programs to access hardware in a uniform way.
(otherwise you would have to deal with every device ever made)
* it handles all device specific interactions.
* it handles file access
* it handles interaction between processes
* controls flow of information between programs
  it is a programs gate to the world around it
  every time that data moves between processes, the kernel controls the messaging.

the kernel as a fence:
* prevents programs from overwriting each others data
* prevents programs from accessing files and data they don't have the permission to.
  - it limits the amount of damage a poorly written program can do

### languages

Essentially there are three kinds of languages:
1) Machine Language
2) Assembly language
	This is the same as machine language,
	except the command numbers have been replaced by letter sequences which are easier to memorize (mnemonics).
	other small things are done to make it easier as well.
3) High-level languages
	abstract the machine level


## Computer Architecture

How a computer interprets programs.
Modern computers are based on the Von-Neumann-architecture.

The Von-Neumann-architecture divides computers into two main parts:
1) the CPU (Central Processing Unit)
2) the memory

### Structure of Computer Memory

### The CPU

The CPU reads in instructions from memory one at a time and executes them;
this is kown as "fetch-execute-cycle".

The CPU contains the following elements to accomplish this:
1) Program Counter
2) Instruction Decoder
3) Data bus
4) General-purpose registers
5) Arithmetic and logical Unit (ALU)

#### Program Counter
The program counter is used to tell the computer where to fetch the next instruction from.
There is no difference in the way data and programs are stored,
they are just interpreted differently in the CPU.

The program counter holds the memory address of the next instruction to be executed.
This number is then fetched by the CPU ->
then it is passed to the instrucion decoder

#### instruction decoder
The instruction decoder figures out what the instruction means.
what process:
* addition
* subtraction
* multiplication
* data movement
* ...
what memory locations are involved in that process

#### data bus
Now the computer uses the data bus to fetch the memory locations to be used in the calculation.
The data bus is the connection between the CPU and the memory.

In addition to the memory on the outside of the processor,
the processor itself has some special, high-speed memory locations called registers.

there are two kinds of registers:
1) General registers
2) special-purpose registers

##### General registers
This is where the main action happens:
Addition, Subtracion, multiplication, comparisons, ...

Computers have very few general purpose registers.
Most information is stored in the main memory, 
brought in the registers for processing,
and then put back into memory when the processing is completed.

##### special purpose registers
special purpose registers are registers which have very specific purposes.

#### Arithmetic logical unit
State: 
* the CPU has retrieved all of the data it needs,j
* it has decoded the instrucion
-> now its sent to the arithmetic-logical-unit (ALU) for further processing. 
   here the instrucion is actually executed.

After the results of the computation have been calculated,
the results are then placed on the data bus and sent to the appropriate location in memory or in a register,
as specified in the instruciont.

=> what is really going on under the hood is much more complicated:
there are different cache-levels, pipelining, branch predicition, ...


### some basic terms
Computer memory is a numbered sequence of fixed-size storage locations.
The number attached to each storage location is its address.
The size of each storage location is called a byte
On x86 a byte is a number between 0 and 255 (so 8 bit).
those numbers can be interpreted in a lot of different ways (think Ascii).

THE MEANING OF A NUMBER IS GIVEN BY ITS INTERPRETATION! (-> gödelisierung??)

what about numbers larger than 255?
-> a combination of bytes is used to represent those numbers.
* two bytes: 0 - 65536
* four bytes: 0 - 4294967295

-> it is difficult to glue together larger numbers
-> the computer does this for numbers up to four bytes (this is the default)

-> registers are the numbers being worked on right now
-> on x86 registers are four-bytes long; this is calles a computers word-size.
-> on x86 it is most natural to do computations on values that are 4 bytes long.
-> this gives roughly 4 billion values.
-> addresses are also 4 bytes long and therefore fit into a register.
-> so addresses can be stored as numbers are stored;
   the computer can't tell the difference between 
   a value that is an address or a number or has another purpose.
   (INTERPRETATION)
-> addresses which are stored in memory are called pointers.
   (because they point to some address)
-> computer instructions are also stored in memory;
   they are stored in memory exactly the same way than data is stored;
   the only way the computer knows that a memory location is an instruction is
   that a special purpose register - the instrucion pointer - points to them
   (at one point or another).
   if the instruction pointer points to a memory word it is loaded as an instruction.
-> other than that a computer has no way of knowing the difference 
   between data and an instruction.

### interpreting memory
The computer will execute instructions exactly as specified even if that doesn't make sense.
Therefore as a programmer you have to know how your data is arranged in memory.
Everything you store is just a looong sequence of numbers in memory -- all depends on the interpretation.

### data accessing methods
Processors have a number of different methods of accessing data: addressing modes.
* the simplest mode: immediate mode
   the data to access is embedded in the instruction itself.
   example:
	if we want to initialize a register to 0,
	instead of giving the computer an address to read the 0 from,
	we would specify immediate mode and give the number 0.

* register addressing mode:
	the instruction contains a register to access rather than a memory location.

* direct addressing mode:
	the instruction contains the memory address to access
	example:
		TODO
* indexed addressing mode
	the instruction contains a memory address to access,
	and also specifies an index register to offset that address
	(pointer arithmetic)
	on x86 you can also specify a multiplier for the index.
	(counting starts at 0)

* indirect addressing mode:
	the instrucion contains a register that contains a pointer to where the data should be accessed.
	example:
	TODO

* base pointer addressing mode:
	like indirect addressing but include an offset to add to the registers value.
	(important)

As it boils down: pointer arithmetic!


## chapter 3: your first program

content: 
* linux assembly-language programs.
* structure of assembly-language programs
* (a few) assembly language commands
-> see appendix B and F

=> see example1

### outline of an assembly program

> comments begin with a hash #

> .section .data
anything starting with a dot "." isn't directly translated into a machine instrucion.
instead it's an instruction to the assembler itself.
These are called "assembler directives"/"pseudo-operations"
because they are handled by the assembler and are not actually run by the computer

".section":
	breaks the program up into sections.
* ".section .data" starts the data section,
	where any memory storage is listed that is needed for the data.

* ".section .text" starts the text section
	this is the section of a program where the program instrucions live.

".globl _start" (!important!)
	"_start" is a symbol, 
	which means that it is replaced by something else either during assembly or linking.

Symbols are used to mark locations of programs or data,
so you can refer to them by name instead of by their location number.
Symbols are used so that the assembler and linker can take care of keeping track of addresses.

".globl" means that the assembler shouldn't discard the symbol after assembly,
because the linker will need it.

"_start" is a special symbol that always needs to be marked with ".globl"
because it marks the location of the start of the program.
!! without marking this location in this way, when the computer loads a program 
it won't know where to begin running the program!!

"_start:"
defines the value for the "_start" label.
A label is a symbol follwed by a colon.
Labels define a symbols value.

Labels tell the assembler to make the symbols value be wherever the next instruction of data element will be .
if the actual physical location of the data or instruction changes, you don't have to rewrite any references to it - 
the symbol automatically gets the new value.
	
instructions:
"movl $1, %eax"
When the program runs, this instrucion transgers the number 1 into th %eax register

	




