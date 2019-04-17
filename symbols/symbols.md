# Export Symbol Files

Files containing the export symbol information (mainly ordinal number to function name mappings)
of imported files.

## How to use?

Linux and MacOS user can copy the symbols directory into their `~/.ghidra/.ghidra-9.0.2/`
folder.

If Ghidra is run in Windows there is no need to use the provided files as Ghidra
can dynamically get the information from the files on the system itself.

## How to contribute?

The current files were created by `Batch Import`ing all files that Ghidra can import (`*.exe`, `*.dll`, `*.cpl`, `*.drv`, etc.)
from the

- `C:\Windows\SysWOW64`
- `C:\Windows\System32`
- `C:\Windows\System32\wbem`
- `C:\openjdk-11.0.2_windows-x64\jdk-11.0.2\bin\`

directories on a Microsoft Windows Version 6.1.7601.17514 (win7sp1_rtm.101119-1850).
Just by importing the DLLs the `~/.ghidra/.ghidra-9.0.2/symbols` folder is populated.
No need to run analysis,

If you have a set of commonly imported DLLs, such as commonly used APIs, you can
do the same to those DLLs and share the resulting `.exports` files.



