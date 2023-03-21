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

I have a bunch of systems, booting them is sometimes tedious.
Updating Ventoy USB sticks where I have those, but in other places, and as the number of architectures and OSes I support grows, keeping up with all this installation media is challenging and begets a lot of copies of the same data.

Getting PXE booting operational would be the best bet.  My Unifi Dream Machine Pro can do next-server TFTP PXE Booting, but it's only for one type of booting (BIOS/UEFI) and only for one type of architecture so supporting X86 and ARM isn't a thing.
Off-loading DHCP to a more robust stack makes PXE booting a possibility in my network.

## Helpful Things To Know

### PXE Boot Process Overview

- PXE Booting is a function that's built-in to a Network Interface Cards' firmware.
- There is a more modern and robust implementation called iPXE that is backwards compatible.
- When attempting a PXE Boot, your systems' NIC will call out for a DHCP address on the network it is connected to - some firmwares allow setting static addresses and the next-server option, and iPXE even supports things like VLANs.
- The DHCP Option `next-server` tells the PXE client **where** to download **what** Network Boot File from - this is via TFTP, or Trivial FTP which pretty much just supports specific downloads/uploads and can't even list files.
- The PXE Client will then attempt to download thtat NBF via TFTP and run whatever it is - either a PXE boot binary, or a PXE bootstrap file that loads something like GRUB to present a menu.
- Once you get to the PXE boot menu, you get presented a set of options that then load a Kernel and Initial Ram Disk, or whatever other bootstrap blob that needs to initialize the next phase of booting.

### Gotchas

- Most DHCP servers can only send requests to one specific address, and for one file that is built for one architecture
- Multi-arch PXE booting needs to use ISC DHCPD which supports detection of DHCP client architectures and pushing of the proper boot file.

## Getting Started

### Network Architecture

First, you'll need to assess your network and what you want it to look like.  You need to manage the DHCP server on your default untagged VLAN 0 network - the network that is routed by default when you plug in a cable.

For me, my network is primarily serviced by a Unifi Dream Machine Pro as a router/firewall/etc.  It typically also ran DHCP for all my networks, however it cannot provide PXE services for multiple architectures so I need to offload the DHCP service to something else.

Now the default network's DHCP is serviced by my PXE server, which provides everything I need, and the UDMP is just configured in DHCP Relay mode.

### PXE Server

Next, you'll need a RHEL 9.1 system - a VM works perfectly fine.  Give it enough space to house all the ISOs of the operating systems you want to host, and then a little extra room for some kernels.

For these ISOs, if you can download them from the public internet then the automation can handle that for you - if you're working with something like RHEL ISOs that need authentication to download, you'll need to do that manually and to copy them to another part of the file system or mount them from NFS (which is what I do).

### Configuration & Setup

Then you'll need to get it configured a bit as a base system - as long as subscriptions/repositories are enabled we're good.

1. Modify the variables at the top of the `deploy.yml` Playbook.
2. Modify the `inventory` file to point to the RHEL 9.1 system that will act as your PXE server.
3. Run `ansible-playbook -i inventory deploy.yml`
4. Configure your primary router to forward DHCP requests to the PXE server
5. ???????
6. PROFIT!!!!1
