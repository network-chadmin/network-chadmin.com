---
title: Getting Started
icon: sun
order: 990
---

## Requirements

Labs I publish here use [Containerlab](https://containerlab.dev/), a free network lab solution developed by engineers at Nokia. It doesn't require a costly subscription, and in my experience, it's more resource-efficient, more reliable, and a lot more enjoyable than the emulators of years past.

- **Containerlab installed on a Linux PC, server, WSL, or MacBook.** The labs here have been built and tested on Ubuntu server 24.04.2 LTS.
- **Lab images** — Arista cEOS and Ubuntu Linux
- **Lab files** available on my [GitHub](https://github.com/network-chadmin/containerlab.git)

## Installing Containerlab

Run the command:

```bash
curl -sL https://containerlab.dev/setup | sudo -E bash -s "all"
```

This will install all components needed to run Containerlab, including Docker. See detailed or alternate installation instructions [here](https://containerlab.dev/install/).

## Images

!!!info Why cEOS?
All labs now use Arista cEOS images instead of Cisco IOL.  
cEOS is legal to download, consistent across platforms, and freely available to anyone with an Arista account — making your lab experience easier and more reliable.
!!!

### Network-Host
This is a custom Ubuntu host with a few extra packages installed like sudo, iperf3, tcp dump, traceroute, curl, nginx, and more. Containerlab will pull this image from Docker when you run your first lab.

### Arista cEOS
[Arista](https://www.arista.com/en/support/software-download) offers these images for free once you've created an account. Navigate to "Support → Software Download → cEOS Lab → 4.32 → EOS-4.32.5.1M → cEOS64-lab-4.32.5.1M.tar.xz"

![Arista EOS version used in these labs|300x200](/static/getting-started/ceos-version.png)


## Lab files

Clone the repo containing all my labs/courses:

```bash
git clone https://github.com/network-chadmin/containerlab.git
```

Cloning the repo above will download all the necessary lab files to start, assuming you've acquired the proper images. Move to the directory with your desired lab's YAML file in it:

```bash
cd /path/to/your/labfile.clab.yml
```

Then go ahead and launch your lab:

```bash
containerlab deploy
```

If successful, you’ll see output listing your nodes and their connection status.  
You can now access devices via terminal or your preferred terminal emulator (like SecureCRT, tabby, or PuTTY).

For a more in-depth look at how to administer Containerlab and Docker see my [Containerlab Reference guide](documentation/containerlab-reference).