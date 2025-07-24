---
icon:
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

## :icon-tasklist: Configuration Tasks

### Layer 2 Configuration

Ensure trunk links, VLAN databases, SVIs, and port-channels are configured across the switches and connected to `sea-mdf-dsw1`. VLANs 10, 20, and 99 should have Layer 2 reachability to the DSW.

### 1. Distribution Switch IP Interfaces

Configure the following SVIs on `sea-mdf-dsw1`:

* VLAN 10: 10.1.10.1/24
* VLAN 20: 10.1.20.1/24
* VLAN 99: 10.1.99.1/24

Verify host reachability to their respective gateway:

* Bob: 10.1.10.10
* Alice: 10.1.20.10
* Steve: 10.1.99.100

### 2. Static Routes (Routers to Distribution Switch)

On `sea-mdf-r1` and `sea-mdf-r2`, add static routes for internal VLANs:

```bash
ip route 10.1.10.0/24 10.1.1.1
ip route 10.1.20.0/24 10.1.1.1
ip route 10.1.99.0/24 10.1.1.1
```

Assume the internal-facing interfaces on each router are:

* `sea-mdf-r1`: `Eth1`, IP 10.1.1.2/30
* `sea-mdf-r2`: `Eth1`, IP 10.1.1.6/30

### 3. Default Route on Distribution Switch

Configure `sea-mdf-dsw1` with default routes to each router:

```bash
ip route 0.0.0.0/0 10.1.1.2
ip route 0.0.0.0/0 10.1.1.6
```

This allows hosts to access the internet via either router (basic redundancy).

### 4. Dynamic NAT Configuration

On `sea-mdf-r1`, configure dynamic NAT using an access list and a NAT pool:

```bash
ip access-list standard NAT_VLAN99
 permit 10.1.99.0/24

ip nat pool PUBLIC_POOL 100.10.1.5 100.10.1.10 netmask 255.255.255.240
ip nat inside source list NAT_VLAN99 pool PUBLIC_POOL overload
```

* Mark `Eth2` as `ip nat outside`
* Mark interface facing VLAN 99 as `ip nat inside`

Repeat on `sea-mdf-r2` (optional for redundancy).

### 5. Verification

* Confirm internal host `Steve` can access the SeaMart server (`curl http://123.123.123.123`)
* Confirm NAT translation is working with `show ip nat translations`
* Confirm static routes are working by tracing traffic between VLANs

## \:icon-check-circle: Success Criteria

+++ Primary Goals

* Hosts can reach their default gateway on `sea-mdf-dsw1`
* Steve can access the SeaMart web server
* NAT translations occur via dynamic NAT using a pool
* Static routing works between routers and DSW

+++ Stretch Goals

* Configure NAT redundancy via VRRP and `track` interface for priority failover
* Implement an ACL that limits internet access to VLAN 99
* Use `tcpdump` on Arista routers to verify translation behavior
* Add logging for NAT ACL hits
* Ping the public SeaMart server from each host and verify path via traceroute
  +++

## \:icon-terminal: Verification Commands

+++ Router Commands

```bash
# NAT translations
show ip nat translations

# Check static routes
show ip route

# Check NAT interface configuration
show run interface Ethernet2
```

+++ Switch Commands (DSW)

```bash
# Routing table
show ip route

# Interface and SVI status
show ip interface brief
```

+++ Host Commands

```bash
# Web test
curl http://123.123.123.123

# DNS test (if configured)
dig seamart.com

# Network trace
traceroute 123.123.123.123
```

+++

==- Documentation
* [EOS 4.34.1F - Static Routing](https://www.arista.com/en/um-eos/eos-ip-routing)
* [EOS 4.34.1F - NAT Configuration](https://www.arista.com/en/um-eos/eos-nat)
===

