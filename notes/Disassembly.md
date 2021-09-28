# Disassembly, a first principles approach 
**It's all just ones and zeros at the end of the day bro**
The ultimate product of a binary that a user observes are countless short tasks carried out by a "mostly" reliable machine. The machine and it's peripherals await the leading or falling edge of a clock pulse to consume or transfer a number of bits. The transferred bits are either going to be blocked(ignored), stored, interpreted and executed or transferred somewhere else. We're gonna be talking more about what happens in the case where the machine interprets and executes them.

All processors are going to have some kind of [register](Terms.md#^efcb48) called the [Program Counter](Terms.md#^1d7213) that will point the address of some bits in memory which need to be consumed and executed. Instructions can vary in size by a lot depending on which machine they are meant for and what they are supposed to accomplish. The processor will fetch the first part of the instruction called an [opcode](Terms.md#^3efedf). On arrival, to the processor's dye the bits from the opcode will encounter the [decoder](Terms.md#^d102a2) which will prepare the silicon for the rest of the instruction. Depending on what is specified by the opcode, the processor will know how many more bytes will be used in the instruction and how to operate it's (the processor's) [pipeline](Terms.md#^d7b71b) for the rest of the bytes in the current machine instructions. This might take place over several clock cycles in the worst case but it's necessary for the processor to carry out complex tasks and update it's registers (increasing the program counter/ setting flags and shit).     

Human interpretation of machine code has a lot less nuance to it. Pretty much, the manufacturer for a processor will document the different instructions their machine is capable of executing and document them in their code with memorable mnemonics (called _assembly code_) that we can read as well as the side effects on processor state (registers/flags/PC). We see these show up as ```add eax, 5``` for example in assembly code. It's still pretty daunting because of the number of instructions a program of even mild complexity will have but this is a very useful abstraction above raw machine code described earlier.

# Disassemblers
Disassembly is the translation of machine code to the assembly code mentioned earlier. The disassembler starts at the first byte in the file and translates them into something we can kinda sorta make sense out of as humans. Because not everything in the binary file is executable machine code like I talked about [here](ELF.md) the disassembler needs to be aware of the binary format to give you good output. Most importantly, it needs to be aware of which portions are machine code and which portions are data. The disassembler doesn't stop there though, good ones will also use it's knowledge of the _ABI_ (ELF) in the case of Linux to try and make annotations for you about which portions act like functions and other abstractions understood by the _ABI_ and us to say more insightful things about the ones and zeros.

# Disassembly Methods
The two orders of disassembly you'll usually see are _linear sweep_ and _recursive disassembly_. Each comes with pros and cons.

## Linear Disassembly
Linear disassembly will just single step through whatever portion of the binary you tell it to and translate the bytes it runs across as assembly. This can be bad because some compilers or people will put data in a code section of the binary. The problem is compounded when you consider the fact that not all machine instructions are the same size. 

Think about it, if the disassembler runs into some data and interprets the bytes in there as an opcode that says the size of the instruction is a size that doesn't align with what comes after that data then everything after that point will be all fuckered up. This phenomenon in disassembly is called desyncronization.

## Recursive Disassembly
Recursive disassembly is control flow sensitive in it's operation. This means that the disassembler will decode instructions line by line until it encounters a control transfer (a jump or call type instructions). It will follow those control transfers, re-synchronize itself and begin translating bytes again. Once all of the code has been traversed, it's job is done.

This sounds better because now, even if the disassembler become de-syncronized, the problem will only remain until the next control transfer. However where it falls short is when it encounters an _indirect control transfer_. These are when the binary attempts to transfer control using a register or memory location as a vector. It will manifest in your assembly as something like ```call rbx``` and unless the disassembler is somehow able to predict what address will be in ```rbx``` in this case, the control transfer is totally opaque to the disassembler.

Recursive disassembly is the most popular approach implemented by debugging and analysis tools for multiple reasons. Linear disassembly is used by [objdump](Binutils.md#objdump). As a reverse engineer, you just need to be cautious of the fact that the output of your tools can be skewed in these ways and when things look obviously out of place in your disassembly, you need to take steps guide your tools through the correct paths.

# Dynamic Disassembly
Another approach you might take is running the binary on your system and disassembling the machine code as it makes it's way to the processor. This is what you'll get when you step through the code in a debugger like [GDB](GDB.md) or [Radare2](Radare2.md).

You'll get plenty of honesty out of this approach but what is lacking here is coverage. The only portions of the binary you will see are the of code you encounter by running it and this can vary a lot.

# Metadata
**TAGS:** #Binary_Analysis