# ELF Header
## Description
Identifies the file as an ELF binary to the system and gives offsets and sizes to the Program and Section header tables and fixed sizes of section and program headers in the binary expected to be utilized by the linker and loader

### ELF Header Fields
**e_ident** 
This array of bytes specifies to interpret the file,independent of the processor or the file's remaining contents. Within this array everything is named by macros,which start with the prefix **EI_** such as *EI_VERSION* and may contain values which start with the prefix **ELF** like *ELFOSABI_LINUX* full definitions of these can be read from the <a href="https://github.com/torvalds/linux/blob/master/include/uapi/linux/elf.h">elf.h</a> header file and a FULL description of possible values for each field in the <a href="https://man7.org/linux/man-pages/man5/elf.5.html">ELF(5) man page</a> 

**e_type** 

This member of the structure identifies the object file type

**e_machine**

This member specifies the required architecture for an individual file. 

**e_version**

This member identifies the file version
		
**e_entry**

This member gives the virtual address to which the system
first transfers control, thus starting the process.  If
the file has no associated entry point, this member holds
zero.

**e_phoff**

This member holds the program header table's file offset
in bytes.  If the file has no program header table, this
member holds zero.

**e_shoff**

This member holds the section header table's file offset
in bytes.  If the file has no section header table, this
member holds zero.

**e_flags**

This member holds processor-specific flags associated with
the file.  Flag names take the form **EF_** *machine_flag*.
Currently, no flags have been defined.

**e_ehsize**

This member holds the ELF header's size in bytes.

**e_phentsize**

This member holds the size in bytes of one entry in the
file's program header table; all entries are the same
size.

**e_phnum**

This member holds the number of entries in the program
header table.  Thus the product of **e_phentsize** and **e_phnum** gives the table's size in bytes.  If a file has no program header, **e_phnum** holds the value zero.

If the number of entries in the program header table is
larger than or equal to **PN_XNUM** (0xffff), this member
holds **PN_XNUM** (0xffff) and the real number of entries in
the program header table is held in the **sh_info** member of
the initial entry in section header table.  Otherwise, the
**sh_info** member of the initial entry contains the value
zero.

**PN_XNUM**
This is defined as 0xffff, the largest number
**e_phnum** can have, specifying where the actual
number of program headers is assigned.

**e_shentsize**

This member holds a sections header's size in bytes. A
section header is one entry in the section header table;
all entries are the same size.

**e_shnum**

This member holds the number of entries in the section
header table.  Thus the product of **e_shentsize** and **e_shnum**
gives the section header table's size in bytes.  If a file
has no section header table, **e_shnum**
holds the value zero and the real number of entries in the
section header table is held in the **sh_size** member of the
initial entry in section header table.  Otherwise, the
**sh_size** member of the initial entry in the section header
table holds the value zero.

**e_shstrndx**

This member holds the section header table index of the
entry associated with the section name string table.  If
the file has no section name string table, this member
holds the value **SHN_UNDEF**.

If the index of section name string table section is
larger than or equal to **SHN_LORESERVE** (0xff00), this
member holds **SHN_XINDEX** (0xffff) and the real index of the
section name string table section is held in the **sh_link**
member of the initial entry in section header table.
Otherwise, the **sh_link** member of the initial entry in section header table contains the value zero. This member holds the size in bytes of one entry in the file's program header table; all entries are the same
size.

# Section Headers Table
## Description
A file's section header table lets one locate all the file's
sections.  The section header table is an array of _Elf32_Shdr_ or
_Elf64_Shdr_ structures.  The ELF header's _e_shoff_ member gives the
byte offset from the beginning of the file to the section header
table.  _e_shnum_ holds the number of entries the section header
table contains.  _e_shentsize_ holds the size in bytes of each
entry.

A section header table index is a subscript into this array.
Some section header table indices are reserved: the initial entry
and the indices between **SHN_LORESERVE** and **SHN_HIRESERVE**.  The
initial entry is used in ELF extensions for _e_phnum_, _e_shnum_, and
_e_shstrndx_; in other cases, each field in the initial entry is
set to zero.  An object file does not have sections for these
special indices:

### Fields
**SHN_UNDEF**
       This value marks an undefined, missing, irrelevant, or
       otherwise meaningless section reference.

**SHN_LORESERVE**
       This value specifies the lower bound of the range of
       reserved indices.

**SHN_LOPROC**, **SHN_HIPROC**
       Values greater in the inclusive range **SHN_LOPROC**,
       **SHN_HIPROC** are reserved for processor-specific semantics.

**SHN_ABS**
       This value specifies the absolute value for the
       corresponding reference.  For example, a symbol defined
       relative to section number **SHN_ABS** has an absolute value
       and is not affected by relocation.

**SHN_COMMON**
       Symbols defined relative to this section are common
       symbols, such as FORTRAN COMMON or unallocated C external
       variables.

**SHN_HIRESERVE**
       This value specifies the upper bound of the range of
       reserved indices.  The system reserves indices between
       **SHN_LORESERVE** and **SHN_HIRESERVE**, inclusive.  The section
       header table does not contain entries for the reserved
       indices.      **SHN_UNDEF**
       This value marks an undefined, missing, irrelevant, or
       otherwise meaningless section reference.

# Section Headers
## Description
The contents pointed to by the section header table are descriptions for the code or data between the bounds of the section offset and size of the section. This is used by the linker to determine things like permissions and relocations used in the program headers

### Fields
 **sh_name**
        This member specifies the name of the section.  Its value
        is an index into the section header string table section,
        giving the location of a null-terminated string.

 **sh_type**
        This member categorizes the section's contents and
        semantics.
	
**sh_flags**
        Sections support one-bit flags that describe miscellaneous
        attributes.  If a flag bit is set in **sh_flags**, the
        attribute is "on" for the section.  Otherwise, the
        attribute is "off" or does not apply.  Undefined
        attributes are set to zero.
	
 **sh_addr**
        If this section appears in the memory image of a process,
        this member holds the address at which the section's first
        byte should reside.  Otherwise, the member contains zero.

 **sh_offset**
        This member's value holds the byte offset from the
        beginning of the file to the first byte in the section.
        One section type, **SHT_NOBITS**, occupies no space in the
        file, and its **sh_offset** member locates the conceptual
        placement in the file.

 **sh_size**
        This member holds the section's size in bytes.  Unless the
        section type is **SHT_NOBITS**, the section occupies **sh_size**
        bytes in the file.  A section of type **SHT_NOBITS** may have
        a nonzero size, but it occupies no space in the file.

 **sh_link**
        This member holds a section header table index link, whose
        interpretation depends on the section type.

 **sh_info**
        This member holds extra information, whose interpretation
        depends on the section type.

 **sh_addralign**
        Some sections have address alignment constraints.  If a
        section holds a doubleword, the system must ensure
        doubleword alignment for the entire section.  That is, the
        value of **sh_addr** must be congruent to zero, modulo the
        value of **sh_addralign**.  Only zero and positive integral
        powers of two are allowed.  The value 0 or 1 means that
        the section has no alignment constraints.

 **sh_entsize**
        Some sections hold a table of fixed-sized entries, such as
        a symbol table.  For such a section, this member gives the
        size in bytes for each entry.  This member contains zero
        if the section does not hold a table of fixed-size
        entries.
	 
### Section Descriptions
_.bss_   This section holds uninitialized data that contributes to
       the program's memory image.  By definition, the system
       initializes the data with zeros when the program begins to
       run.  This section is of type **SHT_NOBITS**.  The attribute
       types are **SHF_ALLOC** and **SHF_WRITE**.

_.comment_
       This section holds version control information.  This
       section is of type **SHT_PROGBITS**.  No attribute types are
       used.

_.ctors_ This section holds initialized pointers to the C++
       constructor functions.  This section is of type
       **SHT_PROGBITS**.  The attribute types are **SHF_ALLOC** and
       **SHF_WRITE**.

_.data_  This section holds initialized data that contribute to the
       program's memory image.  This section is of type
       **SHT_PROGBITS**.  The attribute types are **SHF_ALLOC** and
       **SHF_WRITE**.

_.data1_ This section holds initialized data that contribute to the
       program's memory image.  This section is of type
       **SHT_PROGBITS**.  The attribute types are **SHF_ALLOC** and
       **SHF_WRITE**.

_.debug_ This section holds information for symbolic debugging.
       The contents are unspecified.  This section is of type
       **SHT_PROGBITS**.  No attribute types are used.

_.dtors_ This section holds initialized pointers to the C++
       destructor functions.  This section is of type
       **SHT_PROGBITS**.  The attribute types are **SHF_ALLOC** and
       **SHF_WRITE**.

_.dynamic_
       This section holds dynamic linking information.  The
       section's attributes will include the **SHF_ALLOC** bit.
       Whether the **SHF_WRITE** bit is set is processor-specific.
       This section is of type **SHT_DYNAMIC**.  See the attributes
       above. ^5969fb

_.dynstr_
       This section holds strings needed for dynamic linking,
       most commonly the strings that represent the names
       associated with symbol table entries.  This section is of
       type **SHT_STRTAB**.  The attribute type used is **SHF_ALLOC**.

_.dynsym_
       This section holds the dynamic linking symbol table.  This
       section is of type **SHT_DYNSYM**.  The attribute used is
       **SHF_ALLOC**.

_.fini_  This section holds executable instructions that contribute
       to the process termination code.  When a program exits
       normally the system arranges to execute the code in this
       section.  This section is of type **SHT_PROGBITS**.  The
       attributes used are **SHF_ALLOC** and **SHF_EXECINSTR**.

_.gnu.version_
       This section holds the version symbol table, an array of
       _ElfN_Half_ elements.  This section is of type
       **SHT_GNU_versym**.  The attribute type used is **SHF_ALLOC**.

_.gnu.version_d_
       This section holds the version symbol definitions, a table
       of _ElfN_Verdef_ structures.  This section is of type
       **SHT_GNU_verdef**.  The attribute type used is **SHF_ALLOC**.

_.gnu.version_r_
       This section holds the version symbol needed elements, a
       table of _ElfN_Verneed_ structures.  This section is of type
       **SHT_GNU_versym**.  The attribute type used is **SHF_ALLOC**.

_.got_   This section holds the global offset table.  This section
       is of type **SHT_PROGBITS**.  The attributes are processor-
       specific. ^e73a22

_.hash_  This section holds a symbol hash table.  This section is
       of type **SHT_HASH**.  The attribute used is **SHF_ALLOC**.

_.init_  This section holds executable instructions that contribute
       to the process initialization code.  When a program starts
       to run the system arranges to execute the code in this
       section before calling the main program entry point.  This
       section is of type **SHT_PROGBITS**.  The attributes used are
       **SHF_ALLOC** and **SHF_EXECINSTR**.

_.interp_
       This section holds the pathname of a program interpreter.
       If the file has a loadable segment that includes the
       section, the section's attributes will include the
       **SHF_ALLOC** bit.  Otherwise, that bit will be off.  This
       section is of type **SHT_PROGBITS**. ^67114e

_.line_  This section holds line number information for symbolic
       debugging, which describes the correspondence between the
       program source and the machine code.  The contents are
       unspecified.  This section is of type **SHT_PROGBITS**.  No
       attribute types are used.

_.note_  This section holds various notes.  This section is of type
       **SHT_NOTE**.  No attribute types are used.

_.note.ABI-tag_
       This section is used to declare the expected run-time ABI
       of the ELF image.  It may include the operating system
       name and its run-time versions.  This section is of type
       **SHT_NOTE**.  The only attribute used is **SHF_ALLOC**.

_.note.gnu.build-id_
       This section is used to hold an ID that uniquely
       identifies the contents of the ELF image.  Different files
       with the same build ID should contain the same executable
       content.  See the **--build-id** option to the GNU linker (**ld**
       (1)) for more details.  This section is of type **SHT_NOTE**.
       The only attribute used is **SHF_ALLOC**.

_.note.GNU-stack_
       This section is used in Linux object files for declaring
       stack attributes.  This section is of type **SHT_PROGBITS**.
       The only attribute used is **SHF_EXECINSTR**.  This indicates
       to the GNU linker that the object file requires an
       executable stack.

_.note.openbsd.ident_
       OpenBSD native executables usually contain this section to
       identify themselves so the kernel can bypass any
       compatibility ELF binary emulation tests when loading the
       file.

_.plt_   
       This section holds the procedure linkage table.  This
       section is of type **SHT_PROGBITS**.  The attributes are
       processor-specific. ^c4117d

_.relNAME_
       This section holds relocation information as described
       below.  If the file has a loadable segment that includes
       relocation, the section's attributes will include the
       **SHF_ALLOC** bit.  Otherwise, the bit will be off.  By
       convention, "NAME" is supplied by the section to which the
       relocations apply.  Thus a relocation section for **.text**
       normally would have the name **.rel.text**.  This section is
       of type **SHT_REL**.

_.relaNAME_
       This section holds relocation information as described
       below.  If the file has a loadable segment that includes
       relocation, the section's attributes will include the
       **SHF_ALLOC** bit.  Otherwise, the bit will be off.  By
       convention, "NAME" is supplied by the section to which the
       relocations apply.  Thus a relocation section for **.text**
       normally would have the name **.rela.text**.  This section is
       of type **SHT_RELA**.

_.rodata_
       This section holds read-only data that typically
       contributes to a nonwritable segment in the process image.
       This section is of type **SHT_PROGBITS**.  The attribute used
       is **SHF_ALLOC**. ^6bb4e2

_.rodata1_
       This section holds read-only data that typically
       contributes to a nonwritable segment in the process image.
       This section is of type **SHT_PROGBITS**.  The attribute used
       is **SHF_ALLOC**.

_.shstrtab_
       This section holds section names.  This section is of type
       **SHT_STRTAB**.  No attribute types are used.

_.strtab_
       This section holds strings, most commonly the strings that
       represent the names associated with symbol table entries.
       If the file has a loadable segment that includes the
       symbol string table, the section's attributes will include
       the **SHF_ALLOC** bit.  Otherwise, the bit will be off.  This
       section is of type **SHT_STRTAB**.

_.symtab_
       This section holds a symbol table.  If the file has a
       loadable segment that includes the symbol table, the
       section's attributes will include the **SHF_ALLOC** bit.
       Otherwise, the bit will be off.  This section is of type
       **SHT_SYMTAB**.

_.text_  
       This section holds the "text", or executable instructions,
       of a program.  This section is of type **SHT_PROGBITS**.  The
       attributes used are **SHF_ALLOC** and **SHF_EXECINSTR**.

# Program Headers
## Description
Program headers describe segments which logically divide regions of memory used by a process once the loader has generated one from the binary file using this information. A program header table pointed to and described the the ELF header contains these program header structures.

### Program Header Fields
**p_type**

This member of the structure indicates what kind of
segment this array element describes or how to interpret
the array element's information.
		
**p_offset**
       This member holds the offset from the beginning of the
       file at which the first byte of the segment resides.

**p_vaddr**
       This member holds the virtual address at which the first
       byte of the segment resides in memory.

**p_paddr**
       On systems for which physical addressing is relevant, this
       member is reserved for the segment's physical address.
       Under BSD this member is not used and must be zero.

**p_filesz**
       This member holds the number of bytes in the file image of
       the segment.  It may be zero.

**p_memsz**
       This member holds the number of bytes in the memory image
       of the segment.  It may be zero.

**p_flags**
              This member holds a bit mask of flags relevant to the
              segment:
		
**p_align**
             This member holds the value to which the segments are
             aligned in memory and in the file.  Loadable process
             segments must have congruent values for **p_vaddr** and
             **p_offset**, modulo the page size.  Values of zero and one
             mean no alignment is required.  Otherwise, **p_align** should
             be a positive, integral power of two, and **p_vaddr** should
             equal **p_offset**, modulo **p_align**.
