# Radxa-Rock-RK3166
Notes on flashing and usage of Radxa Rock board.


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
