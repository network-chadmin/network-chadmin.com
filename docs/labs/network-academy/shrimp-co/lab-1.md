---
title: Lab 1 - VLANs
icon:
order: 1000
---
![](https://raw.githubusercontent.com/network-chadmin/network-chadmin.github.io/refs/heads/main/static/shrimpco-banner.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Understanding broadcast domains and network segmentation
</p>


==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/01_vlans.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the <strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;">Diagrams</a></strong> folder on my Github</em>
</p>
===

## :icon-tasklist: Configuration Tasks

### Host IP Addresses & Switchport Assignment

| Host | Interface | IP Address | VLAN Assignment |
|------|-----------|------------|-----------------|
| Bob | Eth0/1 | 10.1.10.10/24 | 10 (Sales) |
| Linda | Eth0/2 | 10.1.10.20/24 | 10 (Sales) |
| Alice | Eth0/3 | 10.1.20.10/24 | 20 (Engineering) |

### VLAN Configuration

Create and configure the following VLANs:

- **VLAN 10** - Sales Department
- **VLAN 20** - Engineering Department  
- **VLAN 99** - Network Management

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

```cisco
# Show configured VLANs
show vlan brief

# Show interface status and VLAN membership
show interfaces status

# Show MAC address table
show mac address-table
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