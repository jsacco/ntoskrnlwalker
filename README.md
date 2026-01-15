# NTOSKRNL Walker by Juan Sacco <support@exploitpack.com> https://exploitpack.com

Interactive C++ console tool that uses dbghelp and pulls the PDB symbols from Microsoft for ntoskrnl.exe. Resolve kernel offsets, dump struct layouts, and scan the mapped nt image for gadgets (address ? text and text ? address).

## How it works:
- Loads symbols for `C:\Windows\System32\ntoskrnl.exe` from `_NT_SYMBOL_PATH` or download from `srv*C:\symbols*https://msdl.microsoft.com/download/symbols`.
- REPL commands:
  - `nt!KiApcInterrupt` or `KiApcInterrupt` ? resolve symbol RVAs/addresses.
  - `_CLIENT_ID Cid` or any `TYPE field` ? find field offsets across structs.
  - `struct NAME` / `dump NAME` / just `NAME` ? dump struct layout from PDB.
  - Hex RVA/VA (e.g., `0x6360a6`) ? decode bytes in the mapped image as a short gadget.
  - Gadget text (e.g., `pop rcx ; ret`, `jmp rax`) ? scan executable sections for matches.
- Maps `ntoskrnl.exe` as `SEC_IMAGE` to decode gadget bytes locally

## Why this tool?
Basically it saves you time and helps you build gadgets, obtain offsets and navigate the kernel structures for your target build much easier/faster. Typically you will use a workflow like this: vergiliusproject + WinDBG (Target/Debugger) + rp-win(or an alternative) + MS Symbols. So I decided to save me some time in the future.. Sharing is caring, so here you go:

On the following screenshot you see how it resolves the RVA offset for: "nt!ZwTerminateThread"  and obtains gadgets for: "pop ; rcx ret"
<img src="https://i.ibb.co/3yg7P56z/Screenshot-From-2026-01-15-22-32-29.png"></img>

And on this screenshot you see how its showing the structure for _EPROCESS alongside the offsets:
<img src="https://i.ibb.co/39yRwq3c/Screenshot-From-2026-01-15-22-33-11.png"></img>
## Release
- There are already pre-compiled binaries in x64/ folder
- 
## Requirements
- Windows 10/11 `C:\Windows\System32\ntoskrnl.exe` present.
- `dbghelp.dll`/`symsrv.dll` available (the repo root already includes copies used at runtime).
- Network access to the Microsoft symbol server, unless the PDB is already downloaded.

## Building
1) Open `ntoskrnl-walker.sln` in Visual Studio.  
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






