# Intro

This is an attempt to extract original firmware from Presonus Faderport Classic v1.3.8 updater and run it on a custom bootloaded MCU. This attempt can allow you to repair the unit by yourself. The goal is to preserve device functionality after MCU replacement.

I've bought a used unit on a local store. After adding it to the Studio One, I've discovered that motorized fader doesn't work. The unit has an initial firmware `1.00` and I thought that it can be an issue. So I've downloaded the official updater for Mac OS, named `FaderPort Updater 1.3.8` from my personal product page. The attempt to update the unit with the tool has failed. Support from the company advised me to contact a local dealer to repair the unit. This is not an option for me, so I dig into it.

## Hardware info

The most intersting components for us are

* MCU is [ATMEGA32 16AU](docs/Atmega32.pdf)
* USB phy [PDIUSBD12](docs/PDIUSBD12PWTM.pdf)
* Motor driver [TA7291S](docs/TA7291S.pdf)
* Motorized `ALPS` fader

## Analysis

```
$ cd original-image/FaderPort\ Updater\ 1.3.8.app/Contents/MacOS/
$ symbols FaderPort\ Updater\ 1.3.8

FaderPort Updater 1.3.8 [i386, 0.001380 seconds]:
    E2EE6598-4C92-3CE9-A809-7EA5BC0C0D39 /Users/romansavrulin/projects/DjFix/presonus-faderport-classic-firmware/original-image/FaderPort Updater 1.3...
        0x00000000 (  0x1000) __PAGEZERO SEGMENT
        0x00001000 (  0x8000) __TEXT SEGMENT
            0x00001000 (  0x1740) MACH_HEADER
            0x00002740 (  0x3745) __TEXT __text
                0x00002740 (    0x40) start [FUNC, EXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00002780 (    0x24) dyld_stub_binding_helper [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x000027a4 (     0xe) _dyld_func_lookup [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x000027b2 (     0xa) AtmelX::AtmelX() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                
                ...
                
                0x000029ea (    0x80) AtmelX::ConfirmSysEx(bool) [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00002a6a (    0x70) AtmelX::ConfirmFirmwareVersion(bool) [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00002ada (    0x68) AtmelX::HandleSysEx(unsigned char*, unsigned int) [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, Funct...
                0x00002b42 (    0x44) AtmelX::SendSysEx(unsigned char*, unsigned int) [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, Functio...
                0x00002b86 (    0x42) non-virtual thunk to AtmelX::SendSysEx(unsigned char*, unsigned int) [FUNC, PEXT, NameNList, MangledNameNList, M...
                0x00002bc8 (   0x236) AtmelX::InitSWProductCommunications() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00002dfe (    0x4c) AtmelX::GetBootloaderVersionID() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00002e4a (   0x1c8) AtmelX::SeakAndLockOnSWProductA() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00003012 (   0x1ec) AtmelX::SeakAndLockOnSWProduct() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x000031fe (    0x44) AtmelX::EraseChip() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00003242 (     0xa) AtmelX::ReenableRWWSection() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x0000324c (     0xa) AtmelX::SendPacket(unsigned char*, long) [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00003256 (    0x82) AtmelX::SetAddress(unsigned char, unsigned char) [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, Functi...
                0x000032d8 (    0x1c) AtmelX::UpdateHex() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x000032f4 (   0x292) AtmelX::ProgramFlash() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                
                ...
                
                0x00003630 (    0x16) IntelHex::IntelHex() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00003646 (    0x16) IntelHex::IntelHex() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x0000365c (     0x6) IntelHex::~IntelHex() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00003662 (     0x6) IntelHex::~IntelHex() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00003668 (     0x8) IntelHex::UpdateHex() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                0x00003670 (    0x60) IntelHex::Init() [FUNC, PEXT, NameNList, MangledNameNList, Merged, NList, FunctionStarts]
                
                ...
                
                0x00009000 (  0x5000) __DATA SEGMENT
            0x00009000 (    0x1c) __DATA __dyld
            0x0000901c (    0x1c) __DATA __nl_symbol_ptr
            0x00009038 (   0x15c) __DATA __la_symbol_ptr
            0x00009194 (    0x1c) __DATA __mod_init_func
            0x000091b0 (  0x3b1c) __DATA __data
                0x000091b0 (     0x4) NXArgc [EXT, NameNList, MangledNameNList, NList]
                0x000091b4 (     0x4) NXArgv [EXT, NameNList, MangledNameNList, NList]
                0x000091b8 (     0x4) environ [EXT, NameNList, MangledNameNList, NList]
                0x000091bc (     0x8) __progname [EXT, NameNList, MangledNameNList, NList]
                0x000091c4 (     0x8) gSendDoneLightsSysex [NameNList, MangledNameNList, NList]
                0x000091cc (     0x8) gCheckForIntendedProductSysex [NameNList, MangledNameNList, NList]
                0x000091d4 (     0x6) gCheckUDI [NameNList, MangledNameNList, NList]
                0x000091da (     0x8) AtmelX::GetBootloaderVersionID()::gGetBootloaderVersionSysex [NameNList, MangledNameNList, NList]
                0x000091e2 (     0xa) AtmelX::EraseChip()::gEnterProgrammingMode [NameNList, MangledNameNList, NList]
                0x000091ec (     0x4) gHexCBufferLen [PEXT, NameNList, MangledNameNList, NList]
                0x000091f0 (  0x3a10) gHexCBuffer [PEXT, NameNList, MangledNameNList, NList]
                0x0000cc00 (    0x10) typeinfo for UIManager [PEXT, NameNList, MangledNameNList, NList]
                ....
```

Taking a look into that listing gives us a pretty good first sight on how firmware update process is intended to be:

* Firmware is likely to be updated over MIDI SysEx messages
* Firmware is stored either in raw binary, or `Intel HEX` format
* Most likely, firmware is a raw array and locaded inside `__data` section in `gHexCBuffer` symbol and have length of `gHexCBufferLen`

Lets try to get the data section and pull out the following symbols

```
0x000091ec (     0x4) gHexCBufferLen [PEXT, NameNList, MangledNameNList, NList]
0x000091f0 (  0x3a10) gHexCBuffer [PEXT, NameNList, MangledNameNList, NList]
```

```
$ otool -d FaderPort\ Updater\ 1.3.8 FaderPort\ Updater\ 1.3.8

FaderPort Updater 1.3.8:
Contents of (__DATA,__data) section
000091b0	00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
000091c0	00 10 00 00 f0 00 01 06 02 02 4c f7 f0 00 01 06
000091d0	02 02 78 f7 f0 7e 7f 06 01 f7 f0 00 01 06 02 02
000091e0	56 f7 f0 00 01 06 02 02 65 f7 00 00 00 3a 00 00
000091f0	36 a1 f7 00 c0 ae 76 82 0c 49 f1 0e 03 94 cf 3a
00009200	19 25 fc 00 36 a1 1c 06 c0 ae 09 00 0c 49 c6 0d
00009210	03 94 cf 3a 19 25 fc 00 36 a1 18 03 c0 ae 09 00
00009220	0c 49 c6 0d 03 94 cf 3a 19 25 fc 00 36 a1 3f 00
00009230	c0 ae 78 c0 0c 49 c6 0d 03 94 cf 3a 19 25 fc 00
00009240	36 a1 3f 00 63 2e 43 05 72 21 66 21 5b 14 87 2e
00009250	a2 05 95 41 d5 1f fb 12 fe 28 a2 05 95 41 af 21
00009260	32 12 25 2e d0 c4 c3 31 f9 20 f9 13 f8 28 18 05
00009270	a9 21 88 1f 0e 1a 03 20 97 46 46 a1 6c 27 97 1a
00009280	47 20 83 86 cd 81 72 26 8a 19 db 22 d2 06 1b 91

...

0000cc40	00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0000cc50	08 00 00 00 0d 74 00 00 00 00 00 00 00 00 00 00
0000cc60	00 00 00 00 50 cc 00 00 70 57 00 00 76 57 00 00
0000cc70	00 00 00 00 00 00 00 00 00 00 00 00 a0 6d 00 00
0000cc80	3e 6e 00 00 cf 6e 00 00 82 6f 00 00 01 70 00 00
0000cc90	6d 70 00 00 14 71 00 00 7f 71 00 00 c2 71 00 00
0000cca0	08 00 00 00 33 74 00 00 00 00 00 00 00 00 00 00
0000ccb0	00 00 00 00 a0 cc 00 00 10 5e 00 00 16 5e 00 00
0000ccc0	00 00 00 00 00 00 00 00 00 00 00 00

```

4 bytes at `0x000091ec` offset shows almost the same value we can see in the symbol table (`00 3a 00 00` over `0x3a10`). Maybe an alignment stuff. Note it, may be useful in the future.

## Getting raw frimware

The actual firmware array starts at `0x000091f0` and ends at `0x0000cbe0`

let's extract the binary

```
$ objdump -section=__data -macho FaderPort\ Updater\ 1.3.8 | sed -n "/^000091f0/,/0000cbe0/{p;}" | sed -e 's/^[0-9a-f]\{8\}//g' | xxd -r -p > fw.bin
```