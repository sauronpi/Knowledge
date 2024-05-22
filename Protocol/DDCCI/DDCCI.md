# 显示数据通道命令接口 (Display Data Channel (DDC) / Command Interface (CI) , DDC/CI)
---
## 工作原理 (I2C)
主机的显卡和显示器之间, 通过 I2C 总线通信 (DP, HDMI, DVI, VGA 都有). 在这个过程中, 主机不仅可以获取显示器的信息 (分辨率, 刷新率, 显示器名称等), 也可以向显示器发送命令, 这就是 DDC/CI.

具体使用的协议叫做 显示器控制命令集 (Monitor Control Command Set, MCCS), 其中的每一项功能叫做 虚拟控制面板 (Virtual Control Panel, VCP).


EDID 和 Display ID 不在标准范围，在增强拓展范围。

---
## VESA EDDC-1.2

#### 2.2.3  DDC addresses (A0h / A1h and A4h / A5h) 
Under DDC, each pair of I2C slave addresses allows 256 bytes of data to be accessed. Larger data structures, 
up to 32K bytes, can be accessed using the E-DDC addressing technique – see section 2.2.4

#### 2.2.4  Enhanced DDC (E-DDC) 
A protocol based on I2C and used on a bi-directional data channel between the display and host. This protocol 
accesses devices at I2C addresses of A0h / A1h or A4h / A5h used in conjunction with a register at address 
60h.  
The 60h address is used as a segment pointer register; see section 2.2.5 to allow larger amounts of data to be 
retrieved than is possible using DDC. This mode is backwards compatible with DDC. 
Note: The segment pointer register is shared by both the primary and secondary display capabilities I2C slave 
addresses.

#### 2.2.5  Segment pointer (60h) 
E-DDC allows access of up to 32 Kbytes of data. This is accomplished using a combination of the A0h / A1h 
or A4h / A5h address pair and a segment pointer. 
The segment pointer acts as an adjustable offset so that the address pair A0h / A1h or A4h / A5h can be used 
to access many different 256 byte blocks. This is shown graphically in Figure 2-1. 
The segment pointer is a one byte value at address 60h. If the segment pointer is set = 0 the commands using 
addresses A0h / A1h or A4h / A5h will access blocks 0 and 1 respectively which are the first two 128 byte 
blocks in the EDID or DisplayID memory space. 
Notes:  
a) The default value in the segment pointer is zero. This ensures backward compatibility with DDC 
 
 VESA Enhanced Display Data Channel Standard  Version 1.2 
Copyright  1994 - 2007 Video Electronics Standards Association Page 13 of 44 
operation. 
b) For completeness and consistency, there are several instances where “set the segment pointer = n” 
occurs in this document, where “n” represents any integer value. Since the segment pointer, however,  
is always reset to zero at power on and after each use, this instruction is redundant except when a 
non-zero value of the segment pointer is required. 
Each successive value of the segment pointer allows access to the next two 128 byte blocks in the display 
capability memory space. 
Notes: The segment pointer register is effectively a “write only register” since it is reset to zero (the default 
value) at the completion of each command sequence. In this context a “command sequence” is: 
a) A write operation to load the appropriate value in the segment pointer, and 
b) A read operation(s) to transfer the EDID or DisplayID selected by the combination of the segment 
pointer value and a display capability slave address.

