---
title: Getting Started
icon: sun
order: 990
---

## Requirements

Labs I publish here use [Containerlab](https://containerlab.dev/), a free network lab solution developed by engineers at Nokia. It doesn't require a costly subscription, and in my experience, it's more resource-efficient, more reliable, and a lot more enjoyable than the emulators of years past.

- **Containerlab installed on a Linux PC, Server, WSL or on a Macbook.** The labs here have been built and tested on Ubuntu server 24.04.2 LTS.
- **Lab images** — at the time of writing, I'm using Cisco IOL, Cisco IOL-L2, and Arista cEOS.
- **Lab files** available on my [GitHub](https://github.com/network-chadmin/containerlab.git)

## Installing Containerlab

Run the command:

```bash
curl -sL https://containerlab.dev/setup | sudo -E bash -s "all"
```

This will install all components needed to run Containerlab, including Docker. See detailed or alternate installation instructions [here](https://containerlab.dev/install/).

## Images

### Cisco IOL
IOL can be acquired from Cisco after purchasing a CML subscription and extracting the image file from the refplat ISO. You may be able to find it via other avenues, but this is what I did as I know I'm getting the safest and most up-to-date image. This is technically a VM and not a Container natively so you'll need to use [vrnetlab](https://github.com/vrnetlab/vrnetlab) to import the images into Docker.

### Network-Host
This is a custom Ubuntu host with a few extra packages installed like sudo, iperf3, tcp dump, traceroute, curl, nginx, and more. Containerlab will pull this image from Docker when you run your first lab.

### Arista cEOS
[Arista](https://www.arista.com/en/support/software-download) offers these images for free once you've created an account. Navigate to "Support → Software Download → cEOS Lab → 4.33 → cEOS64-lab-4.33.3F.tar.xz"

![This is the cEOS version I'll be using in my labs|300x200](/static/getting-started/ceos-version.png)


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

For a more in-depth look at how to administer Containerlab and Docker see my [Containerlab Reference guide](documentation/containerlab-reference).