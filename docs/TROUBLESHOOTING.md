# General troubleshooting

Here are some common errors that users may experience while using this patcher:

* [OpenCore Legacy Patcher not launching](#opencore-legacy-patcher-not-launching)
* ["You don't have permission to save..." error when creating USB installer](#you-don-t-have-permission-to-save-error-when-creating-usb-installer)
* [Stuck on `This version of Mac OS X is not supported on this platform` or (🚫) Prohibited Symbol](#stuck-on-this-version-of-mac-os-x-is-not-supported-on-this-platform-or-🚫-prohibited-symbol)
* [Stuck on hard disk selection with greyed out buttons in installer](#stuck-on-hard-disk-selection-with-greyed-out-buttons-in-installer)
* [Cannot boot macOS without the USB](#cannot-boot-macos-without-the-usb)
* [Infinite Recovery OS Booting](#infinite-recovery-os-booting)
* [Internal disk missing when building OpenCore](#internal-disk-missing-when-building-opencore)
* [System version mismatch error when root patching](#system-version-mismatch-error-when-root-patching)
* [Stuck on boot after root patching](#stuck-on-boot-after-root-patching)
* ["Unable to resolve dependencies, error code 71" when root patching](#unable-to-resolve-dependencies-error-code-71-when-root-patching)
* [Reboot when entering Hibernation (`Sleep Wake Failure`)](#reboot-when-entering-hibernation-sleep-wake-failure)
* [Installer fails with "an error occurred preparing the software update"](#installer-fails-with-an-error-occurred-preparing-the-software-update)
* [How to Boot Recovery through OpenCore Legacy Patcher](#how-to-boot-recovery-through-opencore-legacy-patcher)
* [Stuck on "Your Mac needs a firmware update"](#stuck-on-your-mac-needs-a-firmware-update)
* [No Brightness Control](#no-brightness-control)
* [Cannot connect Wi-Fi on Monterey with legacy cards](#cannot-connect-wi-fi-on-monterey-with-legacy-cards)
* [No Graphics Acceleration](#no-graphics-acceleration)
* [Black Screen on MacBookPro11,3 in macOS Monterey](#black-screen-on-macbookpro11-3-in-macos-monterey)
* [No DisplayPort Output on Mac Pros with NVIDIA Kepler](#no-displayport-output-on-mac-pros-with-nvidia-kepler)
* [Volume Hash Mismatch Error in macOS Monterey](#volume-hash-mismatch-error-in-macos-monterey)
* [Cannot Disable SIP in recoveryOS](#cannot-disable-sip-in-recoveryos)
* [Stuck on "Less than a minute remaining..."](#stuck-on-less-than-a-minute-remaining)
* [Secondary CPU not visible on MacPro3,1/Xserve2,1](#secondary-cpu-not-visible-on-macpro3-1-xserve2-1)
* [No acceleration after a Metal GPU swap on Mac Pro](#no-acceleration-after-a-metal-gpu-swap-on-mac-pro)
* [Keyboard, Mouse and Trackpad not working in installer or after update](#keyboard-mouse-and-trackpad-not-working-in-installer-or-after-update)
* [No T1 functionality after installing Sonoma or newer](#no-t1-functionality-after-installing-sonoma-or-newer)


## OpenCore Legacy Patcher not launching

If the application won't launch (e.g. icon will bounce in the Dock), try launching OCLP via Terminal by typing the following command.

```sh
/Library/Application Support/Dortania/OpenCore-Patcher.app/Contents/MacOS/OpenCore-Patcher
```

## "You don't have permission to save..." error when creating USB installer

In some cases, a following error saying "The bless of the installer disk failed" stating the reason as "You don't have permission to save..." may appear. 


<div align="left">
             <img src="./images/Error-No-Permission-To-Save.png" alt="NoPermissionToSave" width="600" />
</div>


To resolve this, you may try adding Full Disk Access permission for OpenCore Legacy Patcher. To add it, first go to the settings

* Ventura and Sonoma: Go to System Settings -> Privacy and Security -> Full Disk Access

* Big Sur and Monterey: Go to System Preferences -> Security and Privacy -> Full Disk Access

Enable OpenCore-Patcher in the list. If not found on the list, press the + sign to add a new entity and find OpenCore Legacy Patcher from Applications.

Restart OpenCore Legacy Patcher and try creating your USB drive again.

Optional: After you've created your USB drive, you can remove OpenCore Legacy Patcher from Full Disk Access again.


## Stuck on `This version of Mac OS X is not supported on this platform` or (🚫) Prohibited Symbol

This means macOS has detected an SMBIOS it does not support. To resolve this, ensure you're booting OpenCore **before** the macOS installer in the boot picker. Reminder that the option will be called `EFI Boot`.

Once you've booted OpenCore at least once, your hardware should now auto-boot it until either an NVRAM reset occurs, or you remove the drive with OpenCore installed.

However, if the 🚫 Symbol only appears after the boot process has already started (the bootscreen appears/verbose boot starts), it could mean that your USB drive has failed to pass macOS' integrity checks. To resolve this, create a new installer using a different USB drive (preferably of a different model.)

## Stuck on hard disk selection with greyed out buttons in installer

Switch installer language to English. If the language selector doesn't show up, [reset NVRAM](https://support.apple.com/en-mide/102603) and boot into the installer again.

You can switch back to different language once macOS has installed.

## Cannot boot macOS without the USB

By default, the OpenCore Patcher won't install OpenCore onto the internal drive itself during installs.

After installing macOS, OpenCore Legacy Patcher should automatically prompt you to install OpenCore onto the internal drive. However, if it doesn't show the prompt, you'll need to either [manually transfer](https://dortania.github.io/OpenCore-Post-Install/universal/oc2hdd.html) OpenCore to the internal drive's EFI or Build and Install again and select your internal drive.

Reminder that once this is done, you'll need to select OpenCore in the boot picker again for your hardware to remember this entry and auto boot from then on.

## Infinite Recovery OS Booting

With OpenCore Legacy Patcher, we rely on Apple Secure Boot to ensure OS updates work correctly and reliably with Big Sur. However this installs NVRAM variables that will confuse your Mac if not running with OpenCore. To resolve this, simply uninstall OpenCore and [reset NVRAM](https://support.apple.com/en-mide/HT201255).

* Note: Machines with modified root volumes will also result in an infinite recovery loop until integrity is restored.

## Internal disk missing when building OpenCore

If you're using a brand new disk that has not been used before or was never formatted in any macOS type, you may face the following error in OCLP when trying to build on the internal disk.

<div align="left">
             <img src="./images/OCLP_Failed_to_find_applicable_disks.png" alt="Failed to find applicable disks" width="600" />
</div>

There are two ways to to try and resolve this.

1. Create a new FAT32 partition using Disk Utility, sized at around 100MB, naming does not matter. OpenCore will detect it and you will be able to build your EFI there.

2. When installing macOS, choose "View -> Show all devices" in Disk Utility and format the entire disk by choosing the topmost option in the sidebar.

<div align="left">
             <img src="./images/wipe-disk.png" alt="Wipe disk" width="800" />
</div>


## System version mismatch error when root patching

Due to a change by Apple, updates now modify the system volume **already while downloading**, which can lead to broken patches out of a sudden, since the operating system gets into a liminal state between two versions. Hence while root patching, you may get an error that looks like the following: 

`SystemVersion.plist build version mismatch: found 15.4 (24E247), expected 13.7.5 (22H527)`

In this example, it is telling that a version 13.7.5 (Ventura) is expected which is currently running but macOS has already staged an update to version 15.4 (Sequoia) and has already modified the filesystem to prepare for an update, including writing the new version in SystemVersion.plist where OCLP is able to read it from. The "version mismatch" error is a safeguard preventing OCLP from patching on a system that is in a weird liminal state, to avoid leading to a very likely boot failure.

There are few options to resolve it:

1. Update/upgrade to the version already staged.
2. Reinstall macOS.
   * You can try doing an in-place install without wiping the disk to keep your data but this may not be possible due to the OS being partially on newer version and it will complain about downgrade.
4. Use an experimental "PurgePendingUpdate" tool [from the Discord server](https://discord.com/channels/417165963327176704/1253268648324235345/1257348959454625985).
   * Download it and then run it in Terminal to get rid of a pending update, then repatch again. If "purge failed" appears, you can ignore it.
   * Sometimes you may have to give the file an execution permission with `chmod +x` and putting the application path after the command (type or drag the file) before the tool can be ran.
   * This may be integrated into OCLP later on, however there is currently no ETA.

**Disabling automatic macOS updates is extremely recommended once recovered, to prevent it from happening again.**

* Note: macOS Sequoia has begun prompting to enable automatic updates from 15.4 onward after an update install has finished and isn't giving a choice to fully decline, this means you may have to keep doing it again after updating to newer versions.

::: details How to disable updates (click to expand)

**macOS Ventura and newer:**

System Settings -> General -> Software Update -> (i) button next to Automatic Updates -> Disable "Download new updates when available".

**macOS Big Sur and Monterey:**

System Preferences -> Software Update -> Advanced -> Disable "Download new updates when available".

:::

## Stuck on boot after root patching

**Applies to macOS Monterey and newer. Big Sur does not support snapshot reversion.**

Boot into recovery by pressing space when your disk is selected on the OCLP bootpicker (if you have it hidden, hold ESC while starting up)

* **Note:** If your disk name is something else than "Macintosh HD", make sure to change the path accordingly. You can figure out your disk name by typing `ls /Volumes`.

Go into terminal and first mount the disk by typing
```sh
mount -uw "/Volumes/Macintosh HD"
```
Then revert the snapshot
```sh
bless --mount "/Volumes/Macintosh HD" --bootefi --last-sealed-snapshot
```
Now we're going to clean the /Library/Extensions folder from offending kexts while keeping needed ones.

Run the following and **make sure to type it carefully**

::: warning
If you have **FileVault 2 enabled**, you will need to mount the Data volume first. This can be done in Disk Utility by locating your macOS volume name, selecting its Data volume, and selecting the Mount option in the toolbar.
:::

```sh
cd "/Volumes/Macintosh HD - Data/Library/Extensions" && ls | grep -v "HighPoint*\|SoftRAID*" | xargs rm -rf
```

Then restart and now your system should be restored to the unpatched snapshot and should be able to boot again.

## "Unable to resolve dependencies, error code 71" when root patching

If you're getting this error, it typically means you have some offending kernel extensions, to fix this you will have to clear them.

Semi-automated way:

1. Open Terminal
2. Type `sudo zsh`
3. Type `cd "/Volumes/Macintosh HD/Library/Extensions" && ls | grep -v "HighPoint*\|SoftRAID*" | xargs rm -rf`
   * Make sure to rename "Macintosh HD" to what your drive name is
4. Run OCLP root patcher again
   
Manual way:

1. Navigate to /Library/Extensions
2. Delete everything **except** HighPointIOP.kext, HighPointRR.kext and SoftRAID.kext
3. Run OCLP root patcher again

If there is no success, navigate to "/Library/Developer/KDKs" and delete everything.

If still no success, type `sudo bless --mount "/Volumes/Macintosh HD/" --bootefi --last-sealed-snapshot` 
* Make sure again to rename "Macintosh HD" to what your drive name is

Run OCLP root patcher again.

## Reboot when entering Hibernation (`Sleep Wake Failure`)

[Known issue on some models](https://github.com/dortania/Opencore-Legacy-Patcher/issues/72), a temporary fix is to disable Hibernation by executing the following command in the terminal:

```
sudo pmset -a hibernatemode 0
```

## Installer fails with "an error occurred preparing the software update"

This issue can be faced in the second phase of the installer with black background and Apple logo, cause of this issue is unknown. To possibly resolve this issue, keep rebooting into 'macOS Installer' (the second phase) multiple times until it ultimately goes through.

## How to Boot Recovery through OpenCore Legacy Patcher

By default, the patcher will try to hide extra boot options such as recovery from the user. To make them appear, simply press the `Spacebar` key while inside OpenCore's Picker to list all boot options.

## Stuck on "Your Mac needs a firmware update"

Full error: "Your Mac needs a firmware update in order to install to this Volume. Please select a Mac OS Extended (Journaled) volume instead."

This error occurs when macOS determines that the current firmware does not have full APFS support. To resolve this, when installing OpenCore, head to "Patcher Settings" and enable "Moderate SMBIOS Patching" or higher. This will ensure that the firmware reported will show support for full APFS capabilities.

## No Brightness Control

With OCLP v0.0.22, we've added support for brightness control on many models. However, some users may have noticed that their brightness keys do not work.

As a work-around, we recommend users try out the below app:

* [Brightness Slider](https://actproductions.net/free-apps/brightness-slider/)

## Cannot connect Wi-Fi on Monterey with legacy cards

With OCLP v0.2.5, we've added support for legacy Wi-Fi on Monterey. However, some users may have noticed that they can't connect to wireless networks.

To work-around this, we recommend that users manually connect using the "Other" option in the Wi-Fi menu bar or manually adding the network in the "Network" preference pane.

## No Graphics Acceleration

In macOS, GPU drivers are often dropped from the OS with each major release of it. With macOS Big Sur, currently, all non-Metal GPUs require additional patches to gain acceleration. In addition, macOS Monterey removed Graphics Drivers for both Intel Ivy Bridge and NVIDIA Kepler graphics processors.

If you're using OCLP v0.4.4, you should have been prompted to install Root Volume patches after the first boot from installation of macOS. If you need to do this manually, you can do so within the patcher app. Once rebooted, acceleration will be re-enabled as well as brightness control for laptops.

## Black Screen on MacBookPro11,3 in macOS Monterey

Due to Apple dropping NVIDIA Kepler support in macOS Monterey, [MacBookPro11,3's GMUX has difficulties switching back to the iGPU to display macOS correctly.](https://github.com/dortania/OpenCore-Legacy-Patcher/issues/522) To work-around this issue, boot the MacBookPro11,3 in Safe Mode and once macOS is installed, run OCLP's Post Install Root Patches to enable GPU Acceleration for the NVIDIA dGPU.

* Safe Mode can be started by holding `Shift` + `Enter` when selecting macOS Monterey in OCLP's Boot Menu.

## No DisplayPort Output on Mac Pros with NVIDIA Kepler

If you're having trouble with DisplayPort output on Mac Pros, try enabling Minimal Spoofing in Settings -> SMBIOS Settings and rebuild/install OpenCore. This will trick macOS drivers into thinking you have a newer MacPro7,1 and resolve the issue.


<div align="left">
             <img src="./images/OCLP-GUI-SMBIOS-Minimal.png" alt="GUI SMBIOS minimal" width="800" />
</div>        


## Volume Hash Mismatch Error in macOS Monterey

A semi-common popup some users face is the "Volume Hash Mismatch" error:

<p align="left">
<img src="./images/Hash-Mismatch.png">
</p>

What this error signifies is that the OS detects that the boot volume's hash does not match what the OS is expecting, this error is generally cosmetic and can be ignored. However if your system starts to crash spontaneously shortly after, you'll want to reinstall macOS fresh without importing any data at first.

* Note that this bug affects native Macs as well and is not due to issues with unsupported Macs: [OSX Daily: “Volume Hash Mismatch” Error in MacOS Monterey](https://osxdaily.com/2021/11/10/volume-hash-mismatch-error-in-macos-monterey/)

Additionally, it can help to disable FeatureUnlock in Settings -> Misc Settings as this tool can be strenuous on systems with weaker memory stability.

## Cannot Disable SIP in recoveryOS

With OCLP, the patcher will always overwrite the current SIP value on boot to ensure that users don't brick an installation after an NVRAM reset. However, for users wanting to disable SIP entirely, this can be done easily.

Head into the GUI, go to Patcher Settings, and toggle the bits you need disabled from SIP:

| SIP Enabled | SIP Lowered (Root Patching) | SIP Disabled |
| :--- | :--- | :--- |
| ![](./images/OCLP-GUI-Settings-SIP-Enabled.png) | ![](./images/OCLP-GUI-Settings-SIP-Root-Patch.png) | ![](./images/OCLP-GUI-Settings-SIP-Disabled.png) |

## Intermediate issues with USB 1.1 and Bluetooth on MacPro3,1 - MacPro5,1

For those experiencing issues with USB 1.1 devices (such as mice, keyboards and bluetooth chipsets), macOS Big Sur and newer have weakened OS-side reliability for the UHCI controller in older Mac Pros.

* UHCI is a USB 1.1 controller that is hooked together with the USB 2.0 ports in your system. Whenever a USB 1.1 device is detected, the UHCI controller is given ownership of the device at a hardware/firmware level.
  * EHCI is the USB 2.0 controller in older Mac Pros

Because of this, we recommend placing a USB 2.0/3.0 hub between your devices and the port on the Mac Pro. UHCI and EHCI cannot both be used at once, so using a USB hub will always force the EHCI controller on.

* Alternatively, you can try cold-starting the hardware and see if macOS recognizes the UHCI controller properly.

## Stuck on "Less than a minute remaining..."

A common area for systems to get "stuck", namely for units that are missing the `AES` CPU instruction/older mobile hardware. During this stage, a lot of heavy cryptography is performed, which can make systems appear to be stuck. In reality they are working quite hard to finish up the installation.

Because this step can take a few hours or more depending on drive speeds, be patient at this stage and do not manually power off or reboot your machine as this will break the installation and require you to reinstall. If you think your system has stalled, press the Caps Lock key. If the light turns on, your system is busy and not actually frozen.

## Secondary CPU not visible on MacPro3,1/Xserve2,1

Starting with macOS Sequoia, OCLP has to disable the secondary CPU in these systems to avoid a panic. This also means by default, only single CPU will be usable even on older versions. To re-enable both CPUs on older versions, do the following: 

1. Open Settings -> `Build` tab.
2. Untick `MacPro3,1/Xserve2,1 Workaround`.
3. Rebuild OpenCore.
4. Reboot.

**Dual CPUs cannot be enabled in any circumstance if Sequoia or newer is installed, even in multiboot scenarios. Doing so will make Sequoia unbootable.**

[More information here](https://github.com/dortania/OpenCore-Legacy-Patcher/issues/1136#:~:text=we%20cannot%20verify.-,MacPro3%2C1%20Dual%20Socket%20Support,-Currently%20on%20macOS)

## No acceleration after a Metal GPU swap on Mac Pro

If you finished installing macOS with the original card installed (to see bootpicker for example) and swapped your GPU to a Metal supported one, you may notice that you're missing acceleration. To fix this, open OCLP and revert root patches to get your Metal-supported GPU work again. In macOS Ventura and newer, repatching is needed after reversion.

Alternatively, you can remove "AutoPkg-Assets.pkg" from /Library/Packages on the USB drive before proceeding with the installation. To see the folder, enable hidden files with `Command` + `Shift` + `.`

The reason for this is that the autopatcher will assume that you will be using the original graphics card and therefore does non-metal patching, which includes removing some drivers for other cards. This causes Metal cards to not accelerate after swapping.

## Keyboard, Mouse and Trackpad not working in installer or after update

Starting from macOS Ventura, USB 1.1 drivers are no longer provided in the operating system. For Macs using legacy USB 1.1 controllers, OpenCore Legacy Patcher can only restore support once it has performed root volume patches which restore the drivers. Thus when installing macOS or after an update, you need to hook up a USB hub between your Mac and keyboard/mouse, forcing USB 2.0 mode in order to install the root patches.

* For MacBook users, you'll need to find an external keyboard/mouse in addition to the USB hub

Applicable models include:

| Family      | Year                 | Model                         | Notes                                            |
| :---------- | :--------------------| :---------------------------- | :----------------------------------------------- |
| MacBook     | Mid 2010 and older   | MacBook5,1 - MacBook7,1       |                                                  |
| MacBook Air | Late 2010 and older  | MacBookAir2,1 - MacBookAir3,x |                                                  |
| MacBook Pro | Mid 2010 and older   | MacBookPro4,1 - MacBookPro7,x | Excludes Mid 2010 15" and 17" (MacBookPro6,x)    |
| iMac        | Late 2009 and older  | iMac7,1 - iMac10,x            | Excludes Core i5/7 27" late 2009 iMac (iMac11,1) |
| Mac mini    | Mid 2011 and older   | Macmini3,1 - Macmini5,x       |                                                  |
| Mac Pro     | Mid 2010 and older   | MacPro3,1 - MacPro5,1         |                                                  |


<div align="left">
             <img src="./images/usb11-chart.png" alt="USB1.1 chart" width="800" />
</div>

::: warning Note

In macOS Sonoma, this seems to have been further weakened and some hubs may not be functional. If you encounter this issue, try another hub.

:::

### Alternative method for Software Update

Alternative way for updates is making sure to enable "Remote Login" in General -> Sharing before updating, which will enable SSH. That means you can take control using Terminal in another system and run Post Install Volume Patching. 

**This only applies to updates via Software Update and is not applicable when booting to installer via USB drive.**

Use the following commands:

1. `ssh username@lan-ip-address` - Connects via SSH, change username and IP address to the system's
2. `/Applications/OpenCore-Patcher.app/Contents/MacOS/OpenCore-Patcher --patch_sys_vol` - Installs root patches via CLI
3. `sudo reboot`.

More information can be found here:

* [Legacy UHCI/OHCI support in Ventura #1021](https://github.com/dortania/OpenCore-Legacy-Patcher/issues/1021)


## No T1 functionality after installing Sonoma or newer

If you notice your Touchbar etc not working, this means loss of T1 functionality. 

Wiping the entire disk using Disk Utility with Sonoma or newer causes the T1 firmware to be removed, which due to removed support, the macOS Sonoma+ installer will not restore. If the firmware is missing from EFI, T1 will not work regardless whether OCLP reinstates the driver during root patching. To restore T1 functionality, Ventura or older has to be reinstalled. This can be done in another volume or external disk as well, as long as the OS is booted once. After this you can wipe the old OS or unplug the external disk.


To prevent this from happening in the future, with T1 systems only wipe the volume containing the operating system.

<div align="left">
             <img src="./images/wipe-volume.png" alt="WipeVolume" width="800" />
</div>

