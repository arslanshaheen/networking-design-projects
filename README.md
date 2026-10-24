# XYZ Company Branch Network - Cisco Packet Tracer Project

This repository contains the network design, subnetting plan, and step-by-step configuration guide for the XYZ company branch network located near Bonalbo, Australia[cite: 1].

---

## 📋 Network Requirements Summary

* **Hardware:** 1 Cisco Router and 1 Cisco Managed Switch[cite: 1].
* **Departments (3 Total):**[cite: 1]
  1. Admin / IT[cite: 1]
  2. Finance / HR[cite: 1]
  3. Customer Service / Reception[cite: 1]
* **VLANs:** Each department is assigned to a separate VLAN[cite: 1] using a **Router-on-a-Stick** architecture.
* **Wireless Support:** Wireless networks are integrated for users in each department[cite: 1] using Access Points connected to the respective VLAN ports.
* **Dynamic IP Assignment:** Host devices automatically obtain IPv4 configurations via **DHCP**[cite: 1].
* **Inter-VLAN Communication:** Devices across all departments can communicate with each other, routed through the router[cite: 1].
* **Base Network:** `192.168.1.0/24`[cite: 1]

---

## 🔢 Subnetting Plan

The base network (`192.168.1.0/24`) is divided into 3 subnets using a `/26` subnet mask (`255.255.255.192`), providing up to 62 usable hosts per subnet.

### **1st Subnet (Admin / IT — VLAN 10)**
* **Network ID:** `192.168.1.0`
* **Broadcast ID:** `192.168.1.63`
* **Host range:** `192.168.1.1` – `192.168.1.62`
* **Default Gateway / Router IP:** `192.168.1.1`

### **2nd Subnet (Finance / HR — VLAN 20)**
* **Network ID:** `192.168.1.64`
* **Broadcast ID:** `192.168.1.127`
* **Host range:** `192.168.1.66` – `192.168.1.126`
* **Default Gateway / Router IP:** `192.168.1.65`

### **3rd Subnet (Customer Service / Reception — VLAN 30)**
* **Network ID:** `192.168.1.128`
* **Broadcast ID:** `192.168.1.191`
* **Host range:** `192.168.1.130` – `192.168.1.190`
* **Default Gateway / Router IP:** `192.168.1.129`

---

## ⚙️ Configuration Commands

### **1. Switch Configuration (VLANs & Trunking)**
```text
Switch> enable
Switch# configure terminal

! Create VLANs
Switch(config)# vlan 10
Switch(config-vlan)# name Admin-IT
Switch(config-vlan)# vlan 20
Switch(config-vlan)# name Finance-HR
Switch(config-vlan)# vlan 30
Switch(config-vlan)# name Customer-Service
Switch(config-vlan)# exit

! Configure Trunk Port connected to the Router
Switch(config)# interface gigabitethernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit













Router> enable
Router# configure terminal
Router(config)# interface gigabitethernet 0/0
Router(config-if)# no shutdown

! Subinterface for VLAN 10
Router(config)# interface gigabitethernet 0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.1.1 255.255.255.192

! Subinterface for VLAN 20
Router(config)# interface gigabitethernet 0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.1.65 255.255.255.192

! Subinterface for VLAN 30
Router(config)# interface gigabitethernet 0/0.30
Router(config-subif)# encapsulation dot1Q 30
Router(config-subif)# ip address 192.168.1.129 255.255.255.192
Router(config-subif)# exit









! Exclude Gateway IPs
Router(config)# ip dhcp excluded-address 192.168.1.1
Router(config)# ip dhcp excluded-address 192.168.1.65
Router(config)# ip dhcp excluded-address 192.168.1.129

! DHCP Pool for VLAN 10
Router(config)# ip dhcp pool VLAN10_Pool
Router(config-dhcp)# network 192.168.1.0 255.255.255.192
Router(config-dhcp)# default-router 192.168.1.1
Router(config-dhcp)# exit

! DHCP Pool for VLAN 20
Router(config)# ip dhcp pool VLAN20_Pool
Router(config-dhcp)# network 192.168.1.64 255.255.255.192
Router(config-dhcp)# default-router 192.168.1.65
Router(config-dhcp)# exit

! DHCP Pool for VLAN 30
Router(config)# ip dhcp pool VLAN30_Pool
Router(config-dhcp)# network 192.168.1.128 255.255.255.192
Router(config-dhcp)# default-router 192.168.1.129
Router(config-dhcp)# exit
