---
icon:
order: 1000
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 1  - VLANs
> Shrimp Co. is opening their first office in Seattle, WA, and you're hired as their network administrator. The switch is racked in IDF-A and it's up to you to set up basic Layer 2 segmentation.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/01_vlans.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

### :icon-tasklist: Configuration Tasks

#### VLAN Configuration

Create and configure the following VLANs:

- **VLAN 10** - Sales Department
- **VLAN 20** - Engineering Department  
- **VLAN 99** - Network Management

#### Host IP Addresses & VLAN Assignment

| **Host** | **VLAN Assignment** | **IP Address** |
|------|-----------|------------|
| Bob | 10 (Sales) | 10.1.10.10/24 |
| Linda | 10 (Sales) | 10.1.10.20/24 |
| Alice | 20 (Engineering) | 10.1.20.10/24 |

!!!warning

Hosts and devices in the diagram have **two** IP addresses shown. 172.25.20.X is a docker management subnet that's pre-configured and allows you SSH access, while the 10.X.X.X will be configured by you in the lab.
!!!

---

### :icon-checkbox: Success Criteria

#### Primary Goals
1. **Connectivity Test**: Bob and Linda can ping each other (same VLAN)
2. **Isolation Test**: Alice cannot ping Bob or Linda (different VLANs)

#### Stretch Goals
- Add descriptive port descriptions to all switchports
- Configure VLAN 99 SVI on the switch (`interface Vlan99`) with IP `10.1.99.10/24`
- Take a packet capture of successful pings between Bob & Lind with `tcpdump interface ethernet1 filter icmp`

---
### :icon-terminal: Verification Commands
+++ Switch Verification
```bash 
# Show configured VLANs
show vlan [brief]

# Show interface status, duplex, speed, and more
show interfaces status

# Show MAC address forwarding table
show mac address-table

# Show configured IP interfaces
show ip interface brief
```
+++ Host Verification
```bash
# Show configured IP information
ifconfig

# Send ICMP echo requests
ping 10.1.10.20
```
+++

---

!!!tip
Confused about syntax?  Hitting `?` will show you all the available options for the next word in your command sequence.  You can even use it after a letter to see which options start with that letter. `c?` will show all options starting with "c"
!!!

### :icon-key: Key Concepts

#### Why VLANs Matter
- **Security**: Network segmentation isolates traffic
- **Broadcast Control**: Reduces broadcast domain size
- **Organization**: Logical grouping of devices

#### Questions to Explore
- What happens when you send a broadcast in VLAN 10?
- How does the switch learn where each host is located?
- Why does interface VLAN 99 say **down**/**lowerlayerdown** in the output of `show interfaces status`?
- When you took the `tcpdump` what parts of the output can you identify?

!!!tip Linux host tips
* Can't SSH to hosts? Use: `docker exec -it <container-name> bash`
* Set static IP: `sudo ip addr add 10.1.10.10/24 dev eth1`
!!!

==- :books: Reference Material
[EOS 4.34.1F - Layer 2 Configuration | Virtual VLANs (VLANS)](https://www.arista.com/en/um-eos/eos-virtual-lans-vlans)
===
