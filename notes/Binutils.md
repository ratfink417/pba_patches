# Binutils
On most \*nix systems, you'll find a set of utilities for working with binary files called **binutils**. They will either come with the system or your package manager will make them readily available. These are staples of binary analysis and debugging. Some of the most commonly used ones are readelf, objdump and nm.

# file
Gives you information about the file you are looking at such as the file type and byte alignment or if it was compressed or not.

```
$ file hello
hello: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=7697276337f7c93e1b2fc5bc5e0cc1efe1866c0d, for GNU/Linux 4.4.0, not stripped
```

# readelf
The **readelf** utility is used to display information about an [ELF](ELF.md) file. Command flags that I have found useful will be covered here to get you started in being effective with this tool but take a look at the [man page](Using%20System%20Documentation.md) 

## Header Data
Reading the ELF header for an executable for a binary can be useful because it can give you insights about the size of the binary _in cases where it has been packed into another file or the size has been obfuscated_. It will also give you the entry point address of the binary.

You can view the elf header with the command
```
readelf -h
```
You'll get output that looks like this
```output
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Position-Independent Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x1040
  Start of program headers:          64 (bytes into file)
  Start of section headers:          14160 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         30
  Section header string table index: 29
```

## Section Data
Knowing the sections available in the binary you are looking at is a good next step in your analysis. To get a listing of the sections in a nice readable format use this command.

```
readelf -S --wide hello
```
**-S** to show sections
**--wide** to display lines longer than 80 chars without wrapping the screen.

You'll get output that looks like this.
```output
There are 30 section headers, starting at offset 0x3750:

Section Headers:
  [Nr] Name              Type            Address          Off    Size   ES Flg Lk Inf Al
  [ 0]                   NULL            0000000000000000 000000 000000 00      0   0  0
  [ 1] .interp           PROGBITS        0000000000000318 000318 00001c 00   A  0   0  1
  [ 2] .note.gnu.property NOTE            0000000000000338 000338 000040 00   A  0   0  8
  [ 3] .note.gnu.build-id NOTE            0000000000000378 000378 000024 00   A  0   0  4
  [ 4] .note.ABI-tag     NOTE            000000000000039c 00039c 000020 00   A  0   0  4
  [ 5] .gnu.hash         GNU_HASH        00000000000003c0 0003c0 00001c 00   A  6   0  8
  [ 6] .dynsym           DYNSYM          00000000000003e0 0003e0 0000a8 18   A  7   1  8
  [ 7] .dynstr           STRTAB          0000000000000488 000488 000082 00   A  0   0  1
  [ 8] .gnu.version      VERSYM          000000000000050a 00050a 00000e 02   A  6   0  2
  [ 9] .gnu.version_r    VERNEED         0000000000000518 000518 000020 00   A  7   1  8
  [10] .rela.dyn         RELA            0000000000000538 000538 0000c0 18   A  6   0  8
  [11] .rela.plt         RELA            00000000000005f8 0005f8 000018 18  AI  6  23  8
  [12] .init             PROGBITS        0000000000001000 001000 00001b 00  AX  0   0  4
  [13] .plt              PROGBITS        0000000000001020 001020 000020 10  AX  0   0 16
  [14] .text             PROGBITS        0000000000001040 001040 000195 00  AX  0   0 16
  [15] .fini             PROGBITS        00000000000011d8 0011d8 00000d 00  AX  0   0  4
  [16] .rodata           PROGBITS        0000000000002000 002000 000012 00   A  0   0  4
  [17] .eh_frame_hdr     PROGBITS        0000000000002014 002014 000034 00   A  0   0  4
  [18] .eh_frame         PROGBITS        0000000000002048 002048 0000d8 00   A  0   0  8
  [19] .init_array       INIT_ARRAY      0000000000003de8 002de8 000008 08  WA  0   0  8
  [20] .fini_array       FINI_ARRAY      0000000000003df0 002df0 000008 08  WA  0   0  8
  [21] .dynamic          DYNAMIC         0000000000003df8 002df8 0001e0 10  WA  7   0  8
  [22] .got              PROGBITS        0000000000003fd8 002fd8 000028 08  WA  0   0  8
  [23] .got.plt          PROGBITS        0000000000004000 003000 000020 08  WA  0   0  8
  [24] .data             PROGBITS        0000000000004020 003020 000010 00  WA  0   0  8
  [25] .bss              NOBITS          0000000000004030 003030 000008 00  WA  0   0  1
  [26] .comment          PROGBITS        0000000000000000 003030 000012 01  MS  0   0  1
  [27] .symtab           SYMTAB          0000000000000000 003048 0003d8 18     28  22  8
  [28] .strtab           STRTAB          0000000000000000 003420 000219 00      0   0  1
  [29] .shstrtab         STRTAB          0000000000000000 003639 000116 00      0   0  1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), I (info),
  L (link order), O (extra OS processing required), G (group), T (TLS),
  C (compressed), x (unknown), o (OS specific), E (exclude),
  D (mbind), l (large), p (processor specific)

```
Armed with this knowledge about the file in question, you can now go take a close look at a section of interest. Any that you are unaware of the purpose for, just reference ``` man 5 ELF``` 

To get a look at one of the sections just, you'll need to use [Binutils](Binutils.md#objdump) against that section.

Check the help page because listing them here would be crazy useless but things you can dump and look at from this command can include
program headers, relocation data, segments and more.

Some other flags you'll want to know are
**-x .section_name** get a hex dmp of a section

# objdump
The **objdump** utility is used to dump object data to the display. It allows you to specify portions of the binary or object file that you would like to examine as well as useful formatting options like if you'd want to show the disassembly and the hex for your dump.

as an example lets dump the [.plt section](ELF5.md#^c4117d) of a binary first just with the default information that objdump gives us.

```output
$ objdump -s --section .plt hello

hello:     file format elf64-x86-64

Contents of section .plt:
 1020 ff35e22f 0000ff25 e42f0000 0f1f4000  .5./...%./....@.
 1030 ff25e22f 00006800 000000e9 e0ffffff  .%./..h.........
```

Since we expect that this section should be utilized for some relocations by the dynamic linker, let's use the **-R** flag to specify that we would like to also see the relocation tags from the file.

```output
$ objdump -s --section .plt -R hello

hello:     file format elf64-x86-64

DYNAMIC RELOCATION RECORDS
OFFSET           TYPE              VALUE
0000000000003de8 R_X86_64_RELATIVE  *ABS*+0x0000000000001130
0000000000003df0 R_X86_64_RELATIVE  *ABS*+0x00000000000010e0
0000000000004028 R_X86_64_RELATIVE  *ABS*+0x0000000000004028
0000000000003fd8 R_X86_64_GLOB_DAT  _ITM_deregisterTMCloneTable@Base
0000000000003fe0 R_X86_64_GLOB_DAT  __libc_start_main@GLIBC_2.2.5
0000000000003fe8 R_X86_64_GLOB_DAT  __gmon_start__@Base
0000000000003ff0 R_X86_64_GLOB_DAT  _ITM_registerTMCloneTable@Base
0000000000003ff8 R_X86_64_GLOB_DAT  __cxa_finalize@GLIBC_2.2.5
0000000000004018 R_X86_64_JUMP_SLOT  puts@GLIBC_2.2.5


Contents of section .plt:
 1020 ff35e22f 0000ff25 e42f0000 0f1f4000  .5./...%./....@.
 1030 ff25e22f 00006800 000000e9 e0ffffff  .%./..h.........
```
some other flags you'll definitely want to be aware of are described in short here.
**-d and -D** show disassembly for a section _-d_ or for ALL section _-D_
**-M intel** specify that the syntax you would like to see for the disasembly is intel as that is more readable. The standared AT&T syntax is used by GNU ASsembler, so it is useful in coming up with portions of inline assembly for your C programs.
**-r** show static relocation tags
**-R** show dynamic relocation tags
**-C** demangles function names. C++ and other languages will mangle function names to support function overloading and will add data to the ends of the functions. This will clear up which function is being referenced in the output

# dd
This command lets you get bytes from a file copied directly to another file. You give it a file to pull from, an offset, count, block size and output and you can have a byte for byte copy of whatever portion of a file you want.

```
$ dd skip=52 count=10296 if=hello of=output bs=1
```
This would jump 0x52 bytes into the file called _hello_, copy 10296 blocks where the block size is 1 byte to a file called output.

It's a pretty handy command, not just for copying iso's to disk.

# nm
The nm command is used to explore symbols in a file. By default, it assumes you want it to display only the static symbols. The **-D** flag can be used to specify dynamic symbols should be loaded. It supports demangling as well.

# xxd
This is the hex dumping utility, use it against a file to get the binary data of a file encoded as hex. Often times you'll want to pipe this to a command like ```head``` or ```less``` to be able to work with the output a little bit.

# ldd
Prints shared object dependencies. This is useful for giving you a listing of libraries that the binary expects to exist on your system before it can be loaded. You could most likely just use readelf and look at the _.dynamic_ section to accomplish the same thing but if you have it at the top of your head, I guess this is a little easier and gives you just this data.

# strace
This command runs the file and displays output for all of the system calls made by the process as it runs. Useful for finding out if files were opened or the network was used for anything or if _mmap_ was called to load libraries or other portions of memory into the runnign process.

Some useful options with this one are
**-i** show the instruction pointer as calls happen


# ltrace
Works similarly to strace but instead of profiling system calls, this tool profiles library calls which is can be really useful when you want to find out which functions of a shared object are being utilized by a running process. Think things like ssh or encryption libraries. 

Some useful options with this one are
**-i** show the instruction pointer as calls happen
**-C** demangle

# strings
List the strings that could be found in a binary file. Sometimes this is super helpful. This isn't a complicated tool and is just listed here for complete coverage I guess.

# Metadata
**TAGS:** #Linux #Binary_Analysis #UsefulCommands 