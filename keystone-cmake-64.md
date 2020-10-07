---
layout : default
---

# KEYSTONE (using CMake) for RV64 Build

* * *

**NOTE: right now CMake doesn't work on FPGA (although it worked perfectly on QEMU)**

# I. Keystone

## I. a) Using their prebuilt toolchain (gcc-7.2)

Note: because their prebuilt toolchain is RV64GC, so for the RV64IMAC build please follow the guide in [I. b) Using our local toolchain](#i-b-using-our-local-toolchain-gcc-83-in-this-example).

Git clone:
```
$ git clone https://github.com/keystone-enclave/keystone.git keystone-rv64gc	#commit 21c77189 on 30-Aug-2020
$ cd keystone-rv64gc/
```

Check PATH:
```
$ echo ${PATH}			#and MAKE SURE that NO ANY TOOLCHAIN is on the PATH
$ . source.sh
$ export KEYSTONE_DIR=`pwd`
```

Download prebuilt toolchain:
```
$ ./fast-setup.sh			#this will download the prebuilt toolchain (gcc-7.2) and set things up
```

Build SDK:
```
$ cd sdk/
$ mkdir build
$ cd build/
$ export KEYSTONE_SDK_DIR=`pwd`
$ cmake ..
$ make
$ make install
$ cd ../../		#go back outside after make
```

Create build folder & make:
```
$ mkdir build
$ cd build/
$ cmake ..
$ make -j`nproc`
```

Build the keystone-test:
```
$ sed -i 's/size_t\sfreemem_size\s=\s48\*1024\*1024/size_t freemem_size = 2*1024*1024/g' ../sdk/examples/tests/test-runner.cpp
(this line is for FPGA board, because usually there is only 1GB of memory on the board)
$ make run-tests	#remake the tests
$ make image		#after this, a new bbl.bin is generated
```

## I. b) Using our local toolchain (gcc-8.3 in this example)

Git clone:
```
If build for RV64GC:		$ git clone -b local-tc-cmake https://github.com/thuchoang90/keystone.git keystone-rv64gc-local
						$ cd keystone-rv64gc-local/

If build for RV64IMAC:		$ git clone -b local-tc-cmake https://github.com/thuchoang90/keystone.git keystone-rv64imac
						$ cd keystone-rv64imac/
```

Check PATH:
```
$ echo ${PATH}			#check if our toolchain is on the PATH or not
# if not then export it to PATH
If build for RV64GC:		$ export RISCV=/opt/GCC8/riscv64gc			#point to RV64GC toolchain
If build for RV64IMAC:		$ export RISCV=/opt/GCC8/riscv64imac		#point to RV64IMAC toolchain

$ export PATH=$RISCV/bin/:$PATH
$ export KEYSTONE_DIR=`pwd`
```

Update submodule:
```
$ ./fast-setup.sh		#this time, it won't download the prebuilt toolchain, just update the submodule
```

Do the following if build for RV64IMAC, skip if build for RV64GC:
```
$ ./patches/imac-patch.sh
```


Build SDK:
```
$ cd sdk/
$ mkdir build
$ cd build/
$ export KEYSTONE_SDK_DIR=`pwd`
$ cmake ..
$ make
$ make install
$ cd ../../		#go back outside after make
```

Create build folder & make:
```
$ mkdir build
$ cd build/
$ cmake ..
$ make -j`nproc`
```

Build the keystone-test:
```
$ sed -i 's/size_t\sfreemem_size\s=\s48\*1024\*1024/size_t freemem_size = 2*1024*1024/g' ../sdk/examples/tests/test-runner.cpp
(this line is for FPGA board, because usually there is only 1GB of memory on the board)
$ make run-tests	#remake the tests
$ make image		#after this, a new bbl.bin is generated
```

* * *

# II. Keystone-demo

Check PATH:
- Pair with the prebuilt-toolchain of Keystone: *(Note: prebuilt-toolchain is RV64GC, so if you want to build for RV64IMAC please follow the local-built-toolchain)*

```
$ echo ${PATH}					#and MAKE SURE that NO ANY TOOLCHAIN is on the PATH
$ cd keystone-rv64gc/				#go to your keystone folder
$ . source.sh
$ export KEYSTONE_DIR=`pwd`
$ export KEYSTONE_BUILD_DIR=`pwd`/build		#point to the build folder
```

- Pair with the local-built-toolchain of Keystone:

```
#go to your keystone folder
$ cd keystone-rv64gc-local/
Or: $ cd keystone-rv64imac/

$ echo ${PATH}			#check if our toolchain is on the PATH or not
# if not then export it to PATH
If build for RV64GC:		$ export RISCV=/opt/GCC8/riscv64gc			#point to RV64GC toolchain
If build for RV64IMAC:		$ export RISCV=/opt/GCC8/riscv64imac		#point to RV64IMAC toolchain

$ export PATH=$RISCV/bin/:$PATH
$ export KEYSTONE_DIR=`pwd`
$ export KEYSTONE_SDK_DIR=`pwd`/sdk
$ export KEYSTONE_BUILD_DIR=`pwd`/build		#point to the build folder
```

Git clone:
```
$ cd ../			#go back outside
$ git clone -b cmake https://github.com/thuchoang90/keystone-demo.git keystone-demo-rv64
```

Make:
```
$ cd keystone-demo-rv64/
$ . source.sh
$ ./quick-start.sh				#type Y when asked
after this step, a new app is generated and coppied to the keystone directory
```

Update keystone-demo to keystone build folder:
```
$ cd ${KEYSTONE_BUILD_DIR}		#now go back to the keystone folder
$ make image -j`nproc`					#and update the bbl.bin there
```

However, it will be a false attestation. To update the new hash value, do the followings:
```
$ cd ../../keystone-demo-rv64/			#first, cd back to the keystone-demo directory
$ make getandsethash
$ rm trusted_client.riscv
$ make trusted_client.riscv
$ make copybins
after this step, the app is updated with the correct hash value and coppied to the keystone directory

$ cd ${KEYSTONE_BUILD_DIR}		#now go back to the keystone folder
$ make image -j`nproc`					#and update the bbl.bin there
```

* * *

# III. Run Test on QEMU

```
$ cd <keystone folder>			#go to your keystone folder
$ cd build/					#go to build folder
$ ./scripts/run-qemu.sh
Login by the id of 'root' and the password of 'sifive'.

$ insmod keystone-driver.ko		#install driver

To do the initial test:
$ time ./tests.ke				#ok if 'Attestation report SIGNATURE is valid' is printed

To do the keystone-demo test:
$ cd keystone-demo/			#go to the keystone-demo test
$ ./enclave-host.riscv &			#run host in localhost
$ ./trusted_client.riscv localhost	#connect to localhost and test
okay if the 'Attestation signature and enclave hash are valid' is printed
exit the Security Monitor by:	$ q

exit QEMU by:	$ poweroff
```

***Note:*** sometimes ***./scripts/run-qemu.sh** encountered a problem like this:
```
**** Running QEMU SSH on port 5291 ****
overriding secure boot ROM (file: /home/ubuntu/Projects/Keystone/CMake/keystone-rv64gc-local/build/bootrom.build/bootrom.bin)
boot ROM size: 54061
fdt dumped at 58157
qemu-system-riscv64: -device virtio-blk-device,drive=hd0: Failed to get "write" lock
Is another process using the image [/home/ubuntu/Projects/Keystone/CMake/keystone-rv64gc-local/build/buildroot.build/images/rootfs.ext2]?
```

Then just remake the image and rerun again:
```
$ make image -j`nproc`
$ ./scripts/run-qemu.sh
```

* * *

# BOTTOM PAGE

| Back | Next |
| :--- | ---: |
| [Keystone (using Makefile) for RV32 Build](./keystone-makefile-32.md) | [Turn on drivers in Keystone](./keystone-drivers.md) |
