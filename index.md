---
layout: default
---

This page is created by Trong-Thuc Hoang.

Currently, he is a Ph.D. candidate at the University of Electro-Communications (UEC), Tokyo, Japan, and also a research assistant at the National Institute of Advanced Industrial Science and Technology (AIST), Tokyo, Japan.

This page first is for my self-study, second is for helping newcomers to understand RISC-V.

The guide in this tutorial has the following contents:

* * *

<p style="text-align:center">INITIAL SETUP</p>

- [Initial Setup](./init.md) *sets up a new Ubuntu machine & install necessary tools*.

* * *

<p style="text-align:center">SOFTWARE</p>

- Keystone: TEE framework *(using Makefile)*

  * [For RV64 Build](./keystone-makefile-64.md)
  * [For RV32 Build](./keystone-makefile-32.md)

- Keystone: TEE framework *(using CMake)* *(\*newer\*)* *Right now CMake doesn't work on FPGA (although it worked perfeclty on QEMU)*

  * [For RV64 Build](./keystone-cmake-64.md)

- [Turn on drivers in Keystone](./keystone-drivers.md)

* * *

<p style="text-align:center">HARDWARE</p>

- [SiFive Freedom](./vc707.md) *implements on VC707 (with/without Keystone)*.

- [VexRiscv 32-bit MCU](./vexriscv.md) *(don't have Keystone)*.

- [TEE-Hardware](./tee-hw.md) *accelerates Keystone by crypto-cores (have demos on VC707, DE4, and TR4)*.

* * *

<p style="text-align:center">** CHISEL STUDY **</p>

- [Scala](./scala.md): *General-purpose programming language*
- [Chisel](./chisel.md): *Hardware constructor language*

* * *
