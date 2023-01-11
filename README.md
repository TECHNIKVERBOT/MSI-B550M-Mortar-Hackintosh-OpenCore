# MSI-B550M-Mortar-Hackintosh-OpenCore
 
### Before you give this EFI a try, make sure you read [this](#set-cpu-core-count) and [this](#generating-your-own-serial-and-editing-rom)!

This repo includes an OpenCore EFI for the MSI B550M Mortar motherboard.

Testing on:

Model | MSI B550M Mortar
------------- | ---------------
CPU | AMD Ryzen 5 3600
GPU | AMD Radeon RX Vega 56
RAM | 16 GB DDR4-3200 (Corsair Vengance RGB)
Disk (NVMe) | WD Black SN750 (Windows 11)
Disk (SATA) | Crucial BX500 (macOS)
Disk (Data) | Samsung 860 QVO

## What works?

- Audio
- Boot
- USB
- Ethernet
- GPU acceleration
- Sleep
- [Power Management](#AMD-CPU-Power-Management)

## What doesn't work?

- microphone output

## Set CPU core count

Core Count patch needs to be modified to boot your system, if not using a 6 core CPU. Find the two `algrey - Force cpuid_cores_per_package` patches and alter the `Replace` value only.

Changing `BA000000 0000`/`BA000000 0090`* to `BA <CoreCount> 0000 0000`/`BA <CoreCount> 0000 0090`* substituting `<CoreCount>` with the hexadecimal value matching your physical core count.

**Note:** *The two different values reflect the patch for different versions of macOS. Be sure to change all two if you boot macOS 10.15 to macOS 13*

See the table below for the values matching your CPU Core Count.

| CoreCount | Hexadecimal |
|--------|---------|
|   4 Core  | `04` |
|   6 Core  | `06` |
|   8 Core  | `08` |
|   12 Core | `0C` |
|   16 Core | `10` |

  For example on a 6 Core CPU the `Replace` value would result in these replace values, `BA 06 0000 0000`/`BA 06 0000 0090` (default configuration in this EFI)

## BIOS settings

[Dortania BIOS Guide](https://dortania.github.io/OpenCore-Install-Guide/AMD/zen.html#amd-bios-settings)

## GPU specific settings

AMD Polaris and Vega GPU users can skip this portion. For other GPUs, you'll need to add their appropriate boot-args:

| GPU type | boot-args |
|--------|---------|
| NVIDIA Kepler (GTX 600/700 series) (Catalina/Big Sur) | `shikigva=40 agdpmod=vit9696` |
| AMD Navi (RX 5000/6000 series) | `agdpmod=pikera`

For Kepler users wanting to use Monterey or Ventura:

1. Set `SecureBootModel` to from `Default` to `Disabled`
2. Add `ipc_control_port_options=0` and (Ventura only) `amfi_get_out_of_my_way=1` in addition to the aformentioned boot-args
3. Change value of `csr-active-config` to `03080000`
4. Grab [OpenCore Legacy Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher/) and install the Post-Install root patches to add back GPU acceleration!

There are two ways you can make a USB installer:

1. Have a working install of macOS, download the Installer from the App Store, then make a bootable Installer with the `createinstallmedia` command
2. If you are using Windows, use [macrecovery.py](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/windows-install.html) from the offical OpenCore release package.

After you have created a bootable Installer, copy the EFI folder to the EFI partition of the installer drive and install as usual (GUID Partiton Map; APFS). After the installation, mount the EFI partition of the installed OS and copy the EFI folder to its partition.

## Generating your own serial and Editing ROM

Use GenSMBIOS (https://github.com/corpnewt/GenSMBIOS) to generate a serial for MacPro7,1

use PlistEdit Pro or any decent plist editor to manually enter the details in the following sections of the config (as shown in the video): (SystemSerialNumber, MLB, UUID and ROM)

https://user-images.githubusercontent.com/59102649/116117179-3ea51200-a6bc-11eb-8a18-a03f7bb5bf1d.mp4

## AMD CPU Power Management

While macOS might not officially support AMD CPU Power management, there are community efforts to add it. Specifically being `SMCAMDProcessor.kext` and `AMDRyzenCPUPowerManagement.kext`, both of which are included in the EFI, but diasbled by deafult.

Warning: They're known to create stability issues as well, if you're receiving random kernel panics or issues booting, do keep in mind these kexts may be the culprit.

## Credits

* [TECHNIKVERBOT](https://github.com/TECHNIKVERBOT) (for creating the EFI)
* [AMD-OSX](https://github.com/AMD-OSX/AMD_Vanilla) (for the kernel patches)
* [acidanthera](https://github.com/acidanthera) (for OpenCore and the kexts)
* [dortania](https://dortania.github.io/OpenCore-Install-Guide/) for their awesome guide
