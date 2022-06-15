# CS61C
伯克利计算机组成原理实验

由于本次课程lab与project很多，而且本人现在也没有多少时间，所以可能会过几个月才开始写。但是首先对目前学过的x86和riscv汇编进行简要复习

## riscv

### 寄存器

Register  | ABI         | Use by convention                     | Preserved?
:-------- | :---------- | :---------------                      | ------
x0        | zero        | hardwired to 0, ignores writes        | _n/a_
x1        | ra          | return address for jumps              | no
x2        | sp          | stack pointer                         | yes
x3        | gp          | global pointer                        | _n/a_
x4        | tp          | thread pointer                        | _n/a_
x5        | t0          | temporary register 0                  | no
x6        | t1          | temporary register 1                  | no
x7        | t2          | temporary register 2                  | no
x8        | s0          | saved register 0 _or_ frame pointer   | yes
x9        | s1          | saved register 1                      | yes
x10       | a0          | return value _or_ function argument 0 | no
x11       | a1          | return value _or_ function argument 1 | no
x12       | a2          | function argument 2                   | no
x13       | a3          | function argument 3                   | no
x14       | a4          | function argument 4                   | no
x15       | a5          | function argument 5                   | no
x16       | a6          | function argument 6                   | no
x17       | a7          | function argument 7                   | no
x18       | s2          | saved register 2                      | yes
x19       | s3          | saved register 3                      | yes
x20       | s4          | saved register 4                      | yes
x21       | s5          | saved register 5                      | yes
x22       | s6          | saved register 6                      | yes
x23       | s7          | saved register 7                      | yes
x24       | s8          | saved register 8                      | yes
x25       | s9          | saved register 9                      | yes
x26       | s10         | saved register 10                     | yes
x27       | s11         | saved register 11                     | yes
x28       | t3          | temporary register 3                  | no
x29       | t4          | temporary register 4                  | no
x30       | t5          | temporary register 5                  | no
x31       | t6          | temporary register 6                  | no
pc        | _(none)_    | program counter                       | _n/a_

这是riscv所有的CSR（control status register 控制状态寄存器）的文档：https://five-embeddev.com/quickref/csrs.html

在这里只列出我所常见的csr（trap进入内核态相关）：

sstatus：ssp等字段给出trap之前所处的状态U或S

sepc：当trap是一个异常时，记录遇到异常指令的虚拟地址

scause：描述trap的原因，可用于trap处理程序

stval：描述trap附加信息

stvec：描述处理trap程序的入口地址（由操作系统负责）

satp：存储根页表的物理地址


### 指令

加减乘除：

add x1, x2, x3    x1 = x2 + x3

add1 x1, x2, 20   x1 = x2 + 20

sub x1, x2, x3    x1 = x2 - x3

mul x1, x2, x3    x1 = x2 * x3

div x1, x2, x3    x1 = x2 / x3

rem x1, x2, x3    x1 = x2 % x3

逻辑运算：

and x1, x2, x3    x1 = x2 & x3

or x1, x2, x3    x1 = x2 | x3

xor x1, x2, x3    x1 = x2 ^ x3

andi x1, x2, 20   x1 = x2 & 20

ori x1, x2, 20   x1 = x2 | 20

xor1 x1, x2, 20   x1 = x2 ^ 20

移位运算：

sll x1, x2, x3    x1 = x2 << x3

srl x1, x2, x3    x1 = x2 >> x3

sra x1, x2, x3    x1 = x2 >> x3

slli x1, x2, 20    x1 = x2 << 20

srli x1, x2, 20    x1 = x2 >> 20

srai x1, x2, 20    x1 = x2 >> 20

数据传输：

lb x1, 40(x2)     x1 = x2[40]（把x2理解为字节数组）

lh x1, 40(x2)     x1 = x2[40]

lw x1, 40(x2)     x1 = x2[40]

ld x1, 40(x2)     x1 = x2[40]

sb x1, 40(x2)     x2[40] = x1

sh x1, 40(x2)     x2[40] = x1

sw x1, 40(x2)     x2[40] = x1

sd x1, 40(x2)     x2[40] = x1

条件分支：

beq x1, x2, 100   if x1 == x2 goto pc+100

bne x1, x2, 100   if x1 != x2 goto pc+100

blt x1, x2, 100   if x1 < x2 goto pc+100

bge x1, x2, 100   if x1 >= x2 goto pc+100

无条件跳转：

jal x1 100        x1 = pc + 4; goto pc+100

jal x1 100(x2)    x1 = pc + 4; goto x2+100


## x86

Register  | ABI         | Use by convention    
:-------- | :---------- | :---------------     
eax       | rax         | accumulate           
ebx       | rbx         | base                 
ecx       | rcx         | function argument 4  
edx       | rdx         | function argument 3  
esi       | rsi         | function argument 2  
edi       | rdi         | function argument 1  
esp       | rsp         | stack pointer        
ebp       | rbp         | temporary register   
r8d       | r8d         | function argument 5  
r9d       | r9d         | function argument 6  
r10d      | r10d        | temporary register   
r11d      | r11d        | temporary register   
r12d      | r12d        | temporary register   
r13d      | r13d        | temporary register   
r14d      | r14d        | temporary register   
r15d      | r15d        | temporary register   
r16d      | r16d        | temporary register   


## 指令

加减乘除：

add src, dest      src可以是立即数值、内存地址、寄存器。dest可以是寄存器或内存中的值，不能同时使用内存地址作为源和目标。结果存放在dest中。需要在add结尾添加b、w、l来指定操作数长度。

sub src, dest      

inc dest          自增，dest可以是寄存器或内存中的值

dec dest          自减

mul src            eax = src * eax

imul src           eax = src * eax           乘法操作使用另一个操作数必须放在al、ax、eax寄存器中

imul src, dest     dest = src * dest

imul 100, src, dest  

div src,           

idiv src           

逻辑运算：

and src, dest

or src, dest

xor src, dest

nor dest

移位运算：

sal dest       

sal %cl dest

sal 100 dest

shl dest

sar dest

shr dest

数据传输：

push src

pop  dest

mov：以%开头的寄存器标识符，立即数是以$开头的数值，指令后带后缀b,w,l,q分别代表8位，16位，32位，64位。

寄存器寻址	movl %:eax, %edx	edx = eax

立即寻址	movl $0x123, %edx	edx = 0x123

直接寻址	movl 0x123, %edx	edx = *(int32_t *)0x123

间接寻址	movl (%ebx), %edx	edx = *(int32_t *)ebx

变址寻址	movl 4(%ebx), %edx	edx = *(int32_t *)(ebx+4)


































