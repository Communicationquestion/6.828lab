

# MIT6.828



## 环境准备



### 所需环境

1. #### 系统 Ubuntu 20.04.3 LTS

2. #### 软件 git

2. #### [qemu](git clone https://github.com/mit-pdos/6.828-qemu.git qemu)

4. #### lab [jos](https://pdos.csail.mit.edu/6.828/2018/jos.git)

3. #### [官方教学网站]([6.828 / Fall 2018 (mit.edu)](https://pdos.csail.mit.edu/6.828/2018/index.html))

### 步骤

1. #### 系统安装

2. #### ubuntu换源

   ##### linux命令

   ```shell
   sudo vi /etc/apt/sources.list
   ```

   [源](https://developer.aliyun.com/mirror)

   ```
   #添加阿里源
    deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
   ```

3. #### ubuntu常用命令

    ```shell
   sudo apt-get update
   sudo apt-get -f install
   sudo apt-get upgrade
   sudo apt-get purge softwarename
   ```
   
4. #### vmTools安装

   ##### 解压

   ```shell
   cd vmtoolfile
   sudo ./INSTALL
   ```

5. #### qemu安装

   ```shell
   git clone https://github.com/mit-pdos/6.828-qemu.git qemu
   ```
   ```shell
   cd qemu
   ```
   
   ```shell
   ./configure --disable-kvm --disable-werror --target-list="i386-softmmu x86_64-softmmu" --python=/usr/bin/python2.7
   ```
   
   ```shell
   make
   ```
   
   ```shell
   sudo make install
   ```
   
   ##### Erorr
   
   ```
   EROOR: 
       /usr/bin/ld: qga/commands-posix.o: in function `dev_major_minor':
       /home/bs/learning/6.828/qemu/qga/commands-posix.c:633: undefined reference to `major'
       /usr/bin/ld: /home/bs/learning/6.828/qemu/qga/commands-posix.c:634: undefined reference to `minor'
       collect2: 错误：ld 返回 1
   ```
   
   ```c
   qemu/qga/中
   commands-posix.c文件中加上头文件<sys/sysmacros.h>
   ```
   
   ```
   EROOR: /usr/bin/ld: ../hw/9pfs/virtio-9p.o: in function `stat_to_v9stat':
       /home/bs/learning/6.828/qemu/hw/9pfs/virtio-9p.c:796: undefined reference to `minor'
       /usr/bin/ld: /home/bs/learning/6.828/qemu/hw/9pfs/virtio-9p.c:796: undefined reference to `major'
       /usr/bin/ld: ../hw/9pfs/virtio-9p.o: in function `v9fs_mknod':
       /home/bs/learning/6.828/qemu/hw/9pfs/virtio-9p.c:2803: undefined reference to `makedev'
       /usr/bin/ld: ../hw/9pfs/virtio-9p.o: in function `v9fs_create':
       /home/bs/learning/6.828/qemu/hw/9pfs/virtio-9p.c:2124: undefined reference to `makedev'
       collect2: 错误：ld 返回 1
       make[1]: *** [Makefile:182：qemu-system-i386] 错误 1
       make: *** [Makefile:173：subdir-i386-softmmu] 错误 2
   ```
   
   ```
   在qemu/hw/9pfs/virtio-9p.c文件中加上头文件<sys/sysmacros.h>
   ```
   
   ```
   ERROR: DTC (libfdt) not present. Your options:
            (1) Preferred: Install the DTC (libfdt) devel package
            (2) Fetch the DTC submodule, using:
                git submodule update --init dtc
   ```
   
   ```shell
   git submodule update --init dtc
   ```
   
   ```shell
   ERORR: 
   	VNC server running on `127.0.0.1:5900′
   ```
   
   ```shell
   sudo apt-get install libsdl1.2-dev
   重新编译
   ```
   
   

## Lab1

###  Part 1: PC Bootstrap

#### The ROM BIOS

1. ##### 开启方式

   ~~~shell
   make qemu-gdb
   make gdb
   Ctrl a x 退出
   ~~~

    ```
    The following line:
    
    [f000:fff0] 0xffff0:	ljmp   $0xf000,$0xe05b
    is GDB's disassembly of the first instruction to be executed. From this output you can conclude a few things:
    
    The IBM PC starts executing at physical address 0x000ffff0, which is at the very top of the 64KB area reserved for the ROM BIOS.
    The PC starts executing with CS = 0xf000 and IP = 0xfff0.
    The first instruction to be executed is a jmp instruction, which jumps to the segmented address CS = 0xf000 and IP = 0xe05b
    ```

2. ##### gdb的使用

   ~~~
   GDB
   See the GDB manual for a full guide to GDB commands. Here are some particularly useful commands for 6.828, some of which don't typically come up outside of OS development.
   
   Ctrl-c
   Halt the machine and break in to GDB at the current instruction. If QEMU has multiple virtual CPUs, this halts all of them.
   c (or continue)
   Continue execution until the next breakpoint or Ctrl-c.
   si (or stepi)
   Execute one machine instruction.
   b function or b file:line (or breakpoint)
   Set a breakpoint at the given function or line.
   b *addr (or breakpoint)
   Set a breakpoint at the EIP addr.
   set print pretty
   Enable pretty-printing of arrays and structs.
   info registers
   Print the general purpose registers, eip, eflags, and the segment selectors. For a much more thorough dump of the machine register state, see QEMU's own info registers command.
   x/Nx addr
   Display a hex dump of N words starting at virtual address addr. If N is omitted, it defaults to 1. addr can be any expression.
   x/Ni addr
   Display the N assembly instructions starting at addr. Using $eip as addr will display the instructions at the current instruction pointer.
   symbol-file file
   (Lab 3+) Switch to symbol file file. When GDB attaches to QEMU, it has no notion of the process boundaries within the virtual machine, so we have to tell it which symbols to use. By default, we configure GDB to use the kernel symbol file, obj/kern/kernel. If the machine is running user code, say hello.c, you can switch to the hello symbol file using symbol-file obj/user/hello.
   QEMU represents each virtual CPU as a thread in GDB, so you can use all of GDB's thread-related commands to view or manipulate QEMU's virtual CPUs.
   
   thread n
   GDB focuses on one thread (i.e., CPU) at a time. This command switches that focus to thread n, numbered from zero.
   info threads
   List all threads (i.e., CPUs), including their state (active or halted) and what function they're in.
   
2. #####  Learning
   
   ~~~
   
   The IBM PC starts executing at physical address 0x000ffff0, which is at the very top of the 64KB area reserved for the ROM BIOS.
   
   The PC starts executing with CS = 0xf000 and IP = 0xfff0.
   
   The first instruction to be executed is a jmp instruction, which jumps to the segmented address CS = 0xf000 and IP = 0xe05b.
   ~~~





### Part 2:The Boot Loader

1. ##### Learning

   ```
   Floppy and hard disks for PCs are divided into 512 byte regions called sectors. A sector is the disk's minimum transfer granularity: each read or write operation must be one or more sectors in size and aligned on a sector boundary. If the disk is bootable, the first sector is called the boot sector, since this is where the boot loader code resides. When the BIOS finds a bootable floppy or hard disk, it loads the 512-byte boot sector into memory at physical addresses 0x7c00 through 0x7dff, and then uses a jmp instruction to set the CS:IP to 0000:7c00, passing control to the boot loader. Like the BIOS load address, these addresses are fairly arbitrary - but they are fixed and standardized for PCs.
   
   0x7c00 开始执行 boot.s 
   cli 关闭中断、
   cld 用于将 DF 位（Direction Flag） 置零，DF 用于串操作指令中决定内存地址的变化方向，DF 置零使得串操作朝地址增加方向。
   
   ```

2. ##### Exercise3

   ```
   Q1: At what point does the processor start executing 32-bit code? What exactly causes the switch from 16- to 32-bit mode?
       A: # Switch from real to protected mode, using a bootstrap GDT
          # and segment translation that makes virtual addresses 
          # identical to their physical addresses, so that the 
          # effective memory map does not change during the switch.
          lgdt    gdtdesc
          movl    %cr0, %eax
          orl     $CR0_PE_ON, %eax
          movl    %eax, %cr0 
   
   Q2: What is the last instruction of the boot loader executed, and what is the first instruction of the kernel it just loaded?
   	A: ((void (*)(void)) (ELFHDR->e_entry))();
   
   Q3: Where is the first instruction of the kernel?
       A: movw 0x1234 0x472
       
   Q4: How does the boot loader decide how many sectors it must read in order to fetch the entire kernel from disk? Where does it find this information?
       A:	pa < end_pa 并且我们使用offset来控制需要读的扇区号。
   
   ```

#### Loading the Kernel

1. ##### Learning

   ```
   ELF二进制文件 binary image such as obj/kern/kernel
   	The C definitions for these ELF headers are in inc/elf.h
   	
   	.text: The program's executable instructions.
   	
   	.rodata: Read-only data, such as ASCII string constants produced by the C compiler. (We will not 			bother setting up the hardware to prohibit writing, however.)
   	
   	.data: The data section holds the program's initialized data, such as global variables declared with 		 initializers like int x = 5;.
   查看ELF相关信息
   	objdump -h obj/kern/kernel
   ```

2. ##### Exercise6

   ```
   重置计算机（退出 QEMU/GDB 并重新启动它们）。检查在 BIOS 进入引导加载程序时0x00100000处的 8 个字内存，然后在引导加载程序进入内核时再次检查。为什么它们不同？第二个断点处有什么？
   	x/8x 0x00100000
   ```

   

### Part 3: The Kernel

1. ##### Learning

   ```
   For now, we'll just map the first 4MB of physical memory, which will be enough to get us up and running. We do this using the hand-written, statically-initialized page directory and page table in kern/entrypgdir.c. For now, you don't have to understand the details of how this works, just the effect that it accomplishes. Up until kern/entry.S sets the flag, memory references are treated as physical addresses (strictly speaking, they're linear addresses, but boot/boot.S set up an identity mapping from linear addresses to physical addresses and we're never going to change that). Once is set, memory references are virtual addresses that get translated by the virtual memory hardware to physical addresses. translates virtual addresses in the range 0xf0000000 through 0xf0400000 to physical addresses 0x00000000 through 0x00400000, as well as virtual addresses 0x00000000 through 0x00400000 to physical addresses 0x00000000 through 0x00400000. Any virtual address that is not in one of these two ranges will cause a hardware exception which, since we haven't set up interrupt handling yet, will cause QEMU to dump the machine state and exit (or endlessly reboot if you aren't using the 6.828-patched version of QEMU).
   
   ```

#### Formatted Printing to the Console

1. Learning

   ```
   Explain the interface between printf.c and console.c. Specifically, what function does console.c export? 
   How is this function used by printf.c?
   
   Explain the following from console.c:
   1      if (crt_pos >= CRT_SIZE) {
   2              int i;
   3              memmove(crt_buf, crt_buf + CRT_COLS, (CRT_SIZE - CRT_COLS) * sizeof(uint16_t));
   4              for (i = CRT_SIZE - CRT_COLS; i < CRT_SIZE; i++)
   5                      crt_buf[i] = 0x0700 | ' ';
   6              crt_pos -= CRT_COLS;
   7      }
       这段代码的意思是，当屏幕满的时候，滚屏。屏幕可以显示的是25*80字节的内容。所以if (crt_pos >= CRT_SIZE)判断当前光标位置是否超过了屏幕。很自然的下面的代码就是为滚屏服务的，memove()函数就是为了在复制内容。新空出来的一行，以黑底白字，空格填充,最后光标位置减80。这个函数当中的其他部主要处理的都是改变光表位置，比如\n，光标位置+80。
   
   
   For the following questions you might wish to consult the notes for Lecture 2. These notes cover GCC's calling convention on the x86.
   Trace the execution of the following code step-by-step:
   
   int x = 1, y = 3, z = 4;
   cprintf("x %d, y %x, z %d\n", x, y, z);
   In the call to cprintf(), to what does fmt point? To what does ap point?
   List (in order of execution) each call to cons_putc, va_arg, and vcprintf. For cons_putc, list its argument as well. For va_arg, list what ap points to before and after the call. For vcprintf list the values of its two arguments.
   
   Run the following code.
       unsigned int i = 0x00646c72;
       cprintf("H%x Wo%s", 57616, &i);
   What is the output? Explain how this output is arrived at in the step-by-step manner of the previous exercise. Here's an ASCII table that maps bytes to characters.
   The output depends on that fact that the x86 is little-endian. If the x86 were instead big-endian what would you set i to in order to yield the same output? Would you need to change 57616 to a different value?
   
   Here's a description of little- and big-endian and a more whimsical description.
   	ascii码
   In the following code, what is going to be printed after 'y='? (note: the answer is not a specific value.) Why does this happen?
       cprintf("x=%d y=%d", 3);
       如果fmt里面指定的格式化字符串数目大于实际参数数目，因为缺少参数，而由可变参数的方式知道会打印第一个参数之上的栈里面的4字节内容，在我的实验环境显示的是 x=3 y=-267321364。
       
   Let's say that GCC changed its calling convention so that it pushed arguments on the stack in declaration order, so that the last argument is pushed last. How would you have to change cprintf or its interface so that it would still be possible to pass it a variable number of arguments?
   	如果GCC参数入栈方式改为从左往右，则可能需要加一个表示参数个数的参数传到cprintf函数中以获取可变参数。
   
   以上问题需理解源码
   	print();的实现，理解调用过程
   	可变参数，对指针的理解，调试过程中留意数据的入栈
   	ascii码
   ```

2. ##### Exercise8

   ```
   Exercise 8. We have omitted a small fragment of code - the code necessary to print octal numbers using patterns of the form "%o". Find and fill in this code fragment.
   	num = getuint(&ap, lflag);
       base = 8;
       goto number;
   ```

#### The Stack

1. ##### Exercise9

   ```
   Exercise 9. Determine where the kernel initializes its stack, and exactly where in memory its stack is located. How does the kernel reserve space for its stack? And at which "end" of this reserved area is the stack pointer initialized to point to?
   	1.# Set the stack pointer
   		movl	$(bootstacktop),%esp
   		f0100034:	bc 00 00 11 f0       	mov    $0xf0110000,%esp
   	2.看 kern/entry.S 
   	
   	
   ```
   
2. ##### Learning

   ```
   深入理解函数调用堆栈
   ```

3. ##### Exercise10

   ```
   Exercise 10. To become familiar with the C calling conventions on the x86, find the address of the test_backtrace function in obj/kern/kernel.asm, set a breakpoint there, and examine what happens each time it gets called after the kernel starts. How many 32-bit words does each recursive nesting level of test_backtrace push on the stack, and what are those words?
   
   Note that, for this exercise to work properly, you should be using the patched version of QEMU available on the tools page or on Athena. Otherwise, you'll have to manually translate all breakpoint and memory addresses to linear addresses.
     在test_backtrace 打断点
     查看ebp esp
   ```

4. ##### Exercise11

   ```
   int i;
   	uint32_t eip;
   	uint32_t* ebp = (uint32_t *)read_ebp();
   
   	while (ebp) {
   		eip = *(ebp + 1);
   		cprintf("ebp %x eip %x args", ebp, eip);
   		uint32_t *args = ebp + 2;
   		for (i = 0; i < 5; i++) {
   			uint32_t argi = args[i];
   			cprintf(" %08x ", argi);
   		}
   		cprintf("\n");
   		ebp = (uint32_t *) *ebp;
   	}
   	return 0;
   ```

5. ##### Exercise12

   ```
   最终debuginfo_eip函数添加代码如下
       stab_binsearch(stabs, &lline, &rline, N_SLINE, addr);
       if (lline <= rline) {
           info->eip_line = stabs[rline].n_desc;
       } else {
           return -1;
       }
   
   mon_backtrace添加代码如下：
       int mon_backtrace(int argc, char **argv, struct Trapframe *tf)
           {
              ...
               while (ebp) {
                   ...
                   struct Eipdebuginfo debug_info;
                   debuginfo_eip(eip, &debug_info);
                   cprintf("\t%s:%d: %.*s+%d\n",
                       debug_info.eip_file, debug_info.eip_line, debug_info.eip_fn_namelen,
                       debug_info.eip_fn_name, eip - debug_info.eip_fn_addr);
                   ...2
               }
               return 0;
           }
   ```

## Lab2

