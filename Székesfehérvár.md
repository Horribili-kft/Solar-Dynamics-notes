# Feheruuaru rea meneh hodu utu rea

## Eszközök konfigurációja

Hibák:
- VLANok nincsenek meg mindkét switchen (lehetőleg VTP-t kéne használni)

SzF-R:
---
```

interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.1.10.1 255.255.255.0
 ipv6 address 2a:1dc:7c0:010A::1/64
 
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.1.20.1 255.255.255.0
 ipv6 address 2a:1dc:7c0:0114::1/64
 
interface GigabitEthernet0/0.50
 encapsulation dot1Q 50
 ip address 10.1.50.1 255.255.255.0
 ipv6 address 2a:1dc:7c0:0132::1/64
 
interface GigabitEthernet0/0.110
 encapsulation dot1Q 110
 ip address 10.1.110.1 255.255.252.0
 ipv6 address 2a:1dc:7c0:016E::1/64
 
interface GigabitEthernet0/0.255
 encapsulation dot1Q 255
 ip address 10.1.252.1 255.255.252.0
 ipv6 address 2a:1dc:7c0:01FF::1/64
```
 
SzF-SW1:
---

```
vlan 10
	name Vezetok
vlan 50
	name Fejlesztok
vlan 255
	name Management
 
interface Gig0/2
	switchport mode trunk
	switchport trunk allowed vlan 10,20,50,110,255
!
interface Gig0/1
	switchport mode trunk
	switchport trunk allowed vlan 10,20,50,110,255
!
 
interface FastEthernet0/1
	switchport mode access
	switchport access vlan 10
!
interface FastEthernet0/2
	switchport mode access
	switchport access vlan 10
!
 
interface FastEthernet0/10
	switchport mode access
	switchport access vlan 50
!
 
interface FastEthernet0/11
	switchport mode access
	switchport access vlan 50
!
interface FastEthernet0/12
	switchport mode access
	switchport access vlan 50
!
interface vlan 255
	ip address 10.1.252.2 255.255.252.0
	no shutdown
exit
```


SzF-SW2:
---



```
vlan 20
	name Tesztelok
vlan 110
	name TerepWLAN
vlan 255
	name Management
 
interface Gig0/2
	switchport mode trunk
	switchport trunk allowed vlan 10,20,50,110,255
!
interface FastEthernet0/1
	switchport mode access
	switchport access vlan 20
!
interface FastEthernet0/2
	switchport mode access
	switchport access vlan 20
!
interface FastEthernet0/3
	switchport mode access
	switchport access vlan 20
!
interface Gig0/1
	switchport mode access
	switchport access vlan 110
!
interface vlan 255
	ip address 10.1.252.3 255.255.252.0
	no shutdown
exit
```
 
Wi-Fi Router konfiguráció
1. SSID beállítása:
   - SSID: Terep_WLAN
   - **Security Mode**: WPA2-PSK
   - **Key**: Solar-Dynamics-2025
2. IP-címek:
   - **LAN IP**: 10.1.110.2
   - **DHCP tartomány**: 10.1.110.3 - 10.1.110.100
   - **WAN IP**: 10.1.110.1 (Router interfész)