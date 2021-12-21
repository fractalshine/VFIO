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

* libvirt hooks for core isolation needs (in repo)
* Using a custom-built linux with `CONFIG_PREEMPT_VOLUNTARY=y` (fixes long boot time with UEFI guests)
* Kernel parameters: `intel_iommu=on iommu=pt default_hugepagesz=1G hugepagesz=1G hugepages=20 vfio-pci.ids=10de:1e81,10de:10f8,10de:1ad8,10de:1ad9,1106:3483,8086:a370 loglevel=3 quiet`
* Added workaround to make onboard bluetooth works again
`   <qemu:capabilities>
    <qemu:del capability='usb-host.hostdevice'/>
  </qemu:capabilities>
`
* on windows, the MSI_util_v2 gets used every update to reset MSI interrupts on the GPU

Libvirt hooks tree

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
