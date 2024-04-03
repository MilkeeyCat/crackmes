# Level 2

[Problem](https://crackmes.one/crackme/65da0fce6d3d2b1fef4be4df)

```bash
$ ./level2
Look at it as cracker

$ ./level2 foo
Look at it as cracker

$ ./level2 foo bar
Look at it as cracker
```

Nothing :( Time to call big boi Ghidra to the rescue.

```c
undefined8 main(void) {
    int iVar1 = strcmp("01234567891","Jsfdb56d65a");

    if (iVar1 == 0) {
        printf("%s is password","01234567891");
    } else {
        printf("Look at it as cracker ");
    }

    return 0;
}
```

Here's basically the whole program. All we gotta do is just to make this
comparison to be evaluated to true. Off the top of my head I can think of two
ways how it can be done. We can change condition from `==` to `!=` or by
replace one string with the other.

## Solution 1

We can call `objdump -d` on da file to find where the condition is placed.
Here's the disassembled main function:

```
0000000000001169 <main>:
    1169:	f3 0f 1e fa          	endbr64
    116d:	55                   	push   %rbp
    116e:	48 89 e5             	mov    %rsp,%rbp
    1171:	48 83 ec 10          	sub    $0x10,%rsp
    1175:	48 8d 05 88 0e 00 00 	lea    0xe88(%rip),%rax        # 2004 <_IO_stdin_used+0x4>
    117c:	48 89 45 f8          	mov    %rax,-0x8(%rbp)
    1180:	48 8b 45 f8          	mov    -0x8(%rbp),%rax
    1184:	48 8d 15 85 0e 00 00 	lea    0xe85(%rip),%rdx        # 2010 <_IO_stdin_used+0x10>
    118b:	48 89 d6             	mov    %rdx,%rsi
    118e:	48 89 c7             	mov    %rax,%rdi
    1191:	e8 da fe ff ff       	call   1070 <strcmp@plt>
    1196:	85 c0                	test   %eax,%eax
    1198:	75 1d                	jne    11b7 <main+0x4e>
    119a:	48 8b 45 f8          	mov    -0x8(%rbp),%rax
    119e:	48 89 c6             	mov    %rax,%rsi
    11a1:	48 8d 05 74 0e 00 00 	lea    0xe74(%rip),%rax        # 201c <_IO_stdin_used+0x1c>
    11a8:	48 89 c7             	mov    %rax,%rdi
    11ab:	b8 00 00 00 00       	mov    $0x0,%eax
    11b0:	e8 ab fe ff ff       	call   1060 <printf@plt>
    11b5:	eb 14                	jmp    11cb <main+0x62>
    11b7:	48 8d 05 6d 0e 00 00 	lea    0xe6d(%rip),%rax        # 202b <_IO_stdin_used+0x2b>
    11be:	48 89 c7             	mov    %rax,%rdi
    11c1:	b8 00 00 00 00       	mov    $0x0,%eax
    11c6:	e8 95 fe ff ff       	call   1060 <printf@plt>
    11cb:	b8 00 00 00 00       	mov    $0x0,%eax
    11d0:	c9                   	leave
    11d1:	c3                   	ret
```

That... looks scary... I think the line we need is `1198` where we can see
`75 1d` instruction. It will jump `1d` bytes down, `0x1198 + 0x1d = 0x11b5`.
So we just find opcode for `je` and put it instead of `jne`. Sounds easy. I
found a [website](https://sparksandflames.com/files/x86InstructionChart.html)
with opcodes and there we can see `jnz` instruction which is basically the same
as `jne`(maybe) and we yoink the opcode of `jz` which is `74`. So now just
replace it. Hit em with good old `xxd level2 | nvim`, find `75 1d`, replace it
and save a file. To transform it back to binary I have a script called
`xxdtobin.sh` which looks like:

```bash
cut -d' ' -f2-9 | xxd -r -p
```

All left to do is call this script and add permission for the file:

```bash
echo edited_file | xxdtobin.sh > solution
chmod +x ./solution
```

That's it, if everything was done correctly it has to output something new
(probably)

```bash
$ ./solution
01234567891 is password
```

YEY

## Solution 2

```bash
sed 's/Jsfdb56d65a/01234567891/' level2 > solution
chmod +x solution
```

That's it =]

```bash
$ ./solution
01234567891 is password
```
