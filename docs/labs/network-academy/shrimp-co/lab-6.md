---
icon: dash
order: 950
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 6 - Static Routing
> Shrimp Co. needs centralized routing and improved NAT efficiency. Configure distribution layer gateways with static routes and dynamic NAT for scalable internet access.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/06_static-routing.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

### :icon-tasklist: Configuration Tasks

#### 1. Layer 2 Configuration

Ensure trunk links, VLAN databases, SVIs, and port-channels are configured across the switches and connected to `sea-mdf-dsw1`. VLANs 10, 20, and 99 should have Layer 2 reachability to the DSW.

#### 2. Distribution Switch Configuration

- Configure the following SVIs on `sea-mdf-dsw1`:
  - **VLAN 10**: `10.1.10.1/24`
  - **VLAN 20**: `10.1.20.1/24`
  - **VLAN 99**: `10.1.99.1/24`
- Configure **Eth3** & **Eth4** as routed interfaces according to the diagram.
- Configure a default route that uses `sea-mdf-r1` Eth0/1 as a next-hop.
- Configure a secondary default route that uses `sea-mdf-r2` Eth0/1 as a next-hop with an administrative distance of 10.

#### 3. Router Configuration

- On `sea-mdf-r1` and `sea-mdf-r2`, add static routes to `sea-mdf-dsw1` for each subnet/VLAN.
- Configure loopback and routed interfaces according to the diagram.
- Configure **dynamic NAT** to choose from a pool of addresses (not PAT/overload).  
  Specify the exact source subnets in your access list.

!!!tip Emulator Quirks
When I ran through this lab myself I struggled to get dynamic NAT working as intended until I applied my NAT access-list inbound on `sea-mdf-r2` Eth0/1, because I wanted to see ACL hits as a sanity check.  Pings immediately started going through even after removal of the ACL.  This is not behavior you'd see on a real router and a good reminder that these emulated environments can be goofy.
!!!

### \:icon-check-circle: Success Criteria

+++ Primary Goals
- **Layer 3 Reachability:**  
  - All hosts can ping the SeaMart web server (`123.123.123.123`).
- **Dynamic NAT:**  
  - NAT translations occur correctly and can be confirmed on both routers with `show ip nat translations`.
+++ Stretch Goals
- **Extended ACL:**  
  - Implement an ACL on VLAN 10 & VLAN 20 SVIs that denies hosts in those VLANs from SSHing outside of their own subnet, but allows all other traffic.  
  - Validate by attempting to SSH from Bob or Alice to `Loopback0` on `sea-mdf-r1`.
- **Local Linux Name Server Configuration:**  
  - Edit `/etc/resolv.conf` on Linux hosts to use `8.8.8.8` as a nameserver.  
  - Validate with `curl http://seamart.com`.
- **IP SLA Failover:**  
  - Configure IP SLA so that internet-bound traffic takes the path through `sea-mdf-r2` if `100.10.1.1` is unreachable.  
    - Verify the gateway of last resort changes on `sea-mdf-dsw1` after issuing `shutdown` on Eth0/1 on `sea-mdf-r1`.
+++

### :icon-terminal: Verification Commands

+++ Router (IOS)

```bash
# NAT translations
show ip nat translations

# NAT statistics
show ip nat statistics

# Check static routes
show ip route

# Check NAT interface configuration
show run interface Ethernet2
```

+++ Distribution Switch (EOS)

```bash
# Routing table
show ip route

# Interface and SVI status
show ip interface brief

# Show configured routes in the running configuration
show running-config | include route
```

+++ Host (Linux)

```bash
# Web test
curl http://123.123.123.123

# DNS test (if configured)
nslookup seamart.com

# Network trace
traceroute 123.123.123.123
```
+++ 
### :icon-question: Questions to explore
- How does the router know which inside addresses to translate when using dynamic NAT with a pool?
- If you add a new VLAN to the network, what changes are required to allow internet access?
- Why is default route through R1 preferred over R2?
- What are some pitfalls you can imagine with the use of static routes?
- curl seamart.com and check your NAT translations. What are the differences between the addresses in the output?
+++

==- :books: Documentation
- [EOS 4.34.1F - Static Routing](https://www.arista.com/en/um-eos/eos-ip-routing)
- [EOS 4.34.1F - NAT Configuration](https://www.arista.com/en/um-eos/eos-nat)
- [How to Set DNS Nameservers on Ubuntu 22.04?](https://itslinuxfoss.com/set-dns-nameservers-ubuntu-22-04/)
===

