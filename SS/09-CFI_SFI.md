# Control Flow Integrity

Motivation (Protection)

* Code inj
    * W \^ X (cant have write and exec priv at same time)
* Code reuse
    * ASLR
    * CFI

# Software Based Fault Isolation

隔离fault domain，每个fault domain有自己的code region和data region

**basic policy**

code RX, data RW

**stronger policy**

* E.g., PittSFIeld [McCamant & Morrisett 06]
    * Disallow jumping into the middle of instructions on x86, which has variable-sized instructions
* E.g., NaCl [Yee et al. 09]
    * Disallow system call instructions in the code region



**Guard zone**

两侧未mapped的内存空间（访问会导致segFault）



**Control-Flow Integrity**

禁止跳转到伪指令的中间

实现方法：

* 对齐的chunk
    * 即使一个chunk放不完所有指令，也可以把最后一个指令设为一个跳到下一个chunk的跳转指令，然后再下一个chunk继续填代码
* 可变长的chunk
    * 
