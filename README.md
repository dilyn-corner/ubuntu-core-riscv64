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


U-Boot 2022.01 (Feb 27 2022 - 23:54:39 -0500)

CPU:   rv64imafdcsu
Model: riscv-virtio,qemu
DRAM:  4 GiB
Flash: 32 MiB
Loading Environment from FAT... *** Warning - bad CRC, using default environment

In:    uart@10000000
Out:   uart@10000000
Err:   uart@10000000
Net:   eth0: virtio-net#1
Hit any key to stop autoboot:  0 

Device 0: QEMU VirtIO Block Device
            Type: Hard Disk
            Capacity: 5339.0 MB = 5.2 GB (10934306 x 512)
... is now current device
Scanning virtio 0:1...
Found U-Boot script /boot.scr
2690 bytes read in 1 ms (2.6 MiB/s)
## Executing script at 88100000
Loading ubuntu-seed configuration...
4096 bytes read in 0 ms
## Warning: defaulting to text format
## Warning: Input data exceeds 1048576 bytes - truncated
## Info: input data size = 1048578 = 0x100002
Snapd boot mode: install
Loading systems/20220228/kernel/dtbs/riscv64-virt.dtb...
1048576 bytes read in 1 ms (1000 MiB/s)
Loading systems/20220228/kernel/kernel.img...
25311744 bytes read in 10 ms (2.4 GiB/s)
Loading systems/20220228/kernel/initrd.img...
15907109 bytes read in 15 ms (1011.3 MiB/s)
Moving Image from 0x84000000 to 0x80200000, end=81ade000
## Flattened Device Tree blob at 82201000
   Booting using the fdt blob at 0x82201000
   Using Device Tree in place at 0000000082201000, end 0000000082204ee1

Starting kernel ...

[    0.000000] Linux version 5.15.12+ (dilyn@Ares) (riscv64-linux-gnu-gcc-10 (Ubuntu 10.3.0-8ubuntu1) 10.3.0, GNU ld (GNU Binutils for Ubuntu) 2.37) #1 SMP Mon Feb 28 00:04:17 EST 2022 (Ubuntu 5.11.0-1029.32~20.04.1-generic 5.11.22)
[    0.000000] OF: fdt: Ignoring memory range 0x80000000 - 0x80200000
[    0.000000] Machine model: riscv-virtio,qemu
[    0.000000] efi: UEFI not found.
[    0.000000] cma: Reserved 32 MiB at 0x0000000084000000
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
[    0.000000] SBI v0.2 HSM extension detected
[    0.000000] riscv: ISA extensions acdfimsu
[    0.000000] riscv: ELF capabilities acdfim
[    0.000000] percpu: Embedded 27 pages/cpu s71640 r8192 d30760 u110592
[    0.000000] Built 1 zonelists, mobility grouping on.  Total pages: 31752
[    0.000000] Kernel command line: console=ttyS0,115200 earlycon=sbi rw snapd_recovery_mode=install snapd_recovery_system=20220228
[    0.000000] Unknown kernel command line parameters "snapd_recovery_mode=install snapd_recovery_system=20220228", will be passed to user space.
[    0.000000] Dentry cache hash table entries: 16384 (order: 5, 131072 bytes, linear)
[    0.000000] Inode-cache hash table entries: 8192 (order: 4, 65536 bytes, linear)
[    0.000000] mem auto-init: stack:off, heap alloc:on, heap free:off
[    0.000000] Memory: 35304K/129024K available (9791K kernel code, 5827K rwdata, 6144K rodata, 2506K init, 740K bss, 60952K reserved, 32768K cma-reserved)
[    0.000000] random: get_random_u64 called from __kmem_cache_create+0x3c/0x52c with crng_init=0
[    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1
[    0.000000] ftrace: allocating 36370 entries in 143 pages
[    0.000000] ftrace: allocated 143 pages with 5 groups
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
[    0.000065] sched_clock: 64 bits at 10MHz, resolution 100ns, wraps every 4398046511100ns
[    0.003167] Console: colour dummy device 80x25
[    0.004269] Calibrating delay loop (skipped), value calculated using timer frequency.. 20.00 BogoMIPS (lpj=40000)
[    0.004435] pid_max: default: 32768 minimum: 301
[    0.006784] LSM: Security Framework initializing
[    0.007344] Yama: becoming mindful.
[    0.009014] AppArmor: AppArmor initialized
[    0.009984] Mount-cache hash table entries: 512 (order: 0, 4096 bytes, linear)
[    0.010021] Mountpoint-cache hash table entries: 512 (order: 0, 4096 bytes, linear)
[    0.032157] ASID allocator using 16 bits (65536 entries)
[    0.032682] rcu: Hierarchical SRCU implementation.
[    0.035182] EFI services will not be available.
[    0.037106] smp: Bringing up secondary CPUs ...
[    0.037173] smp: Brought up 1 node, 1 CPU
[    0.044632] devtmpfs: initialized
[    0.048906] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 7645041785100000 ns
[    0.049274] futex hash table entries: 256 (order: 2, 16384 bytes, linear)
[    0.051863] pinctrl core: initialized pinctrl subsystem
[    0.061924] NET: Registered PF_NETLINK/PF_ROUTE protocol family
[    0.063855] audit: initializing netlink subsys (disabled)
[    0.069027] audit: type=2000 audit(0.060:1): state=initialized audit_enabled=0 res=1
[    0.071073] thermal_sys: Registered thermal governor 'fair_share'
[    0.071127] thermal_sys: Registered thermal governor 'bang_bang'
[    0.071139] thermal_sys: Registered thermal governor 'step_wise'
[    0.071146] thermal_sys: Registered thermal governor 'user_space'
[    0.087292] HugeTLB registered 1.00 GiB page size, pre-allocated 0 pages
[    0.087318] HugeTLB registered 2.00 MiB page size, pre-allocated 0 pages
[    0.100437] iommu: Default domain type: Translated 
[    0.100473] iommu: DMA domain TLB invalidation policy: strict mode 
[    0.103050] SCSI subsystem initialized
[    0.105988] vgaarb: loaded
[    0.107539] usbcore: registered new interface driver usbfs
[    0.107731] usbcore: registered new interface driver hub
[    0.107834] usbcore: registered new device driver usb
[    0.108156] pps_core: LinuxPPS API ver. 1 registered
[    0.108174] pps_core: Software ver. 5.3.6 - Copyright 2005-2007 Rodolfo Giometti <giometti@linux.it>
[    0.108237] PTP clock support registered
[    0.108664] EDAC MC: Ver: 3.0.0
[    0.115335] NetLabel: Initializing
[    0.115353] NetLabel:  domain hash size = 128
[    0.115365] NetLabel:  protocols = UNLABELED CIPSOv4 CALIPSO
[    0.116046] NetLabel:  unlabeled traffic allowed by default
[    0.119328] clocksource: Switched to clocksource riscv_clocksource
[    0.162311] VFS: Disk quotas dquot_6.6.0
[    0.162441] VFS: Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
[    0.165300] AppArmor: AppArmor Filesystem Enabled
[    0.183463] NET: Registered PF_INET protocol family
[    0.184110] IP idents hash table entries: 2048 (order: 2, 16384 bytes, linear)
[    0.187866] tcp_listen_portaddr_hash hash table entries: 256 (order: 0, 4096 bytes, linear)
[    0.187941] TCP established hash table entries: 1024 (order: 1, 8192 bytes, linear)
[    0.188241] TCP bind hash table entries: 1024 (order: 2, 16384 bytes, linear)
[    0.188327] TCP: Hash tables configured (established 1024 bind 1024)
[    0.189352] MPTCP token hash table entries: 256 (order: 0, 6144 bytes, linear)
[    0.189526] UDP hash table entries: 256 (order: 1, 8192 bytes, linear)
[    0.189704] UDP-Lite hash table entries: 256 (order: 1, 8192 bytes, linear)
[    0.190945] NET: Registered PF_UNIX/PF_LOCAL protocol family
[    0.191368] NET: Registered PF_XDP protocol family
[    0.191494] PCI: CLS 0 bytes, default 64
[    0.194292] Trying to unpack rootfs image as initramfs...
[    0.197459] Initialise system trusted keyrings
[    0.198684] Key type blacklist registered
[    0.204081] workingset: timestamp_bits=46 max_order=15 bucket_order=0
[    0.214104] zbud: loaded
[    0.220647] squashfs: version 4.0 (2009/01/31) Phillip Lougher
[    0.223905] fuse: init (API version 7.34)
[    0.226848] integrity: Platform Keyring initialized
[    0.267367] Key type asymmetric registered
[    0.267421] Asymmetric key parser 'x509' registered
[    0.267629] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 244)
[    0.268115] io scheduler mq-deadline registered
[    0.271722] shpchp: Standard Hot Plug PCI Controller Driver version: 0.4
[    0.273387] pci-host-generic 30000000.pci: host bridge /soc/pci@30000000 ranges:
[    0.273995] pci-host-generic 30000000.pci:       IO 0x0003000000..0x000300ffff -> 0x0000000000
[    0.274335] pci-host-generic 30000000.pci:      MEM 0x0040000000..0x007fffffff -> 0x0040000000
[    0.274387] pci-host-generic 30000000.pci:      MEM 0x0400000000..0x07ffffffff -> 0x0400000000
[    0.274701] pci-host-generic 30000000.pci: Memory resource size exceeds max for 32 bits
[    0.279507] pci-host-generic 30000000.pci: ECAM at [mem 0x30000000-0x3fffffff] for [bus 00-ff]
[    0.280338] pci-host-generic 30000000.pci: PCI host bridge to bus 0000:00
[    0.280607] pci_bus 0000:00: root bus resource [io  0x0000-0xffff]
[    0.280666] pci_bus 0000:00: root bus resource [mem 0x40000000-0x7fffffff]
[    0.280679] pci_bus 0000:00: root bus resource [mem 0x400000000-0x7ffffffff]
[    0.280755] pci_bus 0000:00: root bus resource [bus 00-ff]
[    0.281664] pci 0000:00:00.0: [1b36:0008] type 00 class 0x060000
[    0.296036] Serial: 8250/16550 driver, 32 ports, IRQ sharing enabled
[    0.310765] printk: console [ttyS0] disabled
[    0.316181] 10000000.uart: ttyS0 at MMIO 0x10000000 (irq = 2, base_baud = 230400) is a 16550A
[    0.350425] printk: console [ttyS0] enabled
[    0.379755] loop: module loaded
[    0.385562] libphy: Fixed MDIO Bus: probed
[    0.385693] tun: Universal TUN/TAP device driver, 1.6
[    0.387301] PPP generic driver version 2.4.2
[    0.387864] ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
[    0.388103] ehci-pci: EHCI PCI platform driver
[    0.388349] ohci_hcd: USB 1.1 'Open' Host Controller (OHCI) Driver
[    0.388567] ohci-pci: OHCI PCI platform driver
[    0.388805] uhci_hcd: USB Universal Host Controller Interface driver
[    0.395356] mousedev: PS/2 mouse device common for all mice
[    0.397579] goldfish_rtc 101000.rtc: registered as rtc0
[    0.398181] goldfish_rtc 101000.rtc: setting system clock to 2022-02-28T06:29:29 UTC (1646029769)
[    0.398799] i2c_dev: i2c /dev entries driver
[    0.400827] syscon-poweroff soc:poweroff: pm_power_off already claimed for default_power_off
[    0.401132] syscon-poweroff: probe of soc:poweroff failed with error -16
[    0.401465] device-mapper: core: CONFIG_IMA_DISABLE_HTABLE is disabled. Duplicate IMA measurements will not be recorded in the IMA log.
[    0.401902] device-mapper: uevent: version 1.0.3
[    0.407332] device-mapper: ioctl: 4.45.0-ioctl (2021-03-22) initialised: dm-devel@redhat.com
[    0.408199] EDAC DEVICE0: Giving out device to module Sifive ECC Manager controller sifive_edac.0: DEV sifive_edac.0 (INTERRUPT)
[    0.409016] ledtrig-cpu: registered to indicate activity on CPUs
[    0.409787] drop_monitor: Initializing network drop monitor service
[    0.410988] NET: Registered PF_INET6 protocol family
[    0.489291] Initramfs unpacking failed: write error
[    0.493858] Freeing initrd memory: 15532K
[    0.503013] Segment Routing with IPv6
[    0.503344] In-situ OAM (IOAM) with IPv6
[    0.503754] NET: Registered PF_PACKET protocol family
[    0.504324] Key type dns_resolver registered
[    0.505250] registered taskstats version 1
[    0.505718] Loading compiled-in X.509 certificates
[    0.513278] Loaded X.509 cert 'Build time autogenerated kernel key: 876785d58f38e5a496815aec54fe80d35e8bf3bf'
[    0.514666] zswap: loaded using pool lzo/zbud
[    0.515821] Key type ._fscrypt registered
[    0.515904] Key type .fscrypt registered
[    0.515963] Key type fscrypt-provisioning registered
[    0.518878] Key type encrypted registered
[    0.519126] AppArmor: AppArmor sha1 policy hashing enabled
[    0.519838] ima: No TPM chip found, activating TPM-bypass!
[    0.520090] Loading compiled-in module X.509 certificates
[    0.521556] Loaded X.509 cert 'Build time autogenerated kernel key: 876785d58f38e5a496815aec54fe80d35e8bf3bf'
[    0.521949] ima: Allocated hash algorithm: sha1
[    0.523486] ima: No architecture policies found
[    0.524024] evm: Initialising EVM extended attributes:
[    0.524202] evm: security.selinux
[    0.524329] evm: security.SMACK64
[    0.524442] evm: security.SMACK64EXEC
[    0.524565] evm: security.SMACK64TRANSMUTE
[    0.524702] evm: security.SMACK64MMAP
[    0.524826] evm: security.apparmor
[    0.524950] evm: security.ima
[    0.525047] evm: security.capability
[    0.525183] evm: HMAC attrs: 0x1
[    0.531526] md: Waiting for all devices to be available before autodetect
[    0.531718] md: If you don't use raid, use raid=noautodetect
[    0.531874] md: Autodetecting RAID arrays.
[    0.531974] md: autorun ...
[    0.532040] md: ... autorun DONE.
[    0.533948] VFS: Cannot open root device "(null)" or unknown-block(0,0): error -6
[    0.534177] Please append a correct "root=" boot option; here are the available partitions:
[    0.534579] Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)
[    0.534888] CPU: 0 PID: 1 Comm: swapper/0 Not tainted 5.15.12+ #1
[    0.535091] Hardware name: riscv-virtio,qemu (DT)
[    0.535307] Call Trace:
[    0.535468] [<ffffffff80005c22>] dump_backtrace+0x30/0x38
[    0.535686] [<ffffffff809822e6>] dump_stack_lvl+0x44/0x5c
[    0.535826] [<ffffffff80982316>] dump_stack+0x18/0x20
[    0.535945] [<ffffffff8097e50c>] panic+0x106/0x2e0
[    0.536075] [<ffffffff80a01c52>] mount_block_root+0x174/0x228
[    0.536213] [<ffffffff80a01e46>] mount_root+0x140/0x15a
[    0.536336] [<ffffffff80a01fc0>] prepare_namespace+0x160/0x1c6
[    0.536473] [<ffffffff80a0177a>] kernel_init_freeable+0x2b4/0x2d8
[    0.536618] [<ffffffff809884fe>] kernel_init+0x32/0x160
[    0.536742] [<ffffffff8000391c>] ret_from_exception+0x0/0xc
[    0.537275] ---[ end Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0) ]---
```

Of interest(?):
[    0.489291] Initramfs unpacking failed: write error
