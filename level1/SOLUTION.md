## Level1

[Problem](https://crackmes.one/crackme/65d490306d3d2b1fef4be1ad)

First of all I gotta try to call it with and without arguments(maybe it's
already solved and I dont have to do anything ¯\\_(ツ)_/¯)

```bash
$ ./level1
Try more ... 1/5

$ ./level1 foo
Try More ....2/5

$ ./level1 foo bar baz
Try more ... 1/5
```

Ok, it's not solved. But it seems like count of args matters. If I had skill I
would try to read assembly of dis program and try to understand what it does
but what Im gonna do is click "Import" button in
[Ghidra](https://github.com/NationalSecurityAgency/ghidra) and get
representation of this exeutable in C. And here's how `main` function looks
like:

```c
undefined8 main(int param_1,char **param_2) {
    char *pcVar1;

    if (param_1 == 2) {
        pcVar1 = *param_2;

        if ((int)*pcVar1 + (int)*param_2[1] == 0x6e) {
            if ((((pcVar1[3] == 'n') || (pcVar1[4] == 'i')) || (pcVar1[5] == 'm')) || (pcVar1[6] == 'a')) {
                test((int)pcVar1[3],(int)*param_2[1]);
            } else {
                printf("Try more .... 3/5");
            }
        } else {
            printf("Try More ....2/5");
        }
    } else {
        printf("Try more ... 1/5");
    }

    return 0;
}
```

Eeeh. That's it. Problem solved xd. Executable 4th character has to be 'n' or
5th - 'i', 6th - 'm', 7th - 'a', also first character of program name(it's in
form of `./foo` so it's basically it's `.`) + first character of second
argument has to be equal to 110.

Numeric value of `.` is 47, so 110 - 46 = 64, which corresponds to `@`
character. To solve this problem we gotta rename exectable to `_nima`(there're
many variations which satisfy this condition) for example and pass `@` as
argument.

```bash
$ ./_nima @
true point 5/5
```
