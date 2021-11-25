# It's time to talk graphics.
Let's take you back to June 2015. During Apple's Worldwide Developer Conference, they announced OS X El Capitan, version 10.11 of our favorite operating system. With the release of El Capitan, we also saw the introduction of Metal for Mac, bringing Apple's little baby graphics API from iOS to their computer lines for the first time...but there's a catch. Although El Capitan runs on Macs dating back to the Core 2 Duo days, Metal is only supported if the Mac has the following graphics cards:

  * Intel HD Graphics 4000 and newer
  * AMD GCN (HD7000) and newer
  * NVIDIA Kepler (GT6xx-GT7xx) and newer
    * Includes up to GTX10xx if on High Sierra or older
    * Does not include Fermi core GT6xx-GT7xx cards
  
You might notice that none of the cards that were available to order in the Inspiron N5110 are listed above. Because of this reason, they are not Metal-capable (but they do support OpenGL). All graphics cards not capable of dealing with the Metal API were dropped with the release of macOS Mojave in 2018.

Because of this, we need to do some post-installation steps in order to enable hardware acceleration. Although these patches work, they are still slightly unstable, and they cannot enable features that the graphics card does not support.

## Getting ready
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
Before we can get you on your way to patching, you need to take note of a few things.

<details>
 <summary>Apps that require Metal</summary>
 
 Any app that depends on Metal may not work correctly if it works at all. If you can find older versions of the app you want to use, do so. Some examples of apps requiring Metal:
 
 * Pages
 * Numbers
 * Keynote
 * GarageBand
 * Final Cut Pro
 * Logic Pro
 * Motion
 * Compressor
</details>
<details>
 <summary>Backlight gone with HD 3000</summary>
 
 Once you get hardware acceleration on the iGPU-only model, your backlight might turn off. The onyl way I was able to get around this was by patching my BIOS and enabling hidden menus--**which almost resulted in me bricking my system!** If you don't care about your backlight going out (for, say, you're using this system as a server, like me), enable Remote Management before you run the patches, and give your system a static IP so you can always be sure that you can connect to it.
</details>
<details>
 <summary>Disabled security features</summary>
 
 * System Integrity Protection, specifically:
   * (Big Sur and higher) Authenticated root, which verifies the contents of the System volume every boot
   * (Catalina and higher) Filesystem protections, preventing the System volume from being mounted read/write
   * Kext signing, preventing unsigned kexts from loading
 * Apple Mobile File Integrity (AMFI)
   * Responsible for code entitlements and privacy controls
   * You don't need to disable this if you use the patches I have loaded in Kernel > Patch
     * Note that the Music app might crash on Big Sur and older with the said patches
 * FileVault 2
   * On-the-fly disk encryption for your Data partition
   * [Disabling SIP disables this](https://support.apple.com/guide/security/signed-system-volume-security-secd698747c9/web)
   * You don't need to disable this if you use the patches I have loaded in Kernel > Patch.
 * Apple Secure Boot
   * SecureBootModel in Misc > Security
   * Controls what OpenCore will load
   * Not that much of an issue as it's meant to be paired with UEFI Secure Boot, which doesn't work on BIOS-only systems
</details>
<details>
 <summary>Updating a patched system</summary>

 Delta updates are the smaller updates that only contain what Apple changed between your release and the one you're downloading. Because these patches modify protected directories, delta updating is no longer possible and with each update you will essentially be downloading and reinstalling the operating system. Because this means that everything in /System will be overwritten, your patches will be removed and you will need to rerun them manually.
</details>
<details>
 <summary>OS support</summary>
 
 Take note that these patches are designed to work with Big Sur and higher, work for older macOS releases is experimental with OpenCore Legacy Patcher and can have unintended side effects. 
 
 Updating macOS has the potential to break acceleration patching as well so hold off on updating until you know that the OS in question is capable of being patched.
</details>

## Patching your system

## Extra stuff
