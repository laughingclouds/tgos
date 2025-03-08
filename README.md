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

### Logs
A copy of relevant logs from any scripts that were run.

#### Checking system requirements


<details>
<summary>`bash scripts/version-check.sh`</summary>
</br>
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
</details>