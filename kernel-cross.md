# [WIP] A guide for mainline kernel cross compilation and install on fedora/rpi4

Based on [this](https://gist.github.com/lategoodbye/c7317a42bf7f9c07f5a91baed8c68f75#raspberry-pi-how-to-cross-compile-and-use-mainline-kernel)

### Prep

##### On the RPi

Setup an NFS server (as superuser):

```bash
IP=<allowed ip address range> # (e.g. 10.0.0.1/24 for 10.0.0.[0-255])
dnf install nfs-utils
echo / $IP(rw,sync,no_squash_root) > /etc/exports
systemctl start nfs-server # (or restart as needed)
```

##### On the client:

Mount:
```bash
mount -t nfs <pi_ip_addr>:/ <local_mountpoint>
```

Install dependencies on the client:
```bash
dnf -y install gcc flex make bison openssl openssl-devel elfutils-libelf-devel ncurses-devel bc git tar dwarves 
```

Clone the mainline kernel

cross compiler source [here](https://releases.linaro.org/components/toolchain/binaries/latest-7/aarch64-linux-gnu/)

setup cross compiler
```bash
export CROSS_COMPILE=/mnt/HDD0/crosskernel/gcc-linaro-7.5.0-2019.12-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-
export ARCH=arm64
```

build and install:
```bash
make -j $(nproc)
sudo make modules_install ARCH=arm64 INSTALL_MOD_PATH=/mnt/rpi
sudo make zinstall ARCH=arm64 INSTALL_PATH=/mnt/rpi/boot
sudo make dtbs_install ARCH=arm64 INSTALL_PATH=/mnt/rpi/boot
mv /boot/dtbs/<new-version> /boot/dtb-<new-version>
dracut initramfs-5.14.0-rc4-00238-g4d19a608b528 --verbose
```

TODO: get this to work
