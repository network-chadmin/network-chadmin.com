---
icon:
order: 990
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 2 - Trunks
> Construction of IDF-B on the second floor is finished. Configure the new switch and ensure layer 2 is properly spanned between floors.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/02_trunks.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

### :icon-tasklist: Configuration Tasks

#### VLAN Configuration

Create and configure the following VLANs on **both switches**:

- **VLAN 10** - Sales
- **VLAN 20** - Engineering 
- **VLAN 99** - IT

#### Host & Access Port Configuration

| **Host** | **VLAN Assignment** | **IP Address** |
|------|------|------|
| Bob | 10 (Sales) | 10.1.10.10/24 |
| Alice | 20 (Engineering) | 10.1.20.10/24 |
| Steve | 99 (IT) | 10.1.99.100/24 |
| Linda | 10 (Sales) | 10.1.10.20/24 |

#### Switchport Configuration

- Configure the inter-switch link (Eth0/3 on both switches) as an **802.1Q trunk** to carry only VLANs 10,20, and 99.
- Configure VLAN 99 SVI for sea-a1-asw1 - **10.1.99.10/24**
- Configure VLAN 99 SVI for sea-b1-asw1 - **10.1.99.20/24**

---

### :icon-check-circle: Success Criteria

+++ Primary Goals
- Bob and Linda can ping each other (both in VLAN 10)
- Alice cannot ping Bob or Linda (Different VLANs)
- sea-a1-asw1 can ping sea-b1-asw1 VLAN 99 SVI
- Steve can ping both VLAN SVIs
- VLAN database consistent across both switches
+++ Stretch Goals
- SSH to sea-a1-asw1 from Steve
- SSH to sea-b1-asw1 from sea-a1-asw1
- Configure port descriptions for all interfaces
- Configure access-list `DENY_ICMP` and apply it so you cannot ping between VLAN 99 SVIs, but you can SSH
+++

### Verification Commands

```bash eos
# Show VLAN configuration
show vlan brief

# Show trunk interfaces and allowed VLANs
show interfaces trunk

# Show interface status and mode
show interfaces status

# Show MAC address table
show mac address-table [dynamic]
```

---

### Questions to Explore
- Why do we need trunks? What problem do they solve?
- Why do we specify the VLANs on a trunk instead of allowing all?
- Run `show spanning-tree vlan 10` before and after you remove it from the trunk on one side, what changes?
- If Linda can't ping Bob, what are the possible causes?

!!!tip "Host Access"
If SSH isn't working: `docker exec -it <container-name> bash`

Configure static IP: `sudo ip addr add 10.1.10.10/24 dev eth1`
!!!

=== :books: Documentation
* [EOS 4.34.1F - Layer 2 Configuration | Virtual VLANs (VLANS)](https://www.arista.com/en/um-eos/eos-virtual-lans-vlans)
* [EOS 4.34.1F - ACLs & Route-Maps](https://www.arista.com/en/um-eos/eos-acls-and-route-maps#xx1151204)
===