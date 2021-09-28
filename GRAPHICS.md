# Time to talk graphics.
This is gonna take a while. 

## A little backstory.
In 2014, Apple revealed Metal, their successor to the older, and more common, graphics technologies like OpenGL and OpenCL. The following year, with OS X El Capitan, they integrated it with the operating system. The requirements for Apple's Metal API are as follows:

* Intel HD Graphics 4000 or newer
* Any AMD GCN or RDNA or newer
* Any NVIDIA Kepler GPU
* Any 64-bit Apple Silicon GPU

You might notice that the Intel HD Graphics 3000, ATI Radeon 6470M, and NVIDIA GeForce 525M aren't on this list. They are known as **non-Metal GPUs**. They were all dropped with the release of macOS Mojave in 2018, where it was revealed that the older technologies were deprecated and Metal would be the standard for Apple's graphics stack. The Hackintosh community has been able to transplant older drivers into newer operating systems, so luckily they aren't completely dead yet. Let's discuss each GPU to know what we're dealing with first.

### NVIDIA GeForce GT 525M
If you have this GPU, you drew the short stick. There are no macOS drivers for this GPU--in any version. The only thing you can hope to do is disable and fall back to the HD 3000 included with the system by adding `-wegnoegpu` to your boot-args.

### ATI Mobility Radeon HD 6470M
As this GPU is based on the TeraScale 2 microarchitecture, **it most likely has support in macOS High Sierra 10.13.6.** Please do note that this EFI is still specifically tailored for the iGPU-only model of the Inspiron N5110 and you might still need to do patching to boot.

### Intel HD Graphics 3000
**This GPU has support in macOS High Sierra 10.13.6,** and this is the GPU I was based around.

## Before we start
**Make sure to use Big Sur.** This repo will be me uploading my EFI, and I do not plan on dualbooting other operating systems. I will not provide support for Catalina or Mojave questions as the answer will be "I don't know".

**Be patient.** You're gonna be waiting a while, especially on the first boot and initial setup.
