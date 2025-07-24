---
title: Lab 5 - NAT & Internet
icon:
order: 960
---

![](/static/network-academy/shrimpco/banner.png)

# Lab 5 - NAT & Internet

> Shrimp Co. has connected to the internet via Volt Communications. Configure NAT so internal hosts can reach the SeaMart web server while maintaining internal addressing.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/05_nat-internet.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

## :icon-tasklist: Configuration Tasks

### Layer 2 Configuration

Configure VLAN databases, port-channels, trunk links, and SVIs as done in previous labs to ensure L2 connectivity between access switches and the MDF routers. These tasks are assumed and not explicitly outlined below.

### 1. IP Addressing & Reachability

* Configure the WAN interfaces on `sea-mdf-r1` and `sea-mdf-r2`:

  * `Eth2` on both routers
  * Use provider-assigned space: `100.10.1.0/29`
  * `sea-mdf-r1`: `100.10.1.2/29`
  * `sea-mdf-r2`: `100.10.1.3/29`
  * ISP gateway: `100.10.1.1`
* Verify `ping 100.10.1.1` works from both routers

### 2. NAT Configuration (Static NAT)

* Configure **static NAT** on `sea-mdf-r1` to allow internal host `Steve (10.1.99.100)` to access the internet with a public IP:

  * Inside IP: `10.1.99.100`
  * Outside IP: `100.10.1.2`
* Ensure return traffic is translated correctly

### 3. Default Routes

* Configure default route on `sea-mdf-r1`:

  ```
  ip route 0.0.0.0/0 100.10.1.1
  ```
* Configure **Steve** with a default gateway of `10.1.99.1`

### 4. Test Connectivity

* From **Steve**, access the SeaMart web server (`curl http://123.123.123.123`)
* Use `tcpdump` or `tshark` on `sea-mdf-r1` to verify NAT translations

## :icon-check-circle: Success Criteria

+++ Primary Goals

* Steve can `curl` the SeaMart server by IP
* NAT translation occurs correctly (seen via `show ip nat translations` or debug output)
* `sea-mdf-r1` has connectivity to the upstream ISP
  +++ Stretch Goals
* Configure an **ACL** to only allow NAT for VLAN 99
* Block `Bob` or `Alice` from accessing the internet
* Set up **VRRP** between `sea-mdf-r1` and `sea-mdf-r2` for failover
* Create a **loopback interface** on `Steve`, then configure NAT for that address too
* Use `tcpdump` to observe NAT behavior at multiple points (inside interface vs outside)
+++

## :icon-terminal: Verification Commands

+++ Router Verification

```bash
# Check NAT table
show ip nat translations

# Debug NAT events
debug ip nat

# Ping default gateway and internet server
ping 100.10.1.1
ping 123.123.123.123

# Check routing table
show ip route
```

+++ Host Verification (Steve)

```bash
# Check connectivity
curl http://123.123.123.123

# Trace path
traceroute 123.123.123.123

# Monitor packets
sudo tcpdump -i eth1 -n
```

+++

-== Documentation
* [EOS 4.34.1F - NAT Overview](https://www.arista.com/en/um-eos/eos-nat)
* [Linux curl Manual](https://curl.se/docs/manpage.html)
===

!!!info
In this lab, the SeaMart web server and ISP router are not student-configurable. They will respond to HTTP and basic ICMP traffic, but cannot be logged into or modified.
!!!
