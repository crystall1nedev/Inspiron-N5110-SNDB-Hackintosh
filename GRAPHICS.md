# Time to talk graphics.
Here's a bit of a backstory tl;dr:

All of the GPUs that can come inside of an N5110 are non-Metal, meaning that they don't support Apple's Metal graphics APIs. With the release of macOS Mojave 10.14 in 2018, all non-Metal GPUs were dropped. The work done by the Dortania team and @ASentientBot has brought back this support, but you need to take note of these main points:

- Due to the heavy dependence on Metal since Mojave, **there will be glitches!**
- You **must** have SIP disabled. The current setting for SIP disables only what is required so that OTA updates are possible.
- Because the patches modify the system volume, **OTA updates will not be deltas** (you will download the entire OS).
- Performing updates **undoes graphics acceleration patches**, and you need to run them again.
- These patches are known to work on **Big Sur and higher.** Although they may work on Mojave and Catalina, it is not recommended.
- See the note under the "Patching your system" section.  

*Because I have an iGPU-only configuration, I will only provide support for HD 3000 systems. The patches work on the other configurations, however.*

<p align="center">
<img width="75%" src="https://user-images.githubusercontent.com/55281754/139558378-a9772a69-2554-47e5-991e-13148024b6c7.png"
</p>

## Patching your system.
1. **Get everything ready.** You need to have macOS installed already in order to apply the patches. I recommend using Big Sur or higher, as support to patch Mojave and Catalina is highly experimental. You also need a copy of OpenCore Legacy Patcher. <details><summary>Version of OCLP required</summary>To use legacy acceleration patches on your Hackintosh, you need to have a specific minimum depending on the OS you want to boot:<ul><li>macOS Mojave 10.14.x requires **OCLP 0.2.5 or higher.**</li><li>macOS Catalina 10.15.x requires **OCLP 0.2.5 or higher.**</li><li>macOS Big Sur 11.x requires **OCLP 0.1.1 or higher.**</li><li>macOS Monterey 12.0 requires **OCLP 0.2.5 or higher.**</li><li>macOS Monterey 12.0.1+ requires **OCLP 0.3.1 or higher.**</li></ul></details>

<p align="center">
<img width="50%" src="https://user-images.githubusercontent.com/55281754/139558262-02b2fb49-82e3-430b-b341-cd113a9b766e.png">
</p>

2. **Run the Patcher.** I'll be using the TUI variant here. Open the Patcher application and hit `2` for `Post-Install Volume Patch`...

<p align="center">
<img width="50%" src="https://user-images.githubusercontent.com/55281754/139558222-b9e32290-0968-48c5-9805-caa115ceacef.png">
</p>

...and then `1` to apply...

<p align="center">
<img width="50%" src="https://user-images.githubusercontent.com/55281754/139558225-77157740-4c68-4f5c-be08-cc7f2f32ecc8.png">
</p>

...enter your password and wait for it to complete!

<p align="center">
<img width="50%" src="https://user-images.githubusercontent.com/55281754/139558216-2496aca4-defc-4a40-aae4-e9bfffb65cfd.png">
</p>

3. **Reboot into acceleration!** Simply reboot your system and you'll have hardware acceleration, just like it never left.

## Backlighting issue with the HD3000
You may have noticed that after applying the patches on your iGPU-only system, **you just lost the backlight.** I don't know the exact cause of this issue but I know it can be solved...

...but you need a modified BIOS to do it. I patched my own custom BIOS to enable the advanced option menu and flashed it--**this is a serious risk and can result in a brick.** I highly recommend that you know what you are doing before flashing with a modified BIOS.

## Increasing HD3000 VRAM
The way integrated GPUs work depends on the system memory rather than having its own dedicated RAM. Because of this, the following patch is only available on the Intel HD Graphics 3000 chip and not the ATI or NVIDIA cards that may be inside the system. This patch is also tailored for macOS Big Sur or higher; some commands will fail on Catalina and older. The following chart shows the amount of RAM that the iGPU can access in macOS according to the amount of system memory:

| System RAM | iGPU VRAM |
| --- | --- |
| 8GB and under | 384 MB |
| 8-12GB | 512 MB |
| Higher than 12GB | 1024 MB |

**Before doing this, please make sure you have a way to recover in case something goes wrong! I am not responsible if you need to reinstall or repatch.**

1. **Download [Hex Fiend](https://github.com/HexFiend/HexFiend/releases/download/v2.14.1/Hex_Fiend_2.14.1.dmg) and put it in /Applications**. This is a hex editing application, and what we'll use to adjust our VRAM.

2. **Open a terminal, and get ready to type some commands.** You will need an administrator password to perform the patch.

```bash
# List all of the disks connected to the system
diskutil list

# Now we need to find the system volume.
# On Mojave, the system volume is the same as the data volume.
# On Catalina and higher, the system volume shares the same name as the Data volume (without the " - Data" suffix)
#
# /dev/disk0 (internal, physical):
#    #:                       TYPE NAME                    SIZE       IDENTIFIER
#    0:      GUID_partition_scheme                        *256.1 GB   disk0
#    1:                        EFI ⁨EFI⁩                     209.7 MB   disk0s1
#    2:                 Apple_APFS ⁨Container disk1⁩         255.9 GB   disk0s2
#
# /dev/disk1 (synthesized):
#    #:                       TYPE NAME                    SIZE       IDENTIFIER
#    0:      APFS Container Scheme -                      +255.9 GB   disk1
#                                  Physical Store disk0s2
#    1:                APFS Volume ⁨MacintoshSSD - Data⁩     97.2 GB    disk1s1
#    2:                APFS Volume ⁨Preboot⁩                 444.7 MB   disk1s2
#    3:                APFS Volume ⁨Recovery⁩                1.1 GB     disk1s3
#    4:                APFS Volume ⁨VM⁩                      1.1 MB     disk1s4
#    5:                APFS Volume ⁨MacintoshSSD⁩            15.9 GB    disk1s5  <- This is our system volume
#    6:              APFS Snapshot ⁨com.apple.bless.10D5...⁩ 15.9 GB    disk1s5s1

# Make a directory for us to mess around
# I choose this one because it's less intrusive and the directory should already exist
sudo mkdir -p /System/Volumes/Update/mnt1

# Now let's mount the system volume as writable (replace /dev/disk1s5 with your actual location)
sudo mount -o nobrowse -t apfs /dev/disk1s5 /System/Volumes/Update/mnt1

# Let's get our hex editor open as the superuser (you can't save if you do not run as superuser)
sudo /Applications/Hex\ Fiend.app/Contents/MacOS/Hex\ Fiend

# And now prepare our kext editing process
open /System/Volumes/Update/mnt1/System/Library/Extensions/AppleIntelSNBGraphicsFB.kext/Contents/MacOS
```

3. After running all of the above commands, minimize the terminal and **click into the Finder window that just opened.** Drag the `AppleIntelSNBGraphicsFB` file from the Finder window into the Hex Fiend window. 

<p align="center">
<img width="65%" src="https://user-images.githubusercontent.com/55281754/139560030-1a05ded9-7fb2-4c35-b062-7ed55cab0684.png">
</p>

4. **Press Cmd+F to open the search field**, and use the below table as a guide:

| Current VRAM | Paste into `Find` |
| --- | --- |
| 384 MB | `C7 45 D0 00 00 00 18` |
| 512 MB | `C7 45 D0 00 00 00 20` |
| 1024 MB | `C7 45 D0 00 00 00 40` |

5. **In the highlighted section, choose the last hex pair.** For this example, I'm adjusting my VRAM of 512MB. Once `C7 45 D0 00 00 00 20` is located and highlighted, I want to select `20`.

<p align="center">
<img width="65%" src="https://user-images.githubusercontent.com/55281754/139560524-0ed256e6-585d-49a6-b213-18bc3633b3e4.png">
</p>

6. **Press Backspace.** Now type a new pair based on the table below:

| VRAM Amount | Hex pair |
| --- | --- |
| 384 MB | `18` |
| 512 MB | `20` |
| 1024 MB | `40` |
| 2048 MB | `80` |

7. **Don't edit anything else, save, and exit.** 

8. **Wrap up with a few more commands.**

```bash
# Because we modified a kext in /System/Library/Extensions, we need to rebuild the kext cache
sudo kmutil install --volume-root /System/Volumes/Update/mnt1 --update-all

# And now we can create our own snapshot to be able to boot again.
sudo bless --folder /System/Volumes/Update/mnt1/System/Library/CoreServices --bootefi --create-snapshot
```

7. **Reboot for more VRAM!** 
