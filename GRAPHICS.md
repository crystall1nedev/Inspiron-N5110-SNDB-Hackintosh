# It's time to talk graphics.
Let's take you back to June 2015. During Apple's Worldwide Developer Conference, they announced OS X El Capitan, version 10.11 of our favorite operating system. With the release of El Capitan, we also saw the introduction of Metal for Mac, bringing Apple's little baby graphics API from iOS to their computer lines for the first time...but there's a catch. Although El Capitan runs on Macs dating back to the Core 2 Duo days, Metal is only supported if the Mac has the following graphics cards:

  * Intel HD Graphics 4000 and newer
  * AMD GCN (HD7000) and newer
  * NVIDIA Kepler (GT6xx-GT7xx) and newer
    * Includes up to GTX10xx if on High Sierra or older
    * Does not include Fermi core GT6xx-GT7xx cards
  
You might notice that none of the cards that were available to order in the Inspiron N5110 are listed above. Because of this reason, they are not Metal-capable (but they do support OpenGL). All graphics cards not capable of dealing with the Metal API were dropped with the release of macOS Mojave in 2018.

Because of this, we need to do some post-installation steps in order to enable hardware acceleration. Although these patches work, they are still slightly unstable, and they cannot enable features that the graphics card does not support.

## Getting ready to patch your system.
Before you can do anything, you need to get some things down:
<details>
  <summary>macOS Installation</summary>
  
  macOS should already be installed on your system and ready to go. Although these patches should work perfectly fine on Mojave and Catalina, I would recommend using one of the former two as I've only done this on those OS versions. You also can't install macOS on another computer and run the patches there.
</details>
<details>
  <summary>System Integrity Protection</summary>
  
  You need to disable some SIP flags or else you'll spend a lot of time wondering why you bootlooped.
  
  | OS Version | SIP Flags | Value |
  | --- | --- | --- |
  | macOS 10.14.x-10.15.x | `CSR_ALLOW_UNTRUSTED_KEXTS`<br>`CSR_ALLOW_UNRESTRICTED_FS`<br>`CSR_ALLOW_UNAPPROVED_KEXTS` | `03020000` |
  | macOS 11.x and higher | All flags above +<br>`CSR_ALLOW_UNAUTHENTICATED_ROOT` | `030A0000` |
</details>
<details>
  <summary>OpenCore Legacy Patcher</summary>
  
  We're only using OpenCore Legacy Patcher to apply post-installation patches--**do not build and install a new EFI from it!** It will break if you do. If you, for some reason don't want to grab the latest release, here are the minimum releases you require according to OS versions.
  
  | OS Version | OCLP Version Required |
  | --- | --- |
  | macOS 10.14.x | 0.2.5 |
  | macOS 10.15.x | 0.2.5 |
  | macOS 11.x | 0.1.1 |
  | macOS 12.0 | 0.2.5 |
  | macOS 12.0.1+ | 0.3.1 |
</details>
<details>
  <summary>SMBIOS</summary>
  
  You need a supported SMBIOS in order to run the patches, as the values for board-ids are hard-coded in AppleIntelSNBGraphicsFB.kext.
  
  * MacBookPro12,1
  * MacBookPro11,5
  * MacBookAir7,1
  * MacBookAir7,2
  * Macmini7,1
  * iMacPro1,1

</details>  
<details>
  <summary>Miscellanous prep</summary>
  
  If you aren't going to use the patches I have added to Kernel > Patch in my config.plist, you need to add and keep `amfi_get_out_of_my_way=0x1` in NVRAM > 7C436110-AB2A-4BBB-A880-FE41995C9F82 > boot-args.
</details>

## Known issues and drawbacks
