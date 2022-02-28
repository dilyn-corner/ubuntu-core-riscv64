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
dilyn@Ares:~/git/core-riscv64 -> ./qemu-run                                                                                                       

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
Domain0 Boot HART         : 0
Domain0 HARTs             : 0*,1*,2*,3*
Domain0 Region00          : 0x0000000002000000-0x000000000200ffff (I)
Domain0 Region01          : 0x0000000080000000-0x000000008007ffff ()
Domain0 Region02          : 0x0000000000000000-0xffffffffffffffff (R,W,X)
Domain0 Next Address      : 0x0000000080200000
Domain0 Next Arg1         : 0x0000000082200000
Domain0 Next Mode         : S-mode
Domain0 SysReset          : yes

Boot HART ID              : 0
Boot HART Domain          : root
Boot HART ISA             : rv64imafdcsu
Boot HART Features        : scounteren,mcounteren,time
Boot HART PMP Count       : 16
Boot HART PMP Granularity : 4
Boot HART PMP Address Bits: 54
Boot HART MHPM Count      : 0
Boot HART MIDELEG         : 0x0000000000000222
Boot HART MEDELEG         : 0x000000000000b109


U-Boot 2022.01 (Feb 27 2022 - 18:42:18 -0500)

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
2338 bytes read in 1 ms (2.2 MiB/s)
## Executing script at 88100000
Loading ubuntu-seed configuration...
4096 bytes read in 12 ms (333 KiB/s)
## Warning: defaulting to text format
## Warning: Input data exceeds 1048576 bytes - truncated
## Info: input data size = 1048578 = 0x100002
Loading systems/20220228/kernel/kernel.img...
25257472 bytes read in 50 ms (481.7 MiB/s)
Loading systems/20220228/kernel/dtbs/riscv64-virt.dtb...
1048576 bytes read in 3 ms (333.3 MiB/s)
Loading systems/20220228/kernel/initrd.img...
15906690 bytes read in 24 ms (632.1 MiB/s)
Scanning disk virtio-blk#0...
Found 2 disks
No EFI system partition
Booting /systems\20220228\kernel\kernel.img
EFI stub: Booting Linux Kernel...
EFI stub: Using DTB from configuration table
EFI stub: Exiting boot services and installing virtual address map...
[    0.000000] Linux version 5.11.22 (dilyn@Ares) (riscv64-linux-gnu-gcc-10 (Ubuntu 10.3.0-8ubuntu1) 10.3.0, GNU ld (GNU Binutils for Ubuntu) 2.37) #1 SMP Sun Feb 27 19:28:03 EST 2022 (Ubuntu 5.11.0-1029.32~20.04.1-generic 5.11.22)
[    0.000000] OF: fdt: Ignoring memory range 0x80000000 - 0x80200000
[    0.000000] earlycon: sbi0 at I/O port 0x0 (options '')
[    0.000000] printk: bootconsole [sbi0] enabled
[    0.512615] Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)
[    0.515328] CPU: 0 PID: 1 Comm: swapper/0 Not tainted 5.11.22 #1
[    0.516580] Call Trace:
[    0.517125] [<ffffffe000005a4a>] walk_stackframe+0x0/0xcc
[    0.518464] ---[ end Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0) ]---
```
