# The Anatomy of Binary Files
The first thing you'll want to know before you can start getting any meaningful information from a binary is how they get created and what standards dictate their construction. This is gonna vary for different processor architectures, operating systems and programming languages at different layers of the final product. Learning just a couple fairly ubiquitous standards/technologies covered in this series of notes is enough to be effective and get some good use out of your analyses... that's the goal anyways.

To get the most out of this guide, you should know a little bit about the C programming language... like maybe just enough to know how to write a simple program and use pointers.

Most of what will be covered in this guide will be [ELF](ELF.md) binaries produced by the GNU C Compiler ( gcc )

## From Source Code to Binary (The C/C++ Process)
Consider a really simple C program like the one below for this section.

_hello.c_ 
```c 
#include <stdio.h>
int main(){
   printf("Hello, world!\n");
   return 0;
}
```

^d34ff1

### Pre-Processing
That first line calls a [CPP](CPP.md) macro. Macros are just specially formatted text that get that are transformed into more complex bodies of text by another program. 

The job of the pre-processor is to translate a specially formatted line of text that it understands into C code in the case of CPP and that is the first step towards generating a binary from source code. In this case the *"#include"* macro was called which CPP will use to read the header file in the angle brackets *stdio.h* and shit it's contents directly where you had placed the macro in your source file. You can even see this yourself by finding stdio.h and running ``` cpp hello.c ``` from your command line.

### Compilation
In the compilation phase, the C source code will be translated into machine code that can be understood by your processor. You can generate the [assembly code](Terms.md#^73ed6d) on the command line to take a look at it with this command ``` gcc -S hello.c -o hello.s ``` This is gonna give you a new *hello.s* text file so that you can see the program you just created translated to asm.

_hello.s_
```gas
        .file   "hello.c"
        .section        .rodata
.LC0:
        .string "test"
        .text
        .globl  main
        .type   main, @function
main:
.LFB0:
        .cfi_startproc
        pushq   %rbp
        .cfi_def_cfa_offset 16
        .cfi_offset 6, -16
        movq    %rsp, %rbp
        .cfi_def_cfa_register 6
        movl    $.LC0, %edi
        call    puts
        movl    $0, %eax
        popq    %rbp
        .cfi_def_cfa 7, 8
        ret
        .cfi_endproc
.LFE0:
        .size   main, .-main
        .ident  "GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.5) 5.4.0 20160609"
        .section        .note.GNU-stack,"",@progbits
```
While this isn't what will be used to create the binary, it shows a portion of what will go into it. The actual file that will be used next is formatted in object code which is essentially raw machine code that has not yet been gone through the next phase, linking. You can generate this code on the command line like this ``` gcc -c hello.c -o hello.o ``` and examine the output file *hello.o* using the objdump command

### Linking
Linking is the final step that goes into creating the binary. This is where [symbols](Terms.md#^cf1f87) referenced in your object code are resolved by a program on your system called [the linker](Linkers.md). This program is essentially filling in the blanks in your object code that reference other programs that are on your system.

While we may not have explicitly referenced anything external to our hello.c file explicitly beside the stdio.h header file. The linker knows to create those references based on your system to have the data it need to be self sufficient in getting loaded into memory by the system's [[Loader]] and turned into a running [process](Terms.md#^f7945a)

You can link the object file you might have just created or pre-process, link and compile all at the same time using this command ``` gcc hello.c -o hello ```

# Metadata
**TAGS:** #Binary_Analysis #Programming #Linkers #Loaders