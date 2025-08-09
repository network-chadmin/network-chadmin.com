---
title: Getting Started
icon: sun
order: 990
---

### Requirements

Labs I publish here use [Containerlab](https://containerlab.dev/), a free network lab solution developed by engineers at Nokia. It doesn't require a costly subscription, and in my experience, it's more resource-efficient, more reliable, and a lot more enjoyable than the emulators of years past.

- **Containerlab installed** on a Linux PC, server, WSL, or MacBook.  
  These labs are built and tested on **Ubuntu Server 24.04.2 LTS**.
- **Lab images** — Arista cEOS (for switches), Cisco IOL (for routers), and Ubuntu Linux hosts.
- **Lab files** — available on my [GitHub](https://github.com/network-chadmin/containerlab.git).

!!!warning "About Cisco IOL Images"
Cisco IOL (IOS on Linux) images are not publicly downloadable and are not distributed with these labs.  
They can be acquired via a subscription to Cisco Modeling Labs and their refplat ISO.
!!!

!!!tip "Converting Cisco IOL VMs to Containerlab Images"
If you have IOL as a virtual machine image, you must convert it into a containerized image before Containerlab can use it.  
Use the [`vrnetlab`](https://containerlab.dev/manual/vrnetlab/) toolkit to wrap the VM image into a Docker container.  
This process is one-time per image and ensures smooth integration with your lab environment.
!!!

### Installing Containerlab

Run the command:

```bash
curl -sL https://containerlab.dev/setup | sudo -E bash -s "all"
```

This will install all components needed to run Containerlab, including Docker. See detailed or alternate installation instructions [here](https://containerlab.dev/install/).

### Images

#### Network-Host
A custom Ubuntu host with extra packages like `sudo`, `iperf3`, `tcpdump`, `traceroute`, `curl`, `nginx`, and more.  
Containerlab will pull this image from Docker when you run your first lab.

#### Arista cEOS (Switches)
[Arista](https://www.arista.com/en/support/software-download) offers these images for free once you’ve created an account.  
Navigate to:  
**Support → Software Download → cEOS Lab → 4.32 → EOS-4.32.5.1M → cEOS64-lab-4.32.5.1M.tar.xz**

![Arista EOS version used in these labs|300x200](/static/getting-started/ceos-version.png)

#### Cisco IOL (Routers)
IOL images provide IOS-like functionality in a Containerlab environment and are used for routing labs (e.g., NAT, BGP).  
You must have proper licensing to obtain and use these images. If you start with a VM image, see the vrnetlab callout above to containerize it.

### Lab files

Clone the repo containing all my labs/courses:

```bash
git clone https://github.com/network-chadmin/containerlab.git
```

Cloning the repo downloads all lab files. After obtaining the necessary images, move to the directory with your desired lab’s YAML file:

```bash
cd /path/to/your/labfile.clab.yml
```

Then launch your lab:

```bash
containerlab deploy
```

If successful, you’ll see output listing your nodes and their connection status.  
You can now access devices via terminal or your preferred emulator (like SecureCRT, Tabby, or PuTTY).

For a deeper dive into administering Containerlab and Docker, see my [Containerlab Reference Guide](documentation/containerlab-reference).