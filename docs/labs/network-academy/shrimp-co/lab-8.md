---
icon:
order: 930
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 8 - STP, Summarization, & DHCP
> Shrimp Co.'s growing workforce requires more efficient Layer 2 resilience, streamlined routing, and simplified host IP management. Configure spanning-tree for redundancy, summarize routes for scalability, and deploy DHCP services for VLANs across the network.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/08_stp-summarization-dhcp.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

## :icon-tasklist: Configuration Tasks

### Layer 2 Configuration
Ensure trunk links, VLAN databases, SVIs, port-channels are configured from previous labs. Confirm Layer 2 reachability for the following VLANs:
- **VLAN 10** – Sales
- **VLAN 20** – Engineering
- **VLAN 30** – Marketing *(New)*
- **VLAN 99** – IT

### 1. Configure Rapid Per-VLAN Spanning-Tree
- Enable **PVST+** on all switches.
- Assign root bridge priorities so that:
  - `sea-mdf-dsw1` is the root for VLANs 10 and 99
  - `sea-mdf-dsw2` is the root for VLANs 20 and 30
- Verify root bridge election and port roles using the appropriate show commands.

### 2. Configure Distribution Switches
- Configure VRRP according to the diagram, load-balancing active gateways between `dsw1` and `dsw2` and implementing md5 authentication
- On `sea-mdf-dsw1` and `sea-mdf-dsw2`, use a single network statement to advertise a single summary route into OSPF.
- Ensure summary route is seen on `sea-mdf-r1` and `sea-mdf-r2`
- Utilize `passive-interface default` under your OSPF process configuration and enable adjacencies on only on Eth5, Eth6, and VLAN 99.

### 3. Configure Routers
- Configure OSPF to form adjacencies on Eth0/1 and Eth0/2
- Originate a default route into OSPF with a next-hop of your ISP
- Set up a DHCP server and pool for each VLAN (10, 20, 30, 99) on either `sea-mdf-r1` or `sea-mdf-r2`.
- Use **DHCP relay** on SVI's to forward client requests to your DHCP server.
- Exclude gateway IP addresses from your DHCP pools.

!!!tip Linux host DHCP commands
* `sudo dhclient -v eth1` - Requests DHCP lease + verbose output flag
* `sudo dhclient -r eth1` - Release existing lease
!!!

## :icon-check-circle: Success Criteria

+++ Primary Goals
- **Spanning Tree**
    - `sea-mdf-dsw1` is root bridge for VLAN 10,20
    - `sea-mdf-dsw2` is root bridge for VLAN 30,99
- **Routing**
    - Routers are learning a single summary route for all four departments at Shrimp Co.
    - All Loopbacks are reachable from hosts.
    - You can curl http://seamart.com from a host that got DHCP
- **DHCP**
    - All hosts receive a DHCP lease
+++ Stretch Goals
- Capture and inspect a DHCP Discover and Offer exchange in a tcpdump on an access switch | `tcpdump interface [ethernet X] filter udp`
- Configure OSPF with neighbor authentication
- Exclude .1 through .9 from being handed out as DHCP addresses and set a lease time of one day.
+++

## :icon-terminal: Verification Commands

+++ Switch Commands
```bash
# Spanning-tree verification
show spanning-tree vlan [10]
show spanning-tree [detail]
show spanning-tree blockedports
```

+++ Router Commands
```bash
# OSPF 
show ip ospf database summary
show ip route ospf
show ip ospf neighbor
show ip ospf interface [Ethernet5]

# DHCP bindings
show ip dhcp binding

```

+++ Host Commands
```bash
# IP and default gateway
ip route show

# Gateway reachability
ping <default_gateway>

# Internet reachability test
curl http://seamart.com
```
+++

### Questions to Explore
* How does spanning-tree decide which ports go into a blocking state?  
* What is the benefit of route-summarization?
* Why is it necessary for us to run OSPF in this environment over EIGRP?
* Are there other routing protocols that could also work?

==- Documentation
* [EOS - Spanning Tree Configuration](https://www.arista.com/en/um-eos/eos-l2-stp)
* [EOS - DHCP Relay](https://www.arista.com/en/um-eos/eos-ip-addressing-and-hostname)
* [EOS - OSPF Summarization](https://www.arista.com/en/um-eos/eos-ip-routing)
* [IOS - DHCP Server Configuration](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_dhcp/configuration/xe-3se/3650/dhcp-xe-3se-3650-book/config-dhcp-server.pdf)
===