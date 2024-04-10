# T-Display-GD32
T-Display-GD32 Hello World!

# Mac RISCV 开发环境踩坑

开发版买的LILYGO T-Display-GD32，调试器买的mini DAPLink。

## 安装RISCV工具链
RISCV工具链在Mac上提供homebrew安装方式，提供了Mac OS 13+ ARM64 预编译二进制，intel需要下载6.5G自行编译。
https://github.com/riscv-software-src/homebrew-riscv

## make 报错

编译阶段报错 
```
Error: unrecognized opcode `csrrs a5,mstatus,8', extension `zicsr' required
```

解决：
makefile 编译架构参数加上zicsr
```
ARCH = -march=rv32imac -mabi=ilp32 -mcmodel=medlow
#改成
ARCH = -march=rv32imac_zicsr -mabi=ilp32 -mcmodel=medlow
```

链接阶段报错
```
ABI is incompatible with that of the selected emulation: target emulation `elf64-littleriscv' does not match `elf32-littleriscv'
```
解决：
把编译架构参数和链接架构参数分开,链接阶段架构参数不需要zicsr
```
ARCH = -march=rv32imac_zicsr -mabi=ilp32 -mcmodel=medlow
LINKARCH = -march=rv32imac -mabi=ilp32 -mcmodel=medlow
```

## openocd报错
使用自带脚本烧录flash报错 gd32vf103.cfg:54: 
```
Error: wrong # args: should be "expr expression"
```
解决：
```
#报错代码：
    set dmcontrol_dmactive  [expr 1 << 0]
#应该和TCL版本对应语法有关，expr 关键字后面表达式空格删掉：
    set dmcontrol_dmactive  [expr 1<<0]
```
## 串口打印信息无法换行
各平台换行符：
* unix: \n
* macOS: \r
* windows: \r\n

mac上面的测试几款串口软件使用 "\r\n" 才会换行，实在不行自己写个串口软件。

## 点亮第一颗LED灯就可以吃灰了