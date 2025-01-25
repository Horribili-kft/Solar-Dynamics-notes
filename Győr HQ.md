
#cisco #config

# Switch

Minden switch
---
MLS
---
### HQ-MLS1
v
Primary root bridge
-  [x] Hostname
-  [x] Banner
-  [x] VTP
-  [x] LACP
-  [x] STP, Portfast, BPDU guard.
-  [x] IP
-  [x] IPv4 EIGRP
-  [x] IPv6 EIGRP
-  [ ] DHCP snooping
-  [ ] IP helper address 
-  [ ] QOS (voice)
-  [ ] FHRP
-  [x] Login, SSH and authentication
-  [ ] Authentication with RADIUS (bonus)
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
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#


! VTP
vtp domain GyorHQ
vtp mode server
vtp version 3
vtp password Solar-Dynamics-2025
do vtp primary

! STP Configuration (Primary for Server and Voice VLANs)
spanning-tree mode rapid-pvst
spanning-tree vlan 70,200 root primary
spanning-tree vlan 10,15,20,50,100,160,255 root secondary

! Remote access (SUBJECT TO CHANGE TO RADIUS)
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2

! LACP Etherchannel > MLS2
interface range gig3/0 - 3
	channel-group 1 mode active
interface Port-channel 1
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk native vlan 999

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


! Layer 3 VLAN Interfaces for Routing and EIGRP
interface vlan 10
    ip address 10.0.10.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:000A:10:0:15:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 15
    ip address 10.0.15.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:000F:10:0:15:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 20
    ip address 10.0.20.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:0014:10:0:20:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 25
    ip address 10.0.25.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:0019:10:0:25:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 50
    ip address 10.0.50.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:0032:10:0:50:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 51
    ip address 10.0.51.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:0033:10:0:51:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 70
    ip address 10.0.70.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:0046:10:0:70:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 100
    ip address 10.0.100.1 255.255.252.0
    ipv6 address 2a:1dc:7c0:0064:10:0:100:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 104
    ip address 10.0.104.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:0068:10:0:104:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 160
    ip address 10.0.160.1 255.255.240.0
    ipv6 address 2a:1dc:7c0:00A0:10:0:160:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 200
    ip address 10.0.200.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:00C8:10:0:200:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 220
    ip address 10.0.220.1 255.255.255.0
    ipv6 address 2a:1dc:7c0:00DC:10:0:220:1/64
    ipv6 eigrp 100
    no shutdown

interface vlan 252
    ip address 10.0.253.1 255.255.252.0
    ipv6 address 2a:1dc:7c0:00FF:10:0:253:1/64
    ipv6 eigrp 100
    no shutdown

! > R1
interface gig0/0
	no switchport
	ip address 172.16.0.1 255.255.255.254
	ipv6 enable
	ipv6 eigrp 100
	no shutdown

! > R2
interface gig0/1
	no switchport
	ip address 172.16.0.5 255.255.255.254
	ipv6 enable
	ipv6 eigrp 100
	no shutdown

! IPv4 EIGRP Configuration
router eigrp 100
    network 10.0.0.0 0.0.255.255
    network 172.16.0.0 0.0.0.1
    network 172.16.0.4 0.0.0.1
	no auto-summary
	router-id 1.1.1.1
    passive-interface default
    no passive-interface gig0/0
    no passive-interface gig0/1
    no passive-interface Port-channel 1

! IPv6 EIGRP Configuration
ipv6 router eigrp 100
	router-id 1.1.1.1
    passive-interface default
    no passive-interface gig0/0
    no passive-interface gig0/1
    no passive-interface Port-channel 1

!!! Unconfigured from here.


```
### HQ-MLS2
-  [x] Hostname
-  [x] Banner
-  [x] VTP
-  [x] LACP
-  [x] STP, Portfast, BPDU guard.
-  [x] IP
-  [x] IPv4 EIGRP
-  [x] IPv6 EIGRP
-  [ ] DHCP snooping
-  [ ] IP helper address 
-  [ ] QOS (voice)
-  [ ] FHRP
-  [x] Login, SSH and authentication
-  [ ] Authentication with RADIUS (bonus)

```
! Hostname
hostname HQ-MLS2
ip domain name hq.solardynamics.eu

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
vtp domain GyorHQ
vtp mode client
vtp version 3
vtp password Solar-Dynamics-2025

! STP Configuration (Primary for Server and Voice VLANs)
spanning-tree mode rapid-pvst
spanning-tree vlan 70,200 root secondary
spanning-tree vlan 10,15,20,50,100,160,255 root primary

! Remote access (SUBJECT TO CHANGE TO RADIUS)
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2

! LACP Etherchannel > MLS1
interface range gig3/0 - 3
	channel-group 1 mode active
interface Port-channel 1
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk native vlan 999

! Layer 3 VLAN Interfaces for Routing and EIGRP
interface vlan 10
    ip address 10.0.10.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:000A:10:0:15:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 15
    ip address 10.0.15.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:000F:10:0:15:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 20
    ip address 10.0.20.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0014:10:0:20:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 25
    ip address 10.0.25.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0019:10:0:25:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 50
    ip address 10.0.50.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0032:10:0:50:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 51
    ip address 10.0.51.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0033:10:0:51:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 70
    ip address 10.0.70.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0046:10:0:70:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 100
    ip address 10.0.100.2 255.255.252.0
    ipv6 address 2a:1dc:7c0:0064:10:0:100:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 104
    ip address 10.0.104.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0068:10:0:104:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 160
    ip address 10.0.160.2 255.255.240.0
    ipv6 address 2a:1dc:7c0:00A0:10:0:160:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 200
    ip address 10.0.200.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:00C8:10:0:200:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 220
    ip address 10.0.220.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:00DC:10:0:220:2/64
    ipv6 eigrp 100
    no shutdown

interface vlan 252
    ip address 10.0.253.2 255.255.252.0
    ipv6 address 2a:1dc:7c0:00FF:10:0:253:2/64
    ipv6 eigrp 100
    no shutdown

! > R1
interface gig0/0
	no switchport
	ip address 172.16.0.3 255.255.255.254
	ipv6 enable
	ipv6 eigrp 100
	no shutdown

! > R2
interface gig0/1
	no switchport
	ip address 172.16.0.7 255.255.255.254
	ipv6 enable
	ipv6 eigrp 100
	no shutdown

! IPv4 EIGRP Configuration
router eigrp 100! Hostname
    network 10.0.0.0 0.0.255.255
    network 172.16.0.2 0.0.0.1
    network 172.16.0.6 0.0.0.1
	no auto-summary
	router-id 2.2.2.2
    passive-interface default
    no passive-interface gig0/0
    no passive-interface gig0/1
    no passive-interface Port-channel 1

! IPv6 EIGRP Configuration
ipv6 router eigrp 100
	router-id 2.2.2.2
    passive-interface default
    no passive-interface gig0/0
    no passive-interface gig0/1
    no passive-interface Port-channel 1

!!! Unconfigured from here.


```



### HQ-R1
-  [x] Hostname
-  [x] Banner
-  [x] IPv4 EIGRP
-  [x] IPv6 EIGRP
-  [x] IP
-  [x] Login, SSH and authentication
-  [ ] NAT
-  [ ] Authentication with RADIUS

```
! Hostname
hostname HQ-R1
ip domain name hq.solardynamics.eu

! Convenience
no ip domain lookup

! Routing
ip routing
ipv6 unicast-routing

! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! Remote access (SUBJECT TO CHANGE TO RADIUS)
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2

! IP

! > HQ-MLS1
interface gig0/0
    ip address 172.16.0.0 255.255.255.254
    ip nat inside
	ipv6 enable
    ipv6 eigrp 100
    no shutdown

! > HQ-MLS2
interface gig1/0
    ip address 172.16.0.2 255.255.255.254
	ip nat inside
	ipv6 enable
    ipv6 eigrp 100
    no shutdown

interface gig2/0
    ip address 82.136.79.1 255.255.255.0
    ip nat outside
	ipv6 address 2a:1dc:7c0:FFFF:82:136:79:1/64
    ipv6 eigrp 100
    no shutdown

! IPv4 EIGRP Configuration
router eigrp 100! Hostname
    network 82.136.79.0 0.0.0.255
    network 172.16.0.0 0.0.0.1
    network 172.16.0.2 0.0.0.1
	no auto-summary
	router-id 3.3.3.3
    passive-interface default
    no passive-interface gig0/0
    no passive-interface gig1/0


! IPv6 EIGRP Configuration
ipv6 router eigrp 100
	router-id 3.3.3.3
    passive-interface default
    no passive-interface gig0/0
    no passive-interface gig1/0



```
### HQ-R2
-  [x] Hostname
-  [x] Banner
-  [x] IPv4 EIGRP
-  [x] IPv6 EIGRP
-  [x] IP
-  [x] Login, SSH and authentication
-  [ ] NAT
-  [ ] Authentication with RADIUS

```
! Hostname
hostname HQ-R2
ip domain name hq.solardynamics.eu

! Convenience
no ip domain lookup

! Routing
ip routing
ipv6 unicast-routing

! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! Remote access (SUBJECT TO CHANGE TO RADIUS)
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2

! IP

! > HQ-MLS1
interface gig0/0
    ip address 172.16.0.4 255.255.255.254
    ip nat inside
	ipv6 enable
    ipv6 eigrp 100
    no shutdown

! > HQ-MLS2
interface gig1/0
    ip address 172.16.0.6 255.255.255.254
    ip nat inside
	ipv6 enable
    ipv6 eigrp 100
    no shutdown

interface gig2/0
    ip address 82.136.79.2 255.255.255.0
    ip nat outside
	ipv6 address 2a:1dc:7c0:FFFF:82:136:79:2/64
    ipv6 eigrp 100
    no shutdown

! IPv4 EIGRP Configuration
router eigrp 100
    network 82.136.79.0 0.0.0.255
    network 172.16.0.4 0.0.0.1
    network 172.16.0.6 0.0.0.1
	no auto-summary
	router-id 4.4.4.4
    passive-interface default
    no passive-interface gig0/0
    no passive-interface gig1/0


! IPv6 EIGRP Configuration
ipv6 router eigrp 100
	router-id 4.4.4.4
    passive-interface default
    no passive-interface gig0/0
    no passive-interface gig1/0



```