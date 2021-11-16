kdebug. kernel debug practice
'''''''''''''''''''''''''''''

**Useful links:**

- https://www.josehu.com/memo/2021/01/02/linux-kernel-build-debug.html
- https://medium.com/@daeseok.youn/prepare-the-environment-for-developing-linux-kernel-with-qemu-c55e37ba8ade

Build environment:
------------------

**kernel**

.. code::

 git clone https://kernel.googlesource.com/pub/scm/linux/kernel/git/next/linux-next.git
 make ARCH=x86_64 x86_64_defconfig 
 make ARCH=x86_64 menuconfig
 
**rootfs**

Tools:
------

**vim**

- https://github.com/srgmzk/mdsk
- https://www.dannyadam.com/blog/2019/05/debugging-in-vim/

**cscope**

http://cscope.sourceforge.net/large_projects.html

Для ядра Linux это немного сложнее, поскольку мы хотим исключить весь код в каталогах документации и сценариев, а также всю архитектуру и код сборки для всех чипов,
кроме всеми любимого Intel x86 (который, как я предполагаю, является интересующая вас архитектура). Кроме того, я исключаю весь код драйвера ядра в этом примере 
(они более чем вдвое превышают объем кода для анализа, что приводит к раздуванию базы данных Cscope, и они содержат много повторяющихся определений, что часто 
затрудняет поиск. код драйвера, опустите соответствующую строку ниже или измените ее, чтобы распечатать только интересующие вас файлы драйвера):

.. code:: csopegen.sh

 #!/bin/bash

 LNX="."

 echo "Finding relevant source files..."
 find $LNX                                                                \
    -path "$LNX/arch/*" ! -path "$LNX/arch/x86*" -prune -o               \
    -path "$LNX/include/asm-*" ! -path "$LNX/include/asm-generic*"       \
                               ! -path "$LNX/include/asm-x86*" -prune -o \
    -path "$LNX/tmp*" -prune -o                                          \
    -path "$LNX/Documentation*" -prune -o                                \
    -path "$LNX/scripts*" -prune -o                                      \
    -name "*.[chxsS]" -print > $LNX/cscope.files

 echo "Building cscope database..."
 time cscope -q -k -b -i cscope.files

 exit 0

**qemu**

run kernel and debug it
-----------------------

1. run target system:

Debug withoout rootfs:

.. code::

 qemu-system-x86_64 -s -S -no-kvm -kernel arch/x86/boot/bzImage -hda /dev/zero -append "root=/dev/zero console=ttyS0 nokaslr" -serial stdio -display none
 
Debug with rootfs:

.. code::

 qemu-system-x86_64 -kernel arch/x86/boot/bzImage -boot c -m 2049M -hda ../buildroot/output/images/rootfs.ext2 -append "root=/dev/sda rw console=ttyS0,115200 acpi=off nokaslr" -serial stdio -display none 

In another terminal to open vim . inside Kernel src run vim command:

.. code::

 :Termdebug ./vmlinux

.. code::

 debugged program+  gdb communication+                                                      buffers 
 Reading symbols from ./vmlinux...                                                                       
 startupdone                                                                                             
 (gdb) new-ui mi /dev/pts/6                                                                              
 New UI allocated                                                                                        
 (gdb)     
 ...







