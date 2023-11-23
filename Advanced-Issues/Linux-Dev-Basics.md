# Development on Linux

Linux (/ˈlɪnʊks/ LIN-uuks)is a family of open-source Unix-like operating systems based on the Linux kernel,an operating system kernel first released on September 17, 1991, by Linus Torvalds.

Linux was originally developed for personal computers based on the Intel x86 architecture, but has since been ported to more platforms than any other operating system. Linux is now the leading operating system on servers (over 96.4% of the top 1 million web servers' operating systems are Linux).

So it would be very useful if you know how to develop and run programs on Linux.

## Before we start

On Linux, software development tools are usually cut into independent small tools, each of which handles different problems. Taking C language software development as an example, editors (emacs, vim, etc.) are used to edit programs, debuggers (gdb) are used to debug programs, compilers (GCC) are used to compile and link programs, and performance analysis tools ( gcov, gprof) are used to optimize programs, the document generator (doxygen) is used to generate documents, etc.

In this issue, we will use the most basic Vim editor and Gcc compiler to demonstrate how to perform the most basic software development on Linux. Before officially starting, we must first check whether these softwares exist in the environment and their versions.

Use `which` command to check whether required tools are existed in your environment.

```
which gcc
which vim
which tar
```

If not existed, use the following command to install it

```
sudo yum install -y gcc vim tar
```

p.s. if it asks for password, the default password is `openEuler12#$`

You can use the following command to check whether the required tools has correct version.

```
gcc --version
vim --version
```

## Hello World!

Let's write our first C program on Linux, yes, the `Hello World!`

### Step 1

Select your favourite folder and create our working dir:

```
mkdir {some folder name}
cd {some folder name}
```

Use vim to write code:

```
vim hello.c
```

Hit either `a` `i` `o` or `s` on your keyboard and entering the edit mode.

Write down the following codes:

```
#include <stdio.h>

int main(void){
    printf("Hello, World!\n");
}
```

Hit `ESC` on your keyboard to enter the command mode and enter `:wq` to save and exit.

### Step 2

When we talk about programs, we usually refers to a program that can be run directly after double-clicking it. Such a program is called an executable program (Executable Program). Under Windows, the suffixes of executable programs are .exe and .com (where .exe More common); Under UNIX-like systems (Linux, Mac OS, etc.), executable programs do not have a specific suffix, and the system determines whether it is an executable program based on the header information of the file.

The interior of an executable program is a collection of computer instructions and data. They are all in binary form and can be directly recognized by the CPU without any obstacles; but for programmers, they are very obscure and difficult to remember and use. Therefore, programs such as ours were born. High-level programming languages such as the C language program you just wrote.

But for the CPU, the C language code is a bible, which is not recognized at all. The CPU only recognizes a few hundred instructions in binary form. This requires a tool to convert the C language code into binary instructions that the CPU can recognize, that is, to process the code into an executable program; this tool is a special software called a compiler.

GCC developed by the GUN organization is commonly used under Linux, and Linux distributions usually come with GCC.

In this step we will use `gcc` command to compile our code:

```
gcc hello.c -o hello
```

Checkout what we have built:

```
ls -l
```

Run our code:

```
./hello
```

## Step 3

In most cases, our codes are far more complicated, we could have one function callling functions from other file. In this step, we will see how to build a more complicated application.

We have some codes for you, download them using the following command:

```

```
