
#cisco #config

# Switch

Minden switch
---
MLS
---
### MLS1
v
Primary root bridge
-  [x] Hostname
-  [x] Banner
-  [x] VTP
-  [x] LACP
-  [ ] STP, Portfast, BPDU guard.
-  [ ] DHCP snooping
-  [ ] QOS (voice)
-  [ ] FHRP
-  [ ] IP
-  [x] Login, SSH and authentication
-  [ ] Authentication with RADIUS
- 
```
! Hostname
hostname HQ-MLS1
ip domain name hq.solardynamics.eu

! Convenience
no ip domain lookup

! Routing
ip routing
ipv6 unicast-routing

! Banner
banner login #WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming #WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec #WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#


! VTP
vtp domain GyorHQ
vtp mode server
vtp version 3
vtp password Solar-Dynamics-2025
do vtp primary

! STP
spanning-tree mode rapid-pvst

! Remote access (SUBJECT TO CHANGE TO RADIUS)
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh

! > R1
interface gig0/0
	no switchport

! > R2
interface gig0/1
	no switchport

! LACP Etherchannel > MLS2
interface range gig3/0 - 3
	channel-group 1 mode active
interface Port-channel 1
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk native vlan 999
	! Ez lehet valtozik meg, egyenlore kommentelve van
    ! switchport trunk allowed vlan 10,15,20,25,50,51,70,100,104,160,200,220,255

! VLAN
vlan 10
    name "Vezetoseg"

vlan 15
    name "HR"

vlan 20
    name "Marketing"

vlan 25
    name "Ertekesítes"

vlan 50
    name "Fejlesztok 1"

vlan 51
    name "Fejlesztok 2"

vlan 70
    name "Szerver"

vlan 100
    name "Iroda WLAN"

vlan 104
    name "Workshop WLAN"

vlan 160
    name "Vendeg WLAN"

vlan 200
    name "Voice"

vlan 220
    name "Nyomtatok"

vlan 255
    name "Management"


!!! Unconfigured from here.





# Layer 3 VLAN Interfaces (SVIs) for Routing
interface vlan 10
    ip address 10.0.10.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:10::1/64
    no shutdown

interface vlan 15
    ip address 10.0.15.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:15::1/64
    no shutdown

interface vlan 20
    ip address 10.0.20.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:20::1/64
    no shutdown

interface vlan 25
    ip address 10.0.25.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:25::1/64
    no shutdown

interface vlan 50
    ip address 10.0.50.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:50::1/64
    no shutdown

interface vlan 51
    ip address 10.0.51.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:51::1/64
    no shutdown

interface vlan 70
    ip address 10.0.70.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:70::1/64
    no shutdown

interface vlan 100
    ip address 10.0.100.1 255.255.252.0
    ipv6 address 2a:1dc:7c0:100::1/64
    no shutdown

interface vlan 104
    ip address 10.0.104.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:104::1/64
    no shutdown

interface vlan 160
    ip address 10.0.160.1 255.255.240.0
    ipv6 address 2a:1dc:7c0:160::1/64
    no shutdown

interface vlan 200
    ip address 10.0.200.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:200::1/64
    no shutdown

interface vlan 220
    ip address 10.0.220.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:220::1/64
    no shutdown

interface vlan 255
    ip address 10.0.252.1 255.255.252.0
    ipv6 address 2a:1dc:7c0:ff::1/64
    no shutdown

# STP Configuration (Primary for Server and Voice VLANs)
spanning-tree vlan 70,200 root primary
spanning-tree vlan 10,15,20,50,100,160,255 root secondary

# EIGRP Configuration
router eigrp 100
    network 10.0.0.0 0.255.255.255
    network 2a:1dc:7c0::/48
    no auto-summary
    eigrp router-id 1.1.1.1
    passive-interface default
    no passive-interface vlan 10
    no passive-interface vlan 15
    no passive-interface vlan 20
    no passive-interface vlan 25
    no passive-interface vlan 50
    no passive-interface vlan 51
    no passive-interface vlan 70
    no passive-interface vlan 100
    no passive-interface vlan 104
    no passive-interface vlan 160
    no passive-interface vlan 200
    no passive-interface vlan 220
    no passive-interface vlan 255

# Management Interface
interface vlan 255
    description Management VLAN
    ip address 10.0.252.10 255.255.252.0
    ipv6 address 2a:1dc:7c0:ff::10/64


```
### MLS2

Secondary root bridge

```
hostname HQ-MLS2

! Routing
ip routing
ipv6 unicast-routing

! Banner
banner motd #WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! VTP
vtp domain GyorHQ
vtp mode client
vtp version 3
vtp password Solar-Dynamics-2025

! > R1
interface gig0/0
	no switchport

! > R2
interface gig0/1
	no switchport

! LACP Etherchannel > MLS2
interface range gig3/0 - 3
	channel-group 1 mode active
interface Port-channel 1
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk native vlan 999
	! Ez lehet valtozik meg, egyenlore kommentelve van
    ! switchport trunk allowed vlan 10,15,20,25,50,51,70,100,104,160,200,220,255


```

