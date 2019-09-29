# Ghidra Data Type

## What is this?

`.gdt` files are data type archives. They contain data type to symbol correlation
data. When applied to e.g. imported functions, the functions will be assigned the
correct signatures via these files.

## How to use?

1. Download the `.gdt` files.
2. Open the `Data Type Manager` in Ghidra
3. `Open File Archive...` and select an appropriate file
4. On the imported file right click and select `Apply Function Data Types`

## How was `winapi_32.gdt` generated?

Because I'm having trouble getting the `CParser` to work correctly. It has some
bugs. I came up with the following procedure:

Because I couldn't get the Windows SDK headers to work I tried the MinGW32 headers
on CentOS 7. But since that also didn't work I did the following:

1. Open `Parse C Source` in Ghidra.

2. Get default `#include` search paths:

```
echo "" | i686-w64-mingw32-gcc -std=c89 -xc -E -v - 2>&1 | sed -ne '/^#include <\.\.\.> search starts here:/,/End of search list./ p' | sed '1d;$d' | sed 's/^ /-I/g'
```

Past this into `Parse Options` of the `Parse C Source` window.

3. Get compiler paths:

```
echo "" | i686-w64-mingw32-gcc -std=c89 -xc -E -v - 2>&1 | grep "COMPILER_PATH=" | tr '=:' '\n' | tail -n+2 | sed 's/^/-I/g'
```

Past this into also `Parse Options` of the `Parse C Source` window.

4. Manually add:

```
-DCONST="const"
-D__restrict__=""
-D__always_inline__="inline"
-D__gnu_inline__="inline"
-D__builtin_va_list="void *"
```

to the `Parse Options`.

5. Get default defines:

```
echo "" | i686-w64-mingw32-gcc -std=c89 -dM -E - | sed 's/#define \([^[:space:]]\+\)[[:space:]]\+\(.*\)$/-D\1="\2"/g'
```

Past this into `Parse Options`.

6. Generate a combined header for the includes you would like to generate a `.gdt` for:

```
cat malware_headers.txt common_headers.txt | while read include; do echo "#include <${include}>"; done | i686-w64-mingw32-gcc -std=c89 -P -E - | sed 's/__asm__ .*);/\/\*__asm__\*\//g' > winapi_32
```

7. As `Source files to parse` set the `winapi` file.

8. Hit `Parse to File...` and save to `winapi_32.gdt`.

## 64-bit

x86_64 works sort of the same. Just use `x86_64-w64-mingw32-gcc` in the above  scripts.
And save to `winapi_64.gdt`.

Commands are:
```
echo "" | x86_64-w64-mingw32-gcc -std=c89 -xc -E -v - 2>&1 | sed -ne '/^#include <\.\.\.> search starts here:/,/End of search list./ p' | sed '1d;$d' | sed 's/^ /-I/g'
echo "" | x86_64-w64-mingw32-gcc -std=c89 -xc -E -v - 2>&1 | grep "COMPILER_PATH=" | tr '=:' '\n' | tail -n+2 | sed 's/^/-I/g'
echo "" | x86_64-w64-mingw32-gcc -std=c89 -dM -E - | sed 's/#define \([^[:space:]]\+\)[[:space:]]\+\(.*\)$/-D\1="\2"/g'
cat malware_headers.txt common_headers.txt | while read include; do echo "#include <${include}>"; done | x86_64-w64-mingw32-gcc -std=c89 -P -E - | sed 's/__asm__ .*);/\/\*__asm__\*\//g' > winapi_64
```

Then delete all the `_mm...` and `_m_...`  inline functions from `winapi_64`.
Additionally delete: `_mulx_u64`

# How was X generated?

## ntddk_32.gdt

```
echo -n "#include <ntddk.h>\n#include <wdm.h>\n#include <ntifs.h>" > ntddk.c
i686-w64-mingw32-gcc -I/usr/i686-w64-mingw32/sys-root/mingw/include/ddk -std=c89 -P -E ntddk.c | sed 's/__asm__ .*);/\/\*__asm__\*\//g' > ntddk_32
```

Then remove all `__inline__` functions and everything that uses inline `__asm__`, i.e.
the `_mm` and `_m_` inline functions, etc., from `ntddk_32` as CParse complains about them. This has to be done by hand :(.

Then use CentOS7_mingw32-4.9.3_ntddk_i686.prf

## ntddk_64.gdt

```
echo -n "#include <ntddk.h>\n#include <wdm.h>\n#include <ntifs.h>" > ntddk.c
x86_64-w64-mingw32-gcc -I/usr/i686-w64-mingw32/sys-root/mingw/include/ddk -std=c89 -P -E ntddk.c | sed 's/__asm__ .*);/\/\*__asm__\*\//g' > ntddk_64
```
Then remove all `__inline__` functions and everything that uses inline `__asm__`, i.e.
the `_mm` and `_m_` inline functions, etc., from `ntddk_32` as CParse complains about them.
Including: `_umul...`, `RtlCheckBit`,  
This has to be done by hand :(.

Then use CentOS7_mingw32-4.9.3_ntddk_x86_64.prf

## ntdiff_..._{Win7_SP1,Win10_1903_19H1}_{x86,x64}

```
git clone https://github.com/ntdiff/headers
```

Then select your appropriate `ALL.h` files.

Unfortunately, anything later than `Win10_1903_19H1` won't parse.

# How to contribute?

Generate `.gdt` files for other common libraries.

# TODO

- Now that Ghidra can parse GNU asm (see https://github.com/NationalSecurityAgency/ghidra/pull/1082) do a proper regeneration of the .gdt files


