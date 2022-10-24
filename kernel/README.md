# The Kernel Snap

The kernel snap is a powerful snap. It includes both the kernel image as well as
the initrd for Ubuntu Core. The kernel can be very device specific.

This specific kernel is built for Qemu's RISC-V virt machine. Qemu bundles its
own device tree for the RISC-V virt machine and one isn't available in the
kernel source tree. So at snap build-time we dump the dtb file. Note that
the machine definition in the `qemu-system-riscv64` invocation must match the
dtb file used to run the machine. This means that if we want to use e.g. the
full 8 cores available on the virt machine, we would have to modify both the
snap and the Qemu invocation. 

Later kernel will provide better RISC-V support; I would recommend at least
5.11, but any RISC-V supporting kernel should work well enough. Here we use the
kernel debian package available directly from the Ubuntu archives for
simplicity and to speed up snap building.

Building:

`snapcraft`

## For Beginners

Building a kernel for the first time is no easy task. There isn't a very good
resource for what any given kernel option really entails. Your best bet is to
check out the help text for any given symbol and check the code. Ultimately,
starting with a defconfig is the best way to start (check `arch/riscv/configs`).

Slimming down the number of builtins and modules is good for (mostly) one thing:
faster kernel builds. You won't generally see much in terms of performance
improvements without substantial external patching (and even then, unlikely),
but removing device drivers you know won't ever be needed can substantially
reduce build times.

That being said, the kernel can certainly become smaller; with the current
config, this kernel is over 20MB. It need not be. Building more things as
modules can help slim it down, though it does increase the kernel's size on disk
in other locations. On embedded hardware, a modular kernel is probably less than
ideal. Just build things in, and remove things you know you don't need.

Additionally, the built kernel snap almost crests 500MB. This is almost
exclusively due to the firmware bundled with the snap. Most of it can probably
be removed; you only need firmware for hardware which requires it.

Finally, you'll need an initrd snap to be squashed up with the kernel thanks to
the plugins we use for this snap. For now, the usual places from which the
uc-initrd snap is fetched from do not have a copy of it. Build your own, or use
the bundled one.
