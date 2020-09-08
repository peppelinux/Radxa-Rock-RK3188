# Radxa-Rock-RK3188
Notes on flashing and usage of Radxa Rock boards.

https://wiki.radxa.com/File:Rock_front.jpg

Rabian official build
---------------------

````
apt install lzop libusb-1.0-0-dev git flex bison build-essential gcc-arm-linux-gnueabihf lzop libncurses5-dev libssl-dev bc
git clone https://github.com/radxa/rabian-build.git
cd rabian-build
./rabian-build.sh rock
````

#### Troubleshooting

Can't use 'defined(@array)' (Maybe you should just omit the defined()?) at kernel/timeconst.pl
````
# -	if (!defined(@val)) {
# +	if (!@val) {
nano $(date +%Y%m%d)/rock-bsp/boards/rock/linux-rockchip/kernel/timeconst.pl
/rabian-build.sh rock
````
#### Flash

````
cd Linux_Upgrade_Tool_v1.21
./upgrade_tool uf /home/wert/RadxaRock_rk30/rabian-build/200905/rock-bsp/boards/rock/rockdev/rock_rabian_rock_200905_30273dc_nand.img
````

#### First Access

usernme: rock
passwd: rock

Build your own Kernel image
---------------------------

````
# get linux
git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git
cd linux-stable

# desidered version
git tag -l
git checkout v5.8.7

# get rockchip default config
wget http://rockchip.fr/radxa/linux/rockchip_defconfig -O arch/arm/configs/rockchip_defconfig

# this is not needed anymore because it's now in the kernel tree
# wget http://rockchip.fr/radxa/linux/rk3188-radxarock.dts -O arch/arm/boot/dts/rk3188-radxarock.dts

# set compiler env
export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabihf-

make rockchip_defconfig
make -j8 zImage dtbs
````

#### Boot image
````
git clone https://github.com/neo-technologies/rockchip-mkbootimg.git
cd rockchip-mkbootimg
make
sudo make install
cd ..

# Append the device tree blob to zImage (CONFIG_ARM_APPENDED_DTB option) until we can use U-Boot device tree support.

cat arch/arm/boot/zImage arch/arm/boot/dts/rk3188-radxarock.dtb > zImage-dtb
cd ..

# deprecated
# make ramdisk inird image
# git clone https://github.com/radxa/initrd.git
# cd initrd
# find . ! -path "./.git*"  | cpio -H newc  -ov > initrd.img

# create initrd image
# these are the modules needed to be included, find compatibles if something changed in newer kernel versions
gspca_main.ko tda827x.ko tda18218.ko tda827x.ko tda18218.ko tda18271.ko mxl5007t.ko tuner-types.ko mc44s803.ko mxl5005s.ko tea5761.ko xc5000.ko mt2060.ko tda18212.ko tda8290.ko tuner-xc2028.ko tea5767.ko tuner-simple.ko max2165.ko qt1010.ko mt20xx.ko tda9887.ko mt2131.ko mt2266.ko scsi_wait_scan.ko

# put them in a folder then
find . ! -path "./.git*"  | cpio -H newc  -ov > initrd.img

# Create the boot.img using mkbootimg (Rockchip version).
mkbootimg --kernel zImage-dtb --ramdisk initrd.img -o boot.img
````

External Resources
------------------

- https://github.com/hanwckf/rk3066a-box-4.4
- https://wiki.radxa.com/Rock/flash_the_image
- https://gist.github.com/sarg/5028505

Build your own image
- https://wiki.radxa.com/Rock/make_sd_image
- https://lewin.co.il/compiling-new-kernel-for-radxa-rock-pro-rtl8187l-support/

Backup
- https://wiki.radxa.com/Backup_and_deploy

Understanding DTS/DTB:
- https://elinux.org/images/f/f9/Petazzoni-device-tree-dummies_0.pdf

Todo
----

Upgrade kernel: https://github.com/mmind/linux-rockchip

Other RK3188 DTS:
- https://github.com/RobertCNelson/device-tree-rebasing/blob/master/src/arm/rk3188-radxarock.dts
- https://elixir.bootlin.com/linux/latest/source/arch/arm/boot/dts/rk3188-radxarock.dts
- Kernel 5.9 http://sbexr.rabexc.org/latest/sources/56/809f64bee90a72.html
- https://saurabhsengarblog.wordpress.com/2015/11/28/device-tree-tutorial-arm/
- https://notabug.org/seokj/linux-rockchip/src/master/arch/arm/boot/dts

