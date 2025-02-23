# A TATABÁNYAI KIRENDELTSÉG CONFIG-JA:

```
prefix:telephely:vlan:ipv4
2a:1dc:7c0:0310:3:10:1/64
2a:1dc:7c0:0314:3:20:1/64
2a:1dc:7c0:031E:3:30:1/64
2a:1dc:7c0:0396:3:150:1/64
2a:1dc:7c0:03FC:3:252:1/64
```

Hibák:
- VLANok nincsenek meg minden switchen (lehetőleg VTP-t kéne használni)
- Switch management IP-k nem követik teljesen a szabványt
- Switch config hiányos
- Switch trunk port allowed vlanok túl engedékenyek
- Router config hiányos, nincs kiadva az ipv6 unicast-routing

TB-R:
---
Ellenőrző checklist:
-  [ ] Hostname
-  [ ] Banner
-  [x] IPv4
-  [ ] IPv6
-  [ ] Login, SSH and authentication
-  [ ] NAT

```
interface GigabitEthernet0/0.10
	encapsulation dot1Q 10
	ip address 10.3.10.1 255.255.255.0
	ipv6 address 2a:1dc:7c0:0310:3:10:1/64

interface GigabitEthernet0/0.20
	encapsulation dot1Q 20
	ip address 10.3.20.1 255.255.255.0
	ipv6 address 2a:1dc:7c0:0314:3:20:1/64

interface GigabitEthernet0/0.30
	encapsulation dot1Q 30
	ip address 10.3.30.1 255.255.255.0
	ipv6 address 2a:1dc:7c0:031E:3:30:1/64

interface GigabitEthernet0/0.150
	encapsulation dot1Q 150
	ip address 10.3.150.1 255.255.255.0
	ipv6 address 2a:1dc:7c0:0396:3:150:1/64

interface GigabitEthernet0/0.252
	encapsulation dot1Q 252
	ip address 10.3.252.1 255.255.255.0
	ipv6 address 2a:1dc:7c0:03FC:3:252:1/64

```



TB-EM1-LOGI:
---
- [ ] Trunk portok, trunk allowed VLAN helyes konfigurációja
- [ ] DHCP snooping limit
- [ ] DHCP snooping trust
- [ ] Storm control
- [ ] Nonegotiate
- [ ] Port security
- [ ] Portfast
- [ ] BPDU guard
```
vlan 10
	name GYAR
vlan 252
	name Management

interface range FastEthernet0/1-FastEthernet0/3
	switchport mode access
	switchport access vlan 10
	no shutdown

! > TB-EM2-IRODA
interface Gig0/2
	switchport mode trunk
	switchport trunk allowed vlan 10,20,30,150,252
	no shutdown

! > TB-R
interface Gig0/1 
	sw mode trunk
	switchport trunk allowed vlan 10,20,30,150,252
	no shutdown

interface vlan 252
	ip address 10.3.252.2 255.255.255.0
	no shu
```

TB-EM2-IRODA:
---
- [ ] Trunk portok, trunk allowed VLAN helyes konfigurációja
- [ ] DHCP snooping limit
- [ ] DHCP snooping trust
- [ ] Storm control
- [ ] Nonegotiate
- [ ] Port security
- [ ] Portfast
- [ ] BPDU guard
```
vlan 20
	name IRODA 
vlan 252
	name Management

interface range FastEthernet0/1-FastEthernet0/3
	switchport mode access
	switchport access vlan 20
	no shu

! > TB-EM1-LOGI
interface Gig0/1
	switchport mode trunk
	switchport trunk allowed vlan 10,20,30,150,252
	no shu

! > TB-EM3-PARTNER
interface Gig0/2
	switchport mode trunk
	switchport trunk allowed vlan 10,20,30,150,252
	no shu

interface vlan 252
	ip address 10.3.252.3 255.255.255.0
	no shu

```

TB-EM3-PARTNER:
---

```
vlan 30
	name PARTNER
vlan 252
	name Management

interface range FastEthernet0/1-FastEthernet0/3
	switchport mode access
	switchport access vlan 30
	no shu

interface Gig0/1
	switchport mode trunk
	switchport trunk allowed vlan 10,20,30,150,252
	no shu

interface vlan 252
	ip address 10.3.252.4 255.255.255.0
	no shu

```

TB-WIFI-VARO:
---

SSID:
   - SSID: Solar_Vendeg
   - WPA2-PSK
   - Solar-Dynamics-2025
IP-címek:
   - LAN: 10.3.150.3
   - DHCP: 10.3.150.100 - 10.3.150.150
   - WAN: 10.3.150.2



