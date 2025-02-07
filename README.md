# Lenovo ThinkPad T530 Hackintosh OpenCore

[![T530](https://img.shields.io/badge/ThinkPad-T530-informational.svg)](https://psref.lenovo.com/syspool/Sys/PDF/withdrawnbook/ThinkPad_T530.pdf) [![OpenCore](https://img.shields.io/badge/OpenCore-0.8.8-cyan.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest) [![Clover Version](https://img.shields.io/badge/Clover-r5150-lime.svg)](https://github.com/CloverHackyColor/CloverBootloader/releases) [![MacOS Catalina](https://img.shields.io/badge/macOS-10.15.7-white.svg)](https://www.apple.com/li/macos/catalina/) [![MacOS Big Sur](https://img.shields.io/badge/macOS-11.7-white.svg)](https://www.apple.com/macos/big-sur/) [![MacOS Monterey](https://img.shields.io/badge/macOS-12.6.2-white.svg)](https://www.apple.com/macos/monterey/) [![macOS Ventura](https://img.shields.io/badge/macOS-13.2-white.svg)](https://www.apple.com/macos/ventura/) [![release](https://img.shields.io/badge/Download-latest-success.svg)](https://github.com/5T33Z0/Lenovo-T530-Hackinosh-OpenCore/releases/latest) ![16123445](https://user-images.githubusercontent.com/76865553/208101604-eb58ec92-2f56-46e2-9ffd-84f9af286415.png)

**TABLE of CONTENTS**

- [ABOUT](#about)
  - [Audio Working on Docking Stations](#audio-working-on-docking-stations)
  - [DSDT-less config](#dsdt-less-config)
- [HARDWARE SPECS](#hardware-specs)
  - [macOS-incompatible Components](#macos-incompatible-components)
- [EFI Folder Content (OpenCore)](#efi-folder-content-opencore)
- [INSTALLATION](#installation)
  - [Preparing the config.plist](#preparing-the-configplist)
    - [About used boot arguments](#about-used-boot-arguments)
  - [EFI How To](#efi-how-to)
  - [BIOS Settings](#bios-settings)
  - [Installing macOS](#installing-macos)
- [POST-INSTALL](#post-install)
  - [Fixing CPU Power Management](#fixing-cpu-power-management)
  - [Fixing Sleep issues](#fixing-sleep-issues)
  - [Reducing boot time](#reducing-boot-time)
  - [Swapping Command ⌘ and Option ⌥ Keys](#swapping-command--and-option--keys)
  - [Changing Themes](#changing-themes)
  - [Eject Button](#eject-button)
  - [Workaround for Apple Music crashing in macOS Catalina](#workaround-for-apple-music-crashing-in-macos-catalina)
- [CPU BENCHMARK](#cpu-benchmark)
- [CREDITS and THANK YOUs](#credits-and-thank-yous)

## ABOUT
OpenCore and Clover EFI Folders for running macOS 10.13 to 13.1+ on a Lenovo ThinkPad T530. They utilize the new `ECEnabler.kext` which enables battery status read-outs without the need for additional Battery Patches. 

The OpenCore EFI also includes the latest Booter and Kernel patches which make use of macOSes virtualization capabilities (VMM) to spoof a special Board-ID which allows installing and running macOS Big Sur and Monterey with SMBIOS `MacBookPro10,1`for Ivy Bridge CPUs. With this, you can enjoy the benefits of optimal CPU Power Management *and* System Updates which wouldn't be possible when using the well-known`-no_compat_chack` boot arg. If you want to know how these patches work, [read this](https://github.com/5T33Z0/OC-Little-Translated/tree/main/09_Board-ID_VMM-Spoof).

|:warning: Issues related to macOS 12+|
|:------------------------------------|
|**macOS Monterey and newer**: requires [**OCLP**](https://github.com/dortania/Opencore-Legacy-Patcher) to enable graphics acceleration

### Audio Working on Docking Stations 
I created my own AppleALC Layout-ID which supports the Lenovo Mini Dock 3 Type 4337 and 4338 Docking Stations. It uses **Layout-ID 39** and has been integrated into AppleALC since [version 1.7.3](https://github.com/acidanthera/AppleALC/releases/tag/1.7.3)

### DSDT-less config

The config contained in this repo is DSDT-less. This means, it doesn't use a patched DSDT. Everything is patched live using binary renames and ACPI Hotpatches (SSDTs). So instead of replacing the *whole* system DSDT by a patched one during boot, only things which need fixing are addressed and patched-in on the fly (hence the term "hot-patching")  – just like it is supposed to be done nowadays. The benefits of this approach are:

- Hotpatching is cleaner, more precise and independent of the installed BIOS version since it only addresses specific areas of ACPI tables which need patching.
- Issues which might occur with newer macOS versions can be addressed and resolved easier by modifying or adding SSDTs without having to update and export the whole patched DSDT again.
- The system boots faster, runs smoother and performance is better compared to using a patched DSDT.

**NOTE**: Read and follow the install instruction carefully and thoroughly before you deploy the EFI folder if you want your system to boot successfully!

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
| Audio               | Realtek ALC269VC Rev.3 (Layout-id:`39`)       |
| Ethernet            | Intel 82579LM Gigabit Network Connection      |
| WiFi and Bluetooth  | Broadcom BCM94352HMB DW1550, 802.11 a/b/g/n/ac|
| Multicard Reader    | Ricoh 4-in-1 reader (MMC, SD, SDHC, SDXC)     |
| ExpressCard/34 slot | disabled                                      |
| Docking Station     | Lenovo ThinkPad 4338 Mini Dock plus Series 3  |

[**ThinkPad T530 User Guide (PDF)**](https://download.lenovo.com/ibmdl/pub/pc/pccbbs/mobiles_pdf/t530_t530i_w530_ug_en.pdf)

### macOS-incompatible Components
- [ ] NVIDIA Optimus GPU must be disabled in BIOS - otherwise no boot!
- [ ] Fingerprint Reader
- [ ] VGA Port – not supported since macOS Mountain Lion: [Intel HD Graphics VGA Support](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#vga-support)

## EFI Folder Content (OpenCore)

<details>
<summary><strong>Click to reveal</strong></summary>

```
EFI
├── BOOT
│   └── BOOTx64.efi
└── OC
    ├── ACPI
    │   ├── SSDT-ALS0.aml
    │   ├── SSDT-BAT1-Disable.aml
    │   ├── SSDT-EXT4.aml
    │   ├── SSDT-EXT5.aml
    │   ├── SSDT-HPET.aml
    │   ├── SSDT-NBCF.aml
    │   ├── SSDT-PM.aml
    │   ├── SSDT-PNLF.aml
    │   ├── SSDT-PRW0.aml
    │   ├── SSDT-PTSWAKTTS.aml
    │   ├── SSDT-PWRB.aml
    │   ├── SSDT-SBUS-MCHC.aml
    │   └── SSDT-TEMPToFans.aml
    ├── Drivers
    │   ├── AudioDXE.efi (disabled)
    │   ├── HfsPlus.efi
    │   ├── OpenCanopy.efi
    │   ├── OpenRuntime.efi
    │   ├── ResetNvramEntry.efi
    │   └── ToggleSipEntry.efi
    ├── Kexts
    │   ├── AirportBrcmFixup.kext
    │   ├── AppleALC.kext
    │   ├── BlueToolFixup.kext
    │   ├── BrcmBluetoothInjector.kext
    │   ├── BrcmFirmwareData.kext
    │   ├── BrcmPatchRAM2.kext
    │   ├── BrcmPatchRAM3.kext
    │   ├── BrightnessKeys.kext
    │   ├── CryptexFixup.kext
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
    │   └── Image
    │       └── Acidanthera
    │       │   ├── Chardonnay
    │       │   ├── GoldenGate
    │       │   └── Syrah
    │       └── Blackosx
    │       │   └── BsxM1
    │       └── velickovicdj
    │       │   └── EnterTwilight
    │       └── Label
    └── config.plist
```
</details>

## INSTALLATION

### Preparing the config.plist
Please read the explanations in the following sections carefully and follow the given instructions. In order to boot macOS with this EFI successfully, adjustments to the `config.plist` and used kexts may be necessary to adapt the config to your  T530 model and the macOS version you want to install/run. 

Open the `config.plist` and adjust the following settings depending on your system:

1. Set your display panel. In `DeviceProperties`, select the correct Framebuffer-Patch for your T530 model. Two types of display panels exist for this model: `HD+` (WSXGA and FullHD) and `HD` panels. Both are using different identifiers:</br>
	
	`AAPL,ig-platform-id 04006601` = `HD+` = FullHD. Resolution: ≥ 1600x900 px. (**Default**)</br>
	`AAPL,ig-platform-id 03006601` = `HD` = SD. Resolution: ≤ 1366x768 px</br>
	
	If your T530 Model uses an `SD` Panel, do the following;
	 
	- Go to `DeviceProperties` 
	- Disable the `PciRoot(0x0)/Pci(0x2,0x0)` by placing `#` in front of it.
	- Enable `#PciRoot(0x0)/Pci(0x2,0x0) 1366x768 px` by deleting the leading `#` and the description ` 1366x768 px`, so that it looks this: `PciRoot(0x0)/Pci(0x2,0x0)`.
	
	:bulb: **HINT**: If your screen turns off during boot, you are using the wrong Framebuffer-Patch!
	
2. **Digital Audio**: If you need digital Audio over HDMI/DP, disable/delete `No-hda-gfx` from the Audio Device `PciRoot(0x0)/Pci(0x1B,0x0)`.

3. Under `NVRAM/Add/7C436110-AB2A-4BBB-A880-FE41995C9F82`, adjust `csr-active-config` according to the macOS version you want to use:
	- For macOS Big Sur to Ventura: `67080000`(0x867)
	- For running Intel HD Patcher in Monterey: `FE0F0000` (0xFEF)
	- For macOS Mojave/Catalina: `EF070000`(0x7EF)
	- For macOSHigh Sierra: `FF030000` (0x3FF)
	
	**NOTE**: Disabling SIP is mandatory if you want to run macOS Monterey or newer in order to install and use patched-in Intel HD 4000 Drivers!

4. Under `SystemProductName`, select the correct SMBIOS for your CPU: 
	-  For Intel i7: `MacBookPro10,1`
	-  For Intel i5: `MacBookPro10,2`
	
	**NOTE**: My config uses the Booter and Kernel Patches from OpenCore Legacy Patcher which allow using the correct SMBIOS for Ivy Bridge CPUs on macOS 11.3 and newer (Darwin Kernel 20.4+) so native Power Management and System Updates are working which wouldn't be possible otherwise past macOS Catalina.

4. **CPU**: The `SSDT-PM.aml` table inside the ACPI Folder is for an **Intel i7 3630QM**. If your T530 has a different CPU model, disable it for now and create your own using `ssdtPRGen` in Post-Install. See [Fixing CPU Power Management](#fixing-cpu-power-Management) for instructions.

5. **WiFi and Bluetooth** (Read carefully!)
	- **Case 1: Intel Wifi/BT Card**. If you have a the stock configuration with an Intel WiFi/Bluetooth card, it may work with the [**OpenIntelWireless**](https://github.com/OpenIntelWireless) kexts. 
		- Check the compatibility list to find out if your card is supported. 
		- Remove `BluetoolFixup` and `Brcm` Kexts
		- Add the required Kexts for your Intel card to `EFI/OC/Kexts` folder and `config.plist` before attempting to boot with this EFI!
	- **Case 2: 3rd Party WiFi/BT Cards**. These require the `1vyrain` Jailbreak to unlock the BIOS to disable the WiFi Whitelist (not required if the 3rd party card is whitelisted).
		- I use a WiFi/BT Card by Broadcom, so my setup requires `AirportBrcmFixup` for WiFi and `BrcmPatchRAM` and additional satellite kexts for Bluetooth. Read the comments in the config for details.
		- `BrcmFirmwareData.kext` is used to inject firmwares of Broadcom devices. Alternatively, you can use `BrcmFirmwareRepo.kext` which is more efficient but needs to be installed into `System/Library/Extensions` since it cannot be injected by Bootloaders.
		- If you use a WiFi/BT Card from a different vendor than Intel or Broadcom, remove BluetoolFixup and the the Brcm Kexts 
		- Add the Kext(s) required for your card to the kext folder and `config.plist` before deploying the EFI folder!

6. **Alternative/Optional Kexts**:
	- [**itlwm**](https://github.com/OpenIntelWireless/itlwm): Kext for Intel WiFi Cards. Use instead of `AirportBrcmFixup`if you don't use a Broadcom WiFi Card
	- [**IntelBluetoothFirmware**](https://github.com/OpenIntelWireless/IntelBluetoothFirmware): Kext for Intel Bluetooth Cards. Use instead of `BrcmPatchRam` and Plugins if you don't use a Broadcom BT Card
	- [**NoTouchID**](https://github.com/al3xtjames/NoTouchID): only required for macOS 10.13 and 10.14 so the boot process won't stall while looking for a Touch ID sensor.
	- [**Feature Unlock**](https://github.com/acidanthera/FeatureUnlock): Unlocks additional features like Sidecar, NighShift, Airplay to Mac, Universal Control and Content Caching. Under macOS Monterey, Content Caching also requires `-allow_assetcache` boot-arg.
	- [**RestictEvents.kext**](https://github.com/acidanthera/RestrictEvents): Combined with boot-arg `revpatch=diskread,memtab`, it enables "Memory" tab in "About this Mac" section and disables "Uninitialized Disk" warning in Finder (for macOS 10.14 and older).

8. **Backlight Brightness Level tweaks** (optional): 
  - Set boot-arg `applbkl=1` for reasonable maximum brightness level controlled by `WhateverGreen`. 
  - Set boot-arg `applbkl=0` for increased maximum brightness as defined in `SSDT-PNLF.aml`

#### About used boot arguments
- `amfi_get_out_of_my_way=1`: Required to be able to install Intel HD 4000 drivers in macOS Ventura using OCLP.
- `brcmfx-country=#a`: Wifi Country Code (`#a` = generic). For details check the documentation for [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup).
- `gfxrst=1`: Draws Apple logo at 2nd boot stage instead of framebuffer copying &rarr; Smoothens transition from the progress bar to the Login Screen/Desktop when an external monitor is attached.
- `#revpatch=diskread,memtab`: For `RestrictEvents.kext` (disabled). `diskread` disables "Uninitialized Disk" warning in macOS 10.14 and older. `memtab` adds `Memory` tab to "About this Mac" section. Enable `RestrictEvents.kext` and remove the `#` from the boot-arg to enable it.

### EFI How To
- Download the EFI Folder from the [Releases](https://github.com/5T33Z0/Lenovo-T530-Hackintosh-OpenCore/releases) section on the right and unpack it
- Open config.plist and follow the instructions given in the "Preparation" Section
- Mount the EFI
- Replace EFI Folder
- Restart
- :warning: Perform a NVRAM Reset (in Bootpicker, hit Space Bar to reveal Tools)
- Select macOS to boot.

### BIOS Settings

<details>
<summary><strong>Click to reveal</strong></summary>

**Latest BIOS Version:** `2.77`
[**DOWNLOAD**](https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-t-series-laptops/thinkpad-t530/downloads/ds029246?clickid=RhAUWZ1-exyLRCuwUx0Mo3ELUkERY-RmHTlwSg0&Program=3786&pid=269814&acid=ww%3Aaffiliate%3A74clty&cid=de%3Aaffiliate%3Axg02ds)

**CONFIG [TAB]**

* USB UEFI BIOS Support: `Enabled`
* USB 3.0 Mode: `Enabled`
* Display > Boot Display Device: `ThinkPad LCD`
* Display > OS Detection for NVIDIA Optimus: `Disabled`
* SATA > SATA Controller Mode: `AHCI`
* CPU > Core Multi-Processing: `Enabled`
* CPU > Intel (R) Hyper-Threading: `Enabled` (CPU must support it)

**SECURITY [TAB]**

* Security Chip: `Disabled`
* UEFI BIOS Update Options > Flash BIOS Updating by End-Users: `Enabled`
* UEFI BIOS Update Options > Secure Rollback Prevention: `Enabled`
* Memory Protection: `Enabled`
* Virtualization > Intel (R) Virtualization Technology: `Enabled` (Relevant for Windows only, can be disabled in macOS via the `DisableIOMapper` Quirk)
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
* Boot Order Lock: `Enabled` Enable this *after* you've set-up the order of the Boot Drives. This prevents `WindowsBootManager` from taking over the first slot of the boot drives. This way, you don't need to enable the `LauncherOption` in OpenCore!
</details>

### Installing macOS
**Coming from Windows/Linux**: If you are on Windows or Linux, follow the guide provided by [Dortania](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/#making-the-installer). **NOTE**: No support from my end for issues related to UBS Installers created in Windows or Linux!

**Coming from macOS**: If you already have access to macOS, you can either download macOS from the App Store or use [**ANYmacOS**](https://www.sl-soft.de/en/anymacos/) instead. It's a hassle-free app than can download any macOS from High Sierra up to Ventura and can create an USB Installer as well.

**macOS Monterey+**: For installing macOS Monterey or newer, follow the Install Instructions included in the EFI Download located in the [**Releases**](https://github.com/5T33Z0/Lenovo-T530-Hackinosh-OpenCore/releases) section.
</details>

## POST-INSTALL
Once your system is up and running you may want to change the following settings to make your system more secure:

- Enable System Integrity Protection (SIP): change `csr-active-config` to `00000000` (SIP needs to stay disabled in macOS 12+)
- Under `UEFI/APFS`, change `MinDate` and `MinVersion` from `-1` (disabled) to the correct values for the macOS version you are using. A list with the correct values for can be found [here](https://github.com/5T33Z0/OC-Little-Translated/tree/main/A_Config_Tips_and_Tricks#mindateminversion-settings-for-the-apfs-driver).

**IMPORTANT**: 

- **SIP**: If you're planning to install macOS Monterey, SIP needs to be disabled! Because installing the graphics drivers with Intel HD 4000 Patcher breaks macOS securiity seal so therefore boot will crash if SIP is enabled!
- **MinDate/MinVersion**: you should keep a working backup of your EFI folder on a FAT32 formatted USB flash drive before changing these settings, because if they are wrong, the APFS driver won't load and you won't see your macOS drive(s)!

### Fixing CPU Power Management 
1. Open your `config.plist`
2. In `ACPI/Add`, disable `SSDT-PM`
3. In `ACPI/Delete`, enable the 2 patches `Drop CpuPm` and `Drop Cpu0Ist`
4. Save the config and reboot
5. Open Terminal
6. Enter the following command to download the ssdtPRGen Script: `curl -o ~/ssdtPRGen.sh https://raw.githubusercontent.com/Piker-Alpha/ssdtPRGen.sh/Beta/ssdtPRGen.sh`
7. To make the scrip executable, enter: `chmod +x ~/ssdtPRGen.sh`
8. Run the script: `sudo ~/ssdtPRGen.sh`
9. The generated `SSDT.aml` will be located under `/Users/YOURUSERNAME/Library/ssdtPRGen`
10. Rename `ssdt.aml` to `SSDT-PM.aml` and copy it
11. Paste it into `EFI/OC/ACPI`, replacing the existing file
12. In config, go to `ACPI/Add` and re-enable `SSDT-PM.aml` if it is disabled
13. Disable the two patches from step 3 again
14. Save config and reboot

CPU Power Management should work fine after that. Optionally, you can install [Intel Power Gadget](https://www.intel.com/content/www/us/en/developer/articles/tool/power-gadget.html) to check if the CPU runs within specs. You don't need SMCProcessor and SMCSuperIO kexts to monitor the CPU if you use Intel Power Gadget, btw.

**NOTES**: 

- Only necessary if you use a different CPU than i7 3630QM
- CPU Power Management doesn't work correctly in Ventura (no Turbo states). Probably because the legacy `ACPI_SMC_PlatformPlugin` has been dropped. Needs further investigation.
- You can add modifiers to the terminal command for building SSDT-PM. For example, you can drop the low frequency from the default 1200 MHz to 900 MHz in 100 MHz increments, but no lower than that. Otherwise the system crashes during boot. I suggests you experiment with the modifiers a bit.
- If you feel really confident and enthusiastic you could also re-enable XCPM. But in my experience the machine does not perform as good. You can [follow this guide](https://github.com/5T33Z0/Lenovo-T530-Hackinosh-OpenCore/tree/main/Enable%20XCPM) if you're so inclined.<br>

### Fixing Sleep issues
If you have issues with sleep, run the following commands in Terminal:

```
sudo pmset hibernatemode 0
sudo rm /var/vm/sleepimage
sudo touch /var/vm/sleepimage
sudo chflags uchg /var/vm/sleepimage
```

**Other Settings**:

If the system still wakes from sleep on its own, check the wake reason. Enter:

```
pmset -g log | grep -e "Sleep.*due to" -e "Wake.*due to"
```

If the wake reason is related to `RTC (Alarm)`, do the following:

- Enter System Settings
- Open Energy Settings
- Disable "Wake on LAN"
- Disable "Power Nap"
- In Bluetooth Settings, "Advanced Options…" disable the 3rd entry about allowing Bluetooth device to exit sleep

**NOTES**
- In my tests, fixing the sleepimage actually prohibited the machine from entering sleep on its own. You can use Hackintool to revert the settings.
- To exit from Sleep you can press a Mouse button. But to wake from Hibernation, you have to press the `Fn` key or the `Power Button`.

### Reducing boot time
- In `UEFI/Drivers`, disable `ConnectDrivers`. This reduces the timeout between the LENOVO logo and the BootPicker by 5 to 8 seconds.

:warning: **CAUTION**: 
- With `ConnectDrivers` disabled, the boot chime cannot be played back since the `AudioDXE.efi` is not loaded. 
- Before installing macOS from a USB flash drive, `ConnectDrivers` needs to be re-enabled, otherwise you won't see the flash drive in the bootpicker.

### Swapping Command ⌘ and Option ⌥ Keys
Prior to version 0.7.4 of my OpenCore EFI Folder, the **[Command]** and **[Option]** keys were set to "swapped" in the `info.plist` of `VoodooPS2Keyboard.kext` by default. So in macOS, the **[WINDOWS]** key was bound to the **[Option]** function and the **[ALT]** Key was bound to the **[Command]** function which felt weird. Therefore, users had to swap these Keys back around in the System Settings so everything worked as expected.

Since then, I've undone the key swap inside the `VoodooPS2Keyboard.kext` plugin so that the Key bindings are working as expected out of the box. So if you are updating from 0.7.3 or lower to 0.7.4, reset the Keyboard Modifier Keys back to Default in System Settings > Keyboard to so everything is back to normal.

If the "<", ">" and "^" Keys are switched/reversed, change `Use ISO layout keyboard` from `false` to `true` in the `info.plist` of `VoodooPS2Keyboard.kext`. 

### Changing Themes
Besides the 3 default themes by Acidanthera included in the OpenCore package, I've added an additional theme by Blackosx called BsxM1 which is set as default. To change the theme to something else, do the following: 

- Open `config.plist` in OpenCore Auxiliary Tools
- Go to `Misc/Boot` 
- Select a different theme from the dropdown menu in `PickerVariant`
- Save `config.plist` and reboot to apply the theme.

To revert these changes, enter `Acidanthera\GoldenGate` as `PickerVariant` and change the Flavor for the NVRAM Reset Tool back to `Auto`.

### Eject Button 
macOS locks the optical drive sometimes so that you can't open it with the physical eject button – even if no media is present. To fix this you have 2 options:

- **Option 1**: Go to `System/Library/CoreServices/Menu Extras` and double-click on `Eject.menu`. This adds an Eject button Icon to the Menu Bar.
- **Option 2**: Press and hold the `INS` button (right below the Power Button) until the Eject Icon appears on the screen and the CD tray opens.
</details>

### Workaround for Apple Music crashing in macOS Catalina
I've noticed that Apple Music crashes when running the latest version of macOS Catalina (10.15.7) – it's working fine in Big Sur and newer, though. I am sure this is not a config issue, because: a) all other apps work as expected, b) since I know Apple Music was working on earlier builds of Catalina and c) I've seen unresolved issue reports on Apple Support Forums. 

As a workaround, you can use [Retroactive](https://github.com/cormiertyshawn895/Retroactive) to install and run older versions of iTunes (11.4 worked for me).

## CPU BENCHMARK

![Screenshot](https://raw.githubusercontent.com/5T33Z0/Lenovo-T530-Hackinosh-OpenCore/main/Pics/benchmark_latest.png)</br>[Benchmark Results](https://browser.geekbench.com/v5/cpu/9553877)

## CREDITS and THANK YOUs

- George Kushnir for [1vyrain Jailbreak](https://github.com/n4ru/1vyrain) to remove WLAN whitelist from BIOS
- Acidanthera and Team for [OpenCore Bootloader](https://github.com/acidanthera/OpenCorePkg), OCLP and additional Kexts
- Dortania for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide)
- SergeySlice for [Clover Bootloader](https://github.com/CloverHackyColor/CloverBootloader)
- Chris1111 for [Patch-HD4000-Monterey](https://github.com/chris1111/Patch-HD4000-Monterey)
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
