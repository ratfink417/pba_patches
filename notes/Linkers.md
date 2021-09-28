# Linkers
When compilers or assemblers are given source code, they generate object files which themselves are not executable files but used as an intermediary between the linking phase and the final executable. In general the object files will contain [sections](Terms.md#^d196df), [symbols](Terms.md#^cf1f87) and [relocations](Terms.md#^ebed26)

# Why We Have a Linking Phase
For one, you can break you programming project into multiple files for organization and speed up your build time because your [[Makefile]] will only need to perform compilation and linking of the files you have changed.

This also allows you to build programs that make use of [shared objects](Terms.md#^0fe5e4) without having to update your program EVERY time the library that created that object got updated. In fact you don't really have to know shit about the library besides how it's called in your program's source code.

It also makes it possible to compile the same source code for any architecture you want. The only thing you have to do is customize your linker and link scripts to be able to work with the ISA and address layout/registers of the machine you want to compile for.

# The Run Time Linker
The run time linker, also referred to as the _dynamic linker_ finds and loads shared objects needed by a program to run. It can be run either indirectly by running some dynamically linked program or shared object (in which case no arguments are passed by the user and the parameters used for execution will be gathered from the [.interp section](ELF5.md#^67114e) of the loaded binary) or directly by running:
```
       _/lib/ld-linux.so.*_  [OPTIONS] [PROGRAM [ARGUMENTS]]
```
in your code.

**First**: The loader reads the .interp section of your binary to decide which dynamic linker to map into the process's memory and which parameters to use.

**Second**: The dynamic linker will read dynamic symbols from your running process. These are the same ones you'd see examining the [.dynamic](ELF5.md#^5969fb) section of your ELF file using readelf to determine which shared objects will be needed to be mapped into the process's memory space to keep running.

**Third**: The dynamic linker will be idling until it's needed by your program to resolve a symbol address through the [Lazy Binding](Lazy%20Binding.md) operation 

During Lazy Binding, the process will push 2 pieces of data onto the stack an identifier onto the stack that represents an offset into the [.got](ELF5.md#^e73a22) and an address referenced in the [.plt](ELF5.md#^c4117d) whenever the process calls that specific external symbol.

**Finally**: The dynamic linker will resolve the address using this data and place it at the needed address and idle till it is needed again.

# The Compile Time Linker
The compile time linker **ld** combines a number of object and archive files, relocates their data and ties up symbol references.

**ld** accepts [[Linker Command Language]] files written in a superset to provide explicit and total control over the linking process.

Most of the operation behind this linker has already been covered in this document.
# Metadata
**TAGS:** #Binary_Analysis #Programming 