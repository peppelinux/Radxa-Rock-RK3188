# Radxa-Rock-RK3166
Notes on flashing and usage of Radxa Rock board


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
