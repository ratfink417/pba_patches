**Symbols** are human readable representations of code or data that can be more readily be worked with or reasoned about than the code or data itself. For example, you probably wouldn't want to write out and multiply 5x4x3x2x1 every time you wanted to reason about it, you'd just use the number or even better the factorial _symbol_ of that sequence _5!_ ^cf1f87

**Relocations** are annotations found in object code that convey information to a linker or loader about how a symbol is to be handled by it. ^ebed26

**Sections** are contiguous bodies of machine code or symbols that are related by their function and capabilities for utilization by the processor ^d196df

a **Process** is the product of an executable binary that has been brought to a running state by a loader and assigned it's attributes by the operating system running it. ^f7945a

**Shared Objects** are object files kept in a central location on your system that contain code and data designed to be used by any number of programs external to the shared object. They are brought into a process by the run time linker and a technique employed by the process called [Lazy Binding](Lazy%20Binding.md) ^0fe5e4

**Assembly Language** is a system of mnemonics used to represent machine code in a more human readable format. Assembly languages will vary with machine architecture because the language refers to facilities provided by the mechanical design of the processor you are attempting to write machine code for. ^73ed6d

### endianness
**endianness** is the order or sequence of bytes of a word (2 bytes) in memory. Endianness is primarily expressed as **big-endian** (**BE**) or **little-endian** (**LE**). 
- big-endian stores the _most significant byte_ of a word at the _smallest memory address_.  ^bce695
- little-endian stores the _least-significant byte_ of a word at the _smallest address_.
### end endianness
### The Stack
**The Stack** is a region of memory allocated by a process for use when calling all manner of functions. It's needed for storing temporary data that the processor will need readily available but does not have room to keep it in it's registers.

In general the stack operates like a stack of plates; you can _push_ an item (put a plate on top of a stack of plates), which then becomes the top item, or you can _pop_ an item (take a plate off, exposing the previous plate).

The active stack can be changed when a process needs to make a function call by setting the upper and lower bounds of the stack for that function with the processor's stack registers (rsp,rsi and rdi) on x86 architectures.
- rsp being the stack pointer (single piece of data loaded into the stack pointer register) also the current top of the stack.
- rbp being the stack base pointer or the bottom of the current stack frame. Used as a reference for locations of data in the stack.
- rsi being the stack index or the size of bytes to read in from each memory location currently in the stack frame.

The assembly instructions _call_ and _ret_ will changes stack register values as needed to set up the stack frame for a function being called by your process or return to the function that had called the stack function your stack frame is currently in
### end the stack

**Virtual Memory** is allocated to a process by the operating system so that loaded processes can operate as if they had access to the whole of system memory and in a single contiguous chunk. Implementation will vary per operating system and architecture but this is a deep and fascinating subject. ^8b44b5

**libc** or on GNU based platforms _glibc_ is the GNU Project's implementation of the C standard library. Which is a collections of functions and data used for writing C programs that must be available to for a programming environment to be considered a compliant ANSI C platform. Some familiar libraries you'd see in here might be _stdio.h_ or _strings.h_. ^498d06

**Tool Chain** refers to all of the utilities and other shit that goes into turning your source code into an ELF binary. This may include and is certainly not limited to compilers, assemblers, linkers and macro processors ^589b8d

**Register**
A processor register is a quickly accessible location available to a digital processor's central processing unit (CPU). Registers usually consist of a small amount of fast storage, although some registers have specific hardware functions, and may be read-only or write-only. Registers are typically addressed by mechanisms other than main memory, but may in some cases be memory mapped. ^efcb48

**Program Counter**
 The program counter (PC) is a register that indicates where a computer is in its program sequence. Usually, the PC is incremented after fetching an instruction, and holds the memory address of (_points to_) the next instruction that would be executed. Program Counter is a general name for this register, in x86 architectures you will see it referred to as EIP or RIP. ^1d7213

**Decoder**
The Decoder reads an instruction in from memory, and sends the component pieces of that instruction to the necessary destinations. ^d102a2

**Opcode**
 An opcode is the portion of an instruction that specifies the operation to be performed. ^3efedf
 
**Processor Pipeline**
The processor pipeline refers to the arrangement of pathways through the silicon that bits from a machine instruction will need to pass through for execution to complete. This can be a pretty complicated topic but in short some bytes need to go to the ALU (operands in an addition instruction) others need to go to the MMU (in the case of a load/store operation) some can go directly to registers or other special portions of the dye. The pipeline needs to be reconfigured as efficiently as possible for instructions to execute reliably and quickly. ^d7b71b
# Metadata
**TAGS:** #Reference