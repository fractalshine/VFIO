# VFIO
Collection of scripts and tweaks for making a Windows 10 virtual machine run with QEMU/KVM/libvirt with GPU passthrough.

Specs

* Motherboard: MPG Z390M GAMING EDGE AC (MS-7B50)
* CPU: Intel Core i9-9900K CPU @ 3.60GHz
* RAM: 4 Modules KHX3200C16D4/32GX
* Host GPU: CoffeeLake-S GT2 (UHD Graphics 630)
* VM GPU: TU104 (GeForce RTX 2080 SUPER)
* VM NVMe: Samsung SSD 970 EVO Plus 1TB

Config

* libvirt hooks for core isolation needs, thanks to [passthroughpo.st](https://passthroughpo.st/simple-per-vm-libvirt-hooks-with-the-vfio-tools-hook-helper/) (in repo)
* Using a custom-built linux with `CONFIG_PREEMPT_VOLUNTARY=y` (fixes long boot time with UEFI guests)
* Kernel parameters: `intel_iommu=on iommu=pt default_hugepagesz=1G hugepagesz=1G hugepages=20 vfio-pci.ids=10de:1e81,10de:10f8,10de:1ad8,10de:1ad9,1106:3483,8086:a370 loglevel=3 quiet`
* Added workaround to make onboard bluetooth works again
```
<qemu:capabilities>
    <qemu:del capability='usb-host.hostdevice'/>
</qemu:capabilities>
```
* on windows, the MSI_util_v2 gets used every update to reset MSI interrupts on the GPU

Libvirt hooks tree
```
/etc/libvirt/hooks
├── qemu
└── qemu.d
    └── win10-isolate
        ├── release
        │   └── end
        │       └── release-host-cpus.sh
        └── started
            └── begin
                └── limit-host-cpus.sh

```

lscpu
```
CPU NODE SOCKET CORE L1d:L1i:L2:L3 ONLINE    MAXMHZ   MINMHZ
  0    0      0    0 0:0:0:0          yes 5000.0000 800.0000
  1    0      0    1 1:1:1:0          yes 5000.0000 800.0000
  2    0      0    2 2:2:2:0          yes 5000.0000 800.0000
  3    0      0    3 3:3:3:0          yes 5000.0000 800.0000
  4    0      0    4 4:4:4:0          yes 5000.0000 800.0000
  5    0      0    5 5:5:5:0          yes 5000.0000 800.0000
  6    0      0    6 6:6:6:0          yes 5000.0000 800.0000
  7    0      0    7 7:7:7:0          yes 5000.0000 800.0000
  8    0      0    0 0:0:0:0          yes 5000.0000 800.0000
  9    0      0    1 1:1:1:0          yes 5000.0000 800.0000
 10    0      0    2 2:2:2:0          yes 5000.0000 800.0000
 11    0      0    3 3:3:3:0          yes 5000.0000 800.0000
 12    0      0    4 4:4:4:0          yes 5000.0000 800.0000
 13    0      0    5 5:5:5:0          yes 5000.0000 800.0000
 14    0      0    6 6:6:6:0          yes 5000.0000 800.0000
 15    0      0    7 7:7:7:0          yes 5000.0000 800.0000

```
