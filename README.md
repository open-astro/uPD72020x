# Renesas uPD72020x USB 3.0 Firmware

Firmware for the Renesas uPD72020x (uPD720201 / uPD720202) USB 3.0 host controller.

## Tested Environment

- **OS:** Debian Trixie (Debian 13)
- **Kernel:** 6.12.75 (minimum tested version)
- **Kernel module:** `xhci_pci`

## Contents

| File | Description |
|------|-------------|
| `UPDATE.mem` | Renesas USB 3.0 host controller firmware binary (13 KB, 2019-07-08) |

## Background

The Renesas uPD720201 and uPD720202 are PCIe USB 3.0 host controller chips commonly found on add-in cards and some motherboards. When the chip is not paired with an external EEPROM containing firmware, the Linux kernel driver can load the firmware from the filesystem at runtime.

Without firmware, the controller may fall back to USB 2.0 speeds or fail to initialise entirely.

## Installation

Download the firmware directly to the kernel firmware search path:

```bash
sudo wget -O /lib/firmware/renesas_usb_fw.mem \
  https://raw.githubusercontent.com/open-astro/uPD72020x/master/UPDATE.mem
```

Update the initramfs so the firmware is available at boot, then reboot:

```bash
sudo update-initramfs -u
sudo reboot
```

After rebooting, confirm the firmware loaded successfully:

```bash
dmesg | grep -i renesas
```

You should see a line similar to:

```
[   11.737016] xhci-pci-renesas 0000:01:00.0: xHCI Host Controller
[   11.737053] xhci-pci-renesas 0000:01:00.0: new USB bus registered, assigned bus number 2
[   11.745989] xhci-pci-renesas 0000:01:00.0: hcc params 0x014051cf hci version 0x100 quirks 0x0000000100000010
[   11.746483] xhci-pci-renesas 0000:01:00.0: xHCI Host Controller
[   11.746500] xhci-pci-renesas 0000:01:00.0: new USB bus registered, assigned bus number 3
[   11.746514] xhci-pci-renesas 0000:01:00.0: Host supports USB 3.0 SuperSpeed
[   11.997391] usb 2-1: new high-speed USB device number 2 using xhci-pci-renesas
[ 1219.561568] usb 2-4: new full-speed USB device number 3 using xhci-pci-renesas
```

## Requirements

- Linux kernel with `xhci_pci` driver (included in all mainstream distributions)
- A PCIe card or device using the Renesas uPD720201 or uPD720202 chipset

## References

- [Renesas uPD720201 product page](https://www.renesas.com/en/products/interface/usb/upd720201.html)
- [Linux kernel firmware repository](https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git)
- Linux kernel source: `drivers/usb/host/xhci-pci-renesas.c`
