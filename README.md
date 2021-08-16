# fedora-rpi4

This repository contains some notes on the usage of Fedora
on the Raspberry Pi 4B+ computer

### Simple UEFI installation of Fedora on the Raspberry Pi 4:

Assume all commands must be run as superuser. Some don't,
but they will work fine with UID 0 anyway.

1. Get the latest RPi4 UEFI firmware

[Releases page on their GitHub](https://github.com/pftf/RPi4/releases)

2. Format an SD card to hold the UEFI firmware:

At the shell:
```bash
fdisk /dev/sdN
```
(where N is alphabetical index of the SD card in `/dev/`)

Enter the following commands (parentheticals are explanation)
```
> o (create dos partition table)
> n (new partition)
> p (primary)
> 1 (first)
> (leave blank, start at default of 2048)
> +512M (plenty of room for doing things)
(if it asks you if you want to remove a vfat signature or something, just do it. We will be formatting anyway)
> t (select partition 1)
> e (type 0e W95 FAT16 (LBA), use > L to see all if you're curious)
> a (toggle the bootable flag on partition 1
> w (write the changes to the disk)
```

And back at the shell:
```bash
mkfs.vfat -F 16 /dev/sd<LETTER>1 (make that VFAT partition)
```

3. Mount the new partition and extract the UEFI bootloader onto it

Run the following comamnds:
```bash
mount /dev/sdN1 <mount_dir> # (mount dir can be any dir)
unzip RPi4_UEFI_Firmware_v<VERSION>.zip -d <mount_dir>
```

---
*Note: I don't think it would be that complicated to put the UEFI
firmware on the flash drive to create a single bootable image,
perhaps by mounting a spin archive before flashing. I have not
tried this, however.*

---

4. Get a a fresh Fedora image

From [here](https://alt.fedoraproject.org/alt/)

I recommend the minimal image because it is small.


5. Extract Fedora onto a flash drive that you are cool with wiping

Run the following command:
```bash
xzcat Fedora-Minimal-Rawhide-<some_numbers_idk>.aarch64.raw.xz | dd of=/dev/sdM status=progress bs=4M
```

(where M is alphabetical index of the Flash drive in `/dev/`)


6. Boot Fedora via UEFI

Insert the SD card and flash drive into your RPi4, but not in the same hole.

If everything went right, you should see a white raspberry logo.
When you do, hit escape on your keyboard (I should have mentioned it at the top,
but you need a keyboard. A monitor might help too).

Select boot manager, and from there select the last option
(I assume it will look something like USB device or storage device,
but this may vary).
If you see Linux boot a few seconds later, then you selected the right device.
If you don't try a different device in the boot manager.

If nothing explodes, you should be greeted with the anaconda CLI interface.
From here, use Fedora as you normally would. Install a GUI maybe, it's up to you.
Things will probably be unstable since you're using Fedora Rawhide
on a device not yet explicitly supported by the maintainers of Fedora ARM,
but that's part of the fun.

---
If you would like to enable Device Tree, do so in the UEFI menu
that appears when you press "esc" at the raspberry pi logo that appears
at the start of booting. By defauly, only ACPI is enabled.
