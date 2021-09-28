# Executable Link Format
ELF is the binary file format understood by Linux and other Unix-Like operating system. A complete ELF file that has it's permission set to executable on your system can be called by it's path on your terminal. This will signal your kernel to set up [virtual memory](Terms.md#^8b44b5), [the stack](Terms.md###The%20Stack) and assign a PID to be used by your process. The kernel will then read the [.interp](ELF5.md#^67114e) section of your elf file to determine the path to the [[Loader]] which will do the rest of the job of getting the process into a running state.

# Construction of an ELF File
A typical ELF file will have 4 basic parts to it

- An Executable Header
- Some Program Headers
- Some Section Headers
- Some Sections

## The Executable Header
The ELF header is a series of bytes in your binary file that are used to identify the file as an ELF binary to the system and convey other useful information such as architecture type and offsets into the binary where other required details are placed.

You can see the actual definition of the ELF header in the source code of you linux system in /usr/include/elf.h the 64bit version of the ELF header definition is shown below

```c
typedef struct
{
  unsigned char e_ident[EI_NIDENT];/* Magic number */
  Elf64_Half	  e_type;		/* Object file type */
  Elf64_Half	  e_machine;	/* Architecture */
  Elf64_Word	  e_version;	/* Object file version */
  Elf64_Addr	  e_entry;		/* Entry point virtual address */
  Elf64_Off       e_phoff;		/* Program header table file offset */
  Elf64_Off	      e_shoff;		/* Section header table file                                           offset */
  Elf64_Word	  e_flags;		/* Processor-specific flags */
  Elf64_Half	  e_ehsize;		/* ELF header size in bytes */
  Elf64_Half	  e_phentsize;	/* Program header table entry size                                  */
  Elf64_Half	  e_phnum;		/* Program header table entry                                         count */
  Elf64_Half	  e_shentsize;	/* Section header table entry size                                  */
  Elf64_Half	  e_shnum;		/* Section header table entry                                         count */
  Elf64_Half	  e_shstrndx;	/* Section header string table                                         index */
} Elf64_Ehdr;
```
While the comments left in the code describe most of these portions of the header pretty well, lets unpack that first one a little bit.
```c
unsigned char e_ident[EI_NIDENT];/* Magic number */
```
This line of code declares an array of unsigned char (unsigned 8 bit value) with 16 pieces of data in it. I know 16 only because that is what the constant EI_NIDENT is defined as.

This array will have 16 bytes of data that I will identify across this table

<table>
<tbody>
  <tr>
    <td>Field</td>
    <td>Name</td>
    <td>Purpose</td>
    <td>Example Values</td>
  </tr>
  <tr>
    <td>1</td>
    <td>EI_MAG0</td>
    <td>File identification</td>
    <td>0x7f (CONSTANT)</td>
  </tr>
  <tr>
    <td>2</td>
    <td>EI_MAG1</td>
    <td>File identification</td>
    <td>'E' (CONSTANT)</td>
  </tr>
  <tr>
    <td>3</td>
    <td>EI_MAG2</td>
    <td>File identification</td>
    <td>'L' (CONSTANT)</td>
  </tr>
  <tr>
    <td>4</td>
    <td>EI_MAG3</td>
    <td>File identification</td>
    <td>'F' (CONSTANT)</td>
  </tr>
  <tr>
    <td>5</td>
    <td>EI_CLASS</td>
    <td>File class</td>
    <td>0/1/2 (invalid/32 bit/64 bit)</td>
  </tr>
  <tr>
    <td>6</td>
    <td>EI_DATA</td>
    <td>Data encoding</td>
    <td>0/1/2(invalid/big/small -endian)</td>
  </tr>
  <tr>
    <td>7</td>
    <td>EI_VERSION</td>
    <td>File version</td>
    <td>ELF Version Number</td>
  </tr>
  <tr>
    <td>8</td>
    <td>EI_OSABI</td>
    <td>Operating system/ABI identification</td>
    <td>0</td>
  </tr>
  <tr>
    <td>9</td>
    <td>EI_ABIVERSION</td>
    <td>ABI Version</td>
    <td>0</td>
  </tr>
  <tr>
    <td>10</td>
    <td>EI_PAD</td>
    <td>Start of padding bytes</td>
    <td>0's till the last byte</td>
  </tr>
  <tr>
    <td>16</td>
    <td>EI_NIDENT</td>
    <td>Size of array</td>
    <td>16</td>
  </tr>
</tbody>
</table>

for a good example of the ELF header content you can use the readelf ``` readelf -h hello ``` command to pull just the executable header and get nice readable output like this
```
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x400430
  Start of program headers:          64 (bytes into file)
  Start of section headers:          6616 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         9
  Size of section headers:           64 (bytes)
  Number of section headers:         31
  Section header string table index: 28

```
Reference the [fields](Binary%20Analysis/Man%20Page%20Snippits/ELF5###ELF%20Header%20Fields) listed in the  man page for more full details on possible values you can expect to find in the elf header.

## Sections and Section Headers

^46c0fd

Your binary file will be logically divided into non-overlapping portions called sections  which are defined by the [section's header](Binary%20Analysis/Man%20Page%20Snippits/ELF5#Section%20Headers). This is a fixed structure found in all ELF binaries which is also defined in.

**/usr/include/elf.h** 
```c
 typedef struct
 {
 Elf64_Word    sh_name;                /* Section name (string tbl index) */
 Elf64_Word    sh_type;                /* Section type */
 Elf64_Xword   sh_flags;               /* Section flags */
 Elf64_Addr    sh_addr;                /* Section virtual addr at execution */
 Elf64_Off     sh_offset;              /* Section file offset */
 Elf64_Xword   sh_size;                /* Section size in bytes */
 Elf64_Word    sh_link;                /* Link to another section */
 Elf64_Word    sh_info;                /* Additional section information */
 Elf64_Xword   sh_addralign;           /* Section alignment */
 Elf64_Xword   sh_entsize;             /* Entry size if section holds table */
 } Elf64_Shdr;
```

You will find section headers in the file's section header table which is a fixed array of these section structures. The location of the table is actually denoted in the ELF header by an offset. It also gave the size fixed size of each section header and the number of these headers the exist in the binary. 

Sections are used by the linker to resolve addresses in the binary
and are not required once the object code has been linked and can even be stripped from it without affecting functionality. They're really just an artifact of the the compilation/linking process.

Some common sections you might encounter are described here.

**.interp** defines the path to the loader that will parse and execute the binary
**.init** contains instructions for the loader to run when loading the binary into a running process 
**.text** hold the executable instructions of a program
**.plt** the procedure linkage table used in combination with the **.got** (global offset table) section to perform [[Lazy Binding]]
**.fini** instruction meant to be carried out by the loader for properly exiting the binary once it has finished running
**.rodata** read only data constants and stuff like that
**.data** initialized data, set variables and stuff like that
**.bss** block started section. has a fixed size and is meant to hold uninitialized data like variables that have not been given a value before compilation
**.shstrtab** table holding string values of all the section names

To get some good, formatted output on the contents of your the section header table for your binary, use a command like this one ``` readelf --sections --wide hello ``` fields in the header are described by the key at the bottom of this output but for all possible values, refer to the <a href="https://man7.org/linux/man-pages/man5/elf.5.html">ELF(5)</a> man page

```output
There are 31 section headers, starting at offset 0x19d8:

Section Headers:
  [Nr] Name              Type            Address          Off    Size   ES Flg Lk Inf Al
  [ 0]                   NULL            0000000000000000 000000 000000 00      0   0  0
  [ 1] .interp           PROGBITS        0000000000400238 000238 00001c 00   A  0   0  1
  [ 2] .note.ABI-tag     NOTE            0000000000400254 000254 000020 00   A  0   0  4
  [ 3] .note.gnu.build-id NOTE            0000000000400274 000274 000024 00   A  0   0  4
  [ 4] .gnu.hash         GNU_HASH        0000000000400298 000298 00001c 00   A  5   0  8
  [ 5] .dynsym           DYNSYM          00000000004002b8 0002b8 000060 18   A  6   1  8
  [ 6] .dynstr           STRTAB          0000000000400318 000318 00003d 00   A  0   0  1
  [ 7] .gnu.version      VERSYM          0000000000400356 000356 000008 02   A  5   0  2
  [ 8] .gnu.version_r    VERNEED         0000000000400360 000360 000020 00   A  6   1  8
  [ 9] .rela.dyn         RELA            0000000000400380 000380 000018 18   A  5   0  8
  [10] .rela.plt         RELA            0000000000400398 000398 000030 18  AI  5  24  8
  [11] .init             PROGBITS        00000000004003c8 0003c8 00001a 00  AX  0   0  4
  [12] .plt              PROGBITS        00000000004003f0 0003f0 000030 10  AX  0   0 16
  [13] .plt.got          PROGBITS        0000000000400420 000420 000008 00  AX  0   0  8
  [14] .text             PROGBITS        0000000000400430 000430 000182 00  AX  0   0 16
  [15] .fini             PROGBITS        00000000004005b4 0005b4 000009 00  AX  0   0  4
  [16] .rodata           PROGBITS        00000000004005c0 0005c0 000012 00   A  0   0  4
  [17] .eh_frame_hdr     PROGBITS        00000000004005d4 0005d4 000034 00   A  0   0  4
  [18] .eh_frame         PROGBITS        0000000000400608 000608 0000f4 00   A  0   0  8
  [19] .init_array       INIT_ARRAY      0000000000600e10 000e10 000008 00  WA  0   0  8
  [20] .fini_array       FINI_ARRAY      0000000000600e18 000e18 000008 00  WA  0   0  8
  [21] .jcr              PROGBITS        0000000000600e20 000e20 000008 00  WA  0   0  8
  [22] .dynamic          DYNAMIC         0000000000600e28 000e28 0001d0 10  WA  6   0  8
  [23] .got              PROGBITS        0000000000600ff8 000ff8 000008 08  WA  0   0  8
  [24] .got.plt          PROGBITS        0000000000601000 001000 000028 08  WA  0   0  8
  [25] .data             PROGBITS        0000000000601028 001028 000010 00  WA  0   0  8
  [26] .bss              NOBITS          0000000000601038 001038 000008 00  WA  0   0  1
  [27] .comment          PROGBITS        0000000000000000 001038 000034 01  MS  0   0  1
  [28] .shstrtab         STRTAB          0000000000000000 0018cc 00010c 00      0   0  1
  [29] .symtab           SYMTAB          0000000000000000 001070 000648 18     30  47  8
  [30] .strtab           STRTAB          0000000000000000 0016b8 000214 00      0   0  1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), l (large)
  I (info), L (link order), G (group), T (TLS), E (exclude), x (unknown)
  O (extra OS processing required) o (OS specific), p (processor specific)

```

There are other important [sections](Binary%20Analysis/Man%20Page%20Snippits/ELF5###Section%20Descriptions) you might encounter during analysis and the best resource is gonna be the <a href="https://man7.org/linux/man-pages/man5/elf.5.html">ELF(5)</a> man page

## Program Headers
Just as the binary has a logical division of sections that don't overlap, a process created by the loader that has read in your binary will have a logical division of the process's memory space called _segments_. You'll see their structure defined in elf.h like this.

```c
 typedef struct
 {
 Elf64_Word    p_type;                 /* Segment type */
 Elf64_Word    p_flags;                /* Segment flags */
 Elf64_Off     p_offset;               /* Segment file offset */
 Elf64_Addr    p_vaddr;                /* Segment virtual address */
 Elf64_Addr    p_paddr;                /* Segment physical address */
 Elf64_Xword   p_filesz;               /* Segment size in file */
 Elf64_Xword   p_memsz;                /* Segment size in memory */
 Elf64_Xword   p_align;                /* Segment alignment */
 } Elf64_Phdr;
```
The [program header fields](Binary%20Analysis/Man%20Page%20Snippits/ELF5###Program%20Header%20Fields) are necessary for creating a running process from a binary and cannot be stripped away without breaking shit because they contain arguments to the system function [mmap] called during loading.

``` readelf --wide --segments hello ``` will give you a human readable version of the contents of your program headers.

```output
Elf file type is EXEC (Executable file)
Entry point 0x400430
There are 9 program headers, starting at offset 64

Program Headers:
  Type           Offset   VirtAddr           PhysAddr           FileSiz  MemSiz   Flg Align
  PHDR           0x000040 0x0000000000400040 0x0000000000400040 0x0001f8 0x0001f8 R E 0x8
  INTERP         0x000238 0x0000000000400238 0x0000000000400238 0x00001c 0x00001c R   0x1
      [Requesting program interpreter: /lib64/ld-linux-x86-64.so.2]
  LOAD           0x000000 0x0000000000400000 0x0000000000400000 0x0006fc 0x0006fc R E 0x200000
  LOAD           0x000e10 0x0000000000600e10 0x0000000000600e10 0x000228 0x000230 RW  0x200000
  DYNAMIC        0x000e28 0x0000000000600e28 0x0000000000600e28 0x0001d0 0x0001d0 RW  0x8
  NOTE           0x000254 0x0000000000400254 0x0000000000400254 0x000044 0x000044 R   0x4
  GNU_EH_FRAME   0x0005d4 0x00000000004005d4 0x00000000004005d4 0x000034 0x000034 R   0x4
  GNU_STACK      0x000000 0x0000000000000000 0x0000000000000000 0x000000 0x000000 RW  0x10
  GNU_RELRO      0x000e10 0x0000000000600e10 0x0000000000600e10 0x0001f0 0x0001f0 R   0x1

 Section to Segment mapping:
  Segment Sections...
   00
   01     .interp
   02     .interp .note.ABI-tag .note.gnu.build-id .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rela.dyn .rela.plt .init .plt .plt.got .text .fini .rodata .eh_frame_hdr .eh_frame
   03     .init_array .fini_array .jcr .dynamic .got .got.plt .data .bss
   04     .dynamic
   05     .note.ABI-tag .note.gnu.build-id
   06     .eh_frame_hdr
   07
   08     .init_array .fini_array .jcr .dynamic .got

```
# Metadata
**TAGS:** #Binary_Analysis 