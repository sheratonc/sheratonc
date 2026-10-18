<p align="center">
  <img src="assets/banner.svg" width="100%" alt="sheraton: a beginner learning Windows and Linux internals and binary analysis, in Rust, C++, C and Python">
</p>

## About

I'm a **beginner** teaching myself systems programming and reverse engineering on **Windows and Linux**. I learn best by building things, so most of my projects are small tools that help me see what happens between source code and a running program: how compilers lay out objects and metadata, how linkers put a binary together, how loaders map it into memory, and how debuggers and unwinders read it back.

Something I've found fascinating so far is the metadata a compiler *has* to leave in a binary, such as RTTI, vtables, unwind tables and import tables. It survives stripping, and it can tell you a surprising amount about how a program is put together.

<p align="center">
  <img src="assets/anatomy.svg" width="100%" alt="The section layout of a Windows PE file and a Linux ELF file, with animated lines from each structure to the tool that reads it: pe-walker and pe-diff read the headers, section table and imports; rtti-dump and vtable-dump read RTTI and vtables in .rdata; pattern-scan scans .text on both; unwind-map reads the program headers, .eh_frame_hdr and .eh_frame">
</p>

### What I'm learning

- **Windows x64 internals:** PE/COFF layout, the MSVC C++ ABI, RTTI and class hierarchies, vtables, imports and exports
- **Linux internals:** ELF, the System V AMD64 ABI, DWARF call frame information and `.eh_frame`, program headers and the dynamic loader
- **Reverse engineering:** reading stripped binaries with Ghidra, GDB, x64dbg and WinDbg, and writing small tools to help along the way
- **Languages:** C++ for my Windows tools, Rust for cross-platform command-line tools, C for the low-level parts, and Python for scripts and tests

### How I'm learning

- **By building:** I learn each file format by writing a tool that reads it, and test it on binaries I've built myself
- **By checking my work:** I compare my results with established tools like `readelf` and `llvm-readobj`, so I find out when I've got something wrong
- **By keeping it small:** each tool does one thing and has no third-party dependencies, so I can understand every line
- **By writing it down:** each README explains the format the tool reads, which helps me check that I really understand it

## Toolbox

The languages and tools I've been learning with so far:

<table>
  <tr>
    <td><b>Languages</b></td>
    <td>
      <img src="https://img.shields.io/badge/Rust-161b22?style=for-the-badge&logo=rust&logoColor=dea584" alt="Rust">
      <img src="https://img.shields.io/badge/C%2B%2B-161b22?style=for-the-badge&logo=cplusplus&logoColor=f34b7d" alt="C++">
      <img src="https://img.shields.io/badge/C-161b22?style=for-the-badge&logo=c&logoColor=a8b9cc" alt="C">
      <img src="https://img.shields.io/badge/Python-161b22?style=for-the-badge&logo=python&logoColor=4f8fd1" alt="Python">
    </td>
  </tr>
  <tr>
    <td><b>Platforms</b></td>
    <td>
      <img src="https://img.shields.io/badge/Windows-161b22?style=for-the-badge&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI%2BPHBhdGggZmlsbD0iIzAwYTRlZiIgZD0iTTAgMGgxMS40djExLjRIMHpNMTIuNiAwSDI0djExLjRIMTIuNnpNMCAxMi42aDExLjRWMjRIMHpNMTIuNiAxMi42SDI0VjI0SDEyLjZ6Ii8%2BPC9zdmc%2B" alt="Windows">
      <img src="https://img.shields.io/badge/Linux-161b22?style=for-the-badge&logo=linux&logoColor=fcc624" alt="Linux">
    </td>
  </tr>
  <tr>
    <td><b>Build</b></td>
    <td>CMake &nbsp;·&nbsp; Cargo &nbsp;·&nbsp; MSVC &nbsp;·&nbsp; MinGW-w64 &nbsp;·&nbsp; GCC &nbsp;·&nbsp; Clang / LLD</td>
  </tr>
  <tr>
    <td><b>Analysis</b></td>
    <td>Ghidra &nbsp;·&nbsp; x64dbg &nbsp;·&nbsp; WinDbg &nbsp;·&nbsp; GDB &nbsp;·&nbsp; readelf / objdump</td>
  </tr>
  <tr>
    <td><b>Formats &amp; ABIs</b></td>
    <td>PE/COFF &nbsp;·&nbsp; ELF &nbsp;·&nbsp; DWARF CFI &nbsp;·&nbsp; MSVC C++ ABI &nbsp;·&nbsp; System V AMD64 &nbsp;·&nbsp; Microsoft x64 calling convention</td>
  </tr>
</table>

## Projects

These are my learning projects: two small toolkits for taking binaries apart, one for each binary format. The PE tools are C++17 with CMake and build on Windows, Linux and macOS; the ELF tool is Rust. They're all MIT-licensed, tested in CI, and have no third-party dependencies.

<table>
  <tr>
    <td colspan="2" valign="top">

### [unwind-map](https://github.com/sheratonc/unwind-map)

My biggest project so far. It works out where functions start and end in **stripped** Linux ELF binaries by reading the `.eh_frame` unwind tables, which `strip` can't remove. It also works when the section headers are gone, flags functions with exception handling, points out code that has no unwind info, can put symbols back with `objcopy`, exports to Ghidra and JSON, and draws the binary as a Hilbert-curve map. I checked it against `llvm-readobj`'s decoder on x86-64, x86 and AArch64 test binaries, and it found every function with unwind info without any false starts.

<p align="center">
  <img src="assets/unwind-map-demo.svg" width="61%" alt="Terminal demo: unwind-map recovers 578 functions from a stripped binary and objcopy puts the symbols back">
  <img src="assets/unwind-map-map.svg" width="36%" alt="Hilbert-curve map of the same stripped binary: every recovered function as a colored trace">
</p>

<sub><code>Linux</code> &nbsp;<code>Rust</code> &nbsp;<code>ELF</code> &nbsp;<code>DWARF CFI</code> &nbsp;<code>x86-64 · x86 · AArch64</code> &nbsp;<code>CI-tested</code></sub>

</td>
  </tr>
  <tr>
    <td width="50%" valign="top">

### [pe-walker](https://github.com/sheratonc/pe-walker)

A command-line inspector I wrote to learn the Portable Executable format. It prints the DOS and NT headers, section layout, imports and exports.

```powershell
pe-walker --summary app.exe
```

<sub><code>Windows</code> &nbsp;<code>C++17</code> &nbsp;<code>PE/COFF</code> &nbsp;<code>x86 / x64</code></sub>

</td>
    <td width="50%" valign="top">

### [pe-diff](https://github.com/sheratonc/pe-diff)

Compares two PE files: headers, sections, imports and exports. It exits non-zero when something changed, so it can be used in a CI pipeline.

```powershell
pe-diff -T old.dll new.dll
```

<sub><code>Windows</code> &nbsp;<code>C++17</code> &nbsp;<code>PE/COFF</code> &nbsp;<code>patch diffing</code></sub>

</td>
  </tr>
  <tr>
    <td width="50%" valign="top">

### [rtti-dump](https://github.com/sheratonc/rtti-dump)

Rebuilds class hierarchies from stripped MSVC x64 binaries by following the RTTI structures: Complete Object Locator → Class Hierarchy Descriptor → Base Class Array.

```powershell
rtti-dump --demangle app.exe
```

<sub><code>Windows</code> &nbsp;<code>C++17</code> &nbsp;<code>MSVC RTTI</code> &nbsp;<code>class recovery</code></sub>

</td>
    <td width="50%" valign="top">

### [vtable-dump](https://github.com/sheratonc/vtable-dump)

A companion to rtti-dump. It finds each class's vtable through its Complete Object Locator back-reference and lists the virtual function slots.

```powershell
vtable-dump -f exception app.exe
```

<sub><code>Windows</code> &nbsp;<code>C++17</code> &nbsp;<code>MSVC ABI</code> &nbsp;<code>vtables</code></sub>

</td>
  </tr>
  <tr>
    <td colspan="2" valign="top">

### [pattern-scan](https://github.com/sheratonc/pattern-scan)

A single-header C++17 library for IDA-style byte signature scanning. It uses `memchr` to find candidates before doing the full compare, which made it about 11× faster than a byte-by-byte scan in my benchmark, and a randomized test checks every result against a simple reference scanner.

```cpp
const auto sig = patscan::parse("48 8B ?? E8 ?? ?? ?? ?? 85 C0");
const auto* hit = patscan::find(data, size, sig);
```

<sub><code>cross-platform</code> &nbsp;<code>C++17</code> &nbsp;<code>header-only</code> &nbsp;<code>signature scanning</code></sub>

</td>
  </tr>
</table>

<br>

<p align="center">
  <sub>I'm still a beginner, so if you spot something wrong or have tips, please open an issue. Every tool here is built and tested against my own test binaries.</sub>
</p>
