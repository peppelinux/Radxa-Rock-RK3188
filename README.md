# Radxa-Rock-RK3166
Notes on flashing and usage of Radxa Rock boards.

https://wiki.radxa.com/File:Rock_front.jpg

````
apt install lzop libusb-1.0-0-dev git
git clone https://github.com/radxa/rabian-build.git
cd rabian-build
./rabian-build.sh rock
````

Troubleshooting
---------------

Can't use 'defined(@array)' (Maybe you should just omit the defined()?) at kernel/timeconst.pl
````
# -	if (!defined(@val)) {
# +	if (!@val) {
nano $(date +%Y%m%d)/rock-bsp/boards/rock/linux-rockchip/kernel/timeconst.pl
/rabian-build.sh rock
````
Flash
-----

````
cd Linux_Upgrade_Tool_v1.21
./upgrade_tool uf /home/wert/RadxaRock_rk30/rabian-build/200905/rock-bsp/boards/rock/rockdev/rock_rabian_rock_200905_30273dc_nand.img
````


First Access
------------

usernme: rock
passwd: rock

Build your own Kernel image
---------------------------

````
apt install flex bison
apt install git build-essential gcc-arm-linux-gnueabihf lzop libncurses5-dev libssl-dev bc
git clone -b stable --depth 1 git://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git

cd linux-next
wget http://rockchip.fr/radxa/linux/rockchip_defconfig -O arch/arm/configs/rockchip_defconfig
wget http://rockchip.fr/radxa/linux/rk3188-radxarock.dts -O arch/arm/boot/dts/rk3188-radxarock.dts
````

Other RK3188 DTS:
- https://github.com/RobertCNelson/device-tree-rebasing/blob/master/src/arm/rk3188-radxarock.dts
- https://elixir.bootlin.com/linux/latest/source/arch/arm/boot/dts/rk3188-radxarock.dts
- Kernel 5.9 http://sbexr.rabexc.org/latest/sources/56/809f64bee90a72.html
- 


````

export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabihf-

make rockchip_defconfig

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
