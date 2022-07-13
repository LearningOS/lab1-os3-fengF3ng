# 实现的功能

在task模块部分对进程第一次调用的时间进行了保存，并封装了taskinfo所需信息的查询功能与syscall调用次数的更新功能

之后在syscall模块针对每次系统调用通过之前的syscall更新函数存入任务控制块中

当需要taskinfo的时候，通过调用task模块封装的函数即可完成

# 1

- [ERROR] [kernel] PageFault in application, bad addr = 0x0, bad instruction = 0x80400408, core dumped.

第一个程序存在页错误，包括访问了非法地址0x0，并向其中写入数据的指令

- [ERROR] [kernel] IllegalInstruction in application, core dumped.

第二个程序在U状态下使用了sret指令

- [ERROR] [kernel] IllegalInstruction in application, core dumped.

第三个程序在U状态下使用了csrr指令

# 2

## 1

a0代表了trap_handler函数的返回值，指向TrapContext内容

使用场景：
1. 时钟中断触发__alltraps调用trap_handler返回后将继续执行下一条指令，即后续的__restore代码，用来回复上下文。
2. 用户程序系统调用除法__alltraps并进一步调用__restore代码

## 2

t0、t1、和t2都是暂存寄存器，用于将sstatus、sepc和sscratch的值恢复到调用前。其中sstatus记录trap之前处于的特权级别，sepc保存trap发生前最后一条指令地址，sscratch存储了用户栈的地址。

## 3

x2寄存器为sp栈顶寄存器，sp指向当前内核栈且随程序实时变化。而且之前的用户栈指针通过sscratch存储在栈上，所以此时不需要通过x2来恢复。

x4为线程寄存器，本程序用不到，也不需要维护。

## 4

sp指向用户栈，sscratch指向内核栈

## 5

sret

该指令为S态指令，会恢复中断、恢复U态、恢复PC并继续向下运行

## 6 

sp指向内核栈，sscratch指向用户栈

## 7

trap触发的瞬间，由硬件完成一系列转换

