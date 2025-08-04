---
icon:
order: 970
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 4 - Redundancy
> As Shrimp Co. grows, network reliability becomes critical. Deploy HSRP and EtherChannel to eliminate single points of failure in your network design.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/04_basic-redundancy.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

## :icon-tasklist: Configuration Tasks

### 1. Host & Access Port Configuration

| **Host** | **VLAN Assignment** | **IP Address** |
|------|------|------|
| Bob | 10 (Sales) | 10.1.10.10/24 |
| Alice | 20 (Engineering) | 10.1.20.10/24 |
| Steve | 99 (IT) | 10.1.99.100/24 |
| Linda | 10 (Sales) | 10.1.10.20/24 |

### 2. Switch Configuration

- Configure VLANs per previous labs
- Bundle Eth3 and Eth4 on both switches as an LACP port-channel in `mode active`
    - Allow all VLANs to traverse the link
- Configure both switches SVI's per diagram
- Ensure L2 reachability to router sub-interfaces

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

!!!warning
VRRP requires `ip routing` be enabled to function
!!!

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

```bash Base VRRP Config
ip routing
!
interface eth1.10
 vrrp 10 ipv4 10.1.10.1
```

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
-   **EtherChannel Status:** The inter-switch Port-Channel (Po1) has both Eth3 & Eth4 as bundled and In-Sync.
-   **Redundancy Test:**
    -   Shut down the active VRRP interface on `sea-mdf-r1` for VLAN 10; verify `sea-mdf-r2` becomes Master for VLAN 10, and Bob can still ping his gateway and Alice.
    -   Bring the interface back up and verify `sea-mdf-r1` preempts and becomes Master again.
+++ Stretch Goals
-   Configure **VRRP authentication** (e.g., plaintext or MD5) on all groups.
-   Configure routers with a username other than `admin` and an encrypted password. SSH to them from Steve.
-   Configure ACL **ALLOW_ICMP** on both `sea-a1-asw1` & `sea-b1-asw1`  Eth5 allowing ICMP from any source to any destination outbound.  Can Steve still SSH to `sea-mdf-r1`?  Why or why not? 
- Capture VRRP traffic with `tcpdump`.  What is the destination IP address for that traffic?
+++

!!!info "Testing VRRP Failover"
After configuring VRRP:
1. Use `ping 10.1.10.1` from Bob
2. Shut down the VLAN 10 subinterface on `sea-mdf-r1`
3. Did Bob drop any pings?
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
show port-channel dense
show interfaces Port-Channel1
```
+++ Router Verification
```bash
# Show VRRP status for all interfaces
show vrrp [brief]

# Show subinterfaces and their IPs
show ip interface brief

# Show ARP and routing table
show ip arp
show ip route
```

### Questions to Explore
* Steve can ping 10.255.1.1 (Loopback0 on `sea-mdf-r1`), but not 10.255.1.2.  Why?
What would need to change in the network for both pings to succeed?
* If the gateway IP address stays the same, how do your host's traffic arrive at the active interface?

+++
=== Documentation
[EOS 4.34.1F - Layer 2 Configuration | Virtual VLANs (VLANS)](https://www.arista.com/en/um-eos/eos-virtual-lans-vlans)
[EOS 4.34.1F - Interface Configuration | Port Channels and LACP)](https://www.arista.com/en/um-eos/eos-port-channels-and-lacp)
[EOS 4.34.1F User Manual - VRRP and VARP](https://www.arista.com/en/support/advisories-notices/end-of-support/77-support/quick-start-guide/um-eos?start=70)
===

