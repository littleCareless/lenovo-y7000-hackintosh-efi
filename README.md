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

The Tahoe USBMap was rebuilt from this laptop's detected ports on 2026-10-05 and installed and verified after reboot on macOS 26.7.1. It matches `MacBookPro16,1`, `AppleIntelCNLUSBXHCI` and PCI parent `0:20:0`, and enables 10 logical ports: HS01, HS02, HS03, HS04, HS06, HS14 and SS01-SS04. USB-A personalities use connector type 3; the internal camera (HS06) and Bluetooth (HS14) use type 255. Both legacy and Tahoe port-property keys are included.

A Kingston DataTraveler 3.0 negotiated **5 Gb/s** on the three USB-A SuperSpeed channels: right SS04, left SS03 and rear SS01. Before the fix, the right port negotiated only 480 Mb/s on HSP3. These are negotiated link speeds, not file-copy throughput measurements. USB-C (HS04 / SS02, connector type 9 retained from firmware) is included but its two plug orientations have not been tested with a USB 3.x device.


The physical USB-A channel record is:

| Physical port | SuperSpeed channel | Controller port number | locationID | Negotiated speed | USB 2.0 channel |
| --- | --- | --- | --- | --- | --- |
| Left USB-A | SS03 | 19 (0x13) | 0x14900000 | 5 Gb/s | Not physically verified |
| Right USB-A | SS04 | 20 (0x14) | 0x14a00000 | 5 Gb/s | HS03, observed before the mapping fix |
| Rear USB-A | SS01 | 17 (0x11) | 0x14700000 | 5 Gb/s | Not physically verified |

The rear physical-port label follows the requested move to the rear port and the resulting SS01 enumeration; the left and right locations were explicitly confirmed by the owner. HS and SS numbers should not be paired by matching their suffixes. See the [Chinese port record](macOS-26/USB-PORTS.md).

A subsequent USB-C check detected an iPhone on HS04 at 480 Mb/s. The owner confirmed charging recovered after replacing the original cable. This verifies the tested USB 2.0 connection and charging behavior; USB-C SuperSpeed and both plug orientations remain untested.

The current installed wireless card model has not been verified. The Tahoe snapshot includes an `IOName` value of `pci14e4,43a0` and legacy Wi-Fi kexts; that spoof is a configuration property, not evidence of the physical card model or a guarantee that Wi-Fi, AirDrop or other continuity features work.

## Before use

1. Back up your existing EFI and have a bootable recovery option.
2. Generate your own SMBIOS values. `SystemSerialNumber`, `MLB`, `SystemUUID` and `ROM` are intentionally blank in these copies. Do not boot the sanitized files until you have configured your own values. See the [Dortania PlatformInfo guide](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/coffee-lake.html#platforminfo).
3. Compare BIOS, Wi-Fi and Bluetooth hardware, audio, trackpad, USB port layout and display routing with your laptop. Same CPU alone does not guarantee compatibility.
4. Test from removable media before replacing an EFI used for daily boot.

These folders are configuration snapshots. The Tahoe USBMap was booted and its three USB-A SuperSpeed channels were verified as described above; this does not establish that every feature has been tested. Sleep/wake, external displays, USB-C plug orientations and a fresh boot of the Sonoma snapshot remain unverified. A listed or enabled kext does not establish that its associated function has been verified.

## Contents and attribution

Each folder contains the boot files and enabled OpenCore components from its source snapshot. Historical `oldConfig.plist` files, previous EFI backups, unused kexts and macOS `._*` metadata files are excluded. Machine-specific SMBIOS identity values were cleared in the published copies.

Third-party drivers and resources remain subject to their upstream terms. Their upstream repositories and license texts collected for this snapshot are in [`LICENSES/`](LICENSES/). In particular, the Tahoe Wi-Fi folder contains Apple-branded binary kexts; they are not original code from this project. This repository's documentation and configuration notes do not relicense third-party files.

Key upstream projects include [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg), [Acidanthera](https://github.com/acidanthera), [OpenIntelWireless](https://github.com/OpenIntelWireless), [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C), [VoodooPS2](https://github.com/acidanthera/VoodooPS2), [YogaSMC](https://github.com/zhen-zen/YogaSMC), [USBMap](https://github.com/corpnewt/USBMap), [RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X), [AMFIPass](https://github.com/bluppus20/AMFIPass/releases/tag/1.4.1) and [OpenCorePkg's binary data](https://github.com/acidanthera/OcBinaryData).

If a feature is not documented as verified here, treat it as unverified on your hardware. Suggestions and corrections can be filed as GitHub issues with the relevant macOS version and hardware revision.
