RK3188 image creation with BareBox
----------------------------------

Notes about the usage of [BareBox](https://www.barebox.org/) with Radxa Rock RK3188.


Prepare environment
-------------------

apt update
apt install lzop libusb-1.0-0-dev git flex bison build-essential gcc-arm-linux-gnueabihf lzop libncurses5-dev libssl-dev bc wget rsync


BareBox bootloader
------------------

````
git clone git://git.pengutronix.de/git/barebox.git
cd barebox

export CROSS_COMPILE=arm-linux-gnueabihf-
export ARCH=arm

# see that your boards would be supported (radxa rock rk3188 in this case)
find . -type f | grep rock | grep radxa | grep defconf

make radxa_rock_defconfig

# further customizations
make menuconfig
nano arch/arm/boards/radxa-rock/env/boot/mshc1

make -j8

# your image in 
ls images/images/barebox-radxa-rock.img 
````

Barebox sd card

As documented here: https://www.barebox.org/doc/latest/boards/rockchip.html

- Make 2 partitions on SD for boot and root filesystems.
    - make a DOS partition table, each partition would be ext4 with labels "boot" and "linuxroot"
- Checkout and compile https://github.com/apxii/rkboottools
    - `git clone https://github.com/apxii/rkboottools.git && cd rkboottools`
    - `make`
    
- Get some RK3188 bootloader from https://github.com/neo-technologies/rockchip-bootloader
- Run “rk-splitboot RK3188Loader(L)_V2.13.bin” command. (for example). You will get FlashData file with others. It’s a DRAM setup blob.
  Otherwise it can be borrowed from RK U-boot sources from https://github.com/linux-rockchip/u-boot-rockchip/blob/u-boot-rk3188/tools/rk_tools/3188_LPDDR2_300MHz_DDR3_300MHz_20130830.bin
  - `rk-splitboot RK3188Loader(L)_V2.13.bin` -> will give use FlashData and FlashBoot in the working directory
- Run `rk-makebootable FlashData barebox-radxa-rock.bin rrboot.bin`
- `dd if=rrboot.bin of=</dev/sdcard> bs=$((0x200)) seek=$((0x40))`

SD card is ready


Linux Kernel
------------

export CROSS_COMPILE=arm-linux-gnueabihf-
export ARCH=arm

git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git
cd linux-stable
git checkout 5.8.7


# rk3188 strictly related
wget http://rockchip.fr/radxa/linux/rockchip_defconfig -O arch/arm/configs/rockchip_defconfig
make rockchip_defconfig

# compilation qith devide tree blob
make -j8 zImage dtbs

# Append the device tree blob to zImage (CONFIG_ARM_APPENDED_DTB option) until we can use device tree support on boot.
cat arch/arm/boot/zImage arch/arm/boot/dts/rk3188-radxarock.dtb > zImage-dtb

# kernel modules
make -j8 modules

export INSTALL_MOD_PATH=../BareBox_setup/rootfs
mkdir -p $INSTALL_MOD_PATH
make modules_install


Rootfs
------

````
apt install binfmt-support debootstrap losetup

export TARGET_DIR=rootfs

sudo debootstrap --arch=armhf --foreign buster $TARGET_DIR

sudo chroot $TARGET_DIR
export LANG=C
/debootstrap/debootstrap --second-stage

apt update
apt install locales dialog openssh-server ntpdate htop net-tools sudo resolvconf udev ifupdown
dpkg-reconfigure locales

apt clean

# configure mount point
nano /etc/fstab
#  otherwise it will run on read-only mode
# /dev/mmcblk0p2	/	ext4	defaults,noatime	0	1

# add users
adduser rock

# change root passwd
passwd

# customize networking
...

# put some net informations
export HOSTNAME=radxa-board
echo $HOSTNAME > /etc/hostname
echo 127.0.0.1	localhost > /etc/hosts
echo 127.0.1.1	$HOSTNAME >> /etc/hosts
hostname $HOSTNAME

# finished
exit
````

Create an image where to store the brand new rootfs (700MB)

````
dd if=/dev/zero of=rootfs.img bs=700K count=1024
losetup /dev/loop15 rootfs.img 
mkfs.ext4 /dev/loop15

# mount  and copy rootfs
mkdir mnt
mount /dev/loop15 mnt
rsync -aHAX rootfs/* mnt/

# umount
sync
umount mnt

# do a fs check, a resize 
gparted /dev/sdg

# write partition to sd card
dd if=rootfs.img of=/dev/sdg2 bs=4K conv=sync status=progress
