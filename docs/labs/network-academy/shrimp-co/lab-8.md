---
icon:
order: 930
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 8 - STP, Summarization,& DHCP
> Shrimp Co.'s growing workforce requires streamlined network operations. Optimize STP for efficient redundancy, implement route summarization for scalability, and deploy DHCP for simplified device management.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/08_stp-summarization-dhcp.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

## :icon-tasklist: Configuration Tasks

### Layer 2 Configuration

Verify port-channels, trunk links, and VLAN databases are configured from previous labs. Ensure VLANs 10, 20, and 99 have L2 reachability across the switching fabric.

### 1. Configure Spanning-Tree

Configure `mstp` on all switches:

* Define MST region `shrimp-co` with instance 1 mapping VLANs 10 and 20
* Set bridge priorities:

  * `dsw1`: priority 4096 (Root)
  * `dsw2`: priority 8192

```bash
spanning-tree mode mstp
spanning-tree mst configuration
 name shrimp-co
 revision 1
 instance 1 vlan 10,20
exit

spanning-tree mst 1 priority 4096  # On dsw1
spanning-tree mst 1 priority 8192  # On dsw2
```

Verify root bridge selection and port states using `show spanning-tree mst`.

### 2. Configure Route Summarization on Routers

Summarize access subnet advertisements from distribution switches. Use summary addresses on:

* `sea-mdf-r1` and `sea-mdf-r2` to summarize `10.1.0.0/16`

```bash
router ospf 1
 area 0.0.0.0 range 10.1.0.0 255.255.0.0
```

This reduces LSA size and keeps the OSPF database lean as the network grows.

### 3. Configure DHCP Server

Deploy a DHCP server on `sea-mdf-dsw1` for VLAN 99:

```bash
ip dhcp excluded-address 10.1.99.1 10.1.99.10
ip dhcp pool VLAN99
 network 10.1.99.0 255.255.255.0
 default-router 10.1.99.1
 dns-server 8.8.8.8
 lease 0 4 0
```

Bind DHCP to the SVI and ensure it responds to client requests from the access layer.

> Note: This DHCP configuration is for testing purposes. In production, DHCP is often centralized.

### 4. Verify Addressing

Ensure each host on VLAN 99 obtains an IP via DHCP. Validate with `show ip dhcp binding` on the DSW.

## :icon-check-circle: Success Criteria

+++ Primary Goals

* MSTP running with correct root bridge election
* Summarized OSPF routes advertised from routers
* VLAN 99 hosts receiving DHCP addresses
+++ Stretch Goals
* Observe MSTP failover by disabling a forwarding link
* Deploy DHCP relay from an access switch to test forwarding
* Run `tcpdump` on a switch and filter for DHCP
+++

## :icon-terminal: Verification Commands

+++ Switch (STP & DHCP)

```bash
show spanning-tree mst
show spanning-tree mst configuration
show ip dhcp binding
```

+++ Router (OSPF Summary)

```bash
show ip ospf database summary
show ip route ospf
```

+++ Host (from bash shell or CLI)

```bash
ip a
ping 10.1.99.1
```

+++

==- Documentation
* [EOS - MSTP Configuration](https://www.arista.com/en/um-eos/eos-l2-stp#ww1106022)
* [EOS - DHCP Server](https://www.arista.com/en/um-eos/eos-ip-addressing-and-hostname#topic14156)
* [EOS - OSPF Summarization](https://www.arista.com/en/um-eos/eos-ip-routing#topic14284)
===
