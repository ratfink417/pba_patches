# Lazy Binding
When an executable file gets loaded into memory by the system's [[Loader]], the resulting [process](Terms.md#^f7945a) will still have some unresolved addresses associated with some of it's [symbols](Terms.md#^cf1f87). This is because most desktop Linux systems are configured not to resolve these symbols until runtime and _only_ when the running process encounters a condition where it _needs_ to make use of that symbol. This technique is called Lazy Binding.

# Lazy Binding Implementation in ELF
Your [ELF](ELF.md) binary will usually declare it's unresolved symbols in a [section](ELF.md#Sections%20and%20Section%20Headers) called [.plt](ELF5.md#^c4117d) _procedure linkage table_ or sometimes .plt.got these sections will consist of a series of ```jmp``` instructions that will direct your [instruction pointer] to the [.got](ELF5.md#^e73a22) _global offset table_ section of your binary followed by a ```push``` instruction meant to place an integer representing an offset into the .got section where the actual address needed by your symbol will reside.

## The Procedure Linkage Table
You can get a look at this section in a binary using a command like this ``` objdump -M intel --section .plt -d hello ``` Examining the readelf output of the .plt section would also show you that it has the flags ```AX``` for _Allocate Memory_ and _Execute_

```output

hello:     file format elf64-x86-64


Disassembly of section .plt:

00000000004003f0 <puts@plt-0x10>:
  4003f0:       ff 35 12 0c 20 00       push   QWORD PTR [rip+0x200c12]        # 601008 <_GLOBAL_OFFSET_TABLE_+0x8>
  4003f6:       ff 25 14 0c 20 00       jmp    QWORD PTR [rip+0x200c14]        # 601010 <_GLOBAL_OFFSET_TABLE_+0x10>
  4003fc:       0f 1f 40 00             nop    DWORD PTR [rax+0x0]

0000000000400400 <puts@plt>:
  400400:       ff 25 12 0c 20 00       jmp    QWORD PTR [rip+0x200c12]        # 601018 <_GLOBAL_OFFSET_TABLE_+0x18>
  400406:       68 00 00 00 00          push   0x0
  40040b:       e9 e0 ff ff ff          jmp    4003f0 <_init+0x28>
```

^dd1f9e

## The Global Offset Table
You can get a look at this section in a binary using a command like this ``` objdump -s --section .got hello ``` Examining the readelf output of this section would also show you that it has the flags ```WA``` for _Write Memory_ and _Allocate Memory_

```output
hello:     file format elf64-x86-64

Contents of section .got.plt:
 601000 280e6000 00000000 00000000 00000000  (.`.............
 601010 00000000 00000000 06044000 00000000  ..........@.....
 601020 16044000 00000000                    ..@.....
```

^28a52a

## Lazy Binding in Action
The Lazy Binding process is initiated when a call to a symbol like the _puts()_ function in our [hello.c](Creation%20of%20Binaries.md#^d34ff1) program calls ```printf("Hello, World!\n");```. You can take a look at this in your binary using ``` objdump -M intel -s main -d hello```

```output
0000000000400526 <main>:
  400526:       55                      push   rbp
  400527:       48 89 e5                mov    rbp,rsp
  40052a:       bf c4 05 40 00          mov    edi,0x4005c4
  40052f:       e8 cc fe ff ff          call   400400 <puts@plt>
  400534:       b8 00 00 00 00          mov    eax,0x0
  400539:       5d                      pop    rbp
  40053a:       c3                      ret
  40053b:       0f 1f 44 00 00          nop    DWORD PTR [rax+rax*1+0x0]
```
this line ```40052f:       e8 cc fe ff ff          call   400400 <puts@plt>``` sends the instruction pointer over to the .plt section, specifically to this line.
```output
0000000000400400 <puts@plt>:
  400400:       ff 25 12 0c 20 00       jmp    QWORD PTR [rip+0x200c12]        # 601018 <_GLOBAL_OFFSET_TABLE_+0x18>
```
The ```jmp``` instruction here reads an address from memory that sits 0x18 bytes past the start of the global offset table. [seen here](Lazy%20Binding.md#^28a52a) on that second line 8 bytes past address ``` 601010 ``` the byte is stored in [little endian](Terms.md###endianness) ```06044000``` which is the same as ``` 400406 ``` and that is just the [next instruction in the .plt](Lazy%20Binding.md#^dd1f9e) table. It's like this for a reason though. Remember the Global Offset Table has _Write Memory_ and _Allocate Memory_ permissions assigned to it so this stub value will eventually get over written by the dynamic linker once the address has been resolved. 

Next, the instruction at ```400406``` pushes ```0x0``` onto the [stack](Terms.md###The%20Stack) jumps you to the portion of the .plt section that calls the linker with these the _puts_ identifier ```0x0``` and the contents of another .got address stored in little endian of course, onto the stack. It says in the output that the content found at ```601008``` will be pushed and is ```280e6000``` or ```600e28``` in big endian. This is an address in the [.dynamic section](ELF5.md#^5969fb) of this binary. This specific entry has the type of _NEEDED_ which signals to the loader that the dynamic linker is needed, meaning that the address jumped to at this point in the binary will be a different address in the running process. It can be followed in a debugger but some serious black magic happens here that returns the address to your shared library [libc.so](Terms.md#^498d06) in this case and places it in the .got at address ``` 601018 ``` which means that any time your process calls puts@plt again, it will be trampolined straight to the address of the _puts_ function shared by libc.so  

I tried to diagram this in vim and it came out like this
```output


                        0000000000400526 <main>:
                           400526:       55                      push   rbp
                           400527:       48 89 e5                mov    rbp,rsp
                           40052a:       bf c4 05 40 00          mov    edi,0x4005c4
     Step 1       -------- 40052f:       e8 cc fe ff ff          call   400400 <puts@plt>
                 |         400534:       b8 00 00 00 00          mov    eax,0x0
                 |         400539:       5d                      pop    rbp
                 |         40053a:       c3                      ret
                 |         40053b:       0f 1f 44 00 00          nop    DWORD PTR [rax+rax*1+0x0]
                 |
                 |       00000000004003f0 <puts@plt-0x10>:
                 | [From 3]4003f0:       ff 35 12 0c 20 00       push   QWORD PTR <<<600e28>>>>  # Signal the loader to use the dynamic linker
                 |                                                                               # and put the resolved address in the .got
                 |         4003f6:       ff 25 14 0c 20 00       jmp    QWORD PTR [rip+0x200c14] # 601010 <_GLOBAL_OFFSET_TABLE_+0x10>
                 |         4003fc:       0f 1f 40 00             nop    DWORD PTR [rax+0x0]
                 |
                 |       0000000000400400 <puts@plt>:
                  -------->400400:       ff 25 12 0c 20 00       jmp    QWORD PTR [rip+0x200c12] # 601018 <_GLOBAL_OFFSET_TABLE_+0x18>
                  __________|Step 2
                  |
                  |   ---->400406:       68 00 00 00 00          push   0x0
                  |  |     40040b:       e9 e0 ff ff ff          jmp    4003f0 <_init+0x28>
                  |  |
                  |  |
                  |  |   Contents of section .got.plt:
                  |  |    601000 280e6000 00000000 00000000 00000000  (.`.............
                  |  -----601010 00000000 00000000 06044000 00000000  ..........@.....
                  |       601020 16044000 00000000  ^                 ..@.....
                  __________________________________|Step 3

                         Contents of section .dynamic:
                         [600e28]01000000 00000000 01000000 00000000  ................
                          600e38 0c000000 00000000 c8034000 00000000  ..........@.....
                          600e48 0d000000 00000000 b4054000 00000000  ..........@.....
                          600e58 19000000 00000000 100e6000 00000000  ..........`.....
                          600e68 1b000000 00000000 08000000 00000000  ................
                          600e78 1a000000 00000000 180e6000 00000000  ..........`.....

```
# Metadata
**TAGS:** #Binary_Analysis 