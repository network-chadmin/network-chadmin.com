---
icon:
order: 980
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 3 - Router-on-a-stick
> Users are complaining they can't access servers in other departments. Deploy a router-on-a-stick solution to enable communication between VLANs

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/03_router-on-a-stick.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

## :icon-tasklist: Configuration Tasks

### VLAN Configuration

Create and configure the following VLANs on **both switches**:

- **VLAN 10** - Sales Department
- **VLAN 20** - Engineering Department  
- **VLAN 99** - Network Management

### Host & Access Port Configuration

| **Host** | **Interface** | **IP Address** | **VLAN Assignment** |
|------|-----------|------------|-----------------|
| Bob | Eth1 | 10.1.10.10/24 | 10 (Sales) |
| Alice | Eth2 | 10.1.20.10/24 | 20 (Engineering) |
| Steve | Eth1 | 10.1.99.100/24 | 99 (IT) |
| Linda | Eth2 | 10.1.10.20/24 | 10 (Sales) |

### Switchport Configuration

- Configure the inter-switch link (Eth0/3 on both switches) as an **802.1Q trunk** to carry only VLANs 10,20, and 99.
- Configure VLAN 99 SVI for sea-a1-asw1 - **10.1.99.10/24**
- Configure VLAN 99 SVI for sea-b1-asw1 - **10.1.99.20/24**

### Router Configuration

- Configure gateways as subinterfaces for inter-VLAN routing:
    - Eth1.10 with IP **10.1.10.1/24**
    - Eth1.20 with IP **10.1.20.1/24**
    - Eth2.99 with IP **10.1.99.1/24**
- Configure Loopback0 with IP **10.255.1.1/32**

!!!primary

While traditionally "router-on-a-stick" refers to a single physical interface carrying multiple tagged VLANs, in this lab `sea-mdf-r1` utilizes two physical interfaces (`Eth1` and `Eth2`) in a "multi-stick" design.  This was done for diagram symmetry and doesn't necessarily reflect a best-practice design.
!!!

---

## :icon-check-circle: Success Criteria

+++ Primary Goals
- Bob and Alice (Inter-VLAN routing working)
- All hosts can ping their respective gateways
- Ping Loopback0 from Linda
+++ Stretch Goals
- Run `sudo tcpdump -i eth1 -n` on Bob.  Ping Bob from Linda and Alice.
- Configure router with SSH access and local user account other than admin, SSH to it from Steve
+++

## Verification Commands

```cisco
# === Switch Verification ===

# Show VLAN configuration
show vlan brief

# Show trunk interfaces and allowed VLANs
show interfaces trunk

# Show interface status and mode
show interfaces status

# Show MAC address table
show mac address-table

# === Router Verification ===

# Show ARP table

show ip arp

# Show routing table

show ip route
```

---

### Questions to Explore
- Why does router-on-a-stick use sub-interfaces instead of separate physical connections?
- What kind of routes are each subnet in the routing table?
- What happens to the VLAN tags when traffic reaches the router sub-interface?
- Why do hosts need default gateways configured now when they didn't before?
- What's the purpose of an ARP table? Where is it found?
- Imagine the scenario: Steve pings Loopback0 on sea-mdf-r1, when the router goes to send the return traffic how will the L2 & L3 headers look? What does the router use to build it?
- When you ran tcpdump on Bob, what types of traffic did you see?

!!!tip "Host Access"
If SSH isn't working: `docker exec -it <container-name> bash`

Configure static IP: `sudo ip addr add 10.1.10.10/24 dev eth1`

*Note: This is the last time I will call out the host acccess and configuration commands.  Either refer back or take notes for future labs!*
!!!