---
icon:
order: 940
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 7 - PAT & Dynamic Routing
> Shrimp Co. requires enterprise-grade infrastructure for reliable operations. Implement full redundancy with dual distribution switches, OSPF dynamic routing, and PAT for scalable internet access.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/07_pat-dynamic-routing.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

## :icon-tasklist: Configuration Tasks

### Layer 2 Configuration

Ensure trunk links, VLAN databases, SVIs, and port-channels are configured to provide Layer 2 reachability between all access and distribution switches.

### 1. Configure Gateway SVIs (VRRP)

On both distribution switches (`sea-mdf-dsw1` and `sea-mdf-dsw2`), configure SVIs with the following addresses and enable VRRP:

* VLAN 10: 10.1.10.1 (VRRP VIP)
* VLAN 20: 10.1.20.1 (VRRP VIP)
* VLAN 99: 10.1.99.1 (VRRP VIP)

Assign actual IPs to the switches (.2 and .3) and ensure VRRP priority is configured to prefer `dsw1`.

### 2. Enable OSPF on Routers and Distribution Switches

Use OSPF process ID 1 across all routing devices. Include the following interfaces:

* All point-to-point links between routers and distribution switches
* Loopback interfaces
* VLAN interfaces (on DSWs only)

Use OSPF area 0.0.0.0 for all interfaces in this lab.

```bash
router ospf 1
 network 10.1.10.0 0.0.0.255 area 0.0.0.0
 network 10.1.20.0 0.0.0.255 area 0.0.0.0
 network 10.1.99.0 0.0.0.255 area 0.0.0.0
 network 10.255.1.0 0.0.0.255 area 0.0.0.0
 network 172.20.25.0 0.0.0.255 area 0.0.0.0
```

### 3. Configure PAT on Routers

On both `sea-mdf-r1` and `sea-mdf-r2`, configure PAT using the following:

```bash
ip access-list standard NAT_INSIDE
 permit 10.1.0.0 0.0.255.255

ip nat inside source list NAT_INSIDE interface Ethernet3 overload
```

* Mark `Eth3` as `ip nat outside`
* Mark interface facing internal subnets (DSWs) as `ip nat inside`

Ensure only one router is actively used via default route advertisement in OSPF, or configure FHRP tracking/failover in stretch goals.

### 4. OSPF Default Route Injection

Inject a default route from each router into OSPF:

```bash
ip route 0.0.0.0 0.0.0.0 [next-hop]
router ospf 1
 default-information originate
```

This enables internet access through dynamically chosen paths.

## :icon-check-circle: Success Criteria

+++ Primary Goals

* VRRP functioning across VLAN gateways
* Full OSPF adjacency and convergence
* Hosts can access the SeaMart web server through PAT
* Default route is learned dynamically from OSPF
  +++

+++ Stretch Goals

* Configure route preference using OSPF cost
* Use `track` to monitor upstream interfaces and trigger VRRP failover
* Log NAT translations and verify with `tcpdump`
* Disable default-information originate on one router and observe failover
* Access OSPF database and LSAs to validate topology propagation
  +++

## :icon-terminal: Verification Commands

+++ Router Commands

```bash
# NAT translations
show ip nat translations

# OSPF neighbor relationships
show ip ospf neighbor

# Default route in routing table
show ip route
```

+++ Switch Commands (DSW)

```bash
# Verify VRRP
show vrrp

# Check OSPF status
show ip ospf interface brief
```

+++ Host Commands

```bash
curl http://123.123.123.123
traceroute 123.123.123.123
dig seamart.com
```

+++

==- Documentation
* [EOS 4.34.1F - OSPF Configuration](https://www.arista.com/en/um-eos/eos-ip-routing)
* [EOS 4.34.1F - NAT Configuration](https://www.arista.com/en/um-eos/eos-nat)
===

