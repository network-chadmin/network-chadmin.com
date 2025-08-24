---
icon:
order: 970
---
![](/static/network-academy/shrimpco/banner.png)

# Lab 4 - Basic Redundancy
> As Shrimp Co. grows, network reliability becomes critical. Deploy HSRP and EtherChannel to eliminate single points of failure in your network design.

==- Lab Topology
![](https://raw.githubusercontent.com/network-chadmin/containerlab/refs/heads/main/network-academy/shrimp-co/diagrams/04_basic-redundancy.png)

<p style="font-style: italic; color: #6b7280; font-size: 0.875rem; margin-top: 8px; text-align: center;">
<em>Tip: Individual topology files are available in the diagrams folder on my<strong><a href="https://github.com/network-chadmin/containerlab/tree/main/network-academy/shrimp-co/diagrams" style="color: #6b7280;"> Github</a></strong></em>
</p>
===

### :icon-tasklist: Configuration Tasks

### 1. Host & Access Port Configuration

| **Host** | **VLAN Assignment** | **IP Address** |
|------|------|------|
| Bob | 10 (Sales) | 10.1.10.10/24 |
| Alice | 20 (Engineering) | 10.1.20.10/24 |
| Steve | 99 (IT) | 10.1.99.100/24 |
| Linda | 10 (Sales) | 10.1.10.20/24 |

#### 2. Switch Configuration

- Configure VLANs per previous labs
- Bundle Eth3 and Eth4 on both switches as an LACP port-channel in `mode active`
    - Allow all VLANs to traverse the link
- Configure both switches SVI's per diagram
- Ensure L2 reachability to router sub-interfaces

#### 3. Router Configuration

- **Loopback Interfaces:**
    - `sea-mdf-r1`: `Loopback0` with IP **10.255.1.1/32**
    - `sea-mdf-r2`: `Loopback0` with IP **10.255.1.2/32**
- **Subinterfaces** on both routers, IP addresses according to diagram
    - `Eth1.10` (VLAN 10)
    - `Eth1.20` (VLAN 20)
    - `Eth2.99` (VLAN 99)

#### 4. HSRP Configuration (`sea-mdf-r1` & `sea-mdf-r2`)

Configure HSRP on the physical subinterfaces to provide redundant gateways:

- **HSRP Group Numbers:** Use HSRP group numbers that match the VLAN IDs (e.g., VLAN 10 uses Group 10).
- **Virtual IP Addresses (VIPs):**
    - VLAN 10: **10.1.10.1/24**
    - VLAN 20: **10.1.20.1/24**
    - VLAN 99: **10.1.99.1/24**
- **Active/Standby Roles (via Priority):**
    - **`sea-mdf-r1`:** Set higher priority for VLANs 10 and 99.
        - VLAN 10: Active (e.g., Priority 120)
        - VLAN 20: Standby (e.g., Priority 90)
        - VLAN 99: Active (e.g., Priority 120)
    - **`sea-mdf-r2`:** Set higher priority for VLAN 20.
        - VLAN 10: Standby (e.g., Priority 90)
        - VLAN 20: Active (e.g., Priority 120)
        - VLAN 99: Standby (e.g., Priority 90)

### :icon-check-circle: Success Criteria

+++ Primary Goals
-   **Inter-VLAN Connectivity:**
    -   Bob can ping Alice (VLAN 10 to VLAN 20).
    -   Linda can ping Steve (VLAN 10 to VLAN 99).
-   **Gateway Reachability:** All hosts can ping their respective Virtual IP (VIP) gateways.
-   **HSRP State Verification:**
    -   `sea-mdf-r1` is the active for VLANs 10 and 99.
    -   `sea-mdf-r2` is the active for VLAN 20.
    -   Verify HSRP status is "Active" or "Standby" for all groups on both routers.
-   **EtherChannel Status:** The inter-switch Port-Channel (Po1) has both Eth3 & Eth4 as bundled and In-Sync.
-   **Redundancy Test:**
    -   Shut down the active HSRP interface on `sea-mdf-r1` for VLAN 10; verify `sea-mdf-r2` becomes active for VLAN 10, and Bob can still ping his gateway and Alice.
    -   Bring the interface back up and verify `sea-mdf-r1` preempts and becomes active again.
+++ Stretch Goals
-   Configure **HSRP authentication** (e.g., plaintext or MD5) on all groups.
-   Configure routers with a username other than `admin` and an encrypted password. SSH to them from Steve.
-   Configure ACL **ALLOW_ICMP** on both `sea-a1-asw1` & `sea-b1-asw1`  Eth5 allowing ICMP from any source to any destination outbound.  Can Steve still SSH to `sea-mdf-r1`?  Why or why not? 
- Capture HSRP traffic with `tcpdump` on `sea-b1-asw1`.  What is the destination IP address for that traffic?
+++

!!!info "Testing HSRP Failover"
After configuring HSRP:
1. Use `ping 10.1.10.1` from Bob
2. Shut down the VLAN 10 subinterface on `sea-mdf-r1`
3. Did Bob drop any pings?
4. Use `show standby brief` on both routers to confirm role changes
!!!

### :icon-terminal: Verification Commands

+++ Switch (EOS)
```bash
# Show VLAN and trunk status
show vlan brief
show interfaces trunk
show interfaces status

# Show EtherChannel (Port-Channel) details
show port-channel dense
show interfaces Port-Channel1
```
+++ Router (IOS)
```bash
# Show HSRP status for all interfaces
show standby [brief]

# Show subinterfaces and their IPs
show ip interface brief

# Show ARP and routing table
show ip arp
show ip route
```

#### Questions to Explore
- Steve can ping 10.255.1.1 (Loopback0 on `sea-mdf-r1`), but not 10.255.1.2.  Why?
    - What configuration changes are needed for both pings to succeed?
- If the gateway IP address stays the same, how do your host's traffic arrive at the active interface?

+++
==- :books: Documentation
- [EOS 4.34.1F - Layer 2 Configuration | Virtual VLANs (VLANS)](https://www.arista.com/en/um-eos/eos-virtual-lans-vlans)
- [EOS 4.34.1F - Interface Configuration | Port Channels and LACP)](https://www.arista.com/en/um-eos/eos-port-channels-and-lacp)
[Network Services Configuration Guide, Cisco IOS XE 17.x Configuring HSRP| ](https://www.cisco.com/c/en/us/td/docs/routers/ios/config/17-x/ntw-servs/b-network-services/m_fhp-hsrp-0.html#GUID-DF65C0B1-B112-4202-BE31-637D8284A7E6)
===

