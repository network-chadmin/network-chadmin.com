---
icon:
order: 1000
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 1  - VLANs
> Shrimp Co. is opening their first office in Seattle, WA, and you're hired as their network administrator. The switch is racked in IDF-A and it's up to you to set up basic layer 2 segmentation

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/01_vlans.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

## :icon-tasklist: Configuration Tasks

### VLAN Configuration

Create and configure the following VLANs:

- **VLAN 10** - Sales Department
- **VLAN 20** - Engineering Department  
- **VLAN 99** - Network Management

### Host IP Addresses & Switchport Assignment

| Host | Interface | IP Address | VLAN Assignment |
|------|-----------|------------|-----------------|
| Bob | Eth1 | 10.1.10.10/24 | 10 (Sales) |
| Linda | Eth2 | 10.1.10.20/24 | 10 (Sales) |
| Alice | Eth3 | 10.1.20.10/24 | 20 (Engineering) |

!!!warning

Hosts and devices in the diagram have **two** IP addresses shown. 172.25.20.X is a docker management subnet that's pre-configured and allows you SSH access, while the 10.X.X.X will be configured by you in the lab.
!!!

---

## :icon-checkbox: Success Criteria

### Primary Goals
1. **Connectivity Test**: Bob and Linda can ping each other (same VLAN)
2. **Isolation Test**: Alice cannot ping Bob or Linda (different VLANs)

### Stretch Goals
- Add descriptive port descriptions to all switchports
- Configure VLAN 99 management interface (10.1.99.10/24)
- Make host IP configurations persistent across reboots

---

## :icon-terminal: Verification Commands

```eos
# Show configured VLANs
show vlan brief

# Show interface status and VLAN membership
show interfaces status

# Show MAC address table
show mac address-table

# Show configured IP interfaces
show ip interface brief
```

---

## :icon-key: Key Concepts

### Why VLANs Matter
- **Security**: Network segmentation isolates traffic
- **Broadcast Control**: Reduces broadcast domain size
- **Organization**: Logical grouping of devices

### Understanding the Topology
```
VLAN 10 (Sales)    │  VLAN 20 (Engineering)
Bob ←→ Linda       │  Alice (isolated)
```

### Questions to Explore
- What happens when you send a broadcast in VLAN 10?
- How does the switch learn where each host is located?
- Why might VLAN 99 SVI show "status down" initially?

!!!tip "Quick Access"
Can't SSH to hosts? Use: `docker exec -it <container-name> bash`

Set static IP: `sudo ip addr add 10.1.10.10/24 dev eth1`
!!!