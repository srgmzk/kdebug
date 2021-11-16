# kdebug. kernel debug practice

useful links:

https://www.josehu.com/memo/2021/01/02/linux-kernel-build-debug.html
https://medium.com/@daeseok.youn/prepare-the-environment-for-developing-linux-kernel-with-qemu-c55e37ba8ade

**1. Get kernel**

..code ::

	$ git clone https://kernel.googlesource.com/pub/scm/linux/kernel/git/next/linux-next.git
	make ARCH=x86_64 x86_64_defconfig
    make ARCH=x86_64 menuconfig


