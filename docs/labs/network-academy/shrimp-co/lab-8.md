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
Ensure trunk links, VLAN databases, SVIs, and port-channels are configured from previous labs. Confirm Layer 2 reachability for the following VLANs:
- **VLAN 10** – Sales
- **VLAN 20** – Engineering
- **VLAN 30** – Marketing *(New)*
- **VLAN 99** – IT

### 1. Configure Spanning Tree (RSTP or PVST+)
- Enable **RSTP** or **PVST+** on all switches.
- Assign root bridge priorities so that:
  - `sea-mdf-dsw1` is the root for VLANs 10 and 99
  - `sea-mdf-dsw2` is the root for VLANs 20 and 30
- Verify root bridge election and port roles using the appropriate show commands.

### 2. Configure Route Summarization on Routers
- On `sea-mdf-r1` and `sea-mdf-r2`, summarize all access VLAN networks into **10.1.0.0/16** before advertising into OSPF.
- Ensure only summarized routes are seen by upstream peers.

### 3. Configure DHCP on Routers
- Configure a DHCP pool for each VLAN (10, 20, 30, 99) on `sea-mdf-r1` and `sea-mdf-r2`.
- Use **DHCP relay** on each distribution switch SVI to forward client requests to both routers.
- Exclude gateway IP addresses and any reserved host IPs.

### 4. Verification
- Confirm that hosts in each VLAN receive correct IP, gateway, and DNS information.
- Validate route summarization in OSPF.
- Test spanning-tree failover by shutting down a forwarding link.

---

## :icon-check-circle: Success Criteria

+++ Primary Goals
- **Spanning Tree**
    - Correct root bridge election per VLAN
    - No Layer 2 loops, redundant links in blocking/alternate state
- **Route Summarization**
    - Routers advertise a single summary route for access VLANs into OSPF
- **DHCP**
    - All VLANs receive DHCP leases from routers via relay
+++ Stretch Goals
- Test DHCP failover by shutting down one router’s DHCP service
- Observe STP convergence time after link failure
- Capture and inspect a DHCP Discover and Offer exchange
+++

---

## :icon-terminal: Verification Commands

+++ Switch Commands
```bash
# Spanning-tree verification
show spanning-tree
show spanning-tree vlan 10
show spanning-tree vlan 20
show spanning-tree vlan 30
show spanning-tree vlan 99

# DHCP relay status
show ip interface brief
```

+++ Router Commands
```bash
# OSPF route summary
show ip ospf database summary

# DHCP bindings
show ip dhcp binding

# Routing table
show ip route ospf
```

+++ Host Commands
```bash
# IP and default gateway
ip a

# Gateway reachability
ping <default_gateway>

# Internet reachability test
curl http://123.123.123.123
```
+++

---

### Questions to Explore
* How does spanning-tree decide which ports go into a blocking state?  
* What are the trade-offs between PVST+ and RSTP?  
* Why is route summarization beneficial in large OSPF domains?  
* How does DHCP relay know where to forward client requests?  
* What happens to DHCP clients if both routers’ DHCP services fail?

---

==- Documentation
* [EOS - Spanning Tree Configuration](https://www.arista.com/en/um-eos/eos-l2-stp)
* [EOS - DHCP Relay](https://www.arista.com/en/um-eos/eos-ip-addressing-and-hostname)
* [EOS - OSPF Summarization](https://www.arista.com/en/um-eos/eos-ip-routing)
===