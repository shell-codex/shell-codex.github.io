---
layout: default
title: write("Hello World", stdout) - Write "Hello World"
permalink: /shellcode/asm-08.html
os: Linux
arch: Intel x86
length: 36
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
	xor ebx, ebx		; null ebx (0)
	mul ebx			; eax, ebx, edx = 0

	push eax		; push null terminator
	push 0x0a646c72		; push "rld\n"
	push 0x6f57206f		; push "o Wo"
	push 0x6c6c6548		; push "Hell"
	mov ecx, esp		; move pointer to "Hwllo World\n" into edi

	mov al, 0x04		; move write syscall number into al (eax)
	mov bl, 0x1		; move 1 into ebx (1 = stdout)
	mov dl, 12		; move length of string (12) into dl
	int 0x80		; call write

	mov al, 0x1		; move exit syscall number into al (eax)
	xor ebx, ebx		; null ebx (0)
	int 0x80		; call exit
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
\x31\xdb\xf7\xe3\x50\x68\x72\x6c\x64\x0a\x68\x6f\x20\x57\x6f\x68\x48\x65\x6c\x6c\x89\xe1\xb0\x04\xb3\x01\xb2\x0c\xcd\x80\xb0\x01\x31\xdb\xcd\x80
```