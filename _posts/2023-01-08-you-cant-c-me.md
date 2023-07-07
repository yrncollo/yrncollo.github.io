---
title: "You Can't see me"
date: 2023-01-08 
image: /assets/img/Posts/You-cant-see-me/reverse-engineering.png
categories: [HackTheBox, reverse-engineering]
tags: [file, strings, ltrace, radare2, r2, reverse engineering]     # TAG names should always be lowercase
---
> **CHALLENGE DESCRIPTION:** Can you see me? 
[Source](https://app.hackthebox.com/challenges/you-cant-c-me) created by: [MinatoTW](https://app.hackthebox.com/users/8308)
{: .prompt-info }

In this reverse engineering challenge, we are given an ELF 64-bit LSB executable file named `"auth"` which had been `stripped`. The first step to do is to check the file format using the `file` command, which revealed that it is an executable file for GNU/Linux 3.2.0.

## Method 1 
### Recon

We shall start by checking the file format and we notice it is an executable file which has been stripped.

```bash
└─$ file auth 
auth: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, stripped
```
Next, we shall use the **`strings`** command to search for any interesting strings within the file. However, we find nothing noteworthy in the output.

```bash
└─$ strings auth           
/lib64/ld-linux-x86-64.so.2
libc.so.6
stdin
...
...
...
```

Then we shall use the **`ltrace`** tool to see if we can get any additional information from the executable. This reveales that the password was being leaked in a string compare operation.

```bash
└─$ ltrace ./auth 
printf("Welcome!\n"Welcome!
)                                                                                     = 9
malloc(21)                                                                                               = 0x206e6b0
fgets(afdsf
"afdsf\n", 21, 0x7f8ec926aa80)                                                                     = 0x206e6b0
strcmp("wh00ps!_y0u_d1d_c_m3", "afdsf\n")                                                                = 22
printf("I said, you can't c me!\n"I said, you can't c me!
)                                                                      = 24
+++ exited (status 0) +++
```

### Exploitation

Now that we have found the password on the string compare we can use it to get the flag

```bash
└─$ ./auth
Welcome!
wh00ps!_y0u_d1d_c_m3
HTB{wh00ps!_y0u_d1d_c_m3}
```

## Method 2

We shall use `radare2` to decompile the file and analyze the binary.
### Recon

Our first step is to analyze the file using the **`radare2`** tool and the **`aaaaaa`** command. This provided us with detailed information about the file, including its size, format, and architecture.
We will start by analyzing the file:

```bash
└─$ r2 auth 
[0x00401070]> aaaaaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Finding and parsing C++ vtables (avrr)
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information (aanr)
[x] Finding function preludes
[x] Enable constraint types analysis for variables
[0x00401070]>
```

Then we shall use `i` flag to get more information about the file

```bash
[0x00401070]> i
fd       3
file     auth
size     0x3848
humansz  14.1K
minopsz  1
maxopsz  16
invopsz  1
mode     r-x
format   elf64
iorw     false
block    0x100
type     EXEC (Executable file)
arch     x86
baddr    0x400000
binsz    12676
bintype  elf
bits     64
canary   false
class    ELF64
compiler GCC: (Ubuntu 9.2.1-9ubuntu2) 9.2.1 20191008 clang version 6.0.1-11 (tags/RELEASE_601/final)
crypto   false
endian   little
havecode true
intrp    /lib64/ld-linux-x86-64.so.2
laddr    0x0
lang     c
linenum  false
lsyms    false
machine  AMD x86-64 architecture
nx       true
os       linux
pic      false
relocs   false
relro    partial
rpath    NONE
sanitize false
static   false
stripped true
subsys   linux
va       true

[0x00401070]>
```

Next, we shall use the **`iz`** command to search for any interesting strings within the file. However, we have find nothing noteworthy in the output.

```bash
[0x00401070]> iz
[Strings]
nth paddr      vaddr      len size section type  string
―――――――――――――――――――――――――――――――――――――――――――――――――――――――
0   0x00002004 0x00402004 9   10   .rodata ascii Welcome!\n
1   0x0000200e 0x0040200e 24  25   .rodata ascii I said, you can't c me!
2   0x00002027 0x00402027 8   9    .rodata ascii 
3   0x00002030 0x00402030 20  21   .rodata ascii this_is_the_password
4   0x00002050 0x00402050 6   7    .rodata ascii m^&&fi
5   0x00002057 0x00402057 13  13   .rodata ascii Uo&kUZ'ZUYUc)

[0x00401070]>
```
We shall list all the funtions by using `afl`
```bash
[0x00401070]> afl
0x00401070    1 47           entry0
0x00401030    1 6            sym.imp.printf
0x00401040    1 6            sym.imp.fgets
0x00401050    1 6            sym.imp.strcmp
0x00401060    1 6            sym.imp.malloc
0x00401160    7 297          main
0x00401150    5 118  -> 55   entry.init0
0x00401120    3 33   -> 32   entry.fini0
0x004010b0    4 33   -> 31   fcn.004010b0
0x00401000    3 27           fcn.00401000
0x004010a0    1 5            fcn.004010a0
0x00401290    4 101          fcn.00401290
0x00401300    1 5            fcn.00401300
0x00401308    1 13           fcn.00401308
[0x00401070]>
```
### Exploitation
Now seek to main function:
```bash
[0x00401070]> s main
```

We shall set the break point on the string compare.

```bash
db 0x00401249.
```

By running the file and hitting the break point, we were able to use the **`afvd`** command to display all local variables on the stack. This reveales the password being used in the string compare operation.

```bash
[0x00401249]> afvd
type:signed int64_t doesn't exist
var var_4h = 0x7ffdfb61267c = (qword)0x0000000100000000
var var_8h = 
var s1 = 0x7ffdfb612660 = "wh00ps!_y0u_d1d_c_m3"
var var_18h = 0x7ffdfb612668 = (qword)0x5f6431645f753079
var var_10h = 0x7ffdfb612670 = (qword)0x00000000336d5f63
var var_ch = 0x7ffdfb612674 = (qword)0x0000001400000000
var var_44h = 0x7ffdfb61263c = (qword)0x26265e6d00000009
var s2 = (*0x1dbc6b0)0x7ffdfb612658 = "asfd\n"
var var_40h = 0x7ffdfb612640 = "m^&&fi\x17Uo&kUZ'ZUYUc)"
var var_38h = 0x7ffdfb612648 = (qword)0x555a275a556b266f
var var_30h = 0x7ffdfb612650 = (qword)0x0000000029635559
var var_50h = 0x7ffdfb612630 = "\xb0\xc6\xdb\x01"
var var_58h = 0x7ffdfb612628 = (qword)0x0000000000000000
var var_54h = 0x7ffdfb61262c = (qword)0x01dbc6b000000000
```

We can see the `s1` variable containing the password. Now we can re-run the program by using `ood` and `dc` command. Then we would input the password we retrieved from the `s1` variable and we shall get the flag.
```bash
[0x00401246]> ood 
native-singlestep: No such process
Stepping failed!
PTRACE_CONT: No such process
(7887) Process exited with status=0x9
child received signal 9
File dbg:///home/yrncollo/Desktop/htb/you_cant_see_me/auth reopened in read-write mode
[0x7f2542a469c0]> dc
Welcome!
wh00ps!_y0u_d1d_c_m3
hit breakpoint at: 0x401246
[0x00401246]> dc
HTB{wh00ps!_y0u_d1d_c_m3}
(7938) Process exited with status=0x0
[0x7f25429051f9]>
```
We have successfully retrieved the flag: `HTB{wh00ps!_y0u_d1d_c_m3}`.

> If you have any other additional information and resources you can add it at the comment section. **THANKS ;)**
{: .prompt-warning }
