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
snapcraft register-key riscy-key
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


U-Boot 2022.01 (Mar 01 2022 - 14:55:46 -0500)

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
2692 bytes read in 0 ms
## Executing script at 88100000
Loading ubuntu-seed configuration...
4096 bytes read in 0 ms
## Warning: defaulting to text format
## Warning: Input data exceeds 1048576 bytes - truncated
## Info: input data size = 1048578 = 0x100002
Snapd boot mode: install
Loading systems/20220301/kernel/kernel.img...
29497856 bytes read in 12 ms (2.3 GiB/s)
Loading systems/20220301/kernel/initrd.img...
15906945 bytes read in 6 ms (2.5 GiB/s)
Moving Image from 0x82250000 to 0x80200000, end=81f1b000
## Flattened Device Tree blob at ff73bb00
   Booting using the fdt blob at 0xff73bb00
   Using Device Tree in place at 00000000ff73bb00, end 00000000ff73fe01

Starting kernel ...

[    0.000000] Linux version 5.13.0-1012-generic (buildd@riscv64-qemu-lcy01-002) (gcc (Ubuntu 11.2.0-7ubuntu2) 11.2.0, GNU ld (GNU Binutils for Ubuntu) 2.37) #14-Ubuntu SMP Wed Feb 9 16:17:11 UTC 2022 (Ubuntu 5.13.0-1012.14-generic 5.13.19)
[    0.000000] OF: fdt: Ignoring memory range 0x80000000 - 0x80200000
[    0.000000] Machine model: riscv-virtio,qemu
[    0.000000] efi: UEFI not found.
[    0.000000] cma: Reserved 32 MiB at 0x00000000fd400000
[    0.000000] NUMA: No NUMA configuration found
[    0.000000] NUMA: Faking a node at [mem 0x0000000080200000-0x00000000ffffffff]
[    0.000000] NUMA: NODE_DATA [mem 0xffff5800-0xffff6fff]
[    0.000000] Zone ranges:
[    0.000000]   DMA32    [mem 0x0000000080200000-0x00000000ffffffff]
[    0.000000]   Normal   empty
[    0.000000] Movable zone start for each node
[    0.000000] Early memory node ranges
[    0.000000]   node   0: [mem 0x0000000080200000-0x00000000ffffffff]
[    0.000000] Initmem setup node 0 [mem 0x0000000080200000-0x00000000ffffffff]
[    0.000000] On node 0, zone DMA32: 512 pages in unavailable ranges
[    0.000000] SBI specification v0.3 detected
[    0.000000] SBI implementation ID=0x1 Version=0x10000
[    0.000000] SBI TIME extension detected
[    0.000000] SBI IPI extension detected
[    0.000000] SBI RFENCE extension detected
[    0.000000] software IO TLB: mapped [mem 0x00000000f6400000-0x00000000fa400000] (64MB)
[    0.000000] SBI v0.2 HSM extension detected
[    0.000000] riscv: ISA extensions acdfimsu
[    0.000000] riscv: ELF capabilities acdfim
[    0.000000] percpu: Embedded 28 pages/cpu s75608 r8192 d30888 u114688
[    0.000000] Built 1 zonelists, mobility grouping on.  Total pages: 515592
[    0.000000] Policy zone: DMA32
[    0.000000] Kernel command line: earlycon=sbi console=ttyS0 init=bin/sh rootwait root=/dev/vda rw snapd_recovery_mode=install snapd_recovery_system=20220301
[    0.000000] Dentry cache hash table entries: 262144 (order: 9, 2097152 bytes, linear)
[    0.000000] Inode-cache hash table entries: 131072 (order: 8, 1048576 bytes, linear)
[    0.000000] Sorting __ex_table...
[    0.000000] mem auto-init: stack:off, heap alloc:on, heap free:off
[    0.000000] Memory: 1897304K/2095104K available (9809K kernel code, 5791K rwdata, 10240K rodata, 2534K init, 995K bss, 165032K reserved, 32768K cma-reserved)
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
[    0.000074] sched_clock: 64 bits at 10MHz, resolution 100ns, wraps every 4398046511100ns
[    0.003380] Console: colour dummy device 80x25
[    0.004888] Calibrating delay loop (skipped), value calculated using timer frequency.. 20.00 BogoMIPS (lpj=40000)
[    0.005052] pid_max: default: 32768 minimum: 301
[    0.007439] LSM: Security Framework initializing
[    0.008057] Yama: becoming mindful.
[    0.009905] AppArmor: AppArmor initialized
[    0.011139] Mount-cache hash table entries: 4096 (order: 3, 32768 bytes, linear)
[    0.011174] Mountpoint-cache hash table entries: 4096 (order: 3, 32768 bytes, linear)
[    0.034362] ASID allocator using 16 bits (65536 entries)
[    0.034907] rcu: Hierarchical SRCU implementation.
[    0.037751] EFI services will not be available.
[    0.039470] smp: Bringing up secondary CPUs ...
[    0.039558] smp: Brought up 1 node, 1 CPU
[    0.048201] devtmpfs: initialized
[    0.052789] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 7645041785100000 ns
[    0.052925] futex hash table entries: 256 (order: 2, 16384 bytes, linear)
[    0.055499] pinctrl core: initialized pinctrl subsystem
[    0.066701] NET: Registered protocol family 16
[    0.068748] audit: initializing netlink subsys (disabled)
[    0.073212] thermal_sys: Registered thermal governor 'fair_share'
[    0.073268] thermal_sys: Registered thermal governor 'bang_bang'
[    0.073280] thermal_sys: Registered thermal governor 'step_wise'
[    0.073287] thermal_sys: Registered thermal governor 'user_space'
[    0.075059] audit: type=2000 audit(0.060:1): state=initialized audit_enabled=0 res=1
[    0.090417] HugeTLB registered 1.00 GiB page size, pre-allocated 0 pages
[    0.090440] HugeTLB registered 2.00 MiB page size, pre-allocated 0 pages
[    0.104040] iommu: Default domain type: Translated 
[    0.107075] SCSI subsystem initialized
[    0.110084] vgaarb: loaded
[    0.110781] usbcore: registered new interface driver usbfs
[    0.110985] usbcore: registered new interface driver hub
[    0.111079] usbcore: registered new device driver usb
[    0.111403] pps_core: LinuxPPS API ver. 1 registered
[    0.111418] pps_core: Software ver. 5.3.6 - Copyright 2005-2007 Rodolfo Giometti <giometti@linux.it>
[    0.111469] PTP clock support registered
[    0.111890] EDAC MC: Ver: 3.0.0
[    0.118297] NetLabel: Initializing
[    0.118317] NetLabel:  domain hash size = 128
[    0.118328] NetLabel:  protocols = UNLABELED CIPSOv4 CALIPSO
[    0.119003] NetLabel:  unlabeled traffic allowed by default
[    0.122272] clocksource: Switched to clocksource riscv_clocksource
[    0.168094] VFS: Disk quotas dquot_6.6.0
[    0.168231] VFS: Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
[    0.179051] AppArmor: AppArmor Filesystem Enabled
[    0.187189] NET: Registered protocol family 2
[    0.187878] IP idents hash table entries: 32768 (order: 6, 262144 bytes, linear)
[    0.192305] tcp_listen_portaddr_hash hash table entries: 1024 (order: 2, 16384 bytes, linear)
[    0.192416] TCP established hash table entries: 16384 (order: 5, 131072 bytes, linear)
[    0.192648] TCP bind hash table entries: 16384 (order: 6, 262144 bytes, linear)
[    0.192842] TCP: Hash tables configured (established 16384 bind 16384)
[    0.194382] MPTCP token hash table entries: 2048 (order: 3, 49152 bytes, linear)
[    0.194565] UDP hash table entries: 1024 (order: 3, 32768 bytes, linear)
[    0.194763] UDP-Lite hash table entries: 1024 (order: 3, 32768 bytes, linear)
[    0.196021] NET: Registered protocol family 1
[    0.196244] NET: Registered protocol family 44
[    0.196363] PCI: CLS 0 bytes, default 64
[    0.199302] Trying to unpack rootfs image as initramfs...
[    0.204755] Initialise system trusted keyrings
[    0.206018] Key type blacklist registered
[    0.210705] workingset: timestamp_bits=44 max_order=19 bucket_order=0
[    0.220452] zbud: loaded
[    0.227064] squashfs: version 4.0 (2009/01/31) Phillip Lougher
[    0.230871] fuse: init (API version 7.34)
[    0.232737] integrity: Platform Keyring initialized
[    0.274326] Key type asymmetric registered
[    0.274382] Asymmetric key parser 'x509' registered
[    0.274542] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 244)
[    0.275058] io scheduler mq-deadline registered
[    0.278004] shpchp: Standard Hot Plug PCI Controller Driver version: 0.4
[    0.280236] pci-host-generic 30000000.pci: host bridge /soc/pci@30000000 ranges:
[    0.280868] pci-host-generic 30000000.pci:       IO 0x0003000000..0x000300ffff -> 0x0000000000
[    0.281221] pci-host-generic 30000000.pci:      MEM 0x0040000000..0x007fffffff -> 0x0040000000
[    0.281266] pci-host-generic 30000000.pci:      MEM 0x0400000000..0x07ffffffff -> 0x0400000000
[    0.281593] pci-host-generic 30000000.pci: Memory resource size exceeds max for 32 bits
[    0.282308] pci-host-generic 30000000.pci: ECAM at [mem 0x30000000-0x3fffffff] for [bus 00-ff]
[    0.286838] pci-host-generic 30000000.pci: PCI host bridge to bus 0000:00
[    0.287120] pci_bus 0000:00: root bus resource [io  0x0000-0xffff]
[    0.287182] pci_bus 0000:00: root bus resource [mem 0x40000000-0x7fffffff]
[    0.287194] pci_bus 0000:00: root bus resource [mem 0x400000000-0x7ffffffff]
[    0.287272] pci_bus 0000:00: root bus resource [bus 00-ff]
[    0.288157] pci 0000:00:00.0: [1b36:0008] type 00 class 0x060000
[    0.302822] Serial: 8250/16550 driver, 32 ports, IRQ sharing enabled
[    0.318081] printk: console [ttyS0] disabled
[    0.323107] 10000000.uart: ttyS0 at MMIO 0x10000000 (irq = 2, base_baud = 230400) is a 16550A
[    0.353302] printk: console [ttyS0] enabled
[    0.466475] loop: module loaded
[    0.469805] libphy: Fixed MDIO Bus: probed
[    0.469913] tun: Universal TUN/TAP device driver, 1.6
[    0.474439] PPP generic driver version 2.4.2
[    0.474985] ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
[    0.475146] ehci-pci: EHCI PCI platform driver
[    0.475417] ohci_hcd: USB 1.1 'Open' Host Controller (OHCI) Driver
[    0.475597] ohci-pci: OHCI PCI platform driver
[    0.475813] uhci_hcd: USB Universal Host Controller Interface driver
[    0.476555] mousedev: PS/2 mouse device common for all mice
[    0.482895] goldfish_rtc 101000.rtc: registered as rtc0
[    0.483464] goldfish_rtc 101000.rtc: setting system clock to 2022-03-01T20:17:46 UTC (1646165866)
[    0.484093] i2c /dev entries driver
[    0.490549] device-mapper: uevent: version 1.0.3
[    0.491159] device-mapper: ioctl: 4.45.0-ioctl (2021-03-22) initialised: dm-devel@redhat.com
[    0.492010] EDAC DEVICE0: Giving out device to module Sifive ECC Manager controller sifive_edac.0: DEV sifive_edac.0 (INTERRUPT)
[    0.492594] SPI driver mmc_spi has no spi_device_id for mmc-spi-slot
[    0.493152] ledtrig-cpu: registered to indicate activity on CPUs
[    0.493793] drop_monitor: Initializing network drop monitor service
[    0.499142] NET: Registered protocol family 10
[    0.728989] Freeing initrd memory: 15532K
[    0.740219] Segment Routing with IPv6
[    0.740624] NET: Registered protocol family 17
[    0.741119] Key type dns_resolver registered
[    0.742058] registered taskstats version 1
[    0.742501] Loading compiled-in X.509 certificates
[    0.750348] Loaded X.509 cert 'Build time autogenerated kernel key: 4cf43adf9705df8307d4623bdadca4daeba7c027'
[    0.751895] Loaded X.509 cert 'Canonical Ltd. Live Patch Signing: 14df34d1a87cf37625abec039ef2bf521249b969'
[    0.753285] Loaded X.509 cert 'Canonical Ltd. Kernel Module Signing: 88f752e560a1e0737e31163a466ad7b70a850c19'
[    0.753535] blacklist: Loading compiled-in revocation X.509 certificates
[    0.754610] Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing: 61482aa2830d0ab2ad5af10b7250da9033ddcef0'
[    0.755798] zswap: loaded using pool lzo/zbud
[    0.756780] Key type ._fscrypt registered
[    0.756893] Key type .fscrypt registered
[    0.756999] Key type fscrypt-provisioning registered
[    0.759476] Key type encrypted registered
[    0.759590] AppArmor: AppArmor sha1 policy hashing enabled
[    0.759976] ima: No TPM chip found, activating TPM-bypass!
[    0.760109] Loading compiled-in module X.509 certificates
[    0.761372] Loaded X.509 cert 'Build time autogenerated kernel key: 4cf43adf9705df8307d4623bdadca4daeba7c027'
[    0.761543] ima: Allocated hash algorithm: sha1
[    0.762783] ima: No architecture policies found
[    0.763179] evm: Initialising EVM extended attributes:
[    0.763253] evm: security.selinux
[    0.763307] evm: security.SMACK64
[    0.763350] evm: security.SMACK64EXEC
[    0.763395] evm: security.SMACK64TRANSMUTE
[    0.763444] evm: security.SMACK64MMAP
[    0.763488] evm: security.apparmor
[    0.763530] evm: security.ima
[    0.763567] evm: security.capability
[    0.763619] evm: HMAC attrs: 0x1
[    0.801317] Freeing unused kernel memory: 2532K
[    0.811899] Checked W+X mappings: passed, no W+X pages found
[    0.812207] Run /init as init process
[    0.920396] systemd[1]: Failed to look up module alias 'autofs4': Function not implemented
[    0.955196] systemd[1]: systemd 245 running in system mode. (+PAM +AUDIT +SELINUX -IMA +APPARMOR -SMACK -SYSVINIT -UTMP +LIBCRYPTSETUP -GCRYPT -GNUTLS +ACL -XZ +LZ4 +SECCOMP +BLKID -ELFUTILS +KMOD +IDN2 -IDN +PCRE2 default-hierarchy=hybrid)
[    0.956777] systemd[1]: Detected architecture riscv64.
[    0.956951] systemd[1]: Running in initial RAM disk.

Welcome to Linux!

[    0.979836] systemd[1]: No hostname configured.
[    0.980356] systemd[1]: Set hostname to <ubuntu>.
[    0.981278] random: systemd: uninitialized urandom read (16 bytes read)
[    0.981780] systemd[1]: Initializing machine ID from random generator.
[    1.789356] systemd[1]: emergency.target: Requested dependency OnFailure=reboot.target ignored (target units cannot fail).
[    1.891978] random: systemd: uninitialized urandom read (16 bytes read)
[    1.894132] systemd[1]: Started Dispatch Password Requests to Console Directory Watch.
[  OK  ] Started Dispatch Password …ts to Console Directory Watch.
[    1.897446] random: systemd: uninitialized urandom read (16 bytes read)
[    1.897788] systemd[1]: Reached target Local Encrypted Volumes.
[  OK  ] Reached target Local Encrypted Volumes.
[    1.898940] systemd[1]: Reached target Paths.
[  OK  ] Reached target Paths.
[    1.899845] systemd[1]: Reached target Slices.
[  OK  ] Reached target Slices.
[    1.900676] systemd[1]: Reached target Swap.
[  OK  ] Reached target Swap.
[    1.903778] systemd[1]: Listening on initctl Compatibility Named Pipe.
[  OK  ] Listening on initctl Compatibility Named Pipe.
[    1.909569] systemd[1]: Listening on Journal Audit Socket.
[  OK  ] Listening on Journal Audit Socket.
[    1.912753] systemd[1]: Listening on Journal Socket (/dev/log).
[  OK  ] Listening on Journal Socket (/dev/log).
[    1.916017] systemd[1]: Listening on Journal Socket.
[  OK  ] Listening on Journal Socket.
[    1.919064] systemd[1]: Listening on udev Control Socket.
[  OK  ] Listening on udev Control Socket.
[    1.921067] systemd[1]: Listening on udev Kernel Socket.
[  OK  ] Listening on udev Kernel Socket.
[    1.921974] systemd[1]: Reached target Sockets.
[  OK  ] Reached target Sockets.
[    1.942382] systemd[1]: Mounting Huge Pages File System...
         Mounting Huge Pages File System...
[    1.973795] systemd[1]: Mounting POSIX Message Queue File System...
         Mounting POSIX Message Queue File System...
[    2.020216] systemd[1]: Mounting Kernel Debug File System...
         Mounting Kernel Debug File System...
[    2.083235] systemd[1]: Mounting Kernel Trace File System...
         Mounting Kernel Trace File System...
[    2.151485] systemd[1]: Mounting Temporary Directory (/tmp)...
         Mounting Temporary Directory (/tmp)...
[    2.244389] systemd[1]: Starting Journal Service...
         Starting Journal Service...
[    2.250747] systemd[1]: Condition check resulted in Create list of static device nodes for the current kernel being skipped.
[    2.251938] systemd[1]: Condition check resulted in Boot Process Profiler being skipped.
[    2.252961] systemd[1]: Condition check resulted in Rebuild Hardware Database being skipped.
[    2.335697] systemd[1]: Starting Load Kernel Modules...
         Starting Load Kernel Modules...
[    2.379774] systemd[1]: Starting Create System Users...
         Starting Create System Users...
[    2.448296] systemd[1]: Starting udev Coldplug all Devices...
         Starting udev Coldplug all Devices...
[    2.547523] systemd[1]: Mounted Huge Pages File System.
[  OK  ] Mounted Huge Pages File System.
[    2.549934] systemd[1]: Mounted POSIX Message Queue File System.
[  OK  ] Mounted POSIX Message Queue File System.
[    2.568145] systemd[1]: Mounted Kernel Debug File System.
[  OK  ] Mounted Kernel Debug File System.
[    2.571569] systemd[1]: Mounted Kernel Trace File System.
[  OK  ] Mounted Kernel Trace File System.
[    2.587968] systemd[1]: Mounted Temporary Directory (/tmp).
[  OK  ] Mounted Temporary Directory (/tmp).
[    2.595902] systemd[1]: Reached target Local File Systems.
[  OK  ] Reached target Local File Systems.
[    2.611763] systemd[1]: Condition check resulted in Commit a transient machine-id on disk being skipped.
[    2.734467] systemd[1]: Finished Load Kernel Modules.
[  OK  ] Finished Load Kernel Modules.
[    2.795294] systemd[1]: Mounting FUSE Control File System...
         Mounting FUSE Control File System...
[    2.891468] systemd[1]: Mounting Kernel Configuration File System...
         Mounting Kernel Configuration File System...
[    3.040084] systemd[1]: Starting Apply Kernel Variables...
         Starting Apply Kernel Variables...
[    3.065738] systemd[1]: Started Journal Service.
[  OK  ] Started Journal Service.
[  OK  ] Finished Create System Users.
[    3.289233] systemd[1]: Finished Create System Users.
[    3.313139] systemd[1]: Mounted FUSE Control File System.
[  OK  ] Mounted FUSE Control File System.
[    3.323947] systemd-sysctl[130]: Not setting net/ipv4/conf/all/promote_secondaries (explicit setting exists).
[  OK  ] Mounted Kernel Configuration File System.
[    3.327830] systemd-sysctl[130]: Not setting net/ipv4/conf/default/promote_secondaries (explicit setting exists).
[    3.337160] systemd[1]: Mounted Kernel Configuration File System.
[    3.341011] systemd-sysctl[130]: Couldn't write 'fq_codel' to 'net/core/default_qdisc', ignoring: No such file or directory
[    3.370593] systemd[1]: Starting Flush Journal to Persistent Storage...
         Starting Flush Journal to Persistent Storage...
[    3.422509] systemd[1]: Starting Create Static Device Nodes in /dev...
         Starting Create Static Device Nodes in /dev...
[    3.516880] systemd[1]: Finished Apply Kernel Variables.
[  OK  ] Finished Apply Kernel Variables.
[    3.634960] systemd-journald[123]: Received client request to flush runtime journal.
[    3.696918] systemd[1]: Finished Flush Journal to Persistent Storage.
[  OK  ] Finished Flush Journal to Persistent Storage.
[    3.738581] systemd[1]: Starting Create Volatile Files and Directories...
         Starting Create Volatile Files and Directories...
[    3.839390] systemd[1]: Finished Create Static Device Nodes in /dev.
[  OK  ] Finished Create Static Device Nodes in /dev.
[    3.890690] systemd[1]: Starting udev Kernel Device Manager...
         Starting udev Kernel Device Manager...
[    4.093997] systemd-tmpfiles[134]: Failed to copy files to /etc/nsswitch.conf: No such file or directory
[    4.101431] systemd-tmpfiles[134]: Failed to copy files to /etc/pam.d: No such file or directory
[    4.105254] systemd-tmpfiles[134]: Failed to copy files to /etc/issue: No such file or directory
[    4.287317] systemd[1]: Finished Create Volatile Files and Directories.
[  OK  ] Finished Create Volatile Files and Directories.
[    4.335941] systemd[1]: Starting Rebuild Journal Catalog...
         Starting Rebuild Journal Catalog...
[    4.421508] systemd[1]: Started udev Kernel Device Manager.
[  OK  ] Started udev Kernel Device Manager.
[    4.582165] journalctl[137]: No items in catalog.
[    4.647721] systemd[1]: Finished Rebuild Journal Catalog.
[  OK  ] Finished Rebuild Journal Catalog.
[    4.694595] systemd[1]: Starting Update is Completed...
         Starting Update is Completed...
[    4.997310] systemd[1]: Finished Update is Completed.
[  OK  ] Finished Update is Completed.
[    5.408225] systemd[1]: Finished udev Coldplug all Devices.
[  OK  ] Finished udev Coldplug all Devices.
[    5.438552] systemd[1]: Starting udev Wait for Complete Device Initialization...
         Starting udev Wait for Complete Device Initialization...
[    6.763061] udevadm[151]: systemd-udev-settle.service is deprecated.
[    6.786790] systemd[1]: Finished udev Wait for Complete Device Initialization.
[  OK  ] Finished udev Wait for Complete Device Initialization.
[    6.790582] systemd[1]: Reached target System Initialization.
[  OK  ] Reached target System Initialization.
[    6.793936] systemd[1]: Started Daily Cleanup of Temporary Directories.
[  OK  ] Started Daily Cleanup of Temporary Directories.
[    6.796015] systemd[1]: Reached target Basic System.
[    6.806999] random: fast init done
[  OK  ] Reached target Basic System.
[    6.799531] systemd[1]: Reached target Timers.
[  OK  ] Reached target Timers.
[    6.802618] systemd[1]: Condition check resulted in Wait for the Ubuntu Core chooser trigger being skipped.
[    6.817295] systemd[1]: Starting the-tool.service...
         Starting the-tool.service...
[    7.167423] the-tool[153]: 2022/03/01 20:17:53.189073 cmd_initramfs_mounts.go:1254: waiting up to 1m0s for /dev/disk/by-label/ubuntu-seed to appear
[   68.149568] the-tool[153]: 2022/03/01 20:18:54.164288 secboot_tpm.go:185: cannot open TPM connection: no TPM2 device is available
[   68.155183] the-tool[153]: error: cannot mount source: no /dev/disk/by-label/ubuntu-seed after waiting for 1m0s
[   68.183963] systemd[1]: the-tool.service: Main process exited, code=exited, status=1/FAILURE
[   68.193505] systemd[1]: the-tool.service: Failed with result 'exit-code'.
[   68.219627] systemd[1]: Failed to start the-tool.service.
[FAILED] Failed to start the-tool.service.
See 'systemctl status the-tool.service' for details.
[   68.225824] systemd[1]: Startup finished in 873ms (kernel) + 0 (initrd) + 1min 7.351s (userspace) = 1min 8.224s.
[   68.227794] systemd[1]: the-tool.service: Triggering OnFailure= dependencies.
[   68.240986] systemd[1]: systemd-ask-password-console.path: Succeeded.
[   68.249622] systemd[1]: Stopped Dispatch Password Requests to Console Directory Watch.
[   68.255096] systemd[1]: systemd-tmpfiles-clean.timer: Succeeded.
[   68.259532] systemd[1]: Stopped Daily Cleanup of Temporary Directories.
[   68.261634] systemd[1]: Stopped target Basic System.
[   68.263052] systemd[1]: Stopped target System Initialization.
[   68.264607] systemd[1]: emergency.service: Starting requested but asserts failed.
[   68.266049] systemd[1]: Assertion failed for Emergency Shell.
[   68.268743] systemd[1]: Dependency failed for Emergency Mode.
[   68.271219] systemd[1]: emergency.target: Job emergency.target/start failed with result 'dependency'.
```

So for some reason, the ROOTFS is not visible to Systemd running in the initramfs. Interesting.
