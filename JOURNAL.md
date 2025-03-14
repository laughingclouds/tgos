# Journal
For recording my progress on this project. This section will serve as a primitive form of a timeline.

**These are NOT instructions. Simply a record of what I did to make _my_ version of LFS. There can be little 
changes that may make my installation process different than what the LFS books intends.**

## System Information
The main work is done on a debian (bookworm ver. 12.9.0) distro installed on hyper-v with a virtual hard disk of 70 GB.

- OS: Debian GNU/Linux 12 (bookworm) x86_64 
- Host: Virtual Machine Hyper-V UEFI Release v4.1
- Kernel: 6.1.0-31-amd64
- Shell: bash 5.2.15
- DE: Xfce 4.18

## Slight change(s)
Instead of using the default linker (ld); which is quite slow I have opted to use [mold](https://github.com/rui314/mold?tab=readme-ov-file#compile-mold).
I will attempt to use it and see if I succeed. This is also a completely optional step. If this step works, I 
might update [version-check.sh](scripts/version-check.sh) to incorporate that change.

I have also decided against using a swap partition. I have enough ram for both my host OS and guest OS.

Aside from these, I'm also going to prioritize using GUI tools whenever they make my life easier. For example,
using GParted instead of `fdisk` and `mkfs` since I don't know these tools very well. Learning them takes a lower priority for now.

## Logs
A copy of relevant logs from any scripts that were run.

### Checking host system requirements


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


### LFS Variable, umask, mounting

<details>
  
In `.bashrc` for users **root** and **laughingclouds** (replace with your username):

```bash
export LFS=/mnt/lfs
umask 022    # my systems default value was already 0022
```

For mounting the lfs parition:

```bash
sudo mkdir -pv /mnt/$LFS    # parents, verbose
sudo mount -v -t ext4 /dev/sda3 $LFS    # verbose, type
```

For automatically mounting on every startup `sudo nano /etc/fstab`:

```bash
# new lfs partition /dev/sda3
/dev/sda3 /mnt/lfs      ext4    defaults        1       1
```
</details>


### Fetch sources

First get the list of required 3rd party sources from the LFS site:

```bash
cd $LFS
wget _LIST_LINK_ -O wget-list-sysv
```

And verify the packages using `md5sum`.


### Building binutils

For using with mold you can use `LDFLAGS="-fuse-ld=mold"` with the `configure` program.

```bash
time { ../configure LDFLAGS="-fuse-ld=mold" --prefix=$LFS/tools --with-sysroot=$LFS --target=$LFS_TGT --disable-nls --enable-gprofng=no --disable-werror --enable-new-dtags --enable-default-hash-style=gnu && make && make install; }
```

### Building gcc

**Slight change**:
Since, the final command `cat gcc/limitx.h gcc/glimits.h gcc/limity.h...` could not recognize my built gcc under `$LFS/tools/bin`
I made a slight addition to the above command:

```bash
cat gcc/limitx.h gcc/glimits.h gcc/limity.h >   `dirname $($LFS/tools/bin/$LFS_TGT-gcc -print-libgcc-file-name)`/include/limits.h
```


### Linux API Headers

**Slight change**:
LFS takes a convoluted but more easily-generically-applied-safer approach because rsync may not be available (which it wasn't on my Debian 12 distro).

```bash
sudo apt install rsync
```

And now we can use the `INSTALL_HDR_PATH` variable to install the API headers directly to $LFS/usr

```bash
sudo make INSTALL_HDR_PATH=$LFS/usr headers_install
```

### Building Glibc

I could not for the life of me make the relative paths work when creating symbolic links.
Plus, my host system (debian 12 bookworm) has the required dynamic linker (or loader) under /lib64 (which itself is a link to the `/lib/x86_64-linux-gnu/...` file).

So I ended up using absolute paths.

```bash
ln -sv /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2 $LFS/lib64/
ln -sv /lib/x86_64-linux-gnu/ld-linux-x86-64.so.2 $LFS/lib64/ld-linux-x86-64.so.3
```

