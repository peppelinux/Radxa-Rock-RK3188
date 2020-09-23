````
# create a 6gb file 
dd if=/dev/zero of=6gb.img bs=1M count=$((1024*6))

# check loop bindings
losetup -a

# bind it to /dev/loopN
losetup /dev/loop12 6gb.img 

# copy data
xzcat ~/Downloads/ev3dev-yyyy-mm-dd.img.xz | sudo dd bs=4M of=/dev/loop12 status=progress

# detach device
losetup -d  /dev/loop12
# with kpartx
kpartx -d /dev/loop12

# analyze partitions
kpartx -l 6gb.img 

# bind volume with every partitions
losetup --show -f -P 6gb.img
# or
kpartx -a -v 6gb.img

# here
ls /dev/loop12*
# or
ls /dev/mapper/loop12*

# edit volume
gparted /dev/loop12
# or 
gparted /dev/mapper/loop12p2

# once the last partition have been resized, recreate the image copying only the needed datas
dd if=6gb.img of=2gb.img status=progress bs=1M count=1860

# copy the newly created image to a smaller sd card
dd if=2gb.img of=/dev/sdg status=progress

````
