---
layout: post
title: x86 and x86-64 Instruction Set
permalink: /assembly/instruction-set.html
---

# x86 and x86-64 Instructions
---

## Index
- [Data Transfer Instructions](#data-transfer-instructions)
- [Arithmetic Instructions](#arithmetic-instructions)
- [Logical Instructions](#logical-instructions)
- [Control Flow Instructions](#control-flow-instructions)
- [Syscall Instructions](#syscall-instructions)

*(This list is NOT complete. There are thousands of valid Assembly opcodes. It presents most of the commonly used Assembly opcodes along with brief examples of their usage.)*

## Data Transfer Instructions
---
- **MOV**: Copy data into a register or between registers
    - **Examples (x86)**:
    ```
    mov eax, 0x5
    mov eax, edi
    mov [ecx], esp
    mov [esp-0x2], 0x4141
    ```
    - **Examples (x86-64)**:
    ```
    mov rax, 0x5
    mov rax, rdi
    mov [rcx], rsp
    mov [rsp-0x2], 0x4141
    ```

- **XCHG**: Exchange data between registers
    - **Example (x86)**:
    ```
    mov ecx, 0x5
    mov edx, 0x7
    xchg ecx, edx
    (ecx = 0x7, edx = 0x5)
    ```
    - **Example (x86-64)**:
    ```
    mov rcx, 0x5
    mov rdx, 0x7
    xchg rcx, rdx
    (rcx = 0x7, rdx=0x5)
    ```

- **LEA**: Load effective address
    - **Example (x86)**:

        ```
        section .data
            value dd 123

        section .text
            global _start

        _start:
            lea eax, [value]    ; eax = address of value
        ```

    - **Example (x86-64)**:
        ```
        section .data
            value dq 123

        section .text
            global _start

        _start:
            lea rax, [rel value]   ; rax = address of value
        ```

- **PUSH**: Push data onto the stack
    - **Example**:
    ```
    push 0x5        ; 0x5 is now at the top of the stack
    ```

- **POP**: Pop data from the stack
    - **Example (x86)**:
    ```
    push 0x5
    pop eax         ; eax = 0x5
    ```
    - **Example (x86-64)**:
    ```
    push 0x5
    pop rax         ; rax = 0x5
    ```

- **PUSHF**/**POPF** (**x86**): Push/pop flags
    - **Example**:
        ```
        section .text
            global _start

        _start:
            stc                 ; set Carry Flag (CF = 1)
            pushf               ; push EFLAGS onto stack

            clc                 ; clear Carry Flag (CF = 0)
            popf                ; restore original flags
        ```

- **PUSHFQ**/**POPFQ** (**x86-64**): Push/pop flags
    - **Example**:
        ```
        section .text
            global _start

        _start:
            stc                 ; set Carry Flag (CF = 1)
            pushfq              ; push RFLAGS onto stack

            clc                 ; clear Carry Flag (CF = 0)
            popfq               ; restore original flags
        ```

- **MOVZX**: Zero-extend
    - **Example (x86)**:
        ```
        section .data
            value db 0FFh

        section .text
            global _start

        _start:
            movzx eax, byte [value]     ; eax = 000000FFh
        ```
    - **Example (x86-64)**:
        ```
        section .data
            value db 0FFh

        section .text
            global _start

        _start:
            movzx rax, byte [rel value]     ; rax = 00000000000000FFh
        ```


- **MOVSX**: Sign-extend
    - **Example (x86)**:
        ```
        section .data
            value db -1

        section .text
            global _start

        _start:
            movsx eax, byte [value]     ; eax = FFFFFFFFh
        ```

    - **Example (x86-64)**:
        ```
        section .data
            value db -1

        section .text
            global _start

        _start:
            movsx rax, byte [rel value]     ; rax = FFFFFFFFFFFFFFFFh
        ```

- **BSWAP**: Byte swap (swaps the endianness)
    - **Example (x86)**:
    ```
    mov eax, 0xdeadbeef
    bswap eax           ; eax = 0xefbeadde
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 0xdeadbeefdeadbeef
    bswap rax           ; rax = 0xefbeaddeefbeadde
    ```

- **CMPXCHG**: Compare accumulator (RAX/EAX) with destination and exchange if the accumulator value equals the destination value
    - **Example (x86)**:
        ```
        section .data
            value dd 5

        section .text
            global _start

        _start:
            mov eax, 5
            mov ebx, 10

            cmpxchg [value], ebx

            ; eax == value
            ; value becomes 10
        ```

    - **Example (x86-64)**:
        ```
        section .data
            value dq 5

        section .text
            global _start

        _start:
            mov rax, 5
            mov rbx, 10

            cmpxchg [rel value], rbx

            ; rax == value
            ; value becomes 10
        ```

- **XADD**: Exchange and add
    - **Example (x86)**:
        ```
        section .data
            value dd 5

        section .text
            global _start

        _start:
            mov eax, 3

            xadd [value], eax

            ; eax = old 'value' (5)
            ; value = 8
        ```

    - **Example (x86-64)**:
        ```
        section .data
            value dq 5

        section .text
            global _start

        _start:
            mov rax, 3

            xadd [rel value], rax

            ; rax = old 'value' (5)
            ; value = 8
        ```

- **XLAT**: Table lookup translate
    - **Example (x86)**:
        ```
        section .data
            table db 10,20,30,40

        section .text
            global _start

        _start:
            mov ebx, table
            mov al, 2

            xlat

            ; al = 30
        ```
    
    - **Example (x86-64)**:
        ```
        section .data
            table db 10,20,30,40

        section .text
            global _start

        _start:
            lea rbx, [rel table]
            mov al, 2

            xlat

            ; al = 30
        ```

## Arithmetic Instructions
---
- **ADD**: Add a value to another value in a register
    - **Example (x86)**:
        ```
        mov eax, 0x5
        add eax, 0x2            ; eax = 0x7
        
        mov ebx, 0x4
        add eax, ebx            ; eax = 0x9
        ```

    - **Example (x86-64)**:
        ```
        mov rax, 0x5
        add rax, 0x2            ; rax = 0x7

        mov rbx, 0x4
        add rax, rbx            ; rax = 0x9
        ```

- **ADC**: Add with carry
    - **Example (x86)**:
    ```
    mov eax, 0FFFFFFFFh
    add eax, 1          ; eax = 0, CF = 1
    adc edx, 0          ; edx += carry
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 0FFFFFFFFFFFFFFFFh
    add rax, 1          ; rax = 0, CF = 1
    adc rdx, 0          ; rdx += carry
    ```

- **SUB**: Subtract a value from another value in a register
    - **Example (x86)**:
        ```
        mov eax, 0x5
        sub eax, 0x2            ; eax = 0x3

        mov ebx, 0x2
        sub eax, ebx            ; eax = 0x1
        ```

    - **Example (x86-64)**:
        ```
        mov rax, 0x5
        sub rax, 0x2            ; rax = 0x3

        mov rbx, 0x2
        sub rax, rbx            ; rax = 0x1
        ```

- **SBB**: Subtract with borrow
    - **Example (x86)**:
    ```
    mov eax, 0
    sub eax, 1              ; CF = 1
    sbb edx, 0              ; edx -= borrow
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 0
    sub rax, 1              ; CF = 1
    sbb rdx, 0              ; RDX -= borrow
    ```

- **INC**: Increment a value stored in a register by 1
    - **Example (x86)**:
    ```
    mov eax, 0x2
    inc eax                 ; eax = 0x3
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 0x2
    inc rax                 ; rax = 0x3 
    ```

- **DEC**: Decrement a value stored in a register by 1
    - **Example (x86)**:
    ```
    mov eax, 0x2
    dec eax                 ; eax = 0x1
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 0x2
    dex rax                 ; rax = 0x1
    ```

- **NEG**: Negate a value
    - **Example (x86)**:
    ```
    mov eax, 5
    neg eax                 ; eax = -5
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 5
    neg rax                 ; rax = -5
    ```

- **CMP**: Compare a value in a register against another value
    - **Example (x86)**:
    ```
    mov eax, 0x5
    mov ebx, 0x5
    cmp eax, ebx
    jz equal                ; Jumps to "equal" if the values are the same
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 0x5
    mov rbx, 0x5
    cmp rax, rbx
    jz equal                ; Jumps to "equal" if the value are the same
    ```

- **MUL**: Unsigned multiply
    - **Example (x86)**:
    ```
    mov eax, 5
    mov ebx, 4
    mul ebx                 ; edx and eax = 20
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 5
    mov rbx, 4
    mul rbx                 ; rdx and rax = 20
    ```

- **DIV**: Unsigned divide
    - **Example (x86)**:
    ```
    mov edx, 0
    mov eax, 20
    mov ebx, 4
    div ebx                 ; eax = 5, edx = remainder (0)
    ```

    - **Example (x86-64)**:
    ```
    mov rdx, 0
    mov rax, 20
    mov rbx, 4
    div rbx                 ; rax = 5, rdx = remainder (0)
    ```

- **CDQ**: Sign-extension for division x86
    - **Example**:
        ```
        mov eax, -10
        cdq

        EAX = FFFFFFF6h
        EDX = FFFFFFFFh
        EDX:EAX = FFFFFFFFFFFFFFF6h
        ```
        *CDQ can also be used to make EDX 0 if EAX is a positive value. EDX becomes the sign-flag which is 0.*

- **CQO**: Sign-extension for division x86-64
    - **Example**:
        ```
        mov rax, -10
        cqo

        RAX = FFFFFFFFFFFFFFF6h
        RDX = FFFFFFFFFFFFFFFFh
        ```
        *CQO can also be used to make RDX 0 if RAX is a positive value. RDX becomes the sign-flag which is 0*

## Logical Instructions
---
- **AND**: Bitwise AND
    - **Example (x86)**:
        ```
        mov eax, 12             ; 1100
        and eax, 10             ; 1010
        
        EAX = 8                 ; 1000
        ```

    - **Example (x86-64)**:
        ```
        mov rax, 12             ; 1100
        and rax, 10             ; 1010

        RAX = 8                 ; 1000
        ```

- **OR**: Bitwise OR
    - **Example (x86)**:
        ```
        mov eax, 12             ; 1100
        or eax, 10              ; 1010
        
        EAX = 14                ; 1110
        ```

    - **Example (x86-64)**:
        ```
        mov rax, 12             ; 1100
        or rax, 10              ; 1010

        RAX = 14                ; 1110
        ```

- **XOR**: Bitwise XOR
    - **Example (x86)**:
        ```
        mov eax, 12             ; 1100
        xor eax, 10             ; 1010
        
        EAX = 6                 ; 0110
        ```

    - **Example (x86-64)**:
        ```
        mov rax, 12             ; 1100
        xor rax, 10             ; 1010

        RAX - 6                 ; 0110
        ```

- **NOT**: Bitwise NOT (flips every bit)
    - **Example (x86)**:
    ```
    mov eax, 0
    not eax                 ; eax = -1
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 0
    not rax                 ; rax = -1
    ```

- **TEST**: Logical compare. Performs **AND** but does not store the result. Flags are updated.
    - **Example (x86)**:
        ```
        mov eax, 8              ; 1000
        test eax, 8
        
        1000 AND 1000 = 1000
        ZF = 0
        SF = 0
        ```

        ```
        mov eax, 8              ; 1000
        test eax, 4             ; 0100

        1000 AND 0100 = 0000
        ZF = 1
        ```

    - **Example (x86-64)**:
        ```
        mov rax, 8              ; 1000
        test rax, 8             ; 1000
        
        ZF = 0
        ```

## Shift and Rotate Instructons
---
- **SHL**/**SAL**: Shift left (both instructions are identical)
    - **Example (x86)**:
        ```
        mov eax, 5              ; 0101
        shl eax, 1

        EAX = 10                ; 1010
        ```

    - **Example (x86-64)**:
        ```
        mov rax, 5              ; 0101
        shl rax, 1

        EAX = 10                ; 1010
        ```

- **SHR**: Logical shift right
    - **Example (x86)**:
        ```
        mov eax, 8              ; 1000
        shr eax, 1

        EAX = 4                 ; 0100
        ```

    - **Example (x86-64)**:
        ```
        mov rax, 8
        shr rax, 1

        RAX = 4                 ; 0100
        ```

- **ROL**: Rotate left. Bits shifted out on the left re-enter on the right.
    - **Example (x86)**:
        ```
        mov al, 9               ; 00001001
        rol al, 1

        AL = 18                 ; 00010010
        ```

    - **Example (x86-64)**:
        ```
        mov al, 129             ; 10000001
        rol al, 1

        AL = 3                  ; 00000011
        ```

- **ROR**: Rotate right. Bits shifted out on the right re-enter on the left.
    - **Example (x86)**:
        ```
        mov al, 9               ; 00001001
        ror al, 1

        AL = 132                ; 10000100
        ```

    - **Example (x86-64)**:
        ```
        mov al, 2               ; 00000010
        ror al, 1

        AL = 1                  ; 00000001
        ```

## Control Flow Instructions
- **JMP**: Unconditional jump
    - **Example**:
    ```
    jmp target              ;Execution continues at label "target"
    ```

- **CALL**: Function call. Pushes return address onto stack, then jumps.
    - **Example**:
        ```
        call my_func

        ESP/RSP decreases
        Return address pushed to stack
        Execution jumps to my_func
        ```

- **RET**: Return. Pops return address from stack.
    - **Example**:
        ```
        ret

        EIP/RIP restored from stack
        Execution returns to caller
        ```

- **JE**/**JZ**: Jump if equal/zero
    - **Example**:
    ```
    mov eax, 5
    mov ebx, 5
    cmp eax, ebx
    je target               ; Jumps to "target" because ZF = 1
    ```

- **JNE**/**JNZ**: Jump if not equal
    - **Example**:
    ```
    mov eax, 5
    mov ebx, 7
    cmp eax, ebx
    jne target              ; Jumps to "target" because ZF != 1
    ```

- **JG**/**JNLE**: Jump if greater
    - **Example (x86)**:
    ```
    mov eax, 0x7
    mov ebx, 0x5
    cmp eax, ebx
    jg greater              ; Jumps to "greater" because eax is greater than ebx
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 0x7
    mov rbx, 0x5
    cmp rax, rbx
    jg greater               ; Jumps to "greater" because rax is greater than rbx
    ```

- **JL**/**JNGE**: Jump if less
    - **Example (x86)**:
    ```
    mov eax, 0x5
    mov ebx, 0x7
    cmp eax, ebx
    jl less                 ; Jumps to "less" because eax is less than ebx
    ```

    - **Example (x86-64)**:
    ```
    mov rax, 0x5
    mov rbx, 0x7
    cmp rax, rbx
    jl less                 ; Jumps to "less" because rax is less than rbx
    ```

- **JGE**: Jump if greater/equal
    - **Example (x86)**:
        ```
        mov eax, 0x5
        mov ebx, 0x4
        cmp eax, ebx
        jge target              ; Jumps to "target" because eax is greater than ebx

        inc ebx
        cmp eax, ebx
        jge target              ; Jumps to "target" because eax is equal to ebx
        ```

    - **Example (x86-64)**:
        ```
        mov rax, 0x5
        mov rbx, 0x4
        cmp rax, rbx
        jge target              ; Jumps to "target" because rax is greater than rbx

        inc rbx
        cmp rax, rbx
        jge target              ; Jumps to "target" because rax is equal to rbx
        ```

- **JLE**: Jump if less/equal
    - **Example (x86)**:
        ```
        mov eax, 0x5
        mov ebx, 0x5
        cmp eax, ebx
        jle target              ; Jumps to "target" because eax is equal to ebx

        dec eax
        cmp eax, ebx
        jle target              ; Jumps to "target" because eax is less than ebx
        ```

    - **Example (x86-64)**:
        ```
        mov rax, 0x5
        mov rbx, 0x5
        cmp rax, rbx
        jle target              ; Jumps to "target" because rax is equal to rbx

        dec rax
        cmp rax, rbx
        jle target              ; Jumps to "target" because rax is less than rbx
        ```

- **LOOP**: Loop using counter. Decrements ECX/RCX and jumps if not zero.
    - **Example**:
        ```
        mov ecx, 3

        target:
            ...
        loop target             ; ECX = 3 [jump], 2 [jump], 1 [jump], 0 [no jump]
        ```

- **LOOPE**/**LOOPZ**: Loop while equal. Loops while RCX/ECX != 0 AND ZF = 1.
    - **Example**:
    ```
    loope target
    ```

- **LOOPNE**/**LOOPNZ**: Loop while not equal. Loops while RCX/ECX != 0 AND ZF = 0.
    - **Example**:
    ```
    loopne target
    ```

- **JECXZ**/**JRCXZ**: Jump if CX/ECX zero
    - **Example (x86)**:
        ```
        zero_label:
            ...

        mov ecx, 0
        jecxz zero_label
        ```

    - **Example (x86-64)**:
        ```
        zero_label:
            ...

        mov rcx, 0
        jrcxz zero_label
        ```

## Syscall Instructions
---
- **syscall**: Executes the corresponding syscall based on rax (x86-64).
    - **Example**:
    ```
    mov al, 0x3c            ; RAX = 0x3c (exit syscall number [x86-64])
    xor rdi, rdi            ; RDI = 0
    syscall                 ; Executes exit syscall
    ```

- **int 0x80**: Executes the corresponding syscall based on eax (x86).
    - **Example**:
    ```
    mov al, 0x1             ; EAX = 0x1 (exit syscall number [x86])
    xor ebx, ebx            ; EBX = 0
    int 0x80                ; Executes exit syscall
    ```