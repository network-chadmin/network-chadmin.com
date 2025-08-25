---
title: Lab 5 - NAT & Internet
icon: dash
order: 960
---

![](/static/network-academy/shrimpco/banner.png)

# Lab 5 - NAT & Internet

> Shrimp Co. has connected to the internet via Volt Communications. Configure static NAT so Bob in Sales can reach the SeaMart web server while maintaining internal addressing.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/05_nat-internet.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

### :icon-tasklist: Configuration Tasks

#### 1. Layer 2 Configuration

Configure VLAN databases, port-channels, trunk links, and SVIs as done in previous labs to ensure L2 connectivity between hosts and their gateways. These tasks are assumed and not explicitly outlined below.

#### 2. Router Configuration

* Configure the WAN interfaces on `sea-mdf-r1` and `sea-mdf-r2`:

  * `Eth0/2` on both routers
  * Use provider-assigned space: `100.10.1.0/29` & `100.10.1.8/29`
  * `sea-mdf-r1`: IP - `100.10.1.2/29` Gateway - `100.10.1.1`
  * `sea-mdf-r2`: IP - `100.10.1.10/29` Gateway - `100.10.1.9`
  * ISP gateway: `100.10.1.1`
* Verify `ping 100.10.1.1` works from both routers

#### 3. Static NAT

* Configure **static NAT** on `sea-mdf-r1` to allow internal host `Bob (10.1.10.10)` to access the internet with a public IP:

  * Inside IP: `10.1.10.10`
  * Outside IP: `100.10.1.2`

 !!!info ISP & Server Access
 As a way of maintaining realism, the Volt Communications router and SeaMart server cannot be SSH'd to.  If you really want to put on multiple hats, you can access the devices directly via docker commands.
 !!!

### :icon-check-circle: Success Criteria

+++ Primary Goals

- **L3 Reacability**
  * All hosts can ping each other
- **Bob's Internet Connectivity**
  - Bob can ping SeaMart server at `123.123.123.123`
- **Nat Translation**
  - NAT translation occurs correctly (seen via `show ip nat translations` or debug output)
  +++ Stretch Goals
- **Extended ACL** 
  - Configure ACL on `sea-mdf-r1` Eth0/1.10 that allows ICMP but not SSH to any destination.  Confirm by trying to SSH to Loopback0.
- **Linux Host File Configuration**
  - Find a way to add a local DNS entry to Bob's host file so he can ping seamart.com
+++

### :icon-terminal: Verification Commands

+++ Router (IOS)

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

+++ Host (Linux)

```bash
# Check connectivity
curl http://123.123.123.123

# Trace path
traceroute 123.123.123.123

# Monitor packets
sudo tcpdump -i eth1 -n
```

+++

### :icon-question: Questions to Explore

- Why do pings fail to the SeaMart server before NAT is configured?
- If more employees want to access the internet, what problem do we run into?
- Is static NAT a scalable solution?  Why or why not?

!!!info
In these labs, the SeaMart web server and ISP router are not meant to be configured by students. If you'd like to break the immersion and access the devices you can do so using docker commands.
!!!

==- :books: Documentation
* [IP Addressing: NAT Configuration Guide, Cisco IOS XE Release 2](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_nat/configuration/xe-2/nat-xe-2-book/iadnat-addr-consv.html#GUID-DB135563-E01D-4C49-AD69-F95B3F1C5E67) (Go to "Configuring Static Translation of Inside Source Addresses)
===
