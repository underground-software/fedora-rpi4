# A guide for native mainline kernel build and install on fedora/rpi4

Install dependencies:
```bash
dnf -y install gcc flex make bison openssl openssl-devel elfutils-libelf-devel ncurses-devel bc git tar dwarves 
```

Get the mainline kernel from upstream repo:
```bash
git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/
```

Copy over config and validate it (fill in missing defaults): 
```bash
cp /boot/config-<kernel-version>.aarch64 .config
make olddefconfig
```

Compile The Kernel (use a cooling case or CPU gets extremely hot):
*We suggest thta one run the following commands without the -j4 flag if no cooling case is available (takes longer to build)*

```bash
make -j4
# make modules_install -j4
```

Install:
```bash
make dtbs_install
make install 
```

```bash
cd /boot
to_link="$(ls -t | grep dtb | grep -v ^dtb$ | head -n1)"
ln -sf $to_link dtb
```

Set compiled kernel as default kernel:
```bash
cd /boot
to_grub="$(ls -t $PWD/vmlinuz* | head -n1)"
grubby --set-default $to_grub
```

Reboot system: 
```bash
sudo reboot
```

The following only applies if you are booting with UEFI
 
On reboot, press esc to display menu and go to:
Device manager -> Raspberry Pi Configuration -> Advanced Configuration
Set < System Table Selection > to <ACPI + Devicetree> 

