# ubuntu-core-riscv64

Provides the tooling for building a UC20 Image on RISCV64.


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
repository to have the submodules be pulled in for you :)


### Instructions

The high level instructions for this project are quite simple:
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
snap sign -k riscy-key ubuntu-core-20-riscv64.json > ubuntu-core-20-riscv64.model

ubuntu-image snap        \
    --snap gadget/*.snap \
    --snap kernel/*.snap \
    ubuntu-core-20-riscv64.model
```

Once you have built the Ubuntu Core image, merely write it to an SD card, insert
the card into the Sipeed Lichee RV, and add power. In order to get output,
connect to the board via UART. 

**NOTE**: after some testing, I intend on adding a way for HDMI to be the
default output.


### Future work

Slim the `kconfig` section of the `snapcraft.yaml`, potentially by simply using
the official Ubuntu kernel.


```
{
    "type": "model",
    "series": "16",
    "authority-id": "cHcxHFRxgHBseRyUpLXtu6amXHgPyFQc",
    "brand-id": "cHcxHFRxgHBseRyUpLXtu6amXHgPyFQc",
    "model": "ubuntu-core-20-riscv64",
    "architecture": "riscv64",
    "timestamp": "2022-02-18T21:50:41+00:00",
    "base": "core20",
    "grade": "dangerous",
    "snaps": [
        {
            "name": "sipeed-lichee-rv-gadget",
            "type": "gadget"
        },
        {
            "name": "sipeed-lichee-rv-kernel",
            "type": "kernel"
        },
        {
            "name": "core20",
            "type": "base",
            "default-channel": "latest/edge",
            "id": "DLqre5XGLbDqg9jPtiAhRRjDuPVa5X1q"
        },
        {
            "name": "snapd",
            "type": "snapd",
            "default-channel": "latest/edge",
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
