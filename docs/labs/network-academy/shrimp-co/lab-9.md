---
icon:
order: 920
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 9 - Multiarea OSPF & GRE
> Shrimp Co. is opening remote offices in Milwaukee and New Orleans. Implement multi-area OSPF for scalable routing and configure GRE tunnels to connect all locations over the internet.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/09_multiareaospf-gre-mlag.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

## :icon-tasklist: Configuration Tasks

### 1. Configure OSPF Areas

* Use the following area assignments:

  * Area 0.0.0.0: Core (SEA MDF routers and DSWs)
  * Area 1: Milwaukee site
  * Area 2: New Orleans site

Include the loopbacks and GRE tunnel interfaces in the appropriate areas.

```bash
router ospf 1
 router-id 1.1.1.1
 network 10.255.1.0 0.0.0.255 area 0.0.0.0
 network 172.20.25.0 0.0.0.255 area 0.0.0.0
 network 172.16.0.0 0.0.255.255 area 1
 network 172.18.0.0 0.0.255.255 area 2
```

### 2. Configure GRE Tunnels

* Create point-to-point GRE tunnels over the public internet (using `Eth3` on MDF routers)
* Terminate tunnels between `sea-mdf-r1` ⇆ `mke-at-r1` and `sea-mdf-r2` ⇆ `msp-at-r1`
* Assign tunnel IPs from `192.168.100.0/30`
* Tunnel interfaces should be included in OSPF

```bash
interface Tunnel1
 ip address 192.168.100.1 255.255.255.252
 tunnel source Ethernet3
 tunnel destination [Remote IP]
```

Repeat on each router with appropriate local/remote IPs.

### 3. Summarize OSPF Routes

Configure route summarization on ABRs (`sea-mdf-r1` and `sea-mdf-r2`) to reduce OSPF LSAs:

```bash
router ospf 1
 area 1 range 172.16.0.0 255.255.0.0
 area 2 range 172.18.0.0 255.255.0.0
```

### 4. Configure MLAG for Access Layer

* Implement MLAG between `sea-mdf-dsw1` and `dsw2`
* Create `Port-Channel1` for MLAG peer link, and `Port-Channel10` for downstream access switches
* Use consistent LACP and VLAN settings
* MLAG ID: 10

```bash
interface Port-Channel1
 description MLAG Peer Link
 switchport
 switchport mode trunk
 mlag peer-link

interface Ethernet3
 channel-group 1 mode active
```

```bash
mlag configuration
 domain-id shrimp-co
 local-interface Vlan4094
 peer-address 10.1.1.2
 peer-link Port-Channel1
```

Configure corresponding access switches with `Port-Channel10` to both DSWs.

## :icon-check-circle: Success Criteria

+++ Primary Goals

* OSPF adjacencies established within and between areas
* Remote sites reachable via GRE tunnels
* MLAG operational between DSWs and access
+++ Stretch Goals
* Introduce BFD on GRE tunnel interfaces
* Simulate OSPF area failure and observe convergence
* Use `show mlag` and `show ip ospf database` for deep verification
* Test routing table summarization effectiveness
+++

## :icon-terminal: Verification Commands

+++ OSPF & GRE

```bash
show ip ospf neighbor
show ip route ospf
show interfaces tunnel1
```

+++ MLAG

```bash
show mlag
show mlag interfaces
```

+++ End-to-End

```bash
ping 172.16.25.67
traceroute 172.18.25.100
dig seamart.com
```

+++

==- Documentation
[EOS MLAG Configuration](https://www.arista.com/en/um-eos/eos-mlag)
[EOS OSPF Guide](https://www.arista.com/en/um-eos/eos-ip-routing)
[GRE Tunneling](https://www.arista.com/en/um-eos/eos-tunnel-interfaces)
===
