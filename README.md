# MemTest86_PXE_Patch
This patch removes the rather arbitrary PXE boot restriction from Non-Site Editions of MemTest86

PXE_Check_x64:
.text:0x0000000000000628 : 48 8B 05 A1 1A 15 00 : mov 0x151AA1(%rip), %rax
.text:0x000000000000062F : 4C 8D 05 D2 2B 14 00 : lea 0x142BD2(%rip), %r8
.text:0x0000000000000636 : 48 8B 49 18          : mov 0x18(%rcx), %rcx
.text:0x000000000000063A : 48 8D 15 BF 81 08 00 : lea 0x881BF(%rip), %rdx
.text:0x0000000000000641 : FF 90 98 00 00 00    : call *0x98(%rax)
.text:0x0000000000000647 : 48 85 C0             : test %rax, %rax
.text:0x000000000000064A : 75 42                : jne 0x68E

The PXE_Check subroutine is resposible for determining if this edition of MemTest86 is allowed to use PXE boot,
half of this subroutine varies depending on the build due to different RIP relative addresses, but the last 3 instructions are the same between all of them.
All that needs to be done to bypass the check is to change the jne rel8(0x75) instruction to the jmp rel8(0xEB) instruction, but to not break other functions
the patch also includes the 2 previous instructions, as they do not occur anywhere else in the binary.

Tested to work on versions: 8.1, 8.2, 8.3, 9.0, 9.2
Note: This patch is only for the x64 executable:
Find:    0xFF90980000004885C07542
Replace: 0xFF90980000004885C0EB42

Don't forget to regenererate the PE executable file signature, as some EFI implementations check it.
Obviously, patched executables won't work on machines with Secure Boot enabled, disable it before wasting your time.
