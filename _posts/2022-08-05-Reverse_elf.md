---
title: "Reversing ELF"
date: 2022-08-05 
categories: [Tryhackme, Easy]
tags: [r2, radare2, assembly, ltrace, base64]     # TAG names should always be lowercase
---

> Hey remember that I'll have to change the flag so that you can try it and find the actual flag. You can find the challenges [here](https://tryhackme.com/room/reverselfiles).

### Crackme1
This was one of the easiest crackme challenges. All you have to do is change the file to be an executable: `chmod +x crackme1`
Now if you run it you will find the flag
```
└─$ ./crackme1 
flag{I have removed flag}
```

### Crackme2
Once you have downloaded the binary file and changed the permissions to executable we can now start reversing it.

We are going to try and run the elf file.
```
└─$ ./crackme2        
Usage: ./crackme2 password
```
Well it seems we have to put password just after the elf.
```
└─$ ./crackme2 test 
    Access denied.
```
We can see we don't have the right password. We would use **ltrace** so as we can check if the password is being compared to a certain string.
```
└─$ ltrace ./crackme2 test
__libc_start_main(0x804849b, 2, 0xfff88504, 0x80485c0 <unfinished ...>
strcmp("test", "super_secret_password")                                                                 = -1
puts("Access denied."Access denied.
)                                                                                  = 15
+++ exited (status 1) +++
```
"Test" is being compared to "super_secret_password" meaning that's the actual password being compared to. Now let's run it with the actual password
```
└─$ ./crackme2 super_secret_password
    Access granted.
    flag{I have removed flag}
```
Nice it gives us the flag.

### Crackme3
On this challenge we shall do some recon on the file which we were given. We shall check if there are any strings on the file by using string command
```
└─$ strings crackme3 
/lib/ld-linux.so.2
__gmon_start__
libc.so.6
_IO_stdin_used
puts
strlen
malloc
stderr
fwrite
fprintf
strcmp
__libc_start_main
GLIBC_2.0
PTRh
iD$$
D$,;D$ 
UWVS
[^_]
Usage: %s PASSWORD
malloc failed
ZjByX3kwdXJfNWVjMG5kX2xlNTVvbl91bmJhc2U2NF80bGxfN2gzXzdoMW5nNQ==
Correct password!
Come on, even my aunt Mildred got this one!
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/
;*2$"8
GCC: (Ubuntu/Linaro 4.6.3-1ubuntu5) 4.6.3
.shstrtab
.interp
.note.ABI-tag
.note.gnu.build-id
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rel.dyn
.rel.plt
.init
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.ctors
.dtors
.jcr
.dynamic
.got
.got.plt
.data
.bss
.comment
```
We are able to see a base64 string which we shall decode it later. Let's use **`Radare2`** to check the strings.
The first thing we shall do is open the file on `radare2` and analyze it by using "`aaaa`"

```
└─$ r2 crackme3     
 -- Add more blockchains to your life.
[0x08048440]> aaaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze all functions arguments/locals
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Finding and parsing C++ vtables (avrr)
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information (aanr)
[x] Finding function preludes
[x] Enable constraint types analysis for variables
[0x08048440]>
```
Once we have analyzed it we shall use **`iz`** which `prints strings in data sections`

```
[0x08048440]> iz
[Strings]
nth paddr      vaddr      len size section type  string
―――――――――――――――――――――――――――――――――――――――――――――――――――――――
0   0x00000e68 0x08048e68 19  20   .rodata ascii Usage: %s PASSWORD\n
1   0x00000e7c 0x08048e7c 14  15   .rodata ascii malloc failed\n
2   0x00000e8b 0x08048e8b 64  65   .rodata ascii ZjByX3kwdXJfNWVjMG5kX2xlNTVvbl91bmJhc2U2NF80bGxfN2gzXzdoMW5nNQ==
3   0x00000ed0 0x08048ed0 17  18   .rodata ascii Correct password!
4   0x00000ef0 0x08048ef0 43  44   .rodata ascii Come on, even my aunt Mildred got this one!
5   0x00000f1c 0x08048f1c 64  65   .rodata ascii ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/
[0x08048440]>
```
We are able to get the same `base64` string on the executable file.The next thing we shall do is to decode the `base64` string.

```
└─$ echo "ZjByX3kwdXJfNWVjMG5kX2xlNTVvbl91bmJhc2U2NF80bGxfN2gzXzdoMW5nNQ==" | base64 -d
{I have removed flag}
```

### Crackme4

On this challenge we are provides an elf file which it's interesting because if we run the file we are somehow given hint.
```
└─$ ./crackme4     
Usage : ./crackme4 password
This time the string is hidden and we used strcmp
```
So let's try and supply it with a password
```
└─$ ./crackme4 test
password "test" not OK
```
Because we have some hint that it uses `strcmp` to compare the actual password and the one you supplied it, let us use `ltrace`.
```
└─$ ltrace ./crackme4 test   
__libc_start_main(0x400716, 2, 0x7ffe8ee36218, 0x400760 <unfinished ...>
strcmp("my_m0r3_secur3_pwd", "test")                                                                    = -7
printf("password "%s" not OK\n", "test"password "test" not OK
)                                                                = 23
+++ exited (status 0) +++
```
Well we can see it is comparing my_m0r3_secur3_pwd to test meaning my_m0r3_secur3_pwd is the correct password.
Now running the binary with the password which we have found it gives us the password OK message meaning we are right.
```
└─$ ./crackme4 my_m0r3_secur3_pwd
password OK
```
### Crackme5
By now I think it's becoming easier and easier. So let's have a look at crackme5 binary file. Let's start by seeing if there is any strings on the binary by using strings. Well we ain't lucky this time because their is no unusual string. By running the binary file normal with unknown input it gives us "`Always dig deeper`"
Now we shall run the elf using `ltrace` to see if the input is being compared to a string.
```
└─$ ltrace ./crackme5
__libc_start_main(0x400773, 1, 0x7ffdb0898aa8, 0x4008d0 <unfinished ...>
puts("Enter your input:"Enter your input:
)                                                                               = 18
__isoc99_scanf(0x400966, 0x7ffdb0898960, 0, 0x7ffbcdfbe603test
)                                             = 1
strlen("test")                                                                                          = 4
strlen("test")                                                                                          = 4
strlen("test")                                                                                          = 4
strlen("test")                                                                                          = 4
strlen("test")                                                                                          = 4
strncmp("test", "OfdlDSA|3tXb32~X3tX@sX`4tXtz", 28)                                                     = 37
puts("Always dig deeper"Always dig deeper
)                                                                               = 18
+++ exited (status 0) +++
```
As you can see it is comparing test with ``OfdlDSA|3tXb32~X3tX@sX`4tXtz`` so let's try and use that as our input.
```
└─$ ./crackme5
Enter your input:
OfdlDSA|3tXb32~X3tX@sX`4tXtz
Good game
```
Well game over we are in. ``OfdlDSA|3tXb32~X3tX@sX`4tXtz`` is the actual input being required.

### Crackme6
We would start off by basic recon which is  searching through the strings to check if we have something juicy. I think we are unlucky once again. So we shall use `ltrace` to see if their is string comparison happening.
```
└─$ ltrace ./crackme6 test 
__libc_start_main(0x400711, 2, 0x7ffc9bdb22b8, 0x400760 <unfinished ...>
printf("password "%s" not OK\n", "test"password "test" not OK
)                                                                = 23
+++ exited (status 0) +++
```
Well once again we are unlucky so we need to decompile the file using `radare2` to see what's happening.
Once you have opened and analyzed using `aaa` we would list all the function by `afl`. There is main function so we would seek to main function by `s main` and print assembly of main function by `pdf`.
We can see `sym.compare_pwd` is being called.
```
0x0040074c      4889c7         mov rdi, rax                ; int64_t arg1
0x0040074f      e87dffffff     call sym.compare_pwd
```
We would seek to sym.compare_pwd and print assembly of compare_pwd function and see what's going on there.
```
0x004006dd      488b45f8       mov rax, qword [var_8h]
0x004006e1      4889c7         mov rdi, rax                ; int64_t arg1
0x004006e4      e894feffff     call sym.my_secure_test
```
Another function is being called and that's my_secure_test. We shall seek to my_secure_test and print the assembly of that function.
You will notice it is comparing on the `al` register 
```
0x00400594      0fb600         movzx eax, byte [rax]
0x00400597      3c31           cmp al, 0x31
```
We shall manually take the hex value which compares to the one stored on `al` register which are: `(0x31, 0x33, 0x33, 0x37, 0x5f, 0x70, 0x77, 0x64)`
Now we have to convert the hex values to their corresponding strings. On this case let's keep everything as beginner friendly as we can. So we shall still use `radare2` to convert but also you can use a simple python script
Append a question mark before the hex and it would convert it to different format. We shall grab the string because that's what we want.
```
[0x0040057d]> ? 0x31
int32   49
uint32  49
hex     0x31
octal   061
unit    49
segment 0000:0031
string  "1"       <-----take this value
fvalue  49.0
float   0.000000f
double  0.000000
binary  0b00110001
ternary 0t1211
[0x0040057d]>
```
Take the values one after the other till 0x64
```
[0x0040057d]> ? 0x64
int32   100
uint32  100
hex     0x64
octal   0144
unit    100
segment 0000:0064
string  "d"
fvalue  100.0
float   0.000000f
double  0.000000
binary  0b01100100
ternary 0t10201
```
Well... Again you can automate this process by using python.
If you noticed we would be having `1337_pwd` as our password. Try running with it and you'll have solved the challenge.
```
└─$ ./crackme6 1337_pwd                                                                                   
    password OK
```
### Crackme7
Once you have the elf we can run it to as intended so as we can know what the program is supposed to do.
```
└─$ ./crackme7 
Menu:

[1] Say hello
[2] Add numbers
[3] Quit
```
As we can see you are required to choose one of the three numbers. 
Well let's run the program on `Radare2` so as we can have deep understanding of the program.
```
r2 crackme7
-- A git pull a day keeps the segfault away
[0x080483c0]> aaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze all functions arguments/locals
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Finding and parsing C++ vtables (avrr)
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information (aanr)
[x] Use -AA or aaaa to perform additional experimental analysis.
[0x080483c0]>
```
Seek on the main function and you will see there is a comparison and if it is true it would call a function which prints the flag.
```
0x08048665      3d697a0000     cmp eax, 0x7a69
│ ││││ │╭─< 0x0804866a      7517           jne 0x8048683
│ ││││ ││   0x0804866c      83ec0c         sub esp, 0xc
│ ││││ ││   0x0804866f      68bc880408     push str.Wow_such_h4x0r_    ; 0x80488bc ; "Wow such h4x0r!" ; const char *s
│ ││││ ││   0x08048674      e8f7fcffff     call sym.imp.puts           ; int puts(const char *s)
│ ││││ ││   0x08048679      83c410         add esp, 0x10
│ ││││ ││   0x0804867c      e825000000     call sym.giveFlag
```
`eax`is being compared to `0x7a69` which is a hex value. If you convert it to decimal you will find it is `31337`. Going back to the program and using 31337 instead of 1,2 or 3 it would display the flag.
```
└─$ ./crackme7                                                                                                                                                     105 ⨯
Menu:

[1] Say hello
[2] Add numbers
[3] Quit

[>] 31337
Wow such h4x0r!
flag{I have removed flag}
```
### Crackme8
This one of the best challenges in this series of challenges.We are given an elf file which we have to reverse it. We would start by seeing if we can get something juicy by using strings. Well we don't have anything which can help us there.
Let's use `ltrace` to see if the program compares the input we give with any hard coded string.
```
└─$ ltrace ./crackme8 test
__libc_start_main(0x804849b, 2, 0xff9efb94, 0x80485c0 <unfinished ...>
atoi(0xff9f01be, 0xff9efb94, 0xff9efba0, 0x80485e1)                                                     = 0
puts("Access denied."Access denied.
)                                                                                  = 15
+++ exited (status 1) +++
```
Once again it doesn't.
We shall open it with `Radare2` and analyze then seek to main function.
```
─$ r2 crackme8
 -- Beer in mind.
[0x080483a0]> aaaaaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze all functions arguments/locals
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Finding and parsing C++ vtables (avrr)
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information (aanr)
[x] Finding function preludes
[x] Enable constraint types analysis for variables
[0x080483a0]> s main
```
From here, their are two ways to solve the challenge
**i) Method 1**
set a break point on this address`0x080484e4` i.e `db 0x080484e4`.You can start by using `ood test` then `dc` to continue. On that address `eax` is being compared to `0xcafef00d` then jumps to `0x8048502`

```
0x080484e4      3d0df0feca     cmp eax, 0xcafef00d
0x080484e9      7417           je 0x8048502
```
So we can set `eip` to `0x8048502` meaning we have jumped the comparison happening on `0x080484e9`
```
[0xf7f12070]> dc
hit breakpoint at: 0x80484e4
```
To change the `eip` register we shall use `dr eip=0x8048502`
```
[0x080484e4]> dr eip=0x08048502
0x080484e4 ->0x08048502
```
So continue to run the program and you will notice we have bypassed the comparison and now it prints the flag.
```
[0x080484e4]> dc
Access granted.
flag{I have removed flag}
(25658) Process exited with status=0x0
[0xf7f0f559]>
```
**ii) Method 2**
We shall set a breakpoint on the same address which is `0x080484e4`
Now rather than us jumping to `0x8048502` we shall edit  the value of `eax` register to `0xcafef00d` 
```
[0xf7f00070]> dc
hit breakpoint at: 0x80484e4
[0x080484e4]> dr
eax = 0x00000000
ebx = 0x00000000
ecx = 0xff93418f
edx = 0x0000001d
esi = 0x00000002
edi = 0x080483a0
esp = 0xff933b50
ebp = 0xff933b58
eip = 0x080484e4
eflags = 0x00000286
oeax = 0xffffffff
[0x080484e4]>
```
To change the `eax` register we shall use `dr eax=0xcafef00d` and now when we print all the registers, `eax` has been changed.
```
[0x080484e4]> dr
eax = 0xcafef00d
ebx = 0x00000000
ecx = 0xff93418f
edx = 0x0000001d
esi = 0x00000002
edi = 0x080483a0
esp = 0xff933b50
ebp = 0xff933b58
eip = 0x080484e4
eflags = 0x00000286
oeax = 0xffffffff
[0x080484e4]>
```
Register `eax` is being compared to `0xcafef00d` which it's true and it prints the flag.
```
[0x080484e4]> dc
Access granted.
flag{I have removed flag}
(25951) Process exited with status=0x0
[0xf7efd559]>
```
Well this was fun...
