---
layout : default
---

- [I. Using prebuilt toolchain](./keystone1.md)
  * I. a) Keystone]
  * I. b) Keystone-demo
- [II. Using native toolchain](./keystone2.md)
  * II. a) Keystone
  * II. b) Keystone-demo
- [III. Turn on USB & Ethernet drivers](#iii-turn-on-usb--ethernet-drivers-in-linux-kernel)
- [IV. Run test on QEMU](./keystone4.md)

* * *

# III. Turn on USB & Ethernet drivers

There are two ways of doing this, the 'formal' way, and the shortcut.

### First, check the PATH things

	$ echo ${PATH}				#and MAKE SURE that NO ANY TOOLCHAIN is on the PATH
	$ cd <your keystone folder>		#go to your keystone folder
	$ . source.sh
	$ export KEYSTONE_DIR=`pwd`
	
	$ cd <your keystone-demo folder>	#go to your keystone-demo folder
	$ . source.sh
	
### The 'formal' way

The proper way to modify drivers in Linux kernel is open the kernel, select or diselect some drivers, apply the changes, and remake everything.

	$ cd <your keystone folder>
	$ make -f hifive.mk linux-menuconfig
	wait for the GUI to load, then:
		1. Device drivers -> Network device support -> USB network adapters
		2. choose the appropriate drivers and turn them on (usually they are the RTL** something)
		4. Exit to go back to the Network device support page
		5. Now go to Ethernet driver support page
		6. choose the appropriate drivers and turn them on (mostly they are the Intel and Realtek ones)
		6. Now just keep Exit till the end and choose Yes when asked to save the new configurations
		
	$ cp hifive-conf/linux_defconfig hifive-conf/linux_cma_conf
	this line is for applying the new config file into the build file
	
	finally, remake everything:
	$ make clean
	$ make -j`nproc`
	$ make image -j`nproc`

## The shortcut

So, the bottom line of the 'formal' way above is just to creating a new **linux_cma_conf** file under the *hifive-conf/* directory.

Then, on this tutorial repo, THE FILE is just provided, so you know what to do:

	copy the **linux_cma_conf** file provied in this tutorial repo to your <keystone folder>/hifive-conf/linux_cma_conf
	$ make clean
	$ make -j`nproc`
	$ make image -j`nproc`

## Aftermath

After your changes on the kernel, the hash value of the **bbl.bin** file is different now.

So if you want to use the keystone-demo ([I. b)](#i-b-keystone-demo) or [II. b)](#ii-b-keystone-demo)), you have to do the followings to reapply the hashes to the image file of **bbl.bin**:

	$ cd <your keystone-demo folder>	#go to your keystone-demo folder
	$ make getandsethash
	$ rm trusted_client.riscv
	$ make trusted_client.riscv
	$ make copybins
	
	$ cd <your keystone folder>		#go to your keystone folder
	$ make image -j`nproc`			#and update the bbl.bin there

* * *

| [*back: *](./keystone2.md) | [*next: *](./keystone4.md) |
| :--- | :--- |
||
