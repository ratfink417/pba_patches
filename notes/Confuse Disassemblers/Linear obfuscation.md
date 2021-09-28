# Confuse objdump (Linear Disassembler)
To demonstrate the short comings of the linear approach to disassembly discussed [here](Disassembly.md##Linear%20Disassembly), I've written some code that intentionally places some data in a code section of the binary.

Specifically the data will be in the ```main()``` function and the way this happens is, I create a branch of code that will never be executed _dead code_. In that branch, I write some valid assembly which if interpreted as a string would say _"Hello"_. Then in the branch of code that does get executed, I create a char buffer at the address of my assembly and process it with the _printf()_ function to display it on the screen.

```c
#include <stdio.h>
int main(){
int x = 0;
if(x == 1)
 {
	__asm__ __volatile__(
	"rex.W\n\t"
	"gs insb (%dx),%es:(%rdi)\n\t"
	"insb   (%dx),%es:(%rdi)\n\t"
	"outsl  %ds:(%rsi),(%dx)\n\t"
	"add    %al,(%rax)\n\t"
	);
 }
char *buf = (char*)0x00400542;
   printf("%s\n", buf);
   return 0;
}
```
While this confuses objdump into displaying data as code and sufficiently obfuscates the string value from the disassembler, this doesn't cause desynchronization. Even worse, it only works on the system you compiled it on so it's very much just a demonstration but you can improve this to make it more portable. I think all you'd to do is figure out how to assign the pointer to a position relative to it's declaration.

# What doing this looked like
All I did to figure this out was use **Cyberchef** to translate the word "Hello" into hex and then from hex to x86_64 assembly which actually came out in intel syntax where gcc requires it's own syntax.

## First attempt didn't work out
Here, I realized a way less hard way to do this would be to just create a _normal_ binary with the hello string and use [objdump](Binutils.md#objdump) to disassemble all section with the **-D** flag, take a look at [the .rodata section](ELF5.md#^6bb4e2) to find my string as disassembly in valid at&t syntax. It looked like this.

```output
Disassembly of section .rodata:

0000000000002000 <_IO_stdin_used>:
    2000:       01 00                   add    %eax,(%rax)
    2002:       02 00                   add    (%rax),%al
    2004:       48                      rex.W
    2005:       65 6c                   gs insb (%dx),%es:(%rdi)
    2007:       6c                      insb   (%dx),%es:(%rdi)
    2008:       6f                      outsl  %ds:(%rsi),(%dx)
    2009:       00 00                   add    %al,(%rax)
```

From there, I had what I needed to put in my inline assembler statement. This Compiled and now all I needed to do was figure out how to use the code as data.

I knew I would need to create a _pointer to a char_ **char \*buf** and actually thought it would take more to just type in a literal address from the loaded process but it's not you just do a regular cast like you may have done before when trying to convert something like a _char_ to an _int_ it was just **(\*char)0xADDRESS**. Figuring out the address as it showed up on my machine wasn't too difficult either. I just compiled what I had and took the address of the first instruction from my inserted data ```rex.W```.

Since _printf()_ accepts a string pointer, you can use this to print the hidden string.

if you compile this code like this ```gcc -fno-pic``` (disabling position independent code) and you turn off ASLR on your system by running 
```sudo bash -c 'echo 0 > /proc/sys/kernel/randomize_va_space'``` then this method should work for you.

# Keep in mind
While this works great as a learning exercise, it's not very useful on it's own and if you wanted to do this in a more practical way, you wouldn't want to do it in the source code at all. It would make a lot more sense to work with the compiled binary because you could calculate offsets much more reliably.