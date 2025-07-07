---
title: Shrimp Co.
icon:
order: 1000
---
![An exploration in networking, from basic L2 to dynamic L3 & tunneling](/static/network-academy/shrimpco/banner.png)

## Welcome to Shrimp Co.

This is my beginner friendly lab series where you play the role of network administrator for Shrimp Co, a growing company based out of Seattle, WA.  You'll start with a single network closet and basic layer 2 connectivity, slowly increasing the size and complexity of your network as the series goes on.  Eventually. we'll build up to large headquarters incorporating several layers of redundancy, dynamic routing, and a basic WAN.

### Philosophy

Each lab will build on the one before it, utilizing the same network devices while adding new hardware, and new protocols/technologies.  While some configuration will be pre-existing, you'll be configuring the devices from scratch each time.  This allows you to become familiar with the network as you would one you work in, and develop important muscle memory on the command-line.

!!!info Who is this for?
Shrimp Co. is aimed at beginners to computer networking or IT professionals looking to ramp up their understanding of enterprise networking concepts.  While it is beginner friendly, basic understanding of networking, Cisco CLI, and Linux will be helpful.  Structure, goals, and tips are given, but research of the protocols and configuration are left up to the engineer.  Helpful white papers are linked in the reference section of each lab.
!!!

### Before you start

1. Navigate to your `~/containerlab` directory and pull the latest updates from the repository with `git pull origin main` and if you don't have the repo cloned yet visit Getting Started.
2. If you have SecureCRT make sure to upload the .xml file included in the repo for SSH sessions and commands.
3. The docker management subnet for this set of labs is 172.20.25.0/24; in order for the SSH sessions to work you'll either need a route with a next-hop of your Ubuntu host or if you're using my SecureCRT sessions you can use an existing session as a jump host:

![Create a session to for your Ubuntu host with automated login and choose it here for every session.](/static/network-academy/shrimpco/index-crt.png)

4. Every device has a different management address, even ones with the same hostname between labs.  This was done incase the labs are being run on a server with multiple users and allows multiple  labs to be run without conflict, except for two of the same lab. s
5. Each device tag has both the Docker management IP address, and the in-lab management IP address.  Both will be shown on the device tags on each lab topology.

![172.16.25.62 is the docker management IP, while 10.1.99.30 is the one you will configure in-lab.](/static/network-academy/shrimpco/index-topology.png)

6. Emulated networks like these can be buggy at times and certain things that may not work exactly like they do on a physical device.  An example of this is if *SW1 Eth0/1 <---> Eth0/1 SW2* are connected and you shut down *Eth0/1* on *SW1*, the Line Protocol would go down on the SW2 side in the real world, but that won't happen with these emulated devices.  If your network isn't working how you think it should, these things do get goofy sometimes
7. Have fun and reach out to me at heetland.chad@gmail.com for any comments, questions, or feedback! 

