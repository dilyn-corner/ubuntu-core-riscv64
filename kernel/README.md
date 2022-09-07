# The Kernel Snap

The kernel snap is a powerful snap. It includes both the kernel image as well as
the initrd for Ubuntu Core.

This specific kernel builds for [Microchip's Polarfire SoC Icicle
Kit](https://github.com/linux4microchip/linux).

More specifically, this kernel builds from based on the [Ubuntu RISC-V Jammy
kernel](https://git.launchpad.net/~dilyn-corner/ubuntu/+source/linux-riscv/+git/jammy?h=icicle), with patches plucked from the Microchip kernel and applied to the Ubuntu kernel.

Because `snapcraft 7` does not support user-defined plugins when using a Core22
base, we will make use of `snapcraft 6` and define `base: core20`. This
shouldn't result in any issues with userspace, and the only potential problem is
with `snapd-bootstrap` during first-boot. Luckily, there were no real changes
between Core20 and Core22's implementation of `snapd-bootstrap`, and so we can
"get away" with this abnormal deviation.

The initrd snap was rebuilt from a Focal rootfs running natively on this
hardware using the kernel and modules built for this snap.

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
