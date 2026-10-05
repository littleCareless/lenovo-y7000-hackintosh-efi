# Lenovo Y7000 Hackintosh EFI

Two OpenCore snapshots for one Lenovo Y7000 laptop, with separate folders for macOS 14 Sonoma and macOS 26 Tahoe.

## Hardware

| Item | Specification |
| --- | --- |
| Laptop | Lenovo Y7000 |
| CPU | Intel Core i5-8300H |
| Memory | 16 GB |
| Integrated GPU | Intel UHD Graphics 630 |
| Original discrete GPU | NVIDIA GeForce GTX 1050 Ti |

The EFI identifies the machine as MacBookPro15,2 (macOS 14) or MacBookPro16,1 (macOS 26). These are SMBIOS identities, not the laptop's physical model. The GTX 1050 Ti is a Pascal GPU; current macOS releases use the UHD 630 for graphics. See the [Dortania GPU guide](https://dortania.github.io/GPU-Buyers-Guide/modern-gpus/nvidia-gpu.html#pascal-series-gtx-10xx).

## Browse and download

- macOS 14 Sonoma: [`macOS-14/EFI`](macOS-14/EFI/)
- macOS 26 Tahoe: [`macOS-26/EFI`](macOS-26/EFI/)
- Download the complete repository, including both EFI folders and the license notes: [GitHub ZIP archive](https://github.com/littleCareless/lenovo-y7000-hackintosh-efi/archive/refs/heads/main.zip)

## Pick the matching snapshot

| Folder | Intended system | Snapshot details |
| --- | --- | --- |
| `macOS-14/EFI` | macOS 14 Sonoma | `MacBookPro15,2`; `-igfxblt`; AirportItlwm compatibility patches target Darwin 23. |
| `macOS-26/EFI` | macOS 26 Tahoe | `MacBookPro16,1`; `-igfxblt -ibtcompatbeta -amfipassbeta TSC_sync_margin=0`; includes AMFIPass 1.4.1, CpuTscSync 1.1.2, IOSkywalkFamily 1.0, IO80211FamilyLegacy 1200.12.2b1 and AirportItlwm 2.3.0. |

OpenCore's binary does not expose a version string that could be verified from this snapshot, so no version number is claimed. Both configurations currently set `SecureBootModel` to `Disabled` and `csr-active-config` to `030A0000`; review these settings for your own setup.

The source Tahoe USBMap was labelled `MacBookPro15,2-XHC` and `model=MacBookPro15,2`. I aligned those two descriptive fields to `MacBookPro16,1` in the published copy. I left its actual matching and port data unchanged: it targets the active `AppleIntelCNLUSBXHCI` controller through PCI parent `0:20:0`. The map lists 15 HS entries, enables HS01, HS02, HS03, HS06 and HS14 as connector type 3, and has no SS entries. The physical USB 2 and USB 3 ports have not been tested against this map, so USB mapping remains unverified.

The current installed wireless card model has not been verified. The Tahoe snapshot includes an `IOName` value of `pci14e4,43a0` and legacy Wi-Fi kexts; that spoof is a configuration property, not evidence of the physical card model or a guarantee that Wi-Fi, AirDrop or other continuity features work.

## Before use

1. Back up your existing EFI and have a bootable recovery option.
2. Generate your own SMBIOS values. `SystemSerialNumber`, `MLB`, `SystemUUID` and `ROM` are intentionally blank in these copies. Do not boot the sanitized files until you have configured your own values. See the [Dortania PlatformInfo guide](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/coffee-lake.html#platforminfo).
3. Compare BIOS, Wi-Fi and Bluetooth hardware, audio, trackpad, USB port layout and display routing with your laptop. Same CPU alone does not guarantee compatibility.
4. Test from removable media before replacing an EFI used for daily boot.

These folders are configuration snapshots. The file and kext inventory was checked, but this publication process did not perform a fresh boot, sleep/wake, external display, or per-port USB test. A listed or enabled kext does not establish that its associated function has been verified.

## Contents and attribution

Each folder contains the boot files and enabled OpenCore components from its source snapshot. Historical `oldConfig.plist` files, previous EFI backups, unused kexts and macOS `._*` metadata files are excluded. Machine-specific SMBIOS identity values were cleared in the published copies.

Third-party drivers and resources remain subject to their upstream terms. Their upstream repositories and license texts collected for this snapshot are in [`LICENSES/`](LICENSES/). In particular, the Tahoe Wi-Fi folder contains Apple-branded binary kexts; they are not original code from this project. This repository's documentation and configuration notes do not relicense third-party files.

Key upstream projects include [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg), [Acidanthera](https://github.com/acidanthera), [OpenIntelWireless](https://github.com/OpenIntelWireless), [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C), [VoodooPS2](https://github.com/acidanthera/VoodooPS2), [YogaSMC](https://github.com/zhen-zen/YogaSMC), [USBMap](https://github.com/corpnewt/USBMap), [RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X), [AMFIPass](https://github.com/bluppus20/AMFIPass/releases/tag/1.4.1) and [OpenCorePkg's binary data](https://github.com/acidanthera/OcBinaryData).

If a feature is not documented as verified here, treat it as unverified on your hardware. Suggestions and corrections can be filed as GitHub issues with the relevant macOS version and hardware revision.
