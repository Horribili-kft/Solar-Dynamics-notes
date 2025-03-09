# Feheruuaru rea meneh hodu utu rea

## Eszközök konfigurációja

Hibák:

- [x] VLANok nincsenek meg minden switchen (lehetőleg VTP-t kéne használni)
- [x] Switch management IP-k nem követik teljesen a szabványt
- [x] Management VLAN 255 252 helyett
- [x] Bannerek hináyoznak
- [x] SSH hiányzik
- [x] Hostname hiányzik
- [x] NAT hiányzik
- [x] Switch trunk port allowed vlanok túl engedékenyek
- [x] Router config hiányos, nincs kiadva az ipv6 unicast-routing
- [x] IPv6 címek eszköz része rossz

## SzF-R:

Ellenőrző checklist:

- [x] Hostname
- [x] Banner
- [x] IPv4
- [x] IPv6
- [x] Login, SSH and authentication
- [x] NAT
- [x] DMVPN:
  - [x] GRE (Packet encapsulation into another packet)
  - [x] NHRP (Next Hop Resolution Protocol, don't know what this is)
  - [x] IPsec (Encryption)
  - [x] Routing protocol (probably EIGRP)

```
hostname Szf-R

! Convenience
no ip domain lookup

! Routing
ip routing
ipv6 unicast-routing

! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#


! VTP
vtp domain SzekesF
vtp mode server
vtp version 3
vtp password Solar-Dynamics-2025
do vtp primary

! NAT
ip access-list standard SD-ACL-internal-client
	permit 10.1.10.0 0.0.0.255
	permit 10.1.20.0 0.0.0.255
	permit 10.1.50.0 0.0.0.255
	permit 10.1.110.0 0.0.0.255
	permit 10.1.252.0 0.0.0.255
	exit

	ip access-list standard SD-ACL-external-client
	permit 10.1.120.0 0.0.0.255
	exit

! Overload pool for company clients
ip nat pool SD-internal-client-pool 82.1.79.129 82.1.79.134 netmask 255.255.255.248

! Pool for publically connected devices
ip nat pool SD-external-client-pool 82.1.79.137 82.1.79.142 netmask 255.255.255.248

! NAT for company devices
ip nat inside source list SD-ACL-internal-client pool SD-internal-client-pool overload

! NAT for external devices
ip nat inside source list SD-ACL-external-client pool SD-external-client-pool overload


! > ISP
interface GigabitEthernet1/0
	ip address 82.1.79.145 255.255.255.240
 	ip nat outside
 	ipv6 enable
	ipv6 address 2a:1dc:7c0:0300:82.1.79.145/64
	no shutdown

! > SzF-SW1
interface GigabitEthernet0/0
	no shutdown

! VLAN interfacek
	interface GigabitEthernet0/0.10
		encapsulation dot1Q 10
		ip address 10.1.10.1 255.255.255.0
		ip nat inside
		ipv6 enable
		ipv6 address 2a:1dc:7c0:010A::1/64
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20

	interface GigabitEthernet0/0.20
		encapsulation dot1Q 20
		ip address 10.1.20.1 255.255.255.0
		ip nat inside
		ipv6 enable
		ipv6 address 2a:1dc:7c0:0114::1/64
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20

	interface GigabitEthernet0/0.50
		encapsulation dot1Q 50
		ip address 10.1.50.1 255.255.255.0
		ip nat inside
		ipv6 enable
		ipv6 address 2a:1dc:7c0:0132::1/64
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20

	interface GigabitEthernet0/0.110
		encapsulation dot1Q 110
		ip address 10.1.110.1 255.255.252.0
		ip nat inside
		ipv6 enable
		ipv6 address 2a:1dc:7c0:016E::1/64
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20

	interface GigabitEthernet0/0.252
		encapsulation dot1Q 252
		ip address 10.1.252.1 255.255.252.0
		ip nat inside
		ipv6 enable
		ipv6 address 2a:1dc:7c0:01FF::1/64



! IP route
ip route 0.0.0.0 0.0.0.0 82.1.79.142


! Spoke
! Site to site VPN configuration

	! IPsec Configuration
	crypto isakmp policy 10
		encr aes 256
		hash sha256
		authentication pre-share
		! 2048 bit Diffie-Hellman key exchange
		group 14
		lifetime 86400

	! Wildcard, because this hub will connect to multiple spokes
	crypto isakmp key Solar-Dynamics-2025 address 82.1.79.1
	crypto isakmp key Solar-Dynamics-2025-2 address 85.16.100.3

	crypto ipsec transform-set DMVPN-TRANSFORM-SET esp-aes 256 esp-sha256-hmac
		mode transport

	crypto ipsec profile DMVPN-PROFILE
		set transform-set DMVPN-TRANSFORM-SET

	interface Tunnel0
		tunnel protection ipsec profile DMVPN-PROFILE

	! GRE tunnel
		interface tunnel0
			no shutdown

			! Public IP
			tunnel source 82.1.79.129

			! Multipoint GRE for multiple site connection
			tunnel mode gre multipoint

			! IP for inter tunnel communication
			! Site number + 2
			ip address 192.168.0.3 255.255.255.0

			! NHRP configuration
				! NHRP for dynamic inter-site communication (must match on all sites)
				ip nhrp network-id 1

				! Tunnel key
				tunnel key 123

				! Password authentication (8 char limit)
				ip nhrp authentication Password

				! Allow multicast traffic over the tunnel interfaces (this is the same for all sites)

				! HQ-R1 (Hub 1)
				ip nhrp map multicast 82.1.79.1
				ip nhrp map 192.168.0.1 82.1.79.1
				ip nhrp nhs 192.168.0.1

				! HQ-R2 (Hub 2)
				ip nhrp map multicast 85.16.100.3
				ip nhrp map 192.168.0.2 85.16.100.3
				ip nhrp nhs 192.168.0.2

			! ip mtu 1400
			! ip tcp adjust-mss 1360


! IPv4 EIGRP Configuration
	router eigrp 100
	    network 10.1.10.0 0.0.0.255
	    network 10.1.20.0 0.0.0.255
	    network 10.1.50.0 0.0.0.255
	    network 10.1.150.0 0.0.0.255
	    network 10.1.252.0 0.0.3.255

		no auto-summary
	    passive-interface default
	    ! EIGRP for tunnel configuration
		    network 192.168.0.0 0.0.0.255
		    no passive-interface tunnel0

```

## SzF-SW1:

- [x] Trunk portok, trunk allowed VLAN helyes konfigurációja
- [x] DHCP snooping limit
- [x] DHCP snooping trust
- [x] Storm control
- [x] Nonegotiate
- [x] Port security
- [x] Portfast
- [x] BPDU guard

```
hostname SzF_SW1
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2


vtp domain SzekesF
vtp mode client
vtp password Solar-Dynamics-2025


ip dhcp snooping
ip dhcp snooping vlan 10,20,50,110,252


vlan 10
 name Vezetok
vlan 20
 name Tesztelok
vlan 50
 name Fejlesztok
vlan 110
 name TerepWLAN
vlan 252
 name Management


> SzF_SW2
interface Gig0/2
 	switchport mode trunk
 	switchport trunk allowed vlan 10,20,50,110,252
	ip dhcp snooping trust
	switchport nonegotiate
!

> SzF_R
interface Gig0/1
	switchport mode trunk
 	switchport trunk allowed vlan 10,20,50,110,252
	ip dhcp snooping trust
	switchport nonegotiate
!

interface FastEthernet0/1
 	switchport mode access
	switchport access vlan 10
	switchport port-security
	switchport port-security maximum 1
	switchport port-security mac-address sticky
	switchport port-security violation protect
 	switchport port-security mac-address sticky
	storm-control broadcast level 5.00
	storm-control multicast level 5.00
 	storm-control unicast level 5.00
	spanning-tree portfast
	spanning-tree bpduguard enable
	ip dhcp snooping limit rate 10
!
interface FastEthernet0/2
 	switchport mode access
 	switchport access vlan 10
	switchport port-security
	switchport port-security maximum 1
	switchport port-security mac-address sticky
	switchport port-security violation protect
 	switchport port-security mac-address sticky
	storm-control broadcast level 5.00
	storm-control multicast level 5.00
 	storm-control unicast level 5.00
	spanning-tree portfast
	spanning-tree bpduguard enable
	ip dhcp snooping limit rate 10
!

interface FastEthernet0/10
 	switchport mode access
 	switchport access vlan 50
	switchport port-security
	switchport port-security maximum 1
	switchport port-security mac-address sticky
	switchport port-security violation protect
 	switchport port-security mac-address sticky
	storm-control broadcast level 5.00
	storm-control multicast level 5.00
 	storm-control unicast level 5.00
	spanning-tree portfast
	spanning-tree bpduguard enable
	ip dhcp snooping limit rate 10
!

interface FastEthernet0/11
 	switchport mode access
 	switchport access vlan 50
	switchport port-security
	switchport port-security maximum 1
	switchport port-security mac-address sticky
	switchport port-security violation protect
 	switchport port-security mac-address sticky
	storm-control broadcast level 5.00
	storm-control multicast level 5.00
 	storm-control unicast level 5.00
	spanning-tree portfast
	spanning-tree bpduguard enable
	ip dhcp snooping limit rate 10
!
interface FastEthernet0/12
 	switchport mode access
 	switchport access vlan 50
	switchport port-security
	switchport port-security maximum 1
	switchport port-security mac-address sticky
	switchport port-security violation protect
 	switchport port-security mac-address sticky
	storm-control broadcast level 5.00
	storm-control multicast level 5.00
 	storm-control unicast level 5.00
	spanning-tree portfast
	spanning-tree bpduguard enable
	ip dhcp snooping limit rate 10
!
interface vlan 252
	ip address 10.1.253.1 255.255.252.0
 	no shutdown
	exit

```

## SzF-SW2:

- [x] Trunk portok, trunk allowed VLAN helyes konfigurációja
- [x] DHCP snooping limit
- [x] DHCP snooping trust
- [x] Storm control
- [x] Nonegotiate
- [x] Port security
- [x] Portfast
- [x] BPDU guard

```
hostname SzF_SW2
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2


vtp domain SzekesF
vtp mode client
vtp password Solar-Dynamics-2025


ip dhcp snooping
ip dhcp snooping vlan 10,20,50,110,252


vlan 10
 name Vezetok
vlan 20
 name Tesztelok
vlan 50
 name Fejlesztok
vlan 110
 name TerepWLAN
vlan 252
 name Management

!
interface Gig0/2 > SzF_SW1
 	switchport mode trunk
 	switchport trunk allowed vlan 10,20,50,110,252
	ip dhcp snooping trust
	switchport nonegotiate
!
interface FastEthernet0/1
	switchport mode access
	switchport access vlan 20
	switchport mode access
	switchport port-security
	switchport port-security maximum 1
	switchport port-security mac-address sticky
	switchport port-security violation protect
 	switchport port-security mac-address sticky
	storm-control broadcast level 5.00
	storm-control multicast level 5.00
 	storm-control unicast level 5.00
	spanning-tree portfast
	spanning-tree bpduguard enable
	ip dhcp snooping limit rate 10
!
interface FastEthernet0/2
	switchport mode access
	switchport access vlan 20
	switchport mode access
	switchport port-security
	switchport port-security maximum 1
	switchport port-security mac-address sticky
	switchport port-security violation protect
 	switchport port-security mac-address sticky
	storm-control broadcast level 5.00
	storm-control multicast level 5.00
 	storm-control unicast level 5.00
	spanning-tree portfast
	spanning-tree bpduguard enable
	ip dhcp snooping limit rate 10
!
interface FastEthernet0/3
	switchport mode access
	switchport access vlan 20
	switchport mode access
	switchport port-security
	switchport port-security maximum 1
	switchport port-security mac-address sticky
	switchport port-security violation protect
 	switchport port-security mac-address sticky
	storm-control broadcast level 5.00
	storm-control multicast level 5.00
 	storm-control unicast level 5.00
	spanning-tree portfast
	spanning-tree bpduguard enable
	ip dhcp snooping limit rate 10
!
interface Gig0/1
	switchport mode access
	switchport access vlan 110
	switchport mode access
	switchport port-security
	switchport port-security maximum 1
	switchport port-security mac-address sticky
	switchport port-security violation protect
 	switchport port-security mac-address sticky
	storm-control broadcast level 5.00
	storm-control multicast level 5.00
 	storm-control unicast level 5.00
	spanning-tree portfast
	spanning-tree bpduguard enable
	ip dhcp snooping limit rate 10
!
interface vlan 252
 	ip address 10.1.253.2 255.255.252.0
 	no shutdown
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
