Suspend-aware Segment Cleaning 
==============================
* Maintainer: Seungyong Cheon (chuncsy@gmail.com)
* Contributor: Dongil Park (idoitlpg@gmail.com)
* Reference: Dongil Park, Seungyong Cheon, Youjip Won, "Suspend-aware Segment Cleaning in Log-structured File System",  In Proceedings of USENIX HotStorage'15, Santa Clara, USA, July 6-7, 2015
* http://esos.hanyang.ac.kr/files/publication/conferences/international/sasc.pdf

------------------------------
Suspend-aware Segment Cleaning is advanced segment cleaning policy of Log-structured File System in Android platform device.
Traditional background segment cleaning scheme in Log-structured File System is periodically activated (e.g. 30 sec.). It consolidates one free segment per each execution, and only execueted when the storage device is idle. On the other hand, most of the mobile devices have suspend feature to save battery. After LCD screen turned off, the device is entered to the suspend mode in a few second (1.8 sec. on the average). During the suspend mode, all tasks are frozen, including segment cleaning thread. Therefore, there's no chance to do segment cleaning if excessive I/O request and suspend are repeated.
SASC awares the screen-off signal and then adjust segment cleaning period before entering suspend mode. It uses *Virtual Segment Cleaning* to improve segment cleaning speed and *Utilization-based Segment Cleaning* to adjust segment cleaning period from 300ms to 900ms by the storage utilization. SASC consilidates x6 more free segments than stock segment cleaning does, and could prevent foreground segment cleaning which cause excessive I/O latency.

Kernel Modification
------------------------------
* Kernel source is based on Linux kernel 3.4.0
* AOSP version is 4.4.2 KitKat
* Declare SASC thread
  * fs/f2fs/f2fs.h
* Segment cleaning thread and initialization
  * fs/f2fs/gc.h
  * fs/f2fs/gc.c
* Superblock struct and function for SASC
  * fs/f2fs/super.c
* Aware screen-off signal
  * libsuspend.so
* *sysfs* file permission
  * init.rc

Setup
------------------------------
  1. Download kernel source code
    - Motorola Moto G:
      - Download from https://sourceforge.net/projects/motog.motorola/files/
  2. Patch kernel
    - Patch source code what SASC modified (search CONFIG_F2FS_FS_SUSPEND_GC)
  3. Build kernel
    - $ export CROSS_COMPILE=[androideabi-4.6 toolchain path]
    - $ export ARCH=arm
    - $ mkdir build
    - $ cp kernel/arch/arm/configs/msm8226_defconfig build/.config
    - $ make -C kernel O=build defoldconfig -j16
    - $ make -C kernel O=build menuconfig -j16
    - $ make -C kernel O=build headers_install -j16
    - $ make -C kernel O=build -j16
  4. Unpack boot.img
  5. Copy libsuspend.so and init.rc to ramdisk
  6. Make boot.img with kernel and ramdisk
  7. Flash custom boot.img
  8. Reboot device

2016-08-22, Seungyong Cheon <chuncsy@gmail.com>
