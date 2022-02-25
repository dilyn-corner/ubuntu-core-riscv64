# The Kernel Snap

The kernel snap is a powerful snap. It includes both the kernel image as well as
the initrd for Ubuntu Core. The kernel can be very device specific. Be sure to
modify the `snap/snapcraft.yaml` for the proper source, config, and device tree
files for your particular device.

This specific kernel is built with Qemu's RISC-V virt machine. No device tree is
required for this machine; Qemu loads it into the machine's memory for us.

Building:
`snapcraft --destructive-mode --target-arch=riscv64 --enable-experimental-target-arch`
