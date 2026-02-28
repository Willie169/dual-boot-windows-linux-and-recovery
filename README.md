# dual-boot-windows-linux-and-recovery

Instructions about dual-booting Windows (assumed Windows 11, may apply to Windows 10 with few changes) and Linux, recovery of Windows and Linux, and related stuff.

## Table of Contents

* [Make Bootable Linux USB and Install Linux](#make-bootable-linux-usb-and-install-linux)
* [GRUB](#grub)
* [Shrink Windows Drive Volume](#shrink-windows-drive-volume)
* [Increase Shrinkable Volume on Windows](#increase-shrinkable-volume-on-windows)
* [Intel Rapid Storage Technology (RST) VMD SSD Driver for Windows Reinstallation or Recovery](#intel-rapid-storage-technology-rst-vmd-ssd-driver-for-windows-reinstallation-or-recovery)
* [Clean Windows Reinstallation with USB](#clean-windows-reinstallation-with-usb)
* [TestDisk Partition Table Recovery](#testdisk-partition-table-recovery)
* [Windows Bootloader Recovery](#windows-bootloader-recovery)
* [Time Mismatches](#time-mismatches)
* [My Related Repositories](#my-related-repositories)

## Make Bootable Linux USB and Install Linux

### 0. Requirements

- Internet connection.
- A USB flash drive no less than typically 8 GB. **Any content on it will be deleted during the process**.
- If you want to install Linux (instead of just trying it), disk partition(s) on PC you want to install Linux on. **Any content on them will be deleted during the process**.

### 1. Download ISO

Download ISO file of the Linux distribution you want.

### 2. Flash OS to Flash Drive (Balena Etcher for example)

Download a software that can flash OS to flash drive. Take Balena Etcher for example.

#### 1. Download

Download **balena-etcher_*_amd64.deb** from the lastest release of <https://github.com/balena-io/etcher>.

#### 2. Installation

```
sudo apt install ./balena-etcher_*_amd64.deb -y
rm balena-etcher_*_amd64.deb
```

#### 3. Open Balena Etcher

```
balena-etcher
```

#### 4. Flash

1. Plug in a USB flash drive. **Any content on it will be deleted**.
2. Select the file and drive you want.
3. Flash.

### 3. Try the Linux Distribution

1. Reboot your computer.
2. Boot into the Linux bootable USB drive.
3. You can try the Linux distribution now.

### 4. Install the Linux Distribution

Notes:

1. It is recommended (or required for some distributions and installation options) to connect to Wi-Fi.
2. It is recommended (or required for some distributions and installation options) to disable Fast Startup (if any) in UEFI before installation.

Steps:

1. Reboot your computer.
2. Boot into the Linux bootable USB drive.
3. You can install the Linux distribution now.

### 5. If Stuck at `Remove the installation medium and press ENTER`

1. Force restarting the computer.
2. Choose `Enroll MOK` and follow the prompts. Set a password if prompted.
3. Reboot and enter the password in the blue MOK manager screen. If not prompted to set a password in the last step but prompted to enter a password here, use your `Secure Boot` password (which may be set in the installation process).

### 6. If Stuck Somewhere during Booting

1. Force restarting the computer.
2. If still stuck or anything is broken, reinstall with the bootable USB.

## GRUB

### Dual Booting Linux and Windows

If Windows is not detected in GRUB menu, run:
```
sudo update-grub
```
in the dual-booting Linux system.

It is better to:
- Disabe **fast boot** and **secure boot** in BIOS menu. You can often access this menu by
  - pressing a key while your PC is booting, such as F1, F2, F12, or Esc, or,
  - for GRUB, selecting `UEFI firmware settings` option in GRUB menu, or,
  - for Windows Boot Manager, holding the Shift key while selecting `Restart` and then going to `Troubleshoot` > `Advanced Options: UEFI Firmware Settings`.
- If `/etc/grub.d/30_os_prober` or `/etc/default/grub.d/30_os_prober` exists, add or edit the line to `quick_boot="0"`.
- Do not add the line `GRUB_DISABLE_OS_PROBER=true` in any GRUB configuration files if you want GRUB to detect Windows.

### GRUB Menu

- When `GRUB_TIMEOUT_STYLE=menu`, after `GRUB_TIMEOUT`, highlighted option will be booted.
- When `GRUB_TIMEOUT_STYLE=hidden` or `GRUB_TIMEOUT_STYLE=countdown`, during `GRUB_HIDDEN_TIMEOUT`, press `ESC` to enter GRUB menu.
- In menu, default highlighted option is the default option. 

### GRUB Configuration

You can edit GRUB configuration by`sudo vim /etc/default/grub`. Replace vim with the text editor you want to use, such as `nano`.

You can apply changes in `/etc/default/grub`, `/etc/grub.d/`, and `/etc/default/grub.d/` by running:
```
sudo update-grub
sudo reboot
```

Variables:

- `GRUB_DEFAULT=<number>`: Default boot option to boot. Options and their numbers are showed in GRUB menu.
- `GRUB_TIMEOUT_STYLE=<string>`: GRUB timeout style when booting.
  - `menu`: Show menu, wait until `GRUB_TIMEOUT` ends, and boot default option.
  - `hidden`: Hide menu with black screen, wait until `GRUB_HIDDEN_TIMEOUT` ends, and boot highlighted option. Show menu when `ESC` is pressed during `GRUB_HIDDEN_TIMEOUT`.
  - `countdown`: Hide menu with countdown shown on screen, wait until `GRUB_HIDDEN_TIMEOUT` ends, and boot default option. Show menu when `ESC` is pressed during `GRUB_HIDDEN_TIMEOUT`.
- `GRUB_TIMEOUT=<number>`: When `GRUB_TIMEOUT_STYLE=menu`, the timeout before booting into highlighted option, `-1` for forever. Some versions may need `0.0` for `0`.
- `GRUB_HIDDEN_TIMEOUT=<number>`: When `GRUB_TIMEOUT_STYLE=hidden` or `GRUB_TIMEOUT_STYLE=countdown`, the timeout before booting into the default option. Some versions may need `0.0` for `0`.
- `GRUB_HIDDEN_TIMEOUT_QUIET=<boolean>`: DEPRECATED. `GRUB_TIMEOUT_STYLE=hidden` and `GRUB_HIDDEN_TIMEOUT_QUIET=false` is equivalent to `GRUB_TIMEOUT_STYLE=countdown`; `GRUB_TIMEOUT_STYLE=hidden` and `GRUB_HIDDEN_TIMEOUT_QUIET=true` is equivalent to `GRUB_TIMEOUT_STYLE=hidden`. There's a known bug that make this not work as expected in some versions after this is deprecated.
- `GRUB_DISABLE_OS_PROBER=<boolean>`: Whether to disable OS prober. Set it to `false` when dual booting.

## Shrink Windows Drive Volume

1. Press `Win + R`.
2. Type `diskmgmt.msc` and press `Enter` to open `Disk management`.
3. Right click `Windows (C:)` drive and click `Shrink Volume`. If you encounter any error, follow the following steps. Otherwise, go to step 11 directly.
4. Press `Win`, type `cmd`, click `run as administrator`, and click `Yes`.
5. Type `chkdsk C: /f` and press `Enter`. It will say: `> Chkdsk cannot run because the volume is in use... Schedule this volume to be checked the next time the system restarts? (Y/N)`.
6. Type `Y`, press `Enter`.
7. Reboot your computer.
8. Press `Win + R`.
9. Type `diskmgmt.msc` and press `Enter` to open `Disk management`.
10. Right click `Windows (C:)` drive and click `Shrink Volume`.
11. Enter the amount of volume to shrink in MB. You may see `You cannot shrink a volume beyond the point where any unmovable files are located. See the “defrag” event in the Application log for detailed information about the operation when it has completed.` Refer to [Increase Shrinkable Volume on Windows](#increase-shrinkable-volume-on-windows) for how to increase shrinkable volume.
12. Click Shrink.
13. You'll now see `Unallocated` space (in black) on the disk. Do not format that space from Windows and just leave it unallocated if you're going to install Linux in it.

## Increase Shrinkable Volume on Windows

Restarting the computer after using the methods below may further increase shrinkable volumes on Windows.

You may turn them on back after shrinking volume.

### Turn off Fast Startup and Hibernation

1. Press `Win`, type `cmd`, click `run as administrator` under **Command Prompt**, and click `Yes`.
2. Type `powercfg /h on` and press `Enter`.
2. Search for `Control Panel` in search bar and open it.
2. Search for `Power Options`, click `Choose what the power buttons do`, click `Change settings that are currently unavailable`.
2. Uncheck `Turn on fast startup` and reboot your computer if `Turn on fast startup` is originally checked.
2. Press `Win`, type `cmd`, click `run as administrator` under **Command Prompt**, and click `Yes`.
2. Type `powercfg /h off` and press `Enter`.
2. Type `exit` and press `Enter`.

### Disable System Restore

1. Search for `Control Panel` in search bar and open it.
2. Search for `System`.
2. Click `View advanced systems settings`.
2. Click `System Protection`.
2. If any of the `Available Drives` has `Protection` set to `On`, click on it, click `Configure`, select `Disable system protection`, and select `OK`.

### Disable Paging Files

1. Search for `Control Panel` in search bar and open it.
2. Search for `System`.
2. Click `View advanced systems settings`.
2. Click `Advanced`.
2. Click `Settings` under `Performance`.
2. Click `Advanced`.
2. Click `Change…` under `Virtual memory`.
2. Uncheck `Automatically manage paging files size for all drives`.
2. Select the drive.
2. Check `No paging file`, then `Set`.
2. Click `Yes`.
2. Click `Apply`, `OK`, `OK`.
2. Click `Restart now`.

### Disable Debugging Information Dump

1. Search for `Control Panel` in search bar and open it.
2. Search for `System`.
3. Click `View advanced systems settings`.
4. Click `Advanced`.
5. Click `Settings` under `Startup and Recovery`.
6. Expand the drop-down menu under `Write debugging information` and select `None`.
7. Click `OK`, `OK`.

### Run Disk Cleanup

1. Search for `Disk Cleanup` in search bar and open it.
2. Select the drive.
3. Click `Clean up system files`.
4. Check what you want to delete in the `Files to delete` list.
5. Click `OK`.

### Defrag and Optimize Drives

1. Search for `Defrag and Optimize Drives` in search bar and open it.
2. `Defrag` (for HDD) or `Optimize` (for SSD) the drive.

### Reinstall Windows

Refer to [Clean Windows Reinstallation with USB](#clean-windows-reinstallation-with-usb) section.

### Third-Party Partition Manager

You can use third-party partition manager such as [Paragon Partition Manager](https://www.paragon-software.com/free/pm-express). According to the author's experience, even if all methods above failed, Paragon Partition Manager still successes.

### References

- <https://chrunos.com/increase-shrinkable-space>
- <https://www.thewindowsclub.com/shrink-volume-with-unmovable-files-in-windows>

## Intel Rapid Storage Technology (RST) VMD SSD Driver for Windows Reinstallation or Recovery

This guide will walk through how to make a drive with compatible Intel Rapid Storage Technology (RST) VMD Driver (unzipped) for Windows reinstallation or recovery.

### 0. Requirements

- Internet connection
- A drive that can be recognized by Windows without extra driver of file system type **FAT32** or **NTFS**. No data will be erased. Note that **exFAT** and **ext4** won't work.

### 1. Download Intel Rapid Storage Technology (RST) VMD Drive

We need `.inf`, `.sys`, and `.cat` files of the Intel Rapid Storage Technology VMD Driver that is compatible with your CPU generation (e.g. Intel 12th–15th Gen) to be put in the drive.

These files can be obtained from <https://github.com/sispt/Intel-Rapid-Storage-Technology-RST-VMD-Drivers-Extracted>, which can be cloned with

```
git clone https://github.com/sispt/Intel-Rapid-Storage-Technology-RST-VMD-Drivers-Extracted
```

Copy the driver folder you need to the drive. If not sure, copy all driver folders and select the compatible ones latter during Windows reinstallation or recovery.

## Clean Windows Reinstallation with USB

### 0. Requirements

- Internet connection.
- A USB flash drive no less than typically 8 GB. **Any content on it will be deleted during the process**.
- Activated Windows on your PC. **Any content on the partition you want to install Windows on will be deleted during the process**.
- If the partition you want to install Windows on is on an Intel Rapid Storage Technology (RST) VMD SSD, you nend another drive with compatible Intel Rapid Storage Technology (RST) VMD Driver (unzipped). No data in it will be erased. Hereafter referred to as Driver drive. Refer to [Intel Rapid Storage Technology (RST) VMD SSD Driver for Windows Reinstallation or Recovery](#intel-rapid-storage-technology-rst-vmd-ssd-driver-for-windows-reinstallation-or-recovery) about how to make one.
- You may need a phone with internet connection and a cable that can connect your PC and your phone.

### 1. The Edition of Windows Being Reinstalled Needs to Match Your License

1. Boot into Windows.
2. Go to `Settings` > `System` > `About` > `Windows specifications`. Remember the `Edition` shown here, such as Windows 11 Home.
2. If partition you want to install Windows on is the original Windows partition, skip the following two steps.
2. Go to `Settings` > `Accounts` > `Microsoft accounts`. `Sign in` and `Sign into this computer using your Microsoft account`.
2. Go to `Settings` > `System` > `Activation` > `Activation state`. It should shows `Windows is activated with a digital license linked to your Microsoft account` or  `Windows is activated with a product key linked to your Microsoft account`.

### 2. Create installation media for Windows

For Windows 11:

1. Plug in a USB flash drive. **Any content on it will be deleted**.
2. Go to [Download Windows 11](https://www.microsoft.com/software-download/windows11) site.
2. Under `Create Windows 11 Installation Media`, select `Download Now`. The `media creation tool` is downloaded with file name `MediaCreationTool.exe`.
2. Run `media creation tool`. It walks through creating installation media.

For Windows 10:

1. Plug in a USB flash drive. **Any content on it will be deleted**.
2. Go to [Download Windows 10](https://www.microsoft.com/software-download/windows10) site.
2. Under `Create Windows 10 Installation Media`, select `Download Now`. The `media creation tool` is downloaded with file name `MediaCreationTool.exe` or with the version appended, such as `MediaCreationTool_22H2.exe`. 
2. Run `media creation tool`. It walks through creating installation media.

### 3. Clean install

1. Plug in your USB flash drive with `media creation tool` and boot into it. The **Windows 11 Setup**/**Windows 10 Setup** should shows up.
2. In the `Select language settings` window, select language and localization settings, and then select `Next`.
2. In the `Select keyboard settings` window, select keyboard settings, and then select `Next`.
2. In the `Select setup option` window, select `Install Windows 11`/`Install Windows 10`, select `I agree everything will be deleted including files, apps, and settings`, and then select `Next`.
2. If the `Product key` window appears, select `I don't have a product key`, and then in the `Select Image` window that appears, select the edition of Windows that is installed on the device as determined in the section [1. The Edition of Windows Being Reinstalled Needs to Match Your License](#1-the-edition-of-windows-being-reinstalled-needs-to-match-your-license), and then select `Next`.
2. In the `Applicable notices and license terms` window, select the `Accept` button.
2. In the `Select location to install Windows 11`/`Select location to install Windows 10` window:
If the partition you want to install Windows on is detected, select it and press `Next`, and skip the next step. **Any content on it will be deleted**.
2. Insert the Driver drive and load the Intel Rapid Storage Technology VMD Driver by selecting the folder containing it and pressing <strong>Install</strong> until your the partition you want to install Windows on appears. Check **Hide drivers that aren't compatible with this computer's harware** and load the drivers that are not hidden if you aren't sure which driver(s) to load. Select the partition you want to install Windows on and press `Next`. **Any content on it will be deleted**.
2. In the `Ready to install` window, select `Install`.
2. The Windows reinstall begins. The device restarts several times during the reinstallation
2. Once Windows finishes reinstalling, refer to the next sections. Step through the `Windows configuration wizard` and configure settings as desired. In `Let's connect your to a network` window, if it shows `Ethernet not connected`, follow the next section. Otherwise, skip the next section.

### 4. Ethernet not Connected

1. Press `Shift + F10`, and type `OOBE\BYPASSNRO`.
2. It will reboot into Windows and shows `Windows configuration wizard` again. This time, in `Let's connect your to a network` window, select `I don't have internet`, and follow screen instructions to add a local account.
2. It will boot into normal Windows Desktop.
2. Connect your PC and your phone with a cable.
2. Turn on `USB Tethering` on your phone.
2. Go to `Settings` > `Windows Update`, click `Download & install all`, and reboot your PC after updates finished.

### 5. Activation

1. Go to `Settings` > `Windows Update`, click `Download & install all`, and reboot your PC after updates finished.
2. Go to `Settings` > `System` > `Activation` > `Activation state`. If it is activated, skip the following two steps. If it is not activated and you've linked your digital license or product key with your Microsoft account, follow the following two steps.
2. Go to `Settings` > `Accounts` > `Microsoft accounts`. `Sign in` to your Microsoft account linked with your digital license or product key and `Sign into this computer using your Microsoft account`.
2. Go to `Settings` > `System` > `Activation` > `Activation state`. It should shows `Windows is activated with a digital license linked to your Microsoft account` or  `Windows is activated with a product key linked to your Microsoft account`.

## TestDisk Partition Table Recovery

This guide explains how to use **TestDisk** to recover lost or corrupted partitions, without damaging your existing data.

### 0. Requirements

- Bootable Linux USB or installed Linux system (hereafer referred to as bootable Linux) of any distro with TestDisk installable.

### 1. TestDisk Partition Table Recovery

> **Important:** Do not write to the disk until you are sure of the recovered partitions.

1. Boot into the bootable Linux.
2. Install TestDisk (`sudo apt install testdisk` on Debian derivatives).
3. Run: `sudo testdisk`.
4. Select `Create` to make a new log file.
5. TestDisk will list all available disks. Use arrow keys to select the disk you want to recover and press `Enter`.
6. Select Partition Table Type, GPT for modern UEFI systems and Intel/PC for legacy MBR. Usually, TestDisk automatically detects the partition table type. Confirm the selection and press `Enter`.
7. Select `Analyse` and press `Enter`.
8. Select `Quick Search` and press `Enter`.
9. Wait for TestDisk to scan for partitions. If some partitions are missing or incorrect, select Deeper Search.
10. TestDisk will list all partitions it finds, showing, `Start` and `End` sectors, `Size`, `Label` (if available), etc. Navigate through detected partitions:
  - P: List files to check if data is accessible
  - Left/Right arrow keys: Change partition type:
    - `P`: Primary
    - `D`: Deleted
    - `L`: Load backup
    - `T`: Change file system type (only if incorrect)
Change all wanted partitions to primary. Ensure the partition structure has no overlap (`ok` in bottom left) and match your original setup.
11. Select `Write`, press `Enter`, and confirm by typing `Y`. TestDisk will write the new partition table to disk.
12. Close TestDisk and reboot the system. Check that every dual-boot system can be booted correctly.
13. If Windows does not boot, refer to [Windows Bootloader Recovery on Dual-Boot with Intel VMD SSD](#windows-bootloader-recovery-on-dual-boot-with-intel-vmd-ssd) section.

Notes:
-  Always verify partitions using `lsblk -f` or `fdisk -l` before writing.
- Keep the original disk untouched until you are confident in the recovered layout.
- If unsure about the detected partition start/end, list files (P) to confirm.
- Recovery can leave unallocated gaps. You can later merge them with other partitions or create new ones.

## Windows Bootloader Recovery

### 0. Requirements

- A USB flash drive no less than typically 8 GB. **Any content on it will be deleted during the process**.
- Any computer or Live USB of any system.
- If your Windows partition is on an Intel Rapid Storage Technology (RST) VMD SSD, you nend another drive with compatible Intel Rapid Storage Technology (RST) VMD Driver (unzipped). No data in it will be erased. Hereafter referred to as Driver drive. Refer to [Intel Rapid Storage Technology (RST) VMD SSD Driver for Windows Reinstallation or Recovery](#intel-rapid-storage-technology-rst-vmd-ssd-driver-for-windows-reinstallation-or-recovery) about how to make one.

### 1. Creating a Bootable Windows 11 USB

On any computer or Live USB of any system:

1. Download the **Windows 11 ISO** from [Download Windows 11](https://www.microsoft.com/en-us/software-download/windows11) site.
2. Insert the USB flash drive.
3. Install and open any software that can flash OS to flash drive, e.g. **balenaEtcher** (Linux, Windows, MacOS), **dd** (Linux), **Rufus** (Windows), **WoeUSB** (Linux).
4. Flash the ISO into the USB flash drive (all data will be erased). The software may show warnings, ignore it and continue flashing.

### 2. Boot from Windows 11 USB

<ol>
<li>Insert the Bootable Windows 11 USB and boot from it.</li>
<li>If <strong>Repair your computer</strong> is visible, click it, follow screen instructions, and then boot into Windows. It should load normally. Otherwise, follow below steps.</li>
<li>Click <strong>Browse</strong>, if the Windows partition is not detected, insert the Driver drive and load the Intel Rapid Storage Technology VMD Driver by selecting the folder containing it and pressing <strong>Install</strong> until your Windows partition (assumed `(E:)` here) appears. Check **Hide drivers that aren't compatible with this computer's harware** and load the drivers that are not hidden if you aren't sure which driver(s) to load.</li>
<li>Press <code>Shift + F10</code> to open <strong>Command Prompt</strong>.</li>
<li>Type below line by line and press <code>enter</code>, but replace the <code>0</code> and <code>1</code> to correct numbers according to guides beneath the script:
<pre><code>diskpart
list disk
select disk 0
list partition
select partition 1
assign letter=S
exit
</code></pre>
The partition you choose should be about <code>300MB</code> in <code>size</code> when listed, change the disk and partition numbers to find it.</li>
<li>Type below and press <code>enter</code>, but replace <code>E:</code> with actual partition letter:
<pre><code>bcdboot E:\Windows /s S: /f UEFI
</code></pre>
You should see <code>Boot files successfully created</code>.</li>
<li>Close <strong>Command Prompt</strong> and <strong>Windows 11 Setup</strong>. It will reboot automatically. Don't boot from the bootable Windows 11 USB. The bootable Windows 11 USB and the Driver drive can be removed now.</li>
<li>Boot into Windows. It should load normally.</li>
</ol>

### 3. Remove Stale Windows Entry

This attempts to solve the symptom that, after boot into **Windows Boot Manager**, two or more entries are shown in a blue menu. If your don't encounter this symptom, skip this section.

1. Boot into the working Windows (typically first one with a label showing which disk it's in).
2. Open **Command Prompt** as **Administrator**.
3. Run `bcdedit /enum all`.
4. Look for entries under `Windows Boot Loader` with `description` being the same as the stale one in the previous menu (`Windows 10/11/etc.`), `identifier` not being `{current}`, and `device` being `unknown` or non-existent partition.
5. For each entries found in the last step, let its `identifier` be `{###}` (with `{}`), run `bcdedit /delete {###}`. (`Ctrl C` to copy, `Ctrl V` to paste).
6. Reboot and check. There's should no longer be multiple entries in Windows Boot Manager.

## Time Mismatches

If time mismatches real local time, run below in Linux:
```
sudo timedatectl set-local-rtc 1
sudo timedatectl set-ntp true
```

## My Related Repositories

* [**ubuntu-setup-with-vnc-and-gpu**](https://github.com/Willie169/ubuntu-setup-with-vnc-and-gpu)
* [**switch-firefox-from-snap-to-deb**](https://github.com/Willie169/switch-firefox-from-snap-to-deb)
* [**LinuxAndTermuxTips**](https://github.com/Willie169/LinuxAndTermuxTips)
