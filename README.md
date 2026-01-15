# NTOSKRNL Walker

Interactive C++ console tool that uses `dbghelp`/`symsrv` to pull the matching PDB for `ntoskrnl.exe`, resolve useful kernel offsets, dump struct layouts, and scan the mapped nt image for simple gadgets (address ? text and text ? address) without relying on debuggers or radare2.

## What it does
- Loads symbols for `C:\Windows\System32\ntoskrnl.exe` from `_NT_SYMBOL_PATH` or the built-in default `srv*C:\symbols*https://msdl.microsoft.com/download/symbols`.
- Prints the kernel file version and a list of “useful offsets” (see `kExpectedSymbols` in `main.cpp`).
- REPL commands:
  - `nt!KiApcInterrupt` or `KiApcInterrupt` ? resolve symbol RVAs/addresses.
  - `_CLIENT_ID Cid` or any `TYPE field` ? find field offsets across structs.
  - `struct NAME` / `dump NAME` / just `NAME` ? dump struct layout from PDB.
  - Hex RVA/VA (e.g., `0x6360a6`) ? decode bytes in the mapped image as a short gadget.
  - Gadget text (e.g., `pop rcx ; ret`, `jmp rax`) ? scan executable sections for matches.
- Maps `ntoskrnl.exe` as `SEC_IMAGE` to decode gadget bytes locally (no debugger).

## Requirements
- Windows with `C:\Windows\System32\ntoskrnl.exe` present.
- Visual Studio 2022 with Desktop C++ workload and Windows SDK.
- `dbghelp.dll`/`symsrv.dll` available (the repo root already includes copies used at runtime).
- Network access to the Microsoft symbol server, unless the PDB is already cached.

## Building
1) Open `ntoskrnl-walker.sln` in Visual Studio 2022.  
2) Select `Release` + `x64` and build.  
3) Run from a Developer Command Prompt:
```
<path>\bin\ntoskrnl-walker.exe
```
or
```
msbuild ntoskrnl-walker.sln /p:Configuration=Release /p:Platform=x64 /p:OutDir=bin\
```

## Notes
- Adjust the symbol path via `_NT_SYMBOL_PATH` if you don’t want the default cache/server.
- Gadget scanning is intentionally lightweight; it decodes common short sequences directly from the mapped image and does not invoke any debugger APIs.
