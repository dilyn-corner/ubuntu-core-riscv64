# ubuntu-core-riscv64

Provides the tooling for building an Ubuntu Core image on RISC-V.


## This is in no way supported by Canonical.


### Structure

This repository is segmented into several major pieces:

| Object             | Purpose                            |
|--------------------|------------------------------------|
| gadget/            | Gadget Snap                        |
| kernel/            | Kernel Snap                        |
| \*.json            | Model JSON                         |
| qemu-run           | Qemu Invocation                    |
| riscv64-components | Bits required to build Initrd Snap |

I would recommend doing a recursive clone (`git clone --recursive`) of this
repository to have the submodules be pulled in for you. If you forget to
clone recursively, you can fix that after the fact with:
```
$ git submodule update --init --recursive
```


### Installing QEMU for riscv64

For what follows, you'll need to install the `qemu-system-riscv64`
executable, which is part of the `qemu-system-misc` .deb package:

```
$ sudo apt install qemu-system-misc
```
### Instructions

**Note**: a premade Ubuntu Core 20 image is available for download from
[here](https://github.com/dilyn-corner/ubuntu-core-riscv64/releases/tag/qemu-test-image)!
Download the compressed image and the `fw_payload.elf` binary, decompress the
image, modify `qemu-run` or move `fw_payload.elf` to the correct location, and
it should Just Work. Once the image has been booted, you can log into it from
a different terminal session with:
```
$ ssh <SSO name>@localhost -p 22222
```

If you would rather build everything yourself...

The high-level instructions for this project are quite simple:

0) [Build a RISC-V 64-bit Ubuntu Core initrd snap](riscv64-components/README.md)
1) [Build a Kernel Snap using that initrd snap for RISC-V](kernel/README.md)
2) [Build a Gadget Snap for your particular board](gadget/README.md)
3) Create a signed model assertion
4) Create an Ubuntu Core Image using that Gadget & Kernel
5) Successfully boot Ubuntu Core on RISC-V

Step 0 is optional -- all the steps to create an initrd were already performed
and the resulting initrd snap can be found in [the kernel directory](kernel/sources)

More detailed information can be found in the READMEs!

For the model:

```
# Make relevant modifications to ubuntu-core-20-riscv64.json if needed
# Be sure to change authority-id, brand-id
snapcraft create-key   riscy-key
snapcraft register-key riscy-key
snap sign -k riscy-key > ubuntu-core-XX-riscv64.model < ubuntu-core-XX-riscv64.json

ubuntu-image snap        \
    --snap gadget/*.snap \
    --snap kernel/*.snap \
    ubuntu-core-*-riscv64.model
```

From there, you should be able to launch a Qemu virtual machine using the
created Ubuntu Core image as a ROOTFS by simply doing:
`./qemu-run`


### Future work

Drop `kernel/sources/config` and use `kconfig` in `snapcraft.yaml`
    * Potentially just minify the `config` to make kernel builds faster...

`u-boot` builds a device tree for our hardware. Investigate having the gadget
  provide our device tree.


### Explain Key Files
```
#!/bin/sh -e

qemu-system-riscv64 \
    -M virt -m 2G -smp 1 -nographic \
    -bios gadget/prime/fw_payload.elf \
    -drive file=virt.img,format=raw,if=virtio \
    -object rng-random,filename=/dev/urandom,id=rng0 \
    -device virtio-rng-device,rng=rng0 \
    -device virtio-net-device,netdev=usernet \
    -netdev user,id=usernet,hostfwd=tcp::22222-:22
```

We add in a bios line pointing at our payload for two reasons. First, we need
this low-level bit to start everything moving. Second, if a bios file is not
explicitly specified, Qemu will automatically use a predefined one. This one
will, of course, not be using our u-boot, and we won't boot into UC :)

We use a virtio device as the drive (booting from SD is not supported on the
virt machine). This is important to consider for things like having VIRTIO_FS
built into the kernel, as `modprobe` is not part of `busybox` in the initrd.

The RNG device is just for fun (and entropy is great).

Port forwarding for SSH access.


```
{
    "type": "model",
    "series": "16",
    "authority-id": "cHcxHFRxgHBseRyUpLXtu6amXHgPyFQc",
    "brand-id": "cHcxHFRxgHBseRyUpLXtu6amXHgPyFQc",
    "model": "ubuntu-core-20-riscv64",
    "architecture": "riscv64",
    "timestamp": "2022-02-18T21:50:41+00:00",
    "base": "core22",
    "grade": "dangerous",
    "snaps": [
        {
            "name": "virt",
            "type": "gadget"
        },
        {
            "name": "virt-kernel",
            "type": "kernel"
        },
        {
            "name": "core20",
            "type": "base",
            "default-channel": "latest/stable",
            "id": "amcUKQILKXHHTlmSa7NMdnXSx02dNeeT"
        },
        {
            "name": "snapd",
            "type": "snapd",
            "default-channel": "latest/stable",
            "id": "PMrrV4ml8uWuEUDBT8dSGnKUYbevVhc4"
        }
    ]
}
```

`{authority,brand}-id` point at the official Snapcraft store for simplicity.

`grade` is dangerous because we have to sideload our kernel and gadget snaps.

`default-channel` and `id` are optional fields for snaps, and so we don't need
to include any garbage data here for our gadget or kernel snaps. Just make sure
the name match and you actually have a declared gadget and kernel.
