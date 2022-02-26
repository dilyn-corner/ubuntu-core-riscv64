# Contents

This directory contains the prerequisites for cross-building the kernel snap.
Specifically, it contains the dependencies for the `ubuntu-core-initramfs` tool
built for RISC-V 64-bit, required to build the `uc-initrd` snap.

Packages were built on a RISC-V 64-bit machine running Ubuntu Server 20.04.

I assume you are likewise on 20.04. On more recent (or older) versions of
Ubuntu, you might not need to do this. For instance, dracut-core 051 is
available on Impish, so you don't need to install it here - just do so in the
usual way.

`systemd-bootchart` is not built for RISC-V because upstream has abandonned the
project (seems like). If it had kept up with `systemd`, we probably wouldn't
have to build it ourselves. But here we are.

The `ubuntu-core-initramfs` tool is not built for RISC-V, and intentionally so.
We must do it ourselves.

To build your initrd...

Install the following:

dpkg -i systemd-bootchart_233-2_riscv64.deb

dpkg -i dracut-core_051-1_riscv64.deb

dpkg -i ubuntu-core-initramfs_51_riscv64.deb

```
cd uc-initrd/
snapcraft --destructive-mode --target-arch=riscv64 --enable-experimental-target-arch
```

The initrd snap is required to use the `snapcraft` plugins to cross-compile the
kernel snap. For instructions, see [the kernel snap readme](../kernel/README.md).


## Generic Instructions
It will probably be the case at some future time that I am not actively updating
these packages. In such a case, you can follow these more generic instructions
to get newer version of these packages (with small tweaks as necessary, left as
an exercise for the builder).

```
Uncomment the deb-src lines in /etc/apt/sources.list and run apt-get update.

apt-get build-dep systemd-bootchart # Needed for initrd, not built for riscv64
apt-get source systemd-bootchart    # Requires a patch! Diverged from upstream.

cd systemd-bootchart*/
patch -p1 < ../patches/riscv64.patch
dpkg-buildpackage -us -uc -nc

dpkg -i ../systemd-bootchart_233-2_riscv64.deb

# You might require a more recent version of Dracut! 
wget http://ports.ubuntu.com/pool/universe/d/dracut/dracut-core_051-1_riscv64.deb
dpkg -i dracut-core_051-1_riscv64.deb

git clone https://git.launchpad.net/ubuntu-core-initramfs
cd ubuntu-core-initramfs
git checkout core20 # Presumably
patch -p1 < ../patches/ubuntu-core-initramfs.patch
apt-get build-dep .
dpkg-buildpackage -us -uc -nc

dpkg -i ../ubuntu-core-initramfs_51_riscv64.deb

cd uc-initrd/
snapcraft --destructive-mode --target-arch=riscv64 --enable-experimental-target-arch
```
