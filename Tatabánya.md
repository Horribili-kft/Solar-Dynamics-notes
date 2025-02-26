# A TATABÁNYAI KIRENDELTSÉG CONFIG-JA:

```
prefix:telephely:vlan:ipv4
2a:1dc:7c0:0310:3:10:1/64
2a:1dc:7c0:0314:3:20:1/64
2a:1dc:7c0:031E:3:30:1/64
2a:1dc:7c0:0396:3:150:1/64
2a:1dc:7c0:03FC:3:252:1/64
```

Eddigi hibák/feladatok késznek tűnnek, átnézésre várnak.

Hibák:
- [x] VLANok nincsenek meg minden switchen (lehetőleg VTP-t kéne használni)
- [x] Switch management IP-k nem követik teljesen a szabványt
- [x] Switch config hiányos
- [x] Switch trunk port allowed vlanok túl engedékenyek
- [x] Router config hiányos, nincs kiadva az ipv6 unicast-routing

TB-R:
---
Ellenőrző checklist:
-  [x] Hostname
-  [x] Banner
-  [x] IPv4
-  [x] IPv6
-  [x] Login, SSH and authentication
-  [x] NAT

```
hostname TB-RT

ipv6 unicast-routing

banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! SSH
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2

! NAT
ip access-list standard SD-ACL-internal-client
permit 10.3.10.0 0.0.0.255
permit 10.3.20.0 0.0.0.255
permit 10.3.30.0 0.0.0.255
permit 10.3.252.0 0.0.3.255

exit
		
ip access-list standard SD-ACL-external-client
permit 10.3.150.0 0.0.0.255
exit

! Overload pool for company clients
ip nat pool SD-internal-client-pool 82.1.79.150 82.1.79.155 netmask 255.255.255.240

! Pool for publically connected devices
ip nat pool SD-external-client-pool 82.1.79.156 82.1.79.158 netmask 255.255.255.240

! NAT for company devices
ip nat inside source list SD-ACL-internal-client pool SD-internal-client-pool overload

! NAT for external devices
ip nat inside source list SD-ACL-external-client pool SD-external-client-pool overload

ipv6 nat enable


! VLAN interfaces
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.3.10.1 255.255.255.0
 ip nat inside
 ipv6 enable
 ipv6 address 2a:1dc:7c0:0310:3:10:1/64
 ip helper-address 10.0.70.20
 ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.3.20.1 255.255.255.0
 ip nat inside
 ipv6 enable
 ipv6 address 2a:1dc:7c0:0314:3:20:1/64
 ip helper-address 10.0.70.20
 ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20


interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.3.30.1 255.255.255.0
 ip nat inside
 ipv6 enable
 ipv6 address 2a:1dc:7c0:031E:3:30:1/64
 ip helper-address 10.0.70.20
 ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20


interface GigabitEthernet0/0.150
 encapsulation dot1Q 150
 ip address 10.3.150.1 255.255.255.0
 ip nat inside
 ipv6 enable
 ipv6 address 2a:1dc:7c0:0396:3:150:1/64
 ip helper-address 10.0.70.20
 ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20


interface GigabitEthernet0/0.252
 encapsulation dot1Q 252
 ip address 10.3.252.1 255.255.252.0
 ip nat inside
 ipv6 enable
 ipv6 address 2a:1dc:7c0:03FC:3:252:1/64


interface GigabitEthernet0/1
 ip address 82.1.79.145 255.255.255.240
 ip nat outside
 ipv6 enable
 2a:1dc:7c0:0300:82.1.79.145/64												!!!!!!!!!


```



TB-EM1-LOGI:
---
- [x] Trunk portok, trunk allowed VLAN helyes konfigurációja
- [x] DHCP snooping limit
- [x] DHCP snooping trust
- [x] Storm control
- [x] Nonegotiate
- [x] Port security
- [x] Portfast
- [x] BPDU guard
```
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2


vtp domain TATAB
vtp mode client
vtp password Solar-Dynamics-2025

ip dhcp snooping
ip dhcp snooping vlan 10,20,30,150,252
interface range FastEthernet0/1-FastEthernet0/3
ip dhcp snooping limit rate 10

interface FastEthernet0/24				!!!!!!
ip dhcp snooping trust
switchport nonegotiate

interface range FastEthernet0/1-FastEthernet0/3
 switchport mode access
 switchport access vlan 10
 storm-control broadcast level 5.00
 storm-control multicast level 5.00
 storm-control unicast level 5.00
 switchport port-security
 switchport port-security maximum 3			*?
 switchport port-security violation restrict		*?		
 switchport port-security mac-address sticky
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shu

interface FastEthernet0/24
 switchport mode trunk
 switchport trunk allowed vlan 10,20,252
 no shu

interface vlan 252
 ip address 10.3.254.2 255.255.252.0
 no shutdown

```

TB-EM2-IRODA:
---
- [x] Trunk portok, trunk allowed VLAN helyes konfigurációja
- [x] DHCP snooping limit
- [x] DHCP snooping trust
- [x] Storm control
- [x] Nonegotiate
- [x] Port security
- [x] Portfast
- [x] BPDU guard
```
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2


vtp domain TATAB
vtp mode server
vtp version 2
vtp password Solar-Dynamics-2025

vlan 20
 name IRODA 
vlan 252
 name Management
vlan 10
 name GYAR
vlan 30
 name PARTNER
vlan 150
 name WIFI

ip dhcp snooping
ip dhcp snooping vlan 10,20,30,150,252
interface range FastEthernet0/1-FastEthernet0/3
ip dhcp snooping limit rate 10

interface FastEthernet0/23				!!!!!!
ip dhcp snooping trust

interface FastEthernet0/24				!!!!!!
ip dhcp snooping trust

interface Gig0/1
ip dhcp snooping trust


interface Gig0/1
 sw mode trunk
 switchport trunk allowed vlan 10,20,30,150,252
 switchport nonegotiate

interface range FastEthernet0/1-FastEthernet0/3
 switchport mode access
 switchport access vlan 20
 storm-control broadcast level 5.00
 storm-control multicast level 5.00
 storm-control unicast level 5.00
 switchport port-security
 switchport port-security maximum 3			*?
 switchport port-security violation restrict		*?		
 switchport port-security mac-address sticky
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shu

interface FastEthernet0/24
 switchport mode trunk
 switchport trunk allowed vlan 10,20,252
 switchport nonegotiate
 no shu

interface FastEthernet0/23
 switchport mode trunk
 switchport trunk allowed vlan 20,30,150,252
 switchport nonegotiate
 no shu

interface vlan 252
 ip address 10.3.254.3 255.255.252.0
 no shu

```

TB-EM3-PARTNER:
---
- [x] Trunk portok, trunk allowed VLAN helyes konfigurációja
- [x] DHCP snooping limit
- [x] DHCP snooping trust
- [x] Storm control
- [x] Nonegotiate
- [x] Port security
- [x] Portfast
- [x] BPDU guard
```
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2




vtp domain TATAB
vtp mode client
vtp password Solar-Dynamics-2025

ip dhcp snooping
ip dhcp snooping vlan 10,20,30,150,252
interface range FastEthernet0/1-FastEthernet0/3
ip dhcp snooping limit rate 10

interface range FastEthernet0/1-FastEthernet0/3
 switchport mode access
 switchport access vlan 30
 storm-control broadcast level 5.00
 storm-control multicast level 5.00
 storm-control unicast level 5.00
 switchport port-security
 switchport port-security maximum 3			*?
 switchport port-security violation restrict		*?		
 switchport port-security mac-address sticky
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shu

interface FastEthernet0/24
 switchport mode access
 switchport access vlan 150
 spanning-tree portfast
 spanning-tree bpduguard enable
 storm-control broadcast level 5.00
 storm-control multicast level 5.00
 storm-control unicast level 5.00
 switchport port-security
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 no shutdown

interface FastEthernet0/23
 switchport mode trunk
 switchport trunk allowed vlan 20,30,150,252
 switchport nonegotiate				!!!!!!
 ip dhcp snooping trust
 no shu



interface vlan 252
 ip address 10.3.254.4 255.255.252.0
 no shutdown

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



