# Single-Area OSPF with Inter-VLAN Routing and STP Optimization

## 📌 Project Overview
This deployment showcases a secure, redundant multi-site enterprise network layout. It implements Layer 2 segmentation using 802.1Q VLANs, deterministic spanning-tree path optimization to eliminate suboptimal traffic detours, and dynamic multi-subnet routing between sites using Single-Area OSPF (Area 0).

## 🗺️ Network Topology
![Topology OSPF.png]

## 🛠️ Design Objectives & Implementations

### 1. Layer 2 Segmentation & Gateways (ROAS)
* **VLAN 10 (SALES):** Network `10.0.0.0/26` 
* **VLAN 20 (HR):** Network `10.0.0.64/26`
**Router R1:** Configured subinterface `g0/0.10` as the default gateway for **VLAN 10 (SALES)** (`10.0.0.0/26`).
* **Router R2:** Configured subinterface `g0/0.20` as the default gateway for **VLAN 20 (HR)** (`10.0.0.64/26`).
* **Implementation:** Configured Router-on-a-Stick (ROAS) on the local LAN interfaces of both routers using subinterfaces paired with dot1Q encapsulation. This separates internal broadcast domains while enabling controlled inter-VLAN communication.

### 2. Spanning Tree Protocol (STP) Traffic Engineering
* **Problem:** Default STP settings let random MAC address ties decide the Root Bridge, causing traffic hairpinning and latency.
* **Fix:** Hardcoded the primary core switch (`SW1`) to a priority of `4096` to force it as the Root Bridge for standard STP. This ensures all traffic targeting the default gateway takes the absolute shortest path.

### 3. Dynamic Routing Core (OSPFv2)
* **Inter-Router Link:** Point-to-Point network `10.0.0.128/30` linking R1 and R2.
* **Optimization:** Used precise `/30` wildcard masks (`0.0.0.3`) for the point-to-point transit link to optimize the routing domain and preserve IPv4 space.
* **Security:** Implemented `passive-interface` commands on all user-facing LAN subinterfaces to suppress unnecessary OSPF Hello multicasts into the local departments.

---
