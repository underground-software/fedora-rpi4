### How to boot without an SD Card (just one drive)

Prerequisites:
1. Raspberry Pi
2. Main storage device (hard drive, usb stick, sd card, etc.)
3. Installation disk (usb stick, sd card, etc.)
4. Screen + keyboard/mouse

Steps:
1. Download the latest (or desired) UEFI Raspberry Pi Firmware ZIP from:
https://github.com/FwMotion/RPi4/releases/

2. Plug the main drive in to your computer, this is the drive that will host
your Raspberry Pi's operating system

3. Find the name of the drive from the program Disks or by running lsblk,
it should look like /dev/<something> (usually sda, sdb, mm..., etc.)

4. Run the following commands to setup your main drive:
```
sudo fdisk /dev/<disk-name>
o
n
Return (uses default option p - primary)
1
Return (uses default first sector)
+2G (for 2 GB of space)
(if prompted to remove vfat signature, just select yes)
t
c
w
(This should exit fdisk and format your partition)

(notice to add 1 at the end of the disk name, i.e. /dev/sda1)
sudo mkfs.vfat -v -F 32 -n UEFI /dev/<disk-name>1

sudo mkdir /mnt/rpi-uefi

sudo mount /dev/<disk-name>1 /mnt/rpi-uefi

cd /mnt/rpi-uefi

sudo unzip path/to/download/Rpi...Firmware.zip
```

5. To prepare the installer drive, download the latest (or desired) ISO:
https://alt.fedoraproject.org/alt/

6. Find the name of your installer disk (must be different from main disk)
using the Disks program or the command lsblk. It should also look like
/dev/<name> (sdb, mm... , etc.)

7. Install using the Fedora Media Tool or using:
```
sudo dd of=/dev/<disk-name> if=path/to/Fedora-...iso bs=4M conv=nocreat,notrunc
status=progress
```

8. Plug your Raspberry Pi to a screen, a mouse/keyboard, and the two disks:
Note: If either or both the disks are USB, plug them into the blue USB sockets

9. You should see the Raspberry Pi firmware begin to boot, press Esc to get
to the boot menu.

10. Select Boot Manager

11. Select your installation disk (should be labeled Fedora...)

12. Select Install on this device

13. Install Fedora using the default options

14. When finished, unplug the Raspberry Pi from power, remove the installation
disk, and plug it back in. You should be brought into Fedora.
