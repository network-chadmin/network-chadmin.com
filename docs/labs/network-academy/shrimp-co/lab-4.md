---
icon:
order: 970
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 4 - Basic L2-L3 Redundancy
> As Shrimp Co. grows, network reliability becomes critical. Deploy HSRP and EtherChannel to eliminate single points of failure in your network design.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/04_basic-redundancy.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

## 🆕 New Concepts Introduced
- Redundant Layer 3 gateways with VRRP
- EtherChannel bundling and trunking
- VRRP failover testing with preemption
- Basic traffic filtering with ACLs
- Optional: VRRP authentication & interface tracking

## :icon-tasklist: Configuration Tasks

### 1. Host & Access Port Configuration

| **Host** | **Interface** | **IP Address** | **VLAN Assignment** |
|------|-----------|------------|-----------------|
| Bob | Eth1 | 10.1.10.10/24 | 10 (Sales) |
| Alice | Eth2 | 10.1.20.10/24 | 20 (Engineering) |
| Steve | Eth1 | 10.1.99.100/24 | 99 (IT) |
| Linda | Eth2 | 10.1.10.20/24 | 10 (Sales) |

### 2. Switch Configuration

- Configure VLANs per previous labs
- Bundle Eth3 and Eth4 on both switches as an LACP port-channel in `mode active`
    - Allow all VLANs to traverse the link
- Configure both switches with a SVI
- Allow VLANs to reach router sub-interfaces

### 3. Router Configuration

- **Loopback Interfaces:**
    - `sea-mdf-r1`: `Loopback0` with IP **10.255.1.1/32**
    - `sea-mdf-r2`: `Loopback0` with IP **10.255.1.2/32**
- **Subinterfaces** on both routers, IP addresses according to diagram
    - `Eth1.10` (VLAN 10)
    - `Eth1.20` (VLAN 20)
    - `Eth2.99` (VLAN 99)

### 4. VRRP Configuration (`sea-mdf-r1` & `sea-mdf-r2`)

Configure VRRP on the physical subinterfaces to provide redundant gateways:

- **VRRP Group Numbers:** Use VRRP group numbers that match the VLAN IDs (e.g., VLAN 10 uses Group 10).
- **Virtual IP Addresses (VIPs):**
    - VLAN 10: **10.1.10.1/24**
    - VLAN 20: **10.1.20.1/24**
    - VLAN 99: **10.1.99.1/24**
- **Active/Standby Roles (via Priority):**
    - **`sea-mdf-r1`:** Set higher priority for VLANs 10 and 99.
        - VLAN 10: Master (e.g., Priority 120)
        - VLAN 20: Backup (e.g., Priority 90)
        - VLAN 99: Master (e.g., Priority 120)
    - **`sea-mdf-r2`:** Set higher priority for VLAN 20.
        - VLAN 10: Backup (e.g., Priority 90)
        - VLAN 20: Master (e.g., Priority 120)
        - VLAN 99: Backup (e.g., Priority 90)
## :icon-check-circle: Success Criteria

+++ Primary Goals
-   **Inter-VLAN Connectivity:**
    -   Bob can ping Alice (VLAN 10 to VLAN 20).
    -   Linda can ping Steve (VLAN 10 to VLAN 99).
-   **Gateway Reachability:** All hosts can ping their respective Virtual IP (VIP) gateways.
-   **VRRP State Verification:**
    -   `sea-mdf-r1` is the VRRP Master for VLANs 10 and 99.
    -   `sea-mdf-r2` is the VRRP Master for VLAN 20.
    -   Verify VRRP status is "Master" or "Backup" for all groups on both routers.
-   **EtherChannel Status:** The inter-switch Port-Channel (Po1) is operational and bundling interfaces.
-   **Redundancy Test:**
    -   Shut down the active VRRP interface on `sea-mdf-r1` for VLAN 10; verify `sea-mdf-r2` becomes Master for VLAN 10, and Bob can still ping his gateway and Alice.
    -   Bring the interface back up and verify `sea-mdf-r1` preempts and becomes Master again.
+++ Stretch Goals
-   Configure **VRRP authentication** (e.g., plaintext or MD5) on all groups.
-   Configure routers with a username other than `admin` and an encrypted password. SSH to them from Steve.
-   Configure an **extended ACL** on a router subinterface (e.g., `Eth2.99`) that allows ICMP inbound to the `10.1.99.0/24` network and denies everything else, then verify.
-   Implement **VRRP tracking** of a physical interface (e.g., `Loopback0`) to reduce VRRP priority if the tracked object goes down, forcing a failover.
+++

!!!info "Testing VRRP Failover"
After configuring VRRP:
1. Use `ping 10.1.10.1` from a host (e.g. Bob)
2. Shut down the VLAN 10 subinterface on `sea-mdf-r1`
3. Re-test — traffic should failover to `sea-mdf-r2` automatically
4. Use `show vrrp` on both routers to confirm role changes
!!!

## :icon-terminal: Verification Commands

+++ Switch Verification
```bash
# Show VLAN and trunk status
show vlan brief
show interfaces trunk
show interfaces status

# Show EtherChannel (Port-Channel) details
show port-channel summary
show interfaces Port-Channel1
```
+++ Router Verification
```bash
# Show VRRP status for all interfaces
show vrrp

# Show subinterfaces and their IPs
show ip interface brief

# Show ARP and routing table
show ip arp
show ip route

# Verify interface tracking (if configured)
show vrrp track
```
=== Documentation
[EOS 4.34.1F - Layer 2 Configuration | Virtual VLANs (VLANS)](https://www.arista.com/en/um-eos/eos-virtual-lans-vlans)
[EOS 4.34.1F - Interface Configuration | Port Channels and LACP)](https://www.arista.com/en/um-eos/eos-port-channels-and-lacp)
===

