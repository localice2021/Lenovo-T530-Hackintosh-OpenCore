# Lenovo ThinkPad T530 Hackintosh OpenCore

[![T530](https://img.shields.io/badge/ThinkPad-T530-informational.svg)](https://psref.lenovo.com/syspool/Sys/PDF/withdrawnbook/ThinkPad_T530.pdf) [![OpenCore](https://img.shields.io/badge/OpenCore-0.8.1-cyan.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest) [![Clover Version](https://img.shields.io/badge/Clover-r5146-lime.svg)](https://github.com/CloverHackyColor/CloverBootloader/releases) [![MacOS Catalina](https://img.shields.io/badge/macOS-10.15.7-white.svg)](https://www.apple.com/li/macos/catalina/) [![MacOS Big Sur](https://img.shields.io/badge/macOS-11.6.6-white.svg)](https://www.apple.com/macos/big-sur/) [![MacOS Monterey](https://img.shields.io/badge/macOS-12.4-white.svg)](https://www.apple.com/macos/monterey/) [![release](https://img.shields.io/badge/Download-latest-success.svg)](https://github.com/5T33Z0/Lenovo-T530-Hackinosh-OpenCore/releases/latest) ![](https://raw.githubusercontent.com/5T33Z0/Lenovo-T530-Hackinosh-OpenCore/main/Pics/BootPicker_alt2.png)

**TABLE of CONTENTS**

- [ABOUT](#about)
  - [DSDT-less config](#dsdt-less-config)
  - [EFI Folder Structure (OpenCore)](#efi-folder-structure-opencore)
- [HARDWARE SPECS](#hardware-specs)
  - [macOS-incompatible Components](#macos-incompatible-components)
- [INSTALLATION](#installation)
  - [Preparing the config.plist](#preparing-the-configplist)
    - [About used boot arguments](#about-used-boot-arguments)
  - [EFI How To](#efi-how-to)
  - [BIOS Settings](#bios-settings)
  - [Installing macOS](#installing-macos)
- [POST-INSTALL](#post-install)
  - [Fixing CPU Power Management](#fixing-cpu-power-management)
  - [Fixing SLeep issues](#fixing-sleep-issues)
  - [Fixing Command and Option Keys positions](#fixing-command-and-option-keys-positions)
  - [Changing Themes](#changing-themes)
  - [Eject Button](#eject-button)
- [CPU BENCHMARK](#cpu-benchmark)
- [CREDITS and THANK YOUs](#credits-and-thank-yous)

## ABOUT

OpenCore and Clover EFI Folders for running macOS 10.13 to 12.4+ on a Lenovo ThinkPad T530. They utilize the new `ECEnabler.kext` which enables battery status read-outs without the need for additional Battery Patches. The OpenCore EFI includes the latest Booter and Kernel patches which make use of macOS Monterey's virtualization capabilities (VMM) to spoof a fake Board-ID. This allows installing and running macOS Monterey with the `MacBookPro10,1` SMBIOS for Ivy Bridge CPUs which wouldn't be possible otherwise. So you can enjoy the benefits of optimal CPU Power Management and System Updates as well. If you want to know how these patches work, [read this](https://github.com/5T33Z0/OC-Little-Translated/tree/main/09_Board-ID_VMM-Spoof).

|:warning: Issues related to macOS (beyond my control)|
|:----------------------------------------------------|
**macOS Monterey**: requires [Intel HD4000 Patcher](https://github.com/chris1111/Patch-HD4000-Monterey) to get graphics acceleration working
[Bluetooth issues](https://dortania.github.io/OpenCore-Install-Guide/extras/monterey.html#bluetooth) on legacy Broadcom and Intel cards.
Managed to install macOS Ventura ([Proof](https://github.com/5T33Z0/Lenovo-T530-Hackintosh-OpenCore/blob/main/Pics/Ventura_Proof.png)). No graphics acceleration yet. Guide will follow.

### DSDT-less config

The configs contained in this repo are configured DSDT-less. This means, they don't use a patched DSDT. Everything is patched live using binary renames and ACPI Hotpatches (SSDTs). So instead of replacing the *whole* system DSDT by a patched one during boot, only things which need fixing are addressed and patched-in on the fly (hence the term "hot-patching")  – just like it is supposed to be done nowadays. The benefits of this approach are:

- Hotpatching is cleaner, more precise and independent of the installed BIOS version since it only addresses specific areas of ACPI tables which need patching.
- Issues which might occur with newer macOS versions can be addressed and resolved easier by modifying or adding specific SSDTs without having to update and export the whole patched DSDT again.
- The system boots faster, runs smoother and performance is better compared to using a patched DSDT.

**NOTE**: Read and follow the install instruction carefully and thoroughly before you deploy the EFI folder if you want your system to boot successfully!

<details>
<summary><strong>EFI Folder Content (OpenCore)</strong></summary>

### EFI Folder Structure (OpenCore)

```
EFI
├── BOOT
│   └── BOOTx64.efi
└── OC
    ├── ACPI
    │   ├── SSDT-AC.aml (optional, cosmetic)
    │   ├── SSDT-ALS0.aml
    │   ├── SSDT-BAT1-Disable.aml
    │   ├── SSDT-EXT4.aml
    │   ├── SSDT-EXT5.aml
    │   ├── SSDT-FWHD.aml (optional, cosmetic)
    │   ├── SSDT-HPET.aml
    │   ├── SSDT-LID.aml
    │   ├── SSDT-NBCF.aml
    │   ├── SSDT-PM.aml
    │   ├── SSDT-PNLF.aml
    │   ├── SSDT-PTSWAKTTS.aml
    │   ├── SSDT-PWRB.aml
    │   ├── SSDT-SBUS-MCHC.aml
    │   ├── SSDT-Sleep_PRW-0D6D.aml
    │   ├── SSDT-XDSM.aml
    │   └── SSDT-XOSI.aml
    ├── Drivers
    │   ├── HfsPlus.efi
    │   ├── OpenCanopy.efi
    │   └── OpenRuntime.efi
    ├── Kexts
    │   ├── AirportBrcmFixup.kext
    │   ├── AppleALC.kext
    │   ├── BlueToolFixup.kext
    │   ├── BrcmBluetoothInjector.kext
    │   ├── BrcmFirmwareData.kext
    │   ├── BrcmPatchRAM2.kext
    │   ├── BrcmPatchRAM3.kext
    │   ├── BrightnessKeys.kext
    │   ├── ECEnabler.kext
    │   ├── IntelMausi.kext
    │   ├── Lilu.kext
    │   ├── RestrictEvents.kext
    │   ├── SMCBatteryManager.kext
    │   ├── VirtualSMC.kext
    │   ├── VoodooPS2Controller.kext
    │   ├── VoodooSDHC.kext
    │   └── WhateverGreen.kext
    ├── OpenCore.efi
    ├── Resources (NOTE: shows sub-folders only, no files)
    │   ├── Font
    │   ├── Image
    │   │   └── Acidanthera
    │   │   │   ├── Chardonnay
    │   │   │   ├── GoldenGate
    │   │   │   └── Syrah
    │   │   └── Blackosx
    │   │   │   └── BsxM1
    │   │   └── velickovicdj
    │   │   │   └── EnterTwilight
    │   │   └── Label
    ├── Tools
    │   └── CleanNvram.efi
    └── config.plist
```
</details>

## HARDWARE SPECS
| Component           | Details                                       |
| ------------------: | :-------------------------------------------- |
| Model               | Lenovo ThinkPad T530, Model# 2429-62G         |
| Chipset             | [Intel QM77 Express](https://www.intel.com/content/dam/www/public/us/en/documents/datasheets/7-series-chipset-pch-datasheet.pdf)
| BIOS Version        | 2.77, unlocked with 1vyRain                   |
| Processor           | Intel Core i7 3630QM                          |
| Memory              | 16GB Samsung DDR3 1600MHz, Dual-Channel       |
| Hard Disk           | Samsung 840 Evo 250GB                         |
| Integrated Graphics | Intel HD Graphics 4000                        |
| Display             | 15.6" HD+ TFT Display (1600x900 px)           |
| Audio               | Realtek ALC269VC Rev.3 (Layout-id:`40`)       |
| Ethernet            | Intel 82579LM Gigabit Network Connection      |
| WIFI+BT             | Broadcom BCM94352HMB DW1550, 802.11 a/b/g/n/ac|
| Docking Station     | Lenovo ThinkPad 4338 Mini Dock plus Series 3  |

[**ThinkPad T530 User Guide (PDF)**](https://download.lenovo.com/ibmdl/pub/pc/pccbbs/mobiles_pdf/t530_t530i_w530_ug_en.pdf)

### macOS-incompatible Components
- [ ] NVIDIA Optimus GPU must be disabled in BIOS - otherwise no boot!
- [ ] Fingerprint Reader
- [ ] VGA Port – not supported since macOS Mountain Lion: [Intel HD Graphics VGA Support](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#vga-support)
</details>

## INSTALLATION
### Preparing the config.plist
Please read the explanations in the following sections carefully and follow the given instructions. In order to boot macOS with this EFI successfully, adjustments to the `config.plist` may be necessary according to the used hardware and macOS version you want to use. 

Open the `config.plist` and do the following:

**IMPORTANT**: You can skip steps 1 and 2 on macOS Monterey &rarr; the new Board ID skip and VMM patches enable native SMBIOS support for your CPU. No need to use a newer one which was not written for Ivy Bridge CPUs: either use `MacBookPro9,x` or `MacbookPro10,x` and macOS Monterey will boot and install updates via Software Update.

1. Set `SystemProductName` according to the CPU and the macOS version you want to use: 
	-  For Intel i5/i7, macOS 12: `MacBookPro10,1` or `MacBookPro10,2`
	-  For Intel i5/i7, macOS 11.3+: `MacBookPro10,1` or `MacBookPro10,2`
	-  For Intel i7, macOS 10.13 to 10.15: `MacBookPro10,1` (Recommended combo)
	-  For Intel i5, macOS 10.13 to 10.15: `MacBookPro10,2`
	
	**NOTE**: This config uses special Boooter and Kernel Patched in OpenCore which allow using the correct SMBIOS for Ivy Bridge CPUs on macOS 11.3 and newer (Darwin Kernel 20.4+) whch wouldn't be posible otherwise. If you are using macOS Big Sur lower than 11.3, you need to use `MacBookPro11,X` instead, since the patches won't work with earlier versions of Big Sur.

2. Adjust `csr-active-config` according to the macOS version you want to use:
	- For macOS Monterey: `EF0F0000`(0xFEF in Clover)
	- For macOS Big Sur: `67080000`(0x867)
	- For macOS Mojave/Catalina: `FF070000`(0x7FF)
	- For macOSHigh Sierra: `FF030000` (0x3FF)
	
	**NOTE**: You need to disable SIP if you use macOS Monteray with patched-in Intel HD 4000 Drivers!

3. Select the correct Framebuffer-Patch for your T530 model. Two display panels exist: `HD+` (WSXGA and FullHD) and `HD` panels. Both are using different identifiers:</br>
	
	`AAPL,ig-platform-id 04006601` = `HD+` = FullHD. Resolution: ≥ 1600x900 px. (**Default**)</br>
	`AAPL,ig-platform-id 03006601` = `HD` = SD. Resolution: ≤ 1366x768 px</br>
	
	If your T530 Model uses an SD Panel, do the following;
	 
	- Go to `DeviceProperties` 
	- Disable the `PciRoot(0x0)/Pci(0x2,0x0)` by placing `#` in front of it.
	- Next, enable `#PciRoot(0x0)/Pci(0x2,0x0) 1366x768 px` by deleting the leading `#` and the description ` 1366x768 px`, so that it looks this: `PciRoot(0x0)/Pci(0x2,0x0)`.
	
	**HINT**: If your screen turns off during boot, you are using the wrong Framebuffer-Patch!

4. **CPU**: The `SSDT-PM.aml` inside the ACPI Folder is for an **Intel i7 3630QM**. If you use a different CPU model, disable it for now and create your own using `ssdtPRGen` in Post-Install. (See 'Fixing CPU Power Management' in the 'Post-Install Section')

5. **WiFi/Bluetooth** (Read carefully!)
	- I use a 3rd Party WiFi/BT Card with a Broadcom Chip
	- 3rd Party WiFi/BT Cards require the `1vyrain` Jailbreak to unlock the BIOS which disables the WiFi Whitelist (not necessary if the 3rd party card is whitelisted).
	- I use `BrcmFirmwareData.kext` for Bluetooth which can be injected by OpenCore and Clover. Alternatively, you could use `BrcmFirmwareRepo.kext` instead. But it needs to be installed into System/Library/Extensions since it cannot be injected by Bootloaders. It's supposed to be more efficient than BrcmFirmwareData.kext, but it also takes more effort to install and update.
	- If you use a WiFi/BT Card from a different vendor than Broadcom, remove BluetoolFixup and the "Brcm…" Kexts, add the Kext(s) required for your card to your kext folder and `config.plist` before trying to boot from this EFI!
	- If you use the stock Intel(r) WiFi/Bluetooth Card, it may work with the OpenIntelWireless kext. Check [OpenIntelWireless](https://github.com/OpenIntelWireless) to find out if your card is supported (yet). If so, remove the BluetoolFixup and Brcm Kexts, add the required Kext(s) card to your kext folder and `config.plist` before trying to boot from this EFI!

6. **Alternative/Optional Kexts**:
	- [**itlwm**](https://github.com/OpenIntelWireless/itlwm): Kext for Intel WiFi Cards. Use instead of `AirportBrcmFixup`if you don't use a Broadcom WiFi Card
	- [**IntelBluetoothFirmware**](https://github.com/OpenIntelWireless/IntelBluetoothFirmware): Kext for Intel Bluetooth Cards. Use instead of `BrcmPatchRam` and Plugins if you don't use a Broadcom BT Card
	- [**NoTouchID**](https://github.com/al3xtjames/NoTouchID): only required for macOS 10.13 and 10.14 so the boot process won't stall while looking for a Touch ID sensor.
	- [**Feature Unlock**](https://github.com/acidanthera/FeatureUnlock): Unlocks additional features like Sidecar, NighShift, Airplay to Mac, Universal Control and Content Caching. Under macOS Monterey, Content Caching also requires `-allow_assetcache` boot-arg.
	- [**RestictEvents.kext**](https://github.com/acidanthera/RestrictEvents): Combined with boot-arg `revpatch=diskread,memtab`, it enables "Memory" tab in "About this Mac" section and disables "Uninitialized Disk" warning in Finder (for macOS 10.14 and older).

7. **Backlight Brightness Level tweaks** (optional): 
  - Set boot-arg `applbkl=1` for reasonable maximum brightness level controlled by `WhateverGreen`. 
  - Set boot-arg `applbkl=0` for increased maximum brightness as defined in `SSDT-PNLF.aml`

#### About used boot arguments
- `brcmfx-country=#a`: Contry Code for Wifi (`#a` = generic). For details check the documentaion for AirportBrcmFixup.
- `gfxrst=1`: to prefer drawing Apple logo at 2nd boot stage instead of framebuffer copying &rarr; Smoother the transition from the progress bar to desktop when an external monitor is attached.
- `#revpatch=diskread,memtab`: For `RestrictEvents.kext`. `diskread` disables "Uninitialized Disk" warning in macOS 10.14 and older. `memtab` adds `Memory` tab to "About this Mac" section. Enable `RestrictEvents.kext` and Remove the leading `#` from the boot-arg to make it work.
- `#-no_compat_check`: For installing/booting macOS Big Sur and newer without having to change the SMBIOS. Remove leading `#` to enable the boot-arg. Note that installing system updates via the software update will not be possible when using this.

### EFI How To
- Download the EFI Folder from the [Releases](https://github.com/5T33Z0/Lenovo-T530-Hackintosh-OpenCore/releases) section on the right and unpack it
- Open config.plist and follow the instructions given in the "Preparation" Section
- Mount the EFI
- Replace EFI Folder
- Restart
- :warning: Perform a NVRAM Reset (in Bootpicker, hit Space Bar to reveal Tools)
- Reboot
- Select macOS to boot. It's currently configured for running macOS Mojave up to Monterey. You can research a suitable/matching SMBIOS for your CPU on everymac.com.

### BIOS Settings
**Latest BIOS Version:** `2.77`
[**DOWNLOAD**](https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-t-series-laptops/thinkpad-t530/downloads/ds029246?clickid=RhAUWZ1-exyLRCuwUx0Mo3ELUkERY-RmHTlwSg0&Program=3786&pid=269814&acid=ww%3Aaffiliate%3A74clty&cid=de%3Aaffiliate%3Axg02ds)

**CONFIG [TAB]**

* USB UEFI BIOS Support: `Enabled`
* USB 3.0 Mode: `Enabled`
* Display > Boot Display Device: `ThinkPad LCD`
* Display > OS Detection for NVIDIA Optimus: `Disabled`
* SATA > SATA Controller Mode: `XHCI`
* CPU > Core Multi-Processing: `Enabled`
* CPU > Intel (R) Hyper-Threading: `Enabled` (CPU must support it)

**SECURITY [TAB]**

* Security Chip: `Disabled`
* UEFI BIOS Update Options > Flash BIOS Updating by End-Users: `Enabled`
* UEFI BIOS Update Options > Secure Rollback Prevention: `Enabled`
* Memory Protection: `Enabled`
* Virtualization > Intel (R) Virtualization Technology: `Enabled` (Relevant for Windows only, disabled in macOS via `DisableIOMapper` Quirk)
* I/O Port Access (`Disable` the following devices/features):
	* Wireless WAN
	* ExpressCard Slot
	* eSATA Port
	* Fingerprint Reader
	* Antitheft and Computrace
	* Secure Boot: `Disabled`

**STARTUP [TAB]**

* Boot (Set the Order of Boot devices. Set HDD/SSD as first device)
* UEFI/Legacy Boot: `UEFI only`
* CSM Support: `Disabled`
* Boot Mode: `Quick`
* Boot Order Lock: `Enabled` Enable this *after* you've set-up the order of the Boot Drives. This prohibits `WindowsBootManager` from taking over the first slot of the boot drives.

### Installing macOS
**Coming from Windows/Linux**: If you are on Windows or Linux, follow the guide provided by [Dortania](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/#making-the-installer). **NOTE**: No support from my end for issues related to UBS Installers created in Windows or Linux!

**Coming from macOS**: If you already have access to macOS, you can either download macOS from the App Store or use [**ANYmacOS**](https://www.sl-soft.de/en/anymacos/) instead. It's a hassle-free app than can download any macOS from High Sierra up to Monterey and can create an USB Installer as well.

**macOS Monterey**: For installing macOS Monterey, follow the `Monterey Instructions-md` included in the EFI Downloads you find the [**Releases**](https://github.com/5T33Z0/Lenovo-T530-Hackinosh-OpenCore/releases) Section.
</details>

## POST-INSTALL
Once your system is up and running you may want to change the following settings to make your system more secure:

- Enable System Integrity Protection (SIP): change `csr-active-config` to `00000000`
- Under `UEFI/APFS`, change `MinDate` and `MinVersion` from `-1` (disabled) to the correct values for the macOS version you are using. A list with the correct values for can be found [here](https://github.com/5T33Z0/OC-Little-Translated/tree/main/A_Config_Tips_and_Tricks#mindateminversion-settings-for-the-apfs-driver).

**IMPORTANT**: 

- **SIP**: If you're planning to install macOS Monterey, SIP needs to be disabled! Because installing the graphics drivers with Intel HD 4000 Patcher breaks macOS securiity seal so therefore boot will crash if SIP is enabled!
- **MinDate/MinVersion**: you should keep a working backup of your EFI folder on a FAT32 formatted USB flash drive before changing these settings, because if they are wrong, the APFS driver won't load and you won't see your macOS drive(s)!

### Fixing CPU Power Management 
1. Open config.plist
2. Disable `SSDT-PM.aml` under ACPI/Add
3. Enable the 2 Patches under ACPI/Delete (`Drop CpuPm` and `Drop Cpu0Ist`)
4. Save config and reboot
5. Install [ssdtPRGen](https://github.com/Piker-Alpha/ssdtPRGen.sh)
6. Open Terminal and type: sudo /Users/YOURUSERNAME/ssdtPRGen.sh
7. Go to Users/YOURUSERNAME/Library/ssdtPRGen. There you'll find an ssdt.aml
8. Rename `ssdt.aml` to `SSDT-PM.aml` and replace the one in EFI > OC > ACPI with it
9. In config, go to ACPI > Add and re-enable `SSDT-PM.aml` if it is disabled.
10. Disable the two patches from step 2 again.
11. Save config and reboot. 

CPU Power Management should work fine after that. Optionally, you can install [Intel Power Gadget](https://www.intel.com/content/www/us/en/developer/articles/tool/power-gadget.html) to check if the CPU runs within specs. You don't need SMCProcessor and SMCSuperIO kexts if you use Intel Power Gadgets, btw.

**NOTEs**: 

- Only necessary if you use a different CPU than i7 3630QM
- You can add modifiers to the terminal command for building SSDT-PM. For example, you can drop the low frequency from the default 1200 MHz to 900 MHz in 100 MHz increments, but no lower than that. Otherwise the system crashes during boot. I suggests you experiment with the modifiers a bit.
- If you feel really confident and enthusiastic you could also re-enable XCPM. But in my experience the machine does not perform as good. You can [follow this guide](https://github.com/5T33Z0/Lenovo-T530-Hackinosh-OpenCore/tree/main/Enable%20XCPM) if you're so inclined.<br>
- If you are running macOS Big Sur or Monterey, you can achieve better thermals (with a lot less fan activity) if you define the System as `MacBookPro10,1` instead of `MacBookPro11,1` (Big Sur) or `MacBookPro11,4` (Monterey). But then you also need to add the boot-arg `-no_compat_check`. Otherwise your system won't boot since macOS Monterey is not supposed to run on anything older than MacBookPro11,4. The downside of using `-no_compat_check` is that you won't be able to download System Updates directly (use ANYmacOS instead). But in my opinion, using `MacBookPro10,1` makes much more sense because the system is more power efficient and silent since the idle Frequency is around 800 mHz lower.
- Since Big Sur requires `MacBookPro11,1` to boot and Monterey `MacBookPro11,4`, `ssdtPRGen` fails to generate SSDT-PM, because it relies on Board-IDs containing data for Plugin-Type 0. As a workaround, you can either:

	- use `SSDTTime` to generate a `SSDT-PLUG.aml` **or** 
	- use `MacBookPro10,1` but add `-no_compat_check` to `boot-args`.

**Advantages** of using `MacBookPro10,1` with `-no_compat_check` are:

- You can boot Big Sur **and** use ssdtPRGen. 
- The CPU runs at lower clock speeds in idle since this SMBIOS was written for Ivy Bridge, while 11,x was written for Haswell CPUs. Therefore the CPU produces less heat and the machine runs quieter.
- Another benefit of using `MacBookPro10,1` is that you get the correct P-States and C-States for your CPU from ssdtPRGen.

**Disadvantage** of using `MacBookPro10,1`: you won't be able to install System Updates because you won't be notified about them. But there's a simple **workaround**:

  - Change `SystemProductName` back to `MacBookPro11,4`
  - Set `csr-active-config` to `EF0F0000`
  - Disable `-no_compat_check` boot-arg (add a '#' in front of it)
  - Reboot
  - Reset NVRAM
  - Boot macOS
  - Check for and install Updates
  - After the Updates are installed, revert to SMBIOS `MacBookPro10,1`
  - re-enable `-no_compat_check` boot-arg 
  - Reboot

### Fixing SLeep issues
If you have issues with sleep, run the following commands in Terminal:

	sudo pmset hibernatemode 0
	sudo rm /var/vm/sleepimage
	sudo touch /var/vm/sleepimage
	sudo chflags uchg /var/vm/sleepimage

### Fixing Command and Option Keys positions
Prior to version 0.7.4 of my OpenCore EFI Folder, the **[Command]** and **[Option]** keys were set to "swapped" in the `info.plist` of `VoodooPS2Keyboard.kext` by default. So in macOS, the **[WINDOWS]** key was bound to the **[Option]** function and the **[ALT]** Key was bound to the **[Command]** function which felt weird. Therefore, users had to swap these Keys back around in the System Settings so everything worked as expected.

Since then, I've undone the key swap inside the `VoodooPS2Keyboard.kext` plugin so that the Key bindings are working as expected out of the box. So if you are updating from 0.7.3 or lower to 0.7.4, reset the Keyboard Modifier Keys back to Default in System Settings > Keyboard to so everything is back to normal.

If the "<", ">" and "^" Keys are switched/reversed, change `Use ISO layout keyboard` from `false` to `true` in the `info.plist` of `VoodooPS2Keyboard.kext`. 

### Changing Themes
Besides the 3 default themes by Acidanthera included in the OpenCore package, I've added an additional theme by Blackosx called BsxM1 which is set as default. To change the theme to something else, do the following: 

- Open `config.plist` in OpenCore Auxiliary Tools
- Go to Misc > Boot 
- Select a different theme from the dropdown menu in `PickerVariant`
- Save `config.plist` and reboot to apply the theme.

To revert these changes, enter `Acidanthera\GoldenGate` as `PickerVariant` and change the Flavour for the NVRAM Reset Tool back to `Auto`.

### Eject Button 
macOS locks the optical drive sometimes so that you can't open it with the physical eject button – even if no media is present. To fix this you have 2 options:

- **Option 1**: Go to `System > Library > CoreService > Menu Extras` and double-click on `Eject.menu`. This adds an Eject button Icon to the Menu Bar.
- **Option 2**: Press and hold the `INS` button (right below the Power Button) until the Eject Icon appears on the screen and the CD tray opens.
</details>

## CPU BENCHMARK

![Screenshot](https://raw.githubusercontent.com/5T33Z0/Lenovo-T530-Hackinosh-OpenCore/main/Pics/benchmark_latest.png)</br>
[Benchmark Results](https://browser.geekbench.com/v5/cpu/9553877)

## CREDITS and THANK YOUs

- George Kushnir for [1vyrain Jailbreak](https://github.com/n4ru/1vyrain) to remove WLAN whitelist from BIOS
- Acidanthera and Team for [OpenCore Bootloader](https://github.com/acidanthera/OpenCorePkg) and additional Kexts
- Dortania for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide)
- SergeySlice for [Clover Bootloader](https://github.com/CloverHackyColor/CloverBootloader)
- chris1111 for [IntelHD4000 Patcher](https://github.com/chris1111/Patch-HD4000-Monterey) for macOS Monterey and OC Themes
- 1Revenger1 for [ECEnabler](https://github.com/1Revenger1/ECEnabler)
- [ic005k](https://github.com/ic005k/) for OpenCore Auxiliary Tools and PlistEDPlus
- Mackie100 for [Clover Configurator](https://mackie100projects.altervista.org/download-clover-configurator/)
- [Corpnewt](https://github.com/corpnewt) for SSDTTime, GenSMBIOS, ProperTree and BitmaskDecode
- Piker-Alpha for [ssdtPRGen](https://github.com/Piker-Alpha/ssdtPRGen.sh)
- [SL-Soft](https://www.sl-soft.de/software/) for Kext Updater and ANYmacOS
- khronokernel for [Clover Vanilla Install Guide](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/)
- [Rehabman](https://github.com/RehabMan) for Laptop and DSDT patching guides
- daliansky for [OC Little](https://github.com/5T33Z0/OC-Little-Translated) ACPI Hotpatch Collection
- [RealKiro](https://github.com/RealKiro/Hackintosh) for Clover EFI with ACPI Patches for referencing
- [banhbaoxamlan](https://github.com/banhbaoxamlan/X230-Hackintosh) for ACPI files to fix the post-sleep pulsing LED issue.
- jsassu20 for [MacDown](https://macdown.uranusjr.com/) Markdown Editor
