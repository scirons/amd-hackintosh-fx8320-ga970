# OpenCore EFI for AMD FX8230

| **Component**    | **Model**                                  |
| ---------------- | ------------------------------------------ |
| CPU              | AMD FX 8230 @ 3.5GHz                       |
| Motherboard      | Gigabye GA-970-Gaming (rev. 1.0)           |
| RAM              | 8GB (1 x 8GB) Kingston 1333MHz             |
| GPU              | AMD RX 5700 XT                             |
| Audio Chipset    | ALC-1150                                   |
| Ethernet         | Killer E2400                               |
| OS Disk (NVMe)   | Corsair Force Series MP510 960GB M.2 SSD   |
| OS Disk          | SanDisk SSD PLUS 1TB Internal SSD          |

## Table of contents

- [Software Compatibility](#Software-Compatibility)
- [Hardware Compatibility](#Hardware-Compatibility)
- [Installation](#Installation)
- [BIOS Settings](#BIOS-Settings)
- [Virtualization](#Virtualization)
- [Guides](#Guides)
- [Credits](#Credits)

## Software Compatibility

- Big Sur (11.x)
- Catalina (10.15.x)
- Mojave (10.14.x)
- High Sierra (10.13.x)

_There's no real support from AMD Vanilla for Monterey on 15th (FX Series) due to errors applying the patch and initializing the GPU on subsequent boot attempts._


## Hardware Compatibility

This EFI is compatible with all FX processors with
[macOS-compatible peripherals](https://dortania.github.io/Anti-Hackintosh-Buyers-Guide/).

### Graphical Processing Unit (GPU)

| **Model**  | **Compatible?**               |
| ---------- | ----------------------------- |
| Integrated | No                            |
| AMD        | Yes <sup>2</sup> <sup>3</sup> |

For **AMD Navi 10 Series GPUs (RX 5500, RX 5600, RX 5700)** `agdpmod=pikera` is added to `boot-args` to fix the black screen issue.

Use EFI_Dual to blacklist any secondary GPU that might interfere with booting (change the disable-gpu key value, and use SSDTTime to address your GPU properly.

## Installation

### Bootable USB

1. Follow [this guide](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/) to create your bootable USB.

2. Clone this repository and copy the "EFI" directory to the same directory of your BaseSystem.dmg on your bootable USB.

### SMBIOS

3. Use [this tool](https://github.com/corpnewt/GenSMBIOS) to generate your unique SMBIOS info.

- SMBIOS has to be unique, you cannot use one present in this repository.

- Run the tool and select `Generate SMBIOS`.
- Select the appropriate model for your hardware using the table below.
- Go to [Apple Coverage](https://checkcoverage.apple.com/) and paste generated _Serial_. You need "Invalid Serial" or "Purchase Date not Validated" message. If you get something another you have to generate SMBIOS data and check it again.
- Open _config.plist_ and search for `PlatformInfo -> Generic` and replace these values:
  - _SystemProductName_ - Model
  - _MLB_ - Board Serial
  - _SystemSerialNumber_ - Serial
  - _SystemUUID_ - SmUUID
- _ROM_ entry should be set to your [network card's MAC address](https://www.wikihow.com/Find-the-MAC-Address-of-Your-Computer), without separators (e. g. `:`, `-`).

| **GPU Series**       | **Model**               |
| -------------------- | ----------------------- |
| AMD Navi Series      | iMacPro1,1 <sup>1</sup> |
| AMD Vega Series      | iMacPro1,1 <sup>1</sup> |
| AMD Polaris Series   | iMacPro1,1 <sup>1</sup> |
| AMD Radeon R5/R7/R9  | MacPro6,1               |
| AMD HD 8000 Series   | MacPro6,1               |
| AMD HD 7000 Series   | MacPro6,1               |
| Nvidia Kepler Series | MacPro7,1 <sup>2</sup>  |

<sup>1</sup> For Catalina and newer you can also use `MacPro7,1` if you have some issues (e. g. unfixable DRMs).

<sup>2</sup> For Catalina and older use `iMac14,2`.

### Configuration

4. You should update your BIOS to the latest version and configure it appropriately. See [BIOS Settings](#BIOS-Settings) for details.
5. Map your USB ports with [USBToolBox](https://github.com/USBToolBox/tool). Guide about it is available [here](https://github.com/USBToolBox/tool#usage). You have to do it from Windows.
6. That's it! Now you can boot macOS installer.

### Post-Installation

7. Copy your EFI directory onto your main drive EFI partition, you'll be able to boot the system without your bootable USB.
8. When everything work you can disable verbose mode - then you will see Apple's logo instead of logs while booting. To do it you have to remove `-v debug=0x100 keepsyms=1` from `boot-args` in your configuration file.

### Bootstrap
In general, enabling Bootstrap is not required, but it will protect your OpenCore from being overriden. \
Remember to do not enable Bootstrap on pendrive - do it only after copying OpenCore to your disk's EFI.

9. Go to `Misc -> Boot -> LauncherOption` in your configuration file and set it to `Full`.
10. Reboot your computer.
11. Reboot PC again and go to your BIOS settings. In boot options you will see new boot entry named `OpenCore`. Set BIOS to boot from it, instead of your drive.
12. It's done!


## BIOS Settings

| **Option**        | **Status** |
|-------------------|------------|
| OS Type           | Other OS   |
| Boot Mode         | UEFI Only  |
| On Chip Sata Type | AHCI       |
| XHCI Hand-off     | Enabled    |
| EHCI Hand-off     | Enabled    |
| Secure Boot       | Disabled   |

## Virtualization

This setup can be virtualized from Linux with KVM support using my [osx-kvm](https://github.com/scirons/osx-kvm) build.

Isolating the CPU is strongly suggested, to improve latency, especially for any audio I/O Performance optimization.

## Guides

- Creating USB installer: [\*click\*](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/)
- OpenCore configuration: [\*click\*](https://dortania.github.io/OpenCore-Install-Guide/AMD/zen.html)
- Post-Install: [\*click\*](https://dortania.github.io/OpenCore-Post-Install/)
- Troubleshooting: [\*click\*](https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/troubleshooting.html)
- ACPI patching: [\*click\*](https://dortania.github.io/Getting-Started-With-ACPI/)

If you have any other questions or issues, feel free to ask on [AMD-OSX Discord](https://discord.gg/EfCYAJW) or [Forum](https://forum.amd-osx.com).

## Credits
- [Acidanthera](https://github.com/acidanthera) for OpenCore and most of used kexts
- [Dortania](https://github.com/dortania) for OpenCore configuration guides
- [Corpnewt](https://github.com/corpnewt) for ProperTree and help updating SSDTTime to only match the lower address on PciRoot objects for my bogged Nvidia GPU card.
