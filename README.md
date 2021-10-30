# Inspiron-N5110-SNDB-Hackintosh

OpenCore EFI files for the Dell Inspiron N5110. This EFI was built for macOS Big Sur, but can be modified to fully work with older versions.

OpenCore Version: 0.7.4

| Type | Model | Works? | Notes |
| --- | --- | --- | --- |
| CPU | Intel Core i7-2670QM | Yes | Highly recommend upgrading the CPU. Will test Core i7-3xxx compatibility with this computer soon. |
| GPU | Intel HD Graphics 3000 | 50/50 | See GRAPHICS.md |
| | NVIDIA GeForce GT 525M | No | See GRAPHICS.md |
| | ATI Radeon HD 6470M | Maybe? | See GRAPHICS.md |
| Audio | Currently unknown | Most likely | I just haven't patched it yet. |
| USB | N/A | Yes | USBInjectAll works until I do port-mapping. |
| eSATA | N/A | Yes | Acts as a USB port in macOS. |
| Keyboard | N/A | Most likely | I just forgot to add the keyboard kexts. |
| Trackpad | ALPS PS/2 | Maybe? | My trackpad is broken, unable to test. |
| HDMI | N/A | 50/50 | See GRAPHICS.md |
| WiFi | Broadcom BCM94352Z | Yes | |
| Bluetooth | Broadcom BCM94352Z | Yes | Supports native macOS Continuity features as well. |
| Ethernet | Currently unknown | Maybe? | Ethernet port is broken, unable to test. |
| CD-DVD | PLDS DVD+-RW DS-8A5SH | Yes | |
| SD Card Reader | Generic Realtek | Yes | Will add GenericCardReaderFriend for System Information to recognize as an SD card reader, but works fine either way. |
