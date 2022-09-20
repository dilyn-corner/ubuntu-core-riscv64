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
# Make relevant modifications to ubuntu-core-XX-riscv64.json if needed
# Be sure to change authority-id, brand-id
snapcraft create-key   riscy-key
snapcraft register-key riscy-key
snap sign -k riscy-key ubuntu-core-XX-riscv64.json > ubuntu-core-XX-riscv64.model

ubuntu-image snap        \
    --snap gadget/*.snap \
    --snap kernel/*.snap \
    ubuntu-core-*-riscv64.model
```

Once you have built the Ubuntu Core image, merely write it to an SD card, insert
the card into the Sipeed Lichee RV, and add power. In order to get output,
connect to the board via UART. 


### Future work

TBD. This project is *roughly* complete.

If you want to see another way of building out support for this board, check out
Isaac's work:

[Gadget snap](https://github.com/IsaacJT/sipeed-lichee-rv-gadget-snap/tree/22)

[Kernel snap](https://github.com/IsaacJT/sipeed-lichee-rv-kernel-snap/tree/22)
