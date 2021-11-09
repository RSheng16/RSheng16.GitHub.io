---
layout: post
title: "CircuitProject-CPU"
description: "The brain is a good thing, but unfortunately I need to record"
tag: error
---

## 寄存器

 - PC 程序计数器
 - ALU 
 - PSW/FLAG 程序状态字
 - A 寄存器
 - B 寄存器
 - C 寄存器
 - D 寄存器
 - IR 指令寄存器
 - DST 目的操作数寄存器
 - SRC 源操作数寄存器

 - MSR 存储器段寄存器
 - MAR 存储器地址寄存器
 - MC 内存控制器

 - SP 堆栈指针寄存器
 - BP 基址寄存器
 - SI 源变址寄存器
 - DI 目的变址寄存器
 - CS 代码段寄存器
 - DS 数据段寄存器
 - SS 堆栈段寄存器
 - ES 附加段寄存器

 - TMP 临时寄存器若干

## 指令

 - 二操作数：3bit 最高位为：1
     - mov dst, src
     - add dst, src
     - sub dst, src
     - cmp op1, op2
     - and dst, src
     - or dst, src
     - xor dst, src
 - 一操作数：次最高位为 1
     - inc src
     - dec src
     - not src
     - call dst
     - jmp dst
     - jo dst
     - jno dst
     - jz dst
     - jnz dst
     - push dst
     - pop dst
     - int dst
- 临操作数：
    - nop
    - hlt
    - ret 
    - iret

## 寻址方式
 - 立即数寻址 MOV A 5
 - 寄存器寻址 MOV A B
 - 直接寻址 MOV A [5]
 - 寄存器间接寻址 MOV A [B]

## 寄存器控制器
![](/images/CPU/Register_controller.jpg)

## 读写控制器

### 读写控制方式
1. 指令寄存器的数据
2. 目的操作数寄存器的数据
3. 源操作数寄存器的是数据

![](/images/CPU/RW_controller.jpg)

## 控制单元

![](/images/CPU/Control_Unit.jpg)

## 取指令微程序

```python
# coding=utf-8

MSR = 1
MAR = 2
MDR = 3
RAM = 4
IR = 5
DST = 6
SRC = 7
A = 8
B = 9
C = 10
D = 11
DI = 12
SI = 13
SP = 14
BP = 15
CS = 16
DS = 17
SS = 18
ES = 19
VEC = 20
T1 = 21
T2 = 22

MSR_OUT = MSR
MAR_OUT = MAR
MDR_OUT = MDR
RAM_OUT = RAM
IR_OUT = IR
DST_OUT = DST
SRC_OUT = SRC
A_OUT = A
B_OUT = B
C_OUT = C
D_OUT = D
DI_OUT = DI
SI_OUT = SI
SP_OUT = SP
BP_OUT = BP
CS_OUT = CS
DS_OUT = DS
SS_OUT = SS
ES_OUT = ES
VEC_OUT = VEC
T1_OUT = T1
T2_OUT = T2

_DST_SHIFT = 5

MSR_IN = MSR << _DST_SHIFT
MAR_IN = MAR << _DST_SHIFT
MDR_IN = MDR << _DST_SHIFT
RAM_IN = RAM << _DST_SHIFT
IR_IN = IR << _DST_SHIFT
DST_IN = DST << _DST_SHIFT
SRC_IN = SRC << _DST_SHIFT
A_IN = A << _DST_SHIFT
B_IN = B << _DST_SHIFT
C_IN = C << _DST_SHIFT
D_IN = D << _DST_SHIFT
DI_IN = DI << _DST_SHIFT
SI_IN = SI << _DST_SHIFT
SP_IN = SP << _DST_SHIFT
BP_IN = BP << _DST_SHIFT
CS_IN = CS << _DST_SHIFT
DS_IN = DS << _DST_SHIFT
SS_IN = SS << _DST_SHIFT
ES_IN = ES << _DST_SHIFT
VEC_IN = VEC << _DST_SHIFT
T1_IN = T1 << _DST_SHIFT
T2_IN = T2 << _DST_SHIFT

SRC_R = 2 ** 10
SRC_W = 2 ** 11
DST_R = 2 ** 12
DST_W = 2 ** 13

PC_WE = 2 ** 14
PC_CS = 2 ** 15
PC_EN = 2 ** 16

PC_OUT = PC_CS
PC_IN = PC_CS | PC_WE
PC_INC = PC_CS | PC_WE | PC_EN

HLT = 2 ** 31
```

```python
# coding=utf-8

import pin

FETCH = [
    pin.PC_OUT | pin.MAR_IN,
    pin.RAM_OUT | pin.IR_IN | pin.PC_INC,
    pin.PC_OUT | pin.MAR_IN,
    pin.RAM_OUT | pin.DST_IN | pin.PC_INC,
    pin.PC_OUT | pin.MAR_IN,
    pin.RAM_OUT | pin.SRC_IN | pin.PC_INC,
]
```

```python
# coding=utf-8

import os
import pin
import assembly as ASM

dirname = os.path.dirname(__file__)
filename = os.path.join(dirname, 'micro.bin')

micro = [pin.HLT for _ in range(0x10000)]

for addr in range(0x10000):
    ir = addr >> 8
    psw = (addr >> 4) & 0xf
    cyc = addr & 0xf

    if cyc < len(ASM.FETCH):
        micro[addr] = ASM.FETCH[cyc]

with open(filename, 'wb') as file:
    for var in micro:
        value = var.to_bytes(4, byteorder='little')
        file.write(value)

print('Compile micro instruction finish!!!')
```

