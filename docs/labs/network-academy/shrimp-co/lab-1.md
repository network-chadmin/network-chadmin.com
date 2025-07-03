---
title: Lab 1 - VLANs
icon:
order: 1000
---

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/01_vlans.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the <strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;">Diagrams</a></strong> folder on my Github</em>
</p>
===

## Basic Configuration:

- [ ] Set the static IPv4 address for Ethernet1 on each host:
  - [ ] Bob: 10.1.10.10/24
  - [ ] Linda: 10.1.10.20/24
  - [ ] Alice: 10.1.20.10/24
- [ ] Create and name VLANs
  - [ ] VLAN 10 (Sales)
  - [ ] VLAN 20 (Engineering)
  - [ ] VLAN 99 (Network Management)
- [ ] Configure Access Ports
  - [ ] Eth0/1 for Bob (Sales)
  - [ ] Eth0/2 for Linda (Sales)
  - [ ] Eth0/3 for Alice (Engineering)

!!!info "Linux Host Access & Configuration"
In case you are unable to SSH to the Linux hosts, you can `docker exec -it <container-name> bash`
Static IP and subnet mask configuration is done via `sudo ip addr add 10.1.10.10/24 dev eth1`
!!!

!!!success "Success Criteria"
1. Bob and Linda can ping each other (both in VLAN 10)
2. Alice cannot ping Bob or Linda (different VLANs)
!!!

## Stretch Goals:

- [ ] Add port descriptions to all switchports
- [ ] Configure a VLAN interface for VLAN 99 (10.1.99.10/24)
- [ ] Configure each Linux host's IPv4 address in a manner that would persist across reboots

!!!warning "Questions"
- What are a few reason we use VLANs?
- What is a broadcast domain?
- How does a switch know where a particular host lives?
- Why does the Vlan99 SVI show "status down"?
!!!

==- Verification Commands
```cisco
# Show configured VLANS
show vlan brief
```

```cisco
# Show interface connection status, VLAN membership, and more
show interfaces status
```

```cisco
# Show mac forwarding table
show mac address-table
```
===

==- Reference
[Reference content would go here]
===