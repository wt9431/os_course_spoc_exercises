#lec 3 SPOC Discussion

## 第三讲 启动、中断、异常和系统调用-思考题

## 3.1 BIOS
 1. 比较UEFI和BIOS的区别。
 
 - UEFI是BIOS的一种升级替代方案。BIOS用于加载电脑最基本的程序码，负责在开机时初始化硬件、检测硬件功能以及引导操作系统。而UEFI用于操作系统自动从预启动的操作环境加载到一种操作系统上，从而使得开机程序化繁为简节省时间。UEFI通过保护预启动或预引导进程抵御bootkit攻击，安全性更高；启动时间较BIOS更短；比BIOS的兼容性更好。
 
 1. 描述PXE的大致启动流程。
 
 - 1 通过UDP协议发送受限广播(255.255.255.255),目标端口67, 是DHCP命令中的DHCP discover,带PXEClient标志,并在UDP 68端口监听。
 - 2 DHCP服务器在67端口收到请求后,通过UDP协议发送受限广播(255.255.255.255)，目标端口68,是DHCP命令中的DHCPoffer,附带客户端ip,网关,tftp服务器ip,子网掩码,可引导的文件名等.
 - 3 客户机在68端口收到服务器回复的信息,客户机配置自身ip，从此时起，客户机可以使用单播通讯了。
 - 4 客户端通过tftp协议发送获取引导文件的命令,并将获取的引导文件存放在地址 0:7c00 （第3步中获取的引导文件名）.
 - 5.跳转到0:7c00开始执行引导文件中的代码,从此时起cpu控制权己交给引导文件.
    
## 3.2 系统启动流程
 1. 了解NTLDR的启动流程。

 - 1 电源自检程序开始运行
 - 2 主引导记录被装入内存，并且程序开始执行
 - 3 活动分区的引导扇区被装入内存
 - 4 NTLDR从引导扇区被装入并初始化
 - 5 将处理器的实模式改为32位平滑内存模式
 - 6 NTLDR开始运行适当的小文件系统驱动程序。小文件系统驱动程序是建立在NTLDR内部的，它能读FAT或NTFS。
 - 7 NTLDR读boot.ini文件
 - 8 NTLDR装载所选操作系统。如果windows NT/windows 2000/windows XP/windows server2003这些操作系统被选择，NTLDR运行
Ntdetect。对于其他的操作系统，NTLDR装载并运行Bootsect.dos然后向它传递控制。windows NT过程结束。
 - 9 Ntdetect搜索计算机硬件并将列表传送给NTLDR，以便将这些信息写进\\HKE Y_LOCAL_MACHINE\HARDWARE中。
 - 10 然后NTLDR装载Ntoskrnl.exe，Hal.dll和系统信息集合。
 - 11 Ntldr搜索系统信息集合，并装载设备驱动配置以便设备在启动时开始工作
 - 12 Ntldr把控制权交给Ntoskrnl.exe，这时,启动程序结束,装载阶段开始
    
 1. 了解GRUB的启动流程。
 
 - BIOS的任务完成后，将系统启动的控制权移交到MBR部分的代码。
 - 1.装载stage1
 - 2.装载stage1.5
 - 3.装载stage2
 - 4.读取/boot/grub.conf文件并显示启动菜单
 - 5.装载所选的kernel和initrd文件到内存中
 
 1. 比较NTLDR和GRUB的功能有差异。
 
 - NTLDR是xp之前的windows系统的引导文件，只能装在硬盘
 - GRUB是第三方操作系统引导器，可以引导硬盘、光盘、网络、u盘、winxp、winpe、win7、linux、dos....
    
 1. 了解u-boot的功能。
 
 - 1.初始化处理器及外围的硬件资源，配置SDRAM控制器，为主程序提供运行环境，串口初始化，提供交互终端网络，传输镜像文件及其他I/O设备。
 - 2.执行系统自检，报告检测结果。
 - 3.引导操作系统
 - 4.根据系统命令烧写惊醒文件
    
## 3.3 中断、异常和系统调用比较
 1. 什么是中断、异常和系统调用？

 - 中断和异常都是中断当前指令流，去执行其他指令的机制。中断用于硬件设备向CPU发送通知，例如计时器中断、键盘中断等，也可以使用指令触发中断，称为软中断。异常发生于程序指令执行出错，比如除零异常、页异常、权限异常，即使程序没有逻辑错误也可能发生异常，比如页异常发生于程序所需要的内存页并不在内存中，这是由操作系统的页管理机制决定的，与用户程序无关。系统调用用于用户态程序执行内核态代码，系统调用限制了用户态程序可以执行内核代码的范围和能力。

 2. 中断、异常和系统调用的处理流程有什么异同？

 - 当中断、异常或者系统调用发生时，CPU保存当前的CPU状态，切换到对应的中断、异常、系统调用执行状态，并跳转到预设的处理指令处开始执行，直到处理程序执行完成后，恢复被中断前的CPU状态。
 - 在x86系统上，中断、异常与系统调用在进入与退出时的处理流程相似，不同是系统调用简化了切换流程，用于降低系统调用的代价。

 3. 举例说明Linux中有哪些中断，哪些异常？
 
 - 中断：计时器中断、键盘中断、串口中断、不可屏蔽中断.
 - 异常：代数异常、浮点运算器异常、通用保护异常、页异常、总线异常、断点异常、越界异常。

 4. 以ucore lab8的answer为例，uCore的时钟中断处理流程。


 1. Linux的系统调用有哪些？大致的功能分类有哪些？  (w2l1)

 - linux的系统调用大概有250个，主要分为进程控制、文件操作、系统控制、内存管理、网络管理、用户管理、进程间通信这几类
 
 1. 以ucore lab8的answer为例，uCore的系统调用有哪些？大致的功能分类有哪些？(w2l1)
 
 - ucore的系统调用有22个，大致的功能分类有进程控制、文件操作、系统控制、内存管理、用户管理。
 
## 3.4 linux系统调用分析
 1. 通过分析[lab1_ex0](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex0.md)了解Linux应用的系统调用编写和含义。(w2l1)
 
 - objdump的作用是反汇编目标文件，nm的作用是显示一个目标文件中的所有符号，file的作用是查看文件类型。
 - 系统调用是linux内核中设置的一组用于实现系统功能的子程序，通过系统调用，应用程序可以访问硬件设备和其他操作系统资源，使得内核可以和用户空间上运行的进程进行交互，并且保证系统稳定可靠。

 
 1. 通过调试[lab1_ex1](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex1.md)了解Linux应用的系统调用执行过程。(w2l1)
 
 - strace可以跟踪到一个进程产生的系统调用,包括参数，返回值，执行消耗的时间。
 - 用户程序中执行系统调用，通过调用C库中的函数（如printf函数）
 - 此时，cpu会执行多种系统调用（以用户程序调用printf函数为例）：
  - 首先调用execve（在父进程中执行子进程），再调用brk（分配内存，并返回当前堆大小），再调用access（获得文件的访问权限），再调用open（打开文件），fstat（获得文件信息），mmap（把文件映射到内存)。之后通过mprotect更改内存段的访问权限，调用arch_prctl设置架构的线程状态，调用muamap取消映射，最后调用write打印字符串，exit_group退出线程。

 
## 3.5 ucore系统调用分析
 1. ucore的系统调用中参数传递代码分析。
 
在进入中断时，CPU已经将寄存器状态保存到栈上，在中断vector入口和__alltraps中，错误码和中断号、其他段寄存器也被保存到栈上，组成struct trapframe，该结构的指针被当作参数传递到trap()中，并保存到current中（因为syscall一定发生在用户态）。syscall的参数被保存在寄存器中，在syscall()中，这些参数从寄存器中读取到参数数组中（最多五个参数），并传递给syscall的真正执行函数。

   void
   syscall(void) {
       struct trapframe *tf = current->tf;
       uint32_t arg[5];
       int num = tf->tf_regs.reg_eax;
       if (num >= 0 && num < NUM_SYSCALLS) {
           if (syscalls[num] != NULL) {
               arg[0] = tf->tf_regs.reg_edx;
               arg[1] = tf->tf_regs.reg_ecx;
               arg[2] = tf->tf_regs.reg_ebx;
               arg[3] = tf->tf_regs.reg_edi;
               arg[4] = tf->tf_regs.reg_esi;
               tf->tf_regs.reg_eax = syscalls[num](arg);
               return ;
           }
       }
       print_trapframe(tf);
       panic("undefined syscall %d, pid = %d, name = %s.\n",
               num, current->pid, current->name);
   }


 1. 以getpid为例，分析ucore的系统调用中返回结果的传递代码。

   sys_getpid()返回当前进程的进程号，返回值在syscall()中被赋值给eax在栈中的位置，在中断退出时，该值被弹入eax并返回给用户态程序。

 1. 以ucore lab8的answer为例，分析ucore 应用的系统调用编写和含义。
 
  1. exit 结束当前进程
  2. fork 创建子进程
  3. wait 等待子进程结束
  4. exec 在当前进程中加载其他可执行程序
  5. yield 提前让出CPU时间
  6. kill 给特定进程发送信号
  7. getpid 返回当前进程的PID
  8. putc 输出一个字符
  9. pgdir 返回当前页目录地址
  10. gettime 获得系统时间
  11. lab6_set_priority 设置系统优先级
  12. sleep 进程睡眠一段时间
  13. open 打开一个文件
  14. close 关闭一个文件
  15. read 读取一个文件
  16. write 向一个文件写入
  17. seek 修改一个文件的访问偏移量
  18. fstat 查询一个文件的信息
  19. fsync 将所有缓存内容写回磁盘
  20. getcwd 返回当前工作目录
  21. getdirentry 获取文件描述符所对应目录的信息
  22. dup 复制文件描述符
 
 1. 以ucore lab8的answer为例，尝试修改并运行ucore OS kernel代码，使其具有类似Linux应用工具`strace`的功能，即能够显示出应用程序发出的系统调用，从而可以分析ucore应用的系统调用执行过程。
 
## 3.6 请分析函数调用和系统调用的区别
 1. 请从代码编写和执行过程来说明。
   1. 说明`int`、`iret`、`call`和`ret`的指令准确功能
 

## v9-cpu相关题目
---

### 提前准备
```
cd YOUR v9-cpu DIR
git pull 
cd YOUR os_course_spoc_exercise DIR
git pull 
```

### v9-cpu系统调用实现
  1. v9-cpu中os4.c的系统调用中参数传递代码分析。
  1. v9-cpu中os4.c的系统调用中返回结果的传递代码分析。
  1. 理解v9-cpu中os4.c的系统调用编写和含义。
