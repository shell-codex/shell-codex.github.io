---
layout: default
title: write("Hello World", stdout) - Write "Hello World"
permalink: /shellcode/asm-16.html
os: Linux
arch: Intel x86-64
length: 46
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
	xor rbx, rbx			; null rbx (0)
	mul rbx				; rax, rbx, rdx = 0

	push rbx			; push null terminator
	mov rbx, 0x0a646c72		; move "rld\n" into rbx
	push rbx			; push "rld\n"

	mov rbx, 0x6f57206f6c6c6548	; move "Hello Wo" into rbx
	push rbx			; push "Hello Wo"

	mov rsi, rsp			; move pointer to "Hello World\n" into rsi

	xor rdi, rdi			; null rdi (0)
	mov al, 0x1			; move write syscall number into al (rax)
	mov dil, 0x1			; move 1 into dil (1 = stdout)
	mov dl, 12			; move 12 into dl (12 bytes = length)
	syscall				; call write

	xor rdi, rdi			; null rdi (rdi)
	mov al, 0x3c			; move 0x3c into al (exit syscall)
	syscall				; call exit
```

## Compilation and Linking

```bash
# Assemble
nasm -f elf64 -o code.o code.asm

# Link
ld -m elf_x86_64 -s -o code code.o

# Extract Shellcode
printf '\\x' && objdump -d code | grep "^ " | cut -f2 | tr -d ' ' | tr -d '\n' | sed 's/.\{2\}/&\\x /g'| head -c-3 | tr -d ' ' && echo ' '
```

## Shellcode

```
\x48\x31\xdb\x48\xf7\xe3\x53\xbb\x72\x6c\x64\x0a\x53\x48\xbb\x48\x65\x6c\x6c\x6f\x20\x57\x6f\x53\x48\x89\xe6\x48\x31\xff\xb0\x01\x40\xb7\x01\xb2\x0c\x0f\x05\x48\x31\xff\xb0\x3c\x0f\x05
```