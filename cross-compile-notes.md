# Notes on cross compiling upstream kernel for rpi4

## This guide is based on running an x86_64 host on fedora and an rpi4 running fedora on aarch64

On your host machine:
 - install binutils and gcc for cross compilation:
	`sudo dnf install binutils-aarch64-linux-gnu gcc-aarch64-linux-gnu`
 - run `lsblk` to get baseline of installed harddrives
 - power down pi
 - move harddrive to laptop
 - run `lsblk` again, and find the new entry corresponding to harddrive for example: /dev/sda
 - identify the boot partition (I think it defaults to approx 1GB in size) for example /dev/sda1
 - identify the root partition (it will be the largest partition, many GB) for example /dev/sda2
 - run
```
sudo mount /dev/sda2 /mnt #(where /dev/sda2 is the root partition you identified)
sudo mount /dev/sda1 /mnt/boot #(where /dev/sda1 is the boot partition you identified)
cd /mnt/home/pi/path/to/linux/ #(where pi is your username on the pi and path/to/linux is where you keep the kernel source tree)
export ARCH=arm64
export CROSS_COMPILE=/bin/aarch64-linux-gnu- #(this path may need adjusting, but that was where it was installed on my machine)
export INSTALL_MOD_PATH=/mnt/
sudo cp /mnt/boot/config-... ./.config #find the most recent config (i.e. largest kernel version number)
sudo chown $USER:$USER .config
make olddefconfig
make menuconfig #optional, if you want to adjust config options
make -j13 #replace 13 with one more than the number of threads your computer has
sudo make -j13 modules_install #replace 13 with the same number you used on the previous command
sync
sudo umount /mnt/boot
sudo umount /mnt
```
 - unplug harddrive and plug back into pi
 - power up pi and let it boot
On the pi:
 - log in and run `uname -r` and write down which version you are running so you can be sure it updates
 - cd into kernel source directory and run `sudo make install`
 - reboot and log in again and compare output of `uname -r` you should be running a new upstream kernel
