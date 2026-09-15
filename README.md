# Enterprise Network Infrastructure Design & Security

A high-availability enterprise network architecture simulated using Cisco Packet Tracer. This project demonstrates secure multi-branch connectivity across a public ISP infrastructure using GRE Tunneling, OSPF routing, dynamic failover with HSRP, and boundary security with Extended ACLs.

---

## 📐 Network Architecture Overview

The topology is divided into three functional layers:

1. **Access & Distribution Layer:**
   - Client isolation using **VLAN 10**, **VLAN 20**, and **VLAN 30**.
   - **Rapid PVST (RSTP)** with custom STP priorities for per-VLAN load balancing.
   - **DHCP Relay Agent (`ip helper-address`)** forwarding client address requests to a centralized DHCP server.

2. **Core & Services Layer:**
   - Multi-layer Layer 3 switches (`sw1`, `sw2`) executing **OSPF Area 0** routing.
   - **HSRP (Hot Standby Router Protocol)** in Active/Standby configuration ensuring zero single point of failure for default gateways.
   - Passive interface default hardening on client-facing ports.

3. **Edge & WAN Layer:**
   - Boundary routers (`R1`, `R3`) interconnected through a transit ISP router (`R2`).
   - **GRE Tunnel (`Tunnel0`)** encapsulating internal site-to-site traffic over unsecure WAN links.
   - **NAT/PAT Overload** hiding private internal IP blocks (`172.16.0.0/16`).
   - **Extended ACL (`WEB_SECURITY`)** enforced on `R3` permitting secure HTTPS traffic (TCP 443) to the Web Server while dropping ICMP/Ping probes.

---

## 🛠️ Key Technologies & Protocols

| Category | Protocols & Concepts |
| :--- | :--- |
| **Switching & L2** | VLANs, Trunking (802.1Q), Rapid PVST+ |
| **High Availability** | HSRP (Gateway Redundancy) |
| **Routing** | OSPF (Single Area 0), Static Routing |
| **Tunneling & Edge** | GRE Tunneling, NAT / PAT Overload |
| **Security** | Extended ACLs (Port Filtering & Protocol Blocking) |
| **Services** | DHCP Relay (`ip helper-address`) |

---

## 🛡️ Security Policies Implemented

The DMZ router (`R3`) applies strict boundary security via an Extended ACL (`WEB_SECURITY`) on `GigabitEthernet0/0/1`:

```cisco
ip access-list extended WEB_SECURITY
 permit tcp any host 172.17.17.100 eq 443
 deny icmp any host 172.17.17.100
 permit ip any any
