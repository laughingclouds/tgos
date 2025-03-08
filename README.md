# tgos
The Generic Operating System

## Journal
For recording my progress on this project. This section will serve as a primitive form of a timeline.

### System Information
The main work is done on a debian (bookworm ver. 12.9.0) distro installed on hyper-v with a virtual hard disk of 70 GB.

- OS: Debian GNU/Linux 12 (bookworm) x86_64 
- Host: Virtual Machine Hyper-V UEFI Release v4.1
- Kernel: 6.1.0-31-amd64
- Shell: bash 5.2.15
- DE: Xfce 4.18

### Slight change(s)
Instead of using the default linker (ld); which is quite slow I have opted to use [mold](https://github.com/rui314/mold?tab=readme-ov-file#compile-mold).
I will attempt to use it and see if I succeed. This is also a completely optional step. If this step works, I 
might update [version-check.sh](scripts/version-check.sh) to incorporate that change.

I have also decided against using a swap partition. I have enough ram for both my host OS and guest OS.

Aside from these, I'm also going to prioritize using GUI tools whenever they make my life easier. For example,
using GParted instead of `fdisk` and `mkfs` since I don't know these tools very well. Learning them takes a lower priority for now.

### Logs
A copy of relevant logs from any scripts that were run.

#### Checking host system requirements


<details>
<summary><code>bash scripts/version-check.sh</code></summary>
  
```bash 
OK:    Coreutils 9.1    >= 8.1
OK:    Bash      5.2.15 >= 3.2
ERROR: Cannot find ld (Binutils)
ERROR: Cannot find bison (Bison)
OK:    Diffutils 3.8    >= 2.8.1
OK:    Findutils 4.9.0  >= 4.2.31
ERROR: Cannot find gawk (Gawk)
ERROR: Cannot find gcc (GCC)
ERROR: Cannot find g++ (GCC (C++))
OK:    Grep      3.8    >= 2.5.1a
OK:    Gzip      1.12   >= 1.3.12
ERROR: Cannot find m4 (M4)
ERROR: Cannot find make (Make)
OK:    Patch     2.7.6  >= 2.5.4
OK:    Perl      5.36.0 >= 5.8.8
OK:    Python    3.11.2 >= 3.4
OK:    Sed       4.9    >= 4.1.5
OK:    Tar       1.34   >= 1.22
ERROR: Cannot find texi2any (Texinfo)
OK:    Xz        5.4.1  >= 5.0.0
OK:    Linux Kernel 6.1.0 >= 5.4
OK:    Linux Kernel supports UNIX 98 PTY
Aliases:
ERROR: awk  is NOT GNU
ERROR: yacc is NOT Bison
ERROR: sh   is NOT Bash
Compiler check:
scripts/version-check.sh: line 93: g++: command not found
ERROR: g++ does NOT work
OK: nproc reports 8 logical cores are available
```

Most of these errors can be fixed by:
```bash
sudo apt install build-essential
```

**Cannot find texi2any (TexInfo)**: `sudo apt install texinfo`

**awk is NOT GNU** | **yacc is NOT Bison**: `sudo apt install gawk bison`

<details>
<summary><strong>sh is NOT Bash</strong></summary>

The [`chsh`](https://manpages.ubuntu.com/manpages/focal/en/man1/chsh.1.html) command did NOT work on my system.

So I resorted to a rather unsightly solution:

```bash
sudo ln -sf /usr/bin/bash /usr/bin/sh
```
</details>
</details>