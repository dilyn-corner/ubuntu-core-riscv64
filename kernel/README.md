# The Kernel Snap

The kernel snap is a powerful snap. It includes both the kernel image as well as
the initrd for Ubuntu Core.

This specific kernel builds for Sipeed's Lichee RV, using [Samuel
Holland](https://github.com/smaeul/linux)'s riscv/d1-wip branch. Specifically,
the device tree for the dock is built. If you are not using the dock, modify as
appropriate.

Under heavy development currently.

As it stands, this kernel will boot the board on a `debootstrap` built Ubuntu
rootfs. This indicates that there is a problem with the gadget snap. Without
access to UART, I cannot debug further. This should be overcome soon(tm).

Building:

`snapcraft --destructive-mode --target-arch=riscv64 --enable-experimental-target-arch`

## For Beginners

Building a kernel for the first time is no easy task. There isn't a very good
resource for what any given kernel option really entails. Your best bet is to
check out the help text for any given symbol and check the code. Ultimately,
starting with a defconfig is the best way to start (check arch/riscv/configs).

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
