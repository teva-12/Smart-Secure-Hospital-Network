# 🏥 Smart & Secure Enterprise Hospital Network

> A production-grade hospital network simulation built with **Cisco Packet Tracer**, implementing enterprise security protocols, high availability, and zero-trust architecture principles.

---

## 📌 Project Overview

This project simulates a **real-world hospital network** designed from the ground up with security and availability as first-class requirements — not afterthoughts.

The core design question this project answers:

> *"If a patient monitoring device gets compromised, can an attacker reach the hospital's administrative servers?"*
>
> **The answer is: No. By design. ✅**

---

## 🗂️ Network Architecture

```
                        [ Internet ]
                             |
                    [ Firewall / NAT ]
                    /                 \
           [Router-Edge-1]      [Router-Edge-2]
              (Active)            (Standby)
                    \                 /
                     \    HSRP VIP   /
                      \  .10.254    /
                       [Core-L3-Switch]
                    (Inter-VLAN + OSPF + ACL)
          /         /         |         \         \
     [SW-Admin] [SW-Docs] [SW-Nurses] [SW-IoT] [SW-Guests]
     VLAN 10    VLAN 20   VLAN 30    VLAN 40   VLAN 50
```

### VLAN Segmentation

| VLAN | Name    | Subnet              | Purpose                              |
|------|---------|---------------------|--------------------------------------|
| 10   | Admin   | 192.168.10.0/24     | Administrative staff & servers       |
| 20   | Doctors | 192.168.20.0/24     | Medical staff workstations           |
| 30   | Nurses  | 192.168.30.0/24     | Nursing staff terminals              |
| 40   | IoT     | 192.168.40.0/24     | Smart sensors, IP cameras, monitors  |
| 50   | Guests  | 192.168.50.0/24     | Patient & visitor devices (isolated) |
| 99   | Mgmt    | 192.168.99.0/24     | Out-of-band device management        |

---

## ⚙️ Implemented Technologies

### 🔀 Layer 2 — Switching
- **VLANs & 802.1Q Trunking** — broadcast domain segmentation across all access switches
- **EtherChannel (LACP)** — aggregated uplinks for increased bandwidth and redundancy
- **BPDU Guard + PortFast** — prevents STP manipulation attacks on access ports
- **DHCP Snooping** — blocks rogue DHCP servers; only trusted uplink ports accepted
- **Dynamic ARP Inspection (DAI)** — mitigates ARP poisoning / Man-in-the-Middle attacks
- **Port Security (Sticky MAC)** — locks IoT switch ports to registered MAC addresses

### 🌐 Layer 3 — Routing & Services
- **Inter-VLAN Routing via SVIs** — Core L3 switch handles all internal routing
- **OSPF (Area 0)** — dynamic routing between core switch and edge routers
- **NAT/PAT** — private-to-public address translation on both edge routers
- **DHCP Server** — centralized IP allocation per VLAN with per-scope exclusions
- **DNS Server** — internal `hospital.local` domain resolution

### 🛡️ Security — Zero Trust Framework
- **ACL: BLOCK_IOT** — denies IoT VLAN (40) → Admin VLAN (10) traffic
- **ACL: ISOLATE_GUESTS** — denies Guest VLAN (50) → all internal VLANs
- **SSH v2 only** — Telnet disabled globally on all managed devices
- **HSRP Group 1** — virtual gateway `192.168.10.254` with priority 110/100 and preemption

### 📊 Monitoring
- **NTP** — synchronized clocks across all devices
- **Syslog** — centralized security event logging

---

## 🎬 Security Scenario — Proof of Concept

### Scenario: Compromised IoT Device

```bash
# From IoT Device (192.168.40.10) — attacker attempts to reach Admin Server
ping 192.168.10.5
# Result: Request timeout. 100% packet loss. ← ACL blocks it ✅

# From Admin PC (192.168.10.10) — IT team monitors IoT device
ping 192.168.40.10
# Result: Reply from 192.168.40.10 ← Unidirectional ACL allows this ✅
```

**Key insight**: The ACL is applied `inbound` on VLAN 40's SVI — traffic is dropped at the source before it can traverse the network. The Admin team retains full visibility into IoT devices without exposure in the reverse direction.

---

## 🔁 High Availability Test

```bash
# Simulate Router-Edge-1 failure (shutdown G0/0)
# Result: Single ping drop → Router-Edge-2 assumes gateway role via HSRP
# Recovery time: < 3 seconds (preemption enabled)
```

---

## 📁 Repository Structure

```
Smart-Secure-Hospital-Network/
├── Smart_Secure_Hospital_Network.pkt   # Cisco Packet Tracer project file
├── README.md
└── assets/
    ├── topology.png                    # Network topology screenshot
    ├── NTI-Certificate.pdf             # NTI CCNA Certificate
    ├── CCNA-Intro.png
    ├── CCNA-Switching.png
    └── CCNA-Enterprise.png
```

---

## 🏆 Certifications

This project was designed alongside completing the full Cisco CCNA curriculum:

| Institution | Certification | Score |
|-------------|--------------|-------|
| NTI — National Telecommunication Institute | CCNA Routing & Switching | 85% |
| Cisco NetAcad | CCNA: Introduction to Networks | Verified |
| Cisco NetAcad | CCNA: Switching, Routing & Wireless Essentials | Verified |
| Cisco NetAcad | CCNA: Enterprise Networking, Security & Automation | Verified |

---

## 🛠️ Tools Used

- **Cisco Packet Tracer 8.x**
- **Cisco IOS** — 2901, 2911 Routers / 3560 L3 Switch / 2960 L2 Switches

---

## 👤 Author

**Mohamed Towfiq Ali Alsayyed**  
Networking & Security Enthusiast | CCNA Certified  
[LinkedIn](https://www.linkedin.com/in/mohamed-makawye-782686345) · [GitHub](https://github.com/teva-12)
