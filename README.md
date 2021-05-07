# ELF file building tutorial

Useful links to check out:
- https://en.wikipedia.org/wiki/Executable_and_Linkable_Format
- https://wiki.osdev.org/Executable_and_Linkable_Format
- [this picture](https://wiki.osdev.org/File:Elfdiagram.png)

In this small tutorial we are not going to learn everythning about ELF. Instead we will get an easy-to-use code and undestand some basic features of ELF along the way.
Our main goal is: given ```char *buffer``` of machine code and some meta-information produce minimal ELF file, that could be run without segfault.

# Tools
First thing to meantion are main tools that will guide us throw thicket of ELFs. I suppose, you have already heared something about:
- readelf
- xxd

I am not going to show their usage on executables from primitive .c files, because it will still be much more, than we need. Now let's head to our first hand-made ELF!

# Executable Linkable Format
(not linkable in our case)

ELF is composed of several parts:
```
=================
ELF-header
Programm-header-1
Programm-header-2
...
Data
...
=================
```

Let's examine them one by one, writing code as we proceed.

# ELF-header
```
#include <elf.h> // header containing useful types, constants and info

#define B1 __uint8_t     // 1 byte
#define B2 __uint16_t    // 2 bytes
#define B4 __uint32_t    // 4 bytes
#define B8 __uint64_t    // 8 bytes

struct ELF_Header {
    B4 ei_MAG        = 0x464C457F;           // signature - " ELF"
    B1 ei_CLASS      = ELFCLASS64;           // 64 bit format - in my case
    B1 ei_DATA       = ELFDATA2LSB;          // little endian
    B4 ei_VERSION    = 0x00000001;           // signature
    B2 ei_OSABI      = ELFOSABI_NONE;        // UNIX System V ABI
    B4 ei_OSABIVER   = 0x00000000;           // currently are not used, should be filled with 0s
    B2 E_TYPE        = ET_EXEC;              // executable file
    B2 E_MACHINE     = EM_X86_64;            // x86_64 AMD - in my case
    B4 E_VERSION     = EV_CURRENT;           // signature
    B8 E_ENTRY       = 0x0000000000401000;   // default entry point - changed during run-time in my implementation
    B8 E_PHOFF       = 0x0000000000000040;   // start pf program header table - right after ELF-header -> sizeof(ELF_Header)
    B8 E_SHOFF       = 0x0000000000000000;   // start of the section header table - we will not need it
    B4 E_FLAGS       = 0x00000000;           // interpretation of this field depends on the target architecture @wiki
    B2 E_EHSIZE      = 0x0040;               // size of this header -> sizeof(ELF_Header)
    B2 E_PHENTSIZE   = 0x0038;               // size of program header table -> sizeof(ProgHeader), that is covered later
    B2 E_PHNUM       = 0x0003;               // number of entries in the progtam header file - changed during run-time, but in fact 3
    B2 E_SHENTSIZE   = 0x0000;               // size of the section header table - we will not need it
    B2 E_SHNUM       = 0x0000;               // number of entries in the section header table - we will not need it
    B2 E_SHSTRNDX    = 0x0000;               // section header, that contains section names - we will not need it
};
```

# Programm-Header
```
struct ProgHeader {
    B4 P_TYPE    = 0x00000000 | PT_LOAD;       // This segment will loaded in memory
    B4 P_FLAGS   = 0x00000000;                 // We will need Read, Write, or/and Execute from this
    B8 P_OFFSET  = 0x0000000000000000;         // Offset from the start of the ELF file where the data defined by this header is located
    B8 P_VADDR   = 0x0000000000400000;         // Virtual address where the data will be loaded in run-time
    B8 P_PADDR   = 0x0000000000400000;         // Phsical address where the data will be loaded in run-time - we will not need it
    B8 P_FILESZ  = 0x0000000000000080;         // Physical size of data in ELF file
    B8 P_MEMSZ   = 0x0000000000000080;         // How much virtual memory must be allocated to put the data into
    B8 P_ALIGN   = 0x0000000000000001;         // idk, don't touch it, but allign everyhting by 0x1000, else it can segfault
};
```

# eff_builder.cpp
I'm too lazy to continue, just check implementation, there's nothing hard in it (ha-ha-ha)
