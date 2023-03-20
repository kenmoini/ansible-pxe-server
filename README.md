# Multi Architecture PXE Server Automation

This repo has stuff to turn a RHEL 9.1 system into a robust PXE Server that can do:

- BIOS booting - X86_64
- EFI booting - X86_64, ARM 64
- Core Service Setup: DHCPD, TFTP, FTP, HTTP, and NFS!
- Manage ISOs - *and mount them instead of extracting them!*
- Put files where they need to be!
- Supports:
  - RHEL 8/9
  - Rocky 8/9
  - Ubuntu 20.04
  - Ubuntu 22.04
  - Probably more?

## Why?

I have a bunch of systems, booting them is sometimes tedious.  Updating Ventoy USB sticks where I have those, but in other places, and as the number of architectures and OSes I support grows, keeping up with all this installation media is challenging and begets a lot of copies of the same data.  Getting PXE booting operational would be the best bet.  My Unifi Dream Machine Pro can do next-server TFTP PXE Booting, but it's only for one type of booting (BIOS/UEFI) and only for one type of architecture so supporting X86 and ARM isn't a thing.
Off-loading DHCP to a more robust stack makes PXE booting a possibility in my network.

## Getting Started

First, you'll need to assess your network and what you want it to look like.

Next, you'll need a RHEL 9.1 system - a VM works perfectly fine.

Then you'll need to get it configured a bit as a base system - as long as subscriptions/repositories are enabled we're good.