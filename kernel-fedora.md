# A guide on how to build the fedora kernel binary rpm for the rpi4

1. Make a fedora account [here](https://accounts.fedoraproject.org/)

2. Clone the kernel from the "Always Ready Kernel (ARK)" source tree:
```bash
cd && https://gitlab.com/cki-project/kernel-ark
```

The "os-build" branch is the master for kernel-ark and one should open merg e requests (MRs) against this branch.

3. Use of Fedora Linux as a development environment is encouraced but not required. However, the following may need substantial tweaking to work on non-Fedora distributions

4. Install fedora-packager and configure authentication:
```bash
FAS_USERNAME=<fedora account system username in all lowercase>
dnf install fedora-packager
KRB5_TRACE=/dev/stdout kinit ${FAS_USERNAME}@FEDORAPROJECT.ORG
echo ${FAS_USERNAME} > ~/.fedora.upn
```


5. Install dependencies and configure git if necessary:
```bash
sudo dnf install git make gcc flex bison bzip2 rpm-build
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

6. Make a source rpm and send it over to the koji build system:
```bash
TARGET=<the target fedora version, e.g. rawhide, f34, f35>
cd ~/kernel-ark
make dist-srpm
koji build --scratch ${TARGET} redhat/rpm/SRPMS/kernel-*.src.rpm
```

7. Wait for the kernel to build. This may take some time.
The output of the `koji build ...` command at step 6
contains a line like the follwing:
`Task info: https://koji.fedoraproject.org/koji/taskinfo?taskID=XXXXXXX`

Go to this url to check out the progress in more detail.

8. TODO: add repo, install, check grubby, reboot

Appendix:

The following only applies if you are booting with UEFI
 
On reboot, press esc to display menu and go to:
Device manager -> Raspberry Pi Configuration -> Advanced Configuration
Set < System Table Selection > to <ACPI + Devicetree> 

