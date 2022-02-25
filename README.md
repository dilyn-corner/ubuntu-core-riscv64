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

See [the gadget readme](gadget/README.md), [the kernel
readme](kernel/README.md), and [the riscv64-components
readme](riscv64-components/README.md) for more information on them.


### Instructions

The high level instructions for this project are quite simple:
1) Build a RISC-V 64-bit Ubuntu Core initrd snap
2) Build a Kernel Snap using that initrd snap for RISC-V
3) Build a Gadget Snap for your particular board
4) Create an Ubuntu Core Image using that Gadget & Kernel
5) Successfully boot Ubuntu Core on RISC-V

More detailed information can be found in the READMEs!

Specifically, for the model:

```
# Make relevant modifications to ubuntu-core-20-riscv64.json if needed
snapcraft create-key riscy-key
snapcraft registery-key riscy-key
snap sign -k riscy-key > ubuntu-core-20-riscv64.model < ubuntu-core-20-riscv64.json

ubuntu-image snap ubuntu-core-20-riscv64.model \
    --snap gadget/virt_*_riscv64.snap \
    --snap riscv64-virt-kernel_*_riscv64.snap
```

From there, you should be able to launch a Qemu virtual machine using the
created Ubuntu Core image as a ROOTFS by simply doing:
`./qemu-run`



### Troubles
```
dilyn@Ares:~/git/core-riscv64\ -> ./qemu-run                                                                                                      

OpenSBI v1.0
   ____                    _____ ____ _____
  / __ \                  / ____|  _ \_   _|
 | |  | |_ __   ___ _ __ | (___ | |_) || |
 | |  | | '_ \ / _ \ '_ \ \___ \|  _ < | |
 | |__| | |_) |  __/ | | |____) | |_) || |_
  \____/| .__/ \___|_| |_|_____/|____/_____|
        | |
        |_|

Platform Name             : riscv-virtio,qemu
Platform Features         : medeleg
Platform HART Count       : 4
Platform IPI Device       : aclint-mswi
Platform Timer Device     : aclint-mtimer @ 10000000Hz
Platform Console Device   : uart8250
Platform HSM Device       : ---
Platform Reboot Device    : sifive_test
Platform Shutdown Device  : sifive_test
Firmware Base             : 0x80000000
Firmware Size             : 292 KB
Runtime SBI Version       : 0.3

Domain0 Name              : root
Domain0 Boot HART         : 1
Domain0 HARTs             : 0*,1*,2*,3*
Domain0 Region00          : 0x0000000002000000-0x000000000200ffff (I)
Domain0 Region01          : 0x0000000080000000-0x000000008007ffff ()
Domain0 Region02          : 0x0000000000000000-0xffffffffffffffff (R,W,X)
Domain0 Next Address      : 0x0000000080200000
Domain0 Next Arg1         : 0x0000000082200000
Domain0 Next Mode         : S-mode
Domain0 SysReset          : yes

Boot HART ID              : 1
Boot HART Domain          : root
Boot HART ISA             : rv64imafdcsu
Boot HART Features        : scounteren,mcounteren,time
Boot HART PMP Count       : 16
Boot HART PMP Granularity : 4
Boot HART PMP Address Bits: 54
Boot HART MHPM Count      : 0
Boot HART MIDELEG         : 0x0000000000000222
Boot HART MEDELEG         : 0x000000000000b109


U-Boot 2022.01 (Feb 25 2022 - 15:56:54 -0500)

CPU:   rv64imafdcsu
Model: riscv-virtio,qemu
DRAM:  2 GiB
Flash: 32 MiB
Loading Environment from FAT... *** Warning - bad CRC, using default environment

In:    uart@10000000
Out:   uart@10000000
Err:   uart@10000000
Net:   No ethernet found.
Hit any key to stop autoboot:  0 

Device 0: QEMU VirtIO Block Device
            Type: Hard Disk
            Capacity: 5339.0 MB = 5.2 GB (10934306 x 512)
... is now current device
Scanning virtio 0:1...
Found U-Boot script /boot.scr
1643 bytes read in 1 ms (1.6 MiB/s)
## Executing script at 88100000
Loading ubuntu-seed configuration...
4096 bytes read in 2 ms (2 MiB/s)
## Warning: defaulting to text format
## Warning: Input data exceeds 1048576 bytes - truncated
## Info: input data size = 1048578 = 0x100002
Loading systems/20220225/kernel/kernel.img...
22531072 bytes read in 16 ms (1.3 GiB/s)
** No boot file defined **
Loading systems/20220225/kernel/initrd.img...
15906489 bytes read in 15 ms (1011.3 MiB/s)
Moving Image from 0x84000000 to 0x80200000, end=81853000
## Flattened Device Tree blob at ff73b740
   Booting using the fdt blob at 0xff73b740
   Using Device Tree in place at 00000000ff73b740, end 00000000ff73fe0d

Starting kernel ...
```


### Future work

1) Improve `gadget/u-boot/boot.scr.in` to handle non-bootstrap cases.
2) Boot on real hardware.
