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
Platform HART Count       : 1
Platform IPI Device       : aclint-mswi
Platform Timer Device     : aclint-mtimer @ 10000000Hz
Platform Console Device   : uart8250
Platform HSM Device       : ---
Platform Reboot Device    : sifive_test
Platform Shutdown Device  : sifive_test
Firmware Base             : 0x80000000
Firmware Size             : 268 KB
Runtime SBI Version       : 0.3

Domain0 Name              : root
Domain0 Boot HART         : 0
Domain0 HARTs             : 0*
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


U-Boot 2022.01 (Feb 28 2022 - 22:48:06 -0500)

CPU:   rv64imafdcsu
Model: riscv-virtio,qemu
DRAM:  4 GiB
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
2664 bytes read in 1 ms (2.5 MiB/s)
## Executing script at 88100000
Loading ubuntu-seed configuration...
4096 bytes read in 0 ms
## Warning: defaulting to text format
## Warning: Input data exceeds 1048576 bytes - truncated
## Info: input data size = 1048578 = 0x100002
Snapd boot mode: install
Loading systems/20220301/kernel/dtbs/riscv64-virt.dtb...
1048576 bytes read in 1 ms (1000 MiB/s)
Loading systems/20220301/kernel/kernel.img...
29497856 bytes read in 12 ms (2.3 GiB/s)
Loading systems/20220301/kernel/initrd.img...
15906945 bytes read in 6 ms (2.5 GiB/s)
Moving Image from 0x83000000 to 0x80200000, end=81f1b000
## Flattened Device Tree blob at 82201000
   Booting using the fdt blob at 0x82201000
   Using Device Tree in place at 0000000082201000, end 0000000082204ee1

Starting kernel ...

[    0.000000] Linux version 5.13.0-1012-generic (buildd@riscv64-qemu-lcy01-002) (gcc (Ubuntu 11.2.0-7ubuntu2) 11.2.0, GNU ld (GNU Binutils for Ubuntu) 2.37) #14-Ubuntu SMP Wed Feb 9 16:17:11 UTC 2022 (Ubuntu 5.13.0-1012.14-generic 5.13.19)
[    0.000000] OF: fdt: Ignoring memory range 0x80000000 - 0x80200000
[    0.000000] Machine model: riscv-virtio,qemu
[    0.000000] efi: UEFI not found.
[    0.000000] cma: Reserved 32 MiB at 0x0000000086000000
[    0.000000] NUMA: No NUMA configuration found
[    0.000000] NUMA: Faking a node at [mem 0x0000000080200000-0x0000000087ffffff]
[    0.000000] NUMA: NODE_DATA [mem 0x85ff6800-0x85ff7fff]
[    0.000000] Zone ranges:
[    0.000000]   DMA32    [mem 0x0000000080200000-0x0000000087ffffff]
[    0.000000]   Normal   empty
[    0.000000] Movable zone start for each node
[    0.000000] Early memory node ranges
[    0.000000]   node   0: [mem 0x0000000080200000-0x0000000087ffffff]
[    0.000000] Initmem setup node 0 [mem 0x0000000080200000-0x0000000087ffffff]
[    0.000000] On node 0, zone DMA32: 512 pages in unavailable ranges
[    0.000000] SBI specification v0.3 detected
[    0.000000] SBI implementation ID=0x1 Version=0x10000
[    0.000000] SBI TIME extension detected
[    0.000000] SBI IPI extension detected
[    0.000000] SBI RFENCE extension detected
[    0.000000] software IO TLB: Cannot allocate buffer
[    0.000000] SBI v0.2 HSM extension detected
[    0.000000] riscv: ISA extensions acdfimsu
[    0.000000] riscv: ELF capabilities acdfim
[    0.000000] percpu: Embedded 28 pages/cpu s75608 r8192 d30888 u114688
[    0.000000] Built 1 zonelists, mobility grouping on.  Total pages: 31752
[    0.000000] Policy zone: DMA32
[    0.000000] Kernel command line: rootwait rw console=ttyS0 earlycon=sbi snapd_recovery_mode=install snapd_recovery_system=20220301
[    0.000000] Dentry cache hash table entries: 16384 (order: 5, 131072 bytes, linear)
[    0.000000] Inode-cache hash table entries: 8192 (order: 4, 65536 bytes, linear)
[    0.000000] Sorting __ex_table...
[    0.000000] mem auto-init: stack:off, heap alloc:on, heap free:off
[    0.000000] Memory: 31196K/129024K available (9809K kernel code, 5791K rwdata, 10240K rodata, 2534K init, 995K bss, 65060K reserved, 32768K cma-reserved)
[    0.000000] random: get_random_u64 called from kmem_cache_open+0x40/0x3b0 with crng_init=0
[    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1
[    0.000000] ftrace: allocating 38462 entries in 151 pages
[    0.000000] ftrace: allocated 151 pages with 5 groups
[    0.000000] trace event string verifier disabled
[    0.000000] rcu: Hierarchical RCU implementation.
[    0.000000] rcu:     RCU restricting CPUs from NR_CPUS=8 to nr_cpu_ids=1.
[    0.000000]  Rude variant of Tasks RCU enabled.
[    0.000000]  Tracing variant of Tasks RCU enabled.
[    0.000000] rcu: RCU calculated value of scheduler-enlistment delay is 25 jiffies.
[    0.000000] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=1
[    0.000000] NR_IRQS: 64, nr_irqs: 64, preallocated irqs: 0
[    0.000000] riscv-intc: 64 local interrupts mapped
[    0.000000] plic: plic@c000000: mapped 53 interrupts with 1 handlers for 2 contexts.
[    0.000000] riscv_timer_init_dt: Registering clocksource cpuid [0] hartid [0]
[    0.000000] clocksource: riscv_clocksource: mask: 0xffffffffffffffff max_cycles: 0x24e6a1710, max_idle_ns: 440795202120 ns
[    0.000066] sched_clock: 64 bits at 10MHz, resolution 100ns, wraps every 4398046511100ns
[    0.003308] Console: colour dummy device 80x25
[    0.004723] Calibrating delay loop (skipped), value calculated using timer frequency.. 20.00 BogoMIPS (lpj=40000)
[    0.004881] pid_max: default: 32768 minimum: 301
[    0.007309] LSM: Security Framework initializing
[    0.007892] Yama: becoming mindful.
[    0.009669] AppArmor: AppArmor initialized
[    0.010786] Mount-cache hash table entries: 512 (order: 0, 4096 bytes, linear)
[    0.010816] Mountpoint-cache hash table entries: 512 (order: 0, 4096 bytes, linear)
[    0.033313] ASID allocator using 16 bits (65536 entries)
[    0.033831] rcu: Hierarchical SRCU implementation.
[    0.036622] EFI services will not be available.
[    0.038278] smp: Bringing up secondary CPUs ...
[    0.038357] smp: Brought up 1 node, 1 CPU
[    0.045839] devtmpfs: initialized
[    0.050252] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 7645041785100000 ns
[    0.050624] futex hash table entries: 256 (order: 2, 16384 bytes, linear)
[    0.053267] pinctrl core: initialized pinctrl subsystem
[    0.063845] NET: Registered protocol family 16
[    0.065839] audit: initializing netlink subsys (disabled)
[    0.070267] audit: type=2000 audit(0.064:1): state=initialized audit_enabled=0 res=1
[    0.073671] thermal_sys: Registered thermal governor 'fair_share'
[    0.073723] thermal_sys: Registered thermal governor 'bang_bang'
[    0.073735] thermal_sys: Registered thermal governor 'step_wise'
[    0.073742] thermal_sys: Registered thermal governor 'user_space'
[    0.090035] HugeTLB registered 1.00 GiB page size, pre-allocated 0 pages
[    0.090061] HugeTLB registered 2.00 MiB page size, pre-allocated 0 pages
[    0.103821] iommu: Default domain type: Translated 
[    0.106697] SCSI subsystem initialized
[    0.109211] vgaarb: loaded
[    0.109967] usbcore: registered new interface driver usbfs
[    0.110161] usbcore: registered new interface driver hub
[    0.110258] usbcore: registered new device driver usb
[    0.110589] pps_core: LinuxPPS API ver. 1 registered
[    0.110603] pps_core: Software ver. 5.3.6 - Copyright 2005-2007 Rodolfo Giometti <giometti@linux.it>
[    0.110653] PTP clock support registered
[    0.111022] EDAC MC: Ver: 3.0.0
[    0.117005] NetLabel: Initializing
[    0.117023] NetLabel:  domain hash size = 128
[    0.117033] NetLabel:  protocols = UNLABELED CIPSOv4 CALIPSO
[    0.117690] NetLabel:  unlabeled traffic allowed by default
[    0.120308] clocksource: Switched to clocksource riscv_clocksource
[    0.163558] VFS: Disk quotas dquot_6.6.0
[    0.163694] VFS: Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
[    0.166437] AppArmor: AppArmor Filesystem Enabled
[    0.181974] NET: Registered protocol family 2
[    0.182595] IP idents hash table entries: 2048 (order: 2, 16384 bytes, linear)
[    0.186219] tcp_listen_portaddr_hash hash table entries: 256 (order: 0, 4096 bytes, linear)
[    0.186299] TCP established hash table entries: 1024 (order: 1, 8192 bytes, linear)
[    0.186423] TCP bind hash table entries: 1024 (order: 2, 16384 bytes, linear)
[    0.186514] TCP: Hash tables configured (established 1024 bind 1024)
[    0.187643] MPTCP token hash table entries: 256 (order: 0, 6144 bytes, linear)
[    0.187809] UDP hash table entries: 256 (order: 1, 8192 bytes, linear)
[    0.187987] UDP-Lite hash table entries: 256 (order: 1, 8192 bytes, linear)
[    0.189273] NET: Registered protocol family 1
[    0.189498] NET: Registered protocol family 44
[    0.189620] PCI: CLS 0 bytes, default 64
[    0.192488] Trying to unpack rootfs image as initramfs...
[    0.198474] Initialise system trusted keyrings
[    0.199682] Key type blacklist registered
[    0.204423] workingset: timestamp_bits=44 max_order=14 bucket_order=0
[    0.209868] zbud: loaded
[    0.218365] squashfs: version 4.0 (2009/01/31) Phillip Lougher
[    0.224897] fuse: init (API version 7.34)
[    0.226854] integrity: Platform Keyring initialized
[    0.264439] Key type asymmetric registered
[    0.264504] Asymmetric key parser 'x509' registered
[    0.264674] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 244)
[    0.265180] io scheduler mq-deadline registered
[    0.267903] shpchp: Standard Hot Plug PCI Controller Driver version: 0.4
[    0.274097] pci-host-generic 30000000.pci: host bridge /soc/pci@30000000 ranges:
[    0.274735] pci-host-generic 30000000.pci:       IO 0x0003000000..0x000300ffff -> 0x0000000000
[    0.275095] pci-host-generic 30000000.pci:      MEM 0x0040000000..0x007fffffff -> 0x0040000000
[    0.275140] pci-host-generic 30000000.pci:      MEM 0x0400000000..0x07ffffffff -> 0x0400000000
[    0.275474] pci-host-generic 30000000.pci: Memory resource size exceeds max for 32 bits
[    0.280378] pci-host-generic 30000000.pci: ECAM at [mem 0x30000000-0x3fffffff] for [bus 00-ff]
[    0.281240] pci-host-generic 30000000.pci: PCI host bridge to bus 0000:00
[    0.281520] pci_bus 0000:00: root bus resource [io  0x0000-0xffff]
[    0.281577] pci_bus 0000:00: root bus resource [mem 0x40000000-0x7fffffff]
[    0.281589] pci_bus 0000:00: root bus resource [mem 0x400000000-0x7ffffffff]
[    0.281671] pci_bus 0000:00: root bus resource [bus 00-ff]
[    0.282571] pci 0000:00:00.0: [1b36:0008] type 00 class 0x060000
[    0.297203] Serial: 8250/16550 driver, 32 ports, IRQ sharing enabled
[    0.310139] printk: console [ttyS0] disabled
[    0.311394] 10000000.uart: ttyS0 at MMIO 0x10000000 (irq = 2, base_baud = 230400) is a 16550A
[    0.350203] printk: console [ttyS0] enabled
[    0.447654] Initramfs unpacking failed: write error
[    0.452545] Freeing initrd memory: 15532K
[    0.476497] loop: module loaded
[    0.482481] libphy: Fixed MDIO Bus: probed
[    0.482726] tun: Universal TUN/TAP device driver, 1.6
[    0.483341] PPP generic driver version 2.4.2
[    0.485132] ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
[    0.485332] ehci-pci: EHCI PCI platform driver
[    0.485558] ohci_hcd: USB 1.1 'Open' Host Controller (OHCI) Driver
[    0.485730] ohci-pci: OHCI PCI platform driver
[    0.485947] uhci_hcd: USB Universal Host Controller Interface driver
[    0.486688] mousedev: PS/2 mouse device common for all mice
[    0.488849] goldfish_rtc 101000.rtc: registered as rtc0
[    0.489331] goldfish_rtc 101000.rtc: setting system clock to 2022-03-01T04:45:14 UTC (1646109914)
[    0.489877] i2c /dev entries driver
[    0.492051] device-mapper: uevent: version 1.0.3
[    0.492706] device-mapper: ioctl: 4.45.0-ioctl (2021-03-22) initialised: dm-devel@redhat.com
[    0.493480] EDAC DEVICE0: Giving out device to module Sifive ECC Manager controller sifive_edac.0: DEV sifive_edac.0 (INTERRUPT)
[    0.493869] SPI driver mmc_spi has no spi_device_id for mmc-spi-slot
[    0.494315] ledtrig-cpu: registered to indicate activity on CPUs
[    0.494853] drop_monitor: Initializing network drop monitor service
[    0.496009] NET: Registered protocol family 10
[    0.514846] Segment Routing with IPv6
[    0.515312] NET: Registered protocol family 17
[    0.515886] Key type dns_resolver registered
[    0.517003] registered taskstats version 1
[    0.517375] Loading compiled-in X.509 certificates
[    0.525510] Loaded X.509 cert 'Build time autogenerated kernel key: 4cf43adf9705df8307d4623bdadca4daeba7c027'
[    0.527247] Loaded X.509 cert 'Canonical Ltd. Live Patch Signing: 14df34d1a87cf37625abec039ef2bf521249b969'
[    0.529124] Loaded X.509 cert 'Canonical Ltd. Kernel Module Signing: 88f752e560a1e0737e31163a466ad7b70a850c19'
[    0.529417] blacklist: Loading compiled-in revocation X.509 certificates
[    0.530461] Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing: 61482aa2830d0ab2ad5af10b7250da9033ddcef0'
[    0.531672] zswap: loaded using pool lzo/zbud
[    0.532718] Key type ._fscrypt registered
[    0.532837] Key type .fscrypt registered
[    0.532924] Key type fscrypt-provisioning registered
[    0.535429] Key type encrypted registered
[    0.535616] AppArmor: AppArmor sha1 policy hashing enabled
[    0.536210] ima: No TPM chip found, activating TPM-bypass!
[    0.536469] Loading compiled-in module X.509 certificates
[    0.537822] Loaded X.509 cert 'Build time autogenerated kernel key: 4cf43adf9705df8307d4623bdadca4daeba7c027'
[    0.538165] ima: Allocated hash algorithm: sha1
[    0.539492] ima: No architecture policies found
[    0.539958] evm: Initialising EVM extended attributes:
[    0.540089] evm: security.selinux
[    0.540264] evm: security.SMACK64
[    0.540402] evm: security.SMACK64EXEC
[    0.540547] evm: security.SMACK64TRANSMUTE
[    0.540697] evm: security.SMACK64MMAP
[    0.540833] evm: security.apparmor
[    0.540952] evm: security.ima
[    0.541061] evm: security.capability
[    0.541201] evm: HMAC attrs: 0x1
[    0.547179] md: Waiting for all devices to be available before autodetect
[    0.547445] md: If you don't use raid, use raid=noautodetect
[    0.547645] md: Autodetecting RAID arrays.
[    0.547855] md: autorun ...
[    0.547978] md: ... autorun DONE.
[    0.548532] Waiting for root device ...
```

We have to specify a root device in this case on the command line! Gotta specify the right one though; if we don't, the kernel eats itself and OOMs.
