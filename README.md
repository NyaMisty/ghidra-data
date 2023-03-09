# Ghidra Data Type (GDT)

## What is this?

`.gdt` files are data type archives. They contain data type to symbol correlation
data. When applied to e.g. imported functions, the functions will be assigned the
correct signatures via these files.

## GDT Source

- 

- https://github.com/egore/ghidra-data (Last Check: 20230305)
    - Continuation of https://github.com/0x6d696368/ghidra-data
    - Many OSS library (zlib/libpng/...), Windows Stuffs (ntapi), Graphics Stuffs (D3D/ADL/VFW/OVR)

- https://github.com/kohnakagawa/gdt_archive (Last Check: 20230305)
    - ALPC.gdt winternl64.gdt, winternl32.gdt
    - It contains the data types defined in x86\_64-w64-mingw32/include/winternl.h

- https://github.com/knifeyspoony/ghidra-data (Last Check: 20230305)
    - ph_ntapi_x64_06_2021.gdt

- https://github.com/AllsafeCyberSecurity/Ghidra_Data_Type (Last Check: 20230305)
    - Continuation of https://github.com/0x6d696368/ghidra-data
    - Added WINHTTP APIs to winapi_32.gdt
