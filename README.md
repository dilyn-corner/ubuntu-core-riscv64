# ubuntu-core-riscv64

Provides the tooling for building an Ubuntu Core image on RISC-V.


### Structure

This repository is segmented into several major pieces:

| Object             | Purpose                            |
|--------------------|------------------------------------|
| gadget/            | Gadget Snap                        |
| kernel/            | Kernel Snap                        |
| \*.json            | Model JSON                         |
| qemu-run           | Qemu Invocation                    |
| riscv64-components | Bits required to build Initrd Snap |


### Installing QEMU for riscv64

For what follows, you'll need to install the `qemu-system-riscv64`
executable, which is part of the `qemu-system-misc` deb package, along with the
`opensbi` and `u-boot-qemu` packages:

```
sudo apt install qemu-system-misc opensbi u-boot-qemu
```

### Instructions

**Note**: premade Ubuntu Core images is available for download from
[here](https://github.com/dilyn-corner/ubuntu-core-riscv64/releases)!

If you are on an Ubuntu system (20.04+ tested), simple download the image and
execute the `qemu-run` script.

If you are not on an Ubuntu system, you will have to build the gadget snap
yourself and modify the `qemu-run` script; remove the `-kernel` line and modify
the `bios` line to point to `gadget/prime/fw_payload.bin`.

Once the image has been booted, you can log into it from a different terminal
session with:

```
ssh <SSO name>@localhost -p 22222
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

You will require `snapcraft` and `ubuntu-image`. The latest commit tracks Ubuntu
Core 22 and supports snapcraft 7.x. 

```
snap install --classic snapcraft
snap install --classic ubuntu-image
```

For the model:

```
# Make relevant modifications to ubuntu-core-XX-riscv64.json if needed
# Be sure to change authority-id, brand-id; use the id reported by 
# snapcraft whoami | grep id
snapcraft create-key   riscy-key
snapcraft register-key riscy-key

snap sign -k riscy-key > ubuntu-core-XX-riscv64.model < ubuntu-core-XX-riscv64.json
```

You can create an image based on that signed model assertion with:

```
ubuntu-image snap        \
    --snap gadget/*.snap \
    --snap kernel/*.snap \
    ubuntu-core-*-riscv64.model
```

From there, you should be able to launch a Qemu virtual machine using the
created Ubuntu Core image as a ROOTFS by simply doing:

`./qemu-run`


### Explain Key Files
```
#!/bin/sh -e

if [ ! -e /usr/lib/riscv64-linux-gnu/opensbi/generic/fw_jump.elf ]; then
    echo "Please install the opensbi package"
    exit 1
fi
if [ ! -e /usr/lib/u-boot/qemu-riscv64_smode/uboot.elf ]; then
    echo "Please install the u-boot-qemu package"
    exit 1
fi

qemu-system-riscv64 \
    -M virt -m 2G -smp 1 -nographic \
    -bios /usr/lib/riscv64-linux-gnu/opensbi/generic/fw_jump.elf \
    -kernel /usr/lib/u-boot/qemu-riscv64_smode/uboot.elf \
    -drive file=virt.img,format=raw,if=virtio \
    -object rng-random,filename=/dev/urandom,id=rng0 \
    -device virtio-rng-device,rng=rng0 \
    -device virtio-net-device,netdev=usernet \
    -netdev user,id=usernet,hostfwd=tcp::22222-:22
```

This script is designed with Ubuntu systems in mind. We check that the important
payload packages Qemu requires exist on the host, which we will use to boot our
machine. We *could* use the artifacts created when building the gadget snap, and
this script may be modified in the future to have some more robust logic to
fallback to the `fw_payload.bin` in `gadget/prime`.

We use a virtio device as the drive (booting from SD is not supported on the
virt machine). This is important to consider for things like having VIRTIO_FS
available in the initrd. This is why we have to unpack it and manually add some
modules to be available in our early boot environment to be able to find the
virtual disks.

The RNG device is just for fun (and entropy is great).

Port forwarding for SSH access.


```
{
    "type": "model",
    "series": "16",
    "authority-id": "cHcxHFRxgHBseRyUpLXtu6amXHgPyFQc",
    "brand-id": "cHcxHFRxgHBseRyUpLXtu6amXHgPyFQc",
    "model": "ubuntu-core-22-riscv64",
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
            "name": "core22",
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

`{authority,brand}-id` point at me, the signer of the model. 

`grade` is dangerous because we have to sideload our kernel and gadget snaps.

`default-channel` and `id` are optional fields for snaps when using dangerous
grade models, and so we don't need to include any garbage data here for our
gadget or kernel snaps. Just make sure the names match and you actually have a
declared gadget and kernel.
