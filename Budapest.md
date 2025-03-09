BP-MLS1
---

-  [x] Hostname
-  [x] Banner
-  [x] VTP
-  [x] LACP
-  [ ] STP, Portfast, BPDU guard.
-  [x] IP
-  [x] IPv4 EIGRP
-  [x] IPv6 EIGRP
-  [x] Trunk encapsulation / trunk setting 
-  [ ] DHCP snooping
-  [ ] IP helper address 
-  [ ] QOS (voice)
-  [x] FHRP
-  [x] Login, SSH and authentication
-  [ ] Authentication with RADIUS (bonus)

```
! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! Hostname
hostname BP-MLS1


! Convenience
no ip domain lookup

! Routing
ip routing
ipv6 unicast-routing

! VTP
vtp domain BUDAP
vtp mode server
vtp version 3
vtp password Solar-Dynamics-2025
do vtp primary

! STP !!!!!

spanning-tree vlan 10-70 root primary
spanning-tree vlan 100-199 root secondary

! Remote access
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2


! > R1
interface gig0/0
    no sw
    ip address 172.16.2.1 255.255.255.254
    ipv6 enable
    ipv6 eigrp 100
    no shu

! > R2
interface gig0/1
    no sw
    ip address 172.16.2.5 255.255.255.254
    ipv6 enable
    ipv6 eigrp 100
    no shu

! EtherChannel
interface Port-channel 1
 no switchport
 ip address 172.16.2.8 255.255.255.254
 ipv6 enable
 ipv6 eigrp 100
 ! ip

interface Xx/x
 no switchport
 channel-group 1 mode active
 no sh

interface Xx/x
 no switchport
 channel-group 1 mode active
 no sh


! VLANs
	vlan 10
	    name "Technikai ugyfelsz"
    vlan 20
	    name "Kereskedelmi ugyfelsz"
    vlan 30
	    name "Rendelések"
    vlan 40
	    name "Nemzetkozi kapcs"
    vlan 45
	    name "Ugyfelsz manager"
    vlan 50
	    name "Telephely igazgato"
    vlan 55
	    name "Nemzetk kapcs vezető"
    vlan 60
	    name "Rendszergazda"
    vlan 70
	    name "Szerverek"
    vlan 252
        name "Management"


! VLAN interfaces


    interface Vlan10
    ip address 10.2.10.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:010A:10:2:10:2/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    !
    standby 10 version 2
    standby 10 ip 10.2.10.1
    standby 10 priority 90
    standby 10 preempt
    !
    standby 2010 version 2
	standby 2010 preempt
	standby 2010 priority 90
	standby 2010 ipv6 2a:1dc:7c0:010A:10:2:10:1

    interface Vlan20
    ip address 10.2.20.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0114:10:2:20:2/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 20 version 2
    standby 20 ip 10.2.20.1
    standby 20 priority 90
    standby 20 preempt
    !
    standby 2020 version 2
	standby 2020 preempt
	standby 2020 priority 90
	standby 2020 ipv6 2a:1dc:7c0:0114:10:2:10:1

    interface Vlan30
    ip address 10.2.30.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:011E:10:2:30:2/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 30 version 2
    standby 30 ip 10.2.30.1
    standby 30 priority 90
    standby 30 preempt
    !
    standby 2030 version 2
	standby 2030 preempt
	standby 2030 priority 90
	standby 2030 ipv6 2a:1dc:7c0:011E:10:2:10:1

    interface Vlan40
    ip address 10.2.40.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0128:10:2:40:2/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 40 version 2
    standby 40 ip 10.2.40.1
    standby 40 priority 90
    standby 40 preempt
    !
    standby 2040 version 2
	standby 2040 preempt
	standby 2040 priority 90
	standby 2040 ipv6 2a:1dc:7c0:0128:10:2:10:1

    interface Vlan45
    ip address 10.2.45.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:012D:10:2:45:2/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 45 version 2
    standby 45 ip 10.2.45.1
    standby 45 priority 90
    standby 45 preempt
    !
    standby 2045 version 2
	standby 2045 preempt
	standby 2045 priority 90
	standby 2045 ipv6 2a:1dc:7c0:012D:10:2:10:1

    interface Vlan50
    ip address 10.2.50.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0132:10:2:50:2/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 50 version 2
    standby 50 ip 10.2.50.1
    standby 50 priority 90
    standby 50 preempt
    !
    standby 2050 version 2
	standby 2050 preempt
	standby 2050 priority 90
	standby 2050 ipv6 2a:1dc:7c0:0132:10:2:10:1

    interface Vlan55
    ip address 10.2.55.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0137:10:2:55:2/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 55 version 2
    standby 55 ip 10.2.55.1
    standby 55 priority 90
    standby 55 preempt
    !
    standby 2055 version 2
	standby 2055 preempt
	standby 2055 priority 90
	standby 2055 ipv6 2a:1dc:7c0:0137:10:2:10:1

    interface Vlan60
    ip address 10.2.60.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:013C:10:2:60:2/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 60 version 2
    standby 60 ip 10.2.60.1
    standby 60 priority 90
    standby 60 preempt
    !
    standby 2060 version 2
	standby 2060 preempt
	standby 2060 priority 90
	standby 2060 ipv6 2a:1dc:7c0:013C:10:2:10:1

    interface Vlan70
    ip address 10.2.70.2 255.255.255.0
    ipv6 address 2a:1dc:7c0:0146:10:2:70:2/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 70 version 2
    standby 70 ip 10.2.70.1
    standby 70 priority 90
    standby 70 preempt
    !
    standby 2070 version 2
	standby 2070 preempt
	standby 2070 priority 90
	standby 2070 ipv6 2a:1dc:7c0:0146:10:2:10:1

! Link to switches

! > BP-EM1-SW
	interface X
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! switchport trunk allowed vlan 10,20,30,40,45,50,55,60,70
        switchport trunk allowed vlan 10,20

! > BP-EM2-SW
	interface X
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! switchport trunk allowed vlan 10,20,30,40,45,50,55,60,70
        switchport trunk allowed vlan 30,40,45

! > BP-EM3-SW
	interface X
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! switchport trunk allowed vlan 10,20,30,40,45,50,55,60,70
        switchport trunk allowed vlan 50,55,60,70



! EIGRP

router eigrp 100
 router-id 1.1.1.1
 network 172.16.2.0 0.0.0.1  # MLS 1 <-> Router 1
 network 172.16.2.4 0.0.0.1  # MLS 1 <-> Router 2
 network 172.16.2.8 0.0.0.1  # MLS 1 <-> MLS 2
 network 10.2.0.0 0.0.255.255  # Összegzett címek
 no auto-summary
 passive-interface default
 no passive-interface GigabitEthernet0/0 # R1
 no passive-interface GigabitEthernet0/1  # R2
 no passive-interface Port-channel1  # EtherChannel


ipv6 router eigrp 100
 router-id 1.1.1.1
 passive-interface default
 no passive-interface gig0/0
 no passive-interface gig0/1
 no passive-interface Port-channel 1




BP-MLS-2
---

-  [x] Hostname
-  [x] Banner
-  [x] VTP
-  [ ] LACP
-  [ ] STP, Portfast, BPDU guard.
-  [ ] IP
-  [x] IPv4 EIGRP
-  [ ] IPv6 EIGRP
-  [ ] Trunk encapsulation / trunk setting 
-  [ ] DHCP snooping
-  [ ] IP helper address 
-  [ ] QOS (voice)
-  [x] FHRP
-  [x] Login, SSH and authentication
-  [ ] Authentication with RADIUS (bonus)

```
! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! Hostname
hostname BP-MLS2



! Convenience
no ip domain lookup

! Routing
ip routing
ipv6 unicast-routing

! VTP
vtp domain BUDAP
vtp mode client
vtp password Solar-Dynamics-2025

! STP
spanning-tree vlan 10-70 root secondary
spanning-tree vlan 100-199 root primary

! Remote access
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2


! > R1
interface gig0/0
    no sw
    ip address 172.16.2.x 255.255.255.254
    ipv6 enable
    ipv6 eigrp 100
    no shu

! > R2
interface gig0/1
    no sw
    ip address 172.16.2.x 255.255.255.254
    ipv6 enable
    ipv6 eigrp 100
    no shu


! EtherChannel
interface Port-channel1
 no switchport
 ip address 172.16.2.9 255.255.255.254
 ipv6 enable
 ipv6 eigrp 100

interface Xx/x
 no switchport
 channel-group 1 mode active
 no sh

interface Xx/x
 no switchport
 channel-group 1 mode active
 no sh



! SVI


interface Vlan10
 ip address 10.2.10.3 255.255.255.0
 ipv6 address 2a:1dc:7c0:010A:10:2:10:3/64
 ipv6 eigrp 100
 ! DHCP relay
    ip helper-address 10.0.70.20
    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    !
    standby 10 version 2
    standby 10 ip 10.2.10.1
    standby 10 priority 100
    standby 10 preempt
    !
    standby 2010 version 2
	standby 2010 preempt
	standby 2010 priority 100
	standby 2010 ipv6 2a:1dc:7c0:010A:10:2:10:1

interface Vlan20
 ip address 10.2.20.3 255.255.255.0
 ipv6 address 2a:1dc:7c0:0114:10:2:20:3/64
 ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 20 version 2
    standby 20 ip 10.2.20.1
    standby 20 priority 100
    standby 20 preempt
    !
    standby 2020 version 2
	standby 2020 preempt
	standby 2020 priority 100
	standby 2020 ipv6 2a:1dc:7c0:0114:10:2:10:1

interface Vlan30
 ip address 10.2.30.3 255.255.255.0
 ipv6 address 2a:1dc:7c0:011E:10:2:30:3/64
 ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 30 version 2
    standby 30 ip 10.2.30.1
    standby 30 priority 100
    standby 30 preempt
    !
    standby 2030 version 2
	standby 2030 preempt
	standby 2030 priority 100
	standby 2030 ipv6 2a:1dc:7c0:011E:10:2:10:1

interface Vlan40
 ip address 10.2.40.3 255.255.255.0
 ipv6 address 2a:1dc:7c0:0128:10:2:40:3/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 40 version 2
    standby 40 ip 10.2.40.1
    standby 40 priority 100
    standby 40 preempt
    !
    standby 2040 version 2
	standby 2040 preempt
	standby 2040 priority 100
	standby 2040 ipv6 2a:1dc:7c0:0128:10:2:10:1

interface Vlan45
 ip address 10.2.45.3 255.255.255.0
 ipv6 address 2a:1dc:7c0:012D:10:2:45:3/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 45 version 2
    standby 45 ip 10.2.45.1
    standby 45 priority 100
    standby 45 preempt
    !
    standby 2045 version 2
	standby 2045 preempt
	standby 2045 priority 100
	standby 2045 ipv6 2a:1dc:7c0:012D:10:2:10:1


interface Vlan50
 ip address 10.2.50.3 255.255.255.0
 ipv6 address 2a:1dc:7c0:0132:10:2:50:3/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 50 version 2
    standby 50 ip 10.2.50.1
    standby 50 priority 100
    standby 50 preempt
    !
    standby 2050 version 2
	standby 2050 preempt
	standby 2050 priority 100
	standby 2050 ipv6 2a:1dc:7c0:0132:10:2:10:1


interface Vlan55
 ip address 10.2.55.3 255.255.255.0
 ipv6 address 2a:1dc:7c0:0137:10:2:55:3/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 55 version 2
    standby 55 ip 10.2.55.1
    standby 55 priority 100
    standby 55 preempt
    !
    standby 2055 version 2
	standby 2055 preempt
	standby 2055 priority 100
	standby 2055 ipv6 2a:1dc:7c0:0137:10:2:10:1

interface Vlan60
 ip address 10.2.60.3 255.255.255.0
 ipv6 address 2a:1dc:7c0:013C:10:2:60:3/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 60 version 2
    standby 60 ip 10.2.60.1
    standby 60 priority 100
    standby 60 preempt
    !
    standby 2060 version 2
	standby 2060 preempt
	standby 2060 priority 100
	standby 2060 ipv6 2a:1dc:7c0:013C:10:2:10:1

interface Vlan70
 ip address 10.2.70.3 255.255.255.0
 ipv6 address 2a:1dc:7c0:0146:10:2:70:3/64
	ipv6 eigrp 100
    ! DHCP relay
	ip helper-address 10.0.70.20
	ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
    standby 70 version 2
    standby 70 ip 10.2.70.1
    standby 70 priority 100
    standby 70 preempt
    !
    standby 2070 version 2
	standby 2070 preempt
	standby 2070 priority 100
	standby 2070 ipv6 2a:1dc:7c0:0146:10:2:10:1


! Links to switches

! > BP-EM1-SW
	interface X
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! switchport trunk allowed vlan 10,20,30,40,45,50,55,60,70
        switchport trunk allowed vlan 10,20

! > BP-EM2-SW
	interface X
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! switchport trunk allowed vlan 10,20,30,40,45,50,55,60,70
        switchport trunk allowed vlan 30,40,45

! > BP-EM3-SW
	interface X
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! switchport trunk allowed vlan 10,20,30,40,45,50,55,60,70
        switchport trunk allowed vlan 50,55,60,70

! EIGRP

router eigrp 100
 router-id 2.2.2.2
 network 172.16.2.3 0.0.0.0  # MLS 2 <-> Router 1
 network 172.16.2.7 0.0.0.0  # MLS 2 <-> Router 2
 network 10.2.0.0 0.0.255.255  # Összegzett címek
 ! passive-interface default
 no passive-interface GigabitEthernet0/0  # R1
 no passive-interface GigabitEthernet0/1  # R2
 no passive-interface Port-channel1  # EtherChannel

ipv6 router eigrp 100
 router-id 2.2.2.2
 passive-interface default
 no passive-interface gig0/0
 no passive-interface gig0/1
 no passive-interface Port-channel 1
```






R1
---

-  [x] Hostname
-  [x] Banner
-  [x] IPv4 EIGRP
-  [ ] IPv6 EIGRP
-  [ ] IP
-  [x] Login, SSH and authentication
-  [ ] NAT
-  [ ] Authentication with RADIUS
-  [ ] DMVPN:
	- [ ] GRE (Packet encapsulation into another packet)
	- [ ] NHRP (Next Hop Resolution Protocol, don't know what this is)
	- [ ] IPsec (Encryption)
	- [ ] Routing protocol (probably EIGRP)

```
! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#


! Hostname
hostname BP-R1


! Convenience
no ip domain lookup

! Routing
ip routing
ipv6 unicast-routing

ip route 0.0.0.0 0.0.0.0 82.X.X.X
ipv6 route ::/0 gigX/X

! Remote access
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2


! NAT

! ACL
    ip access-list standard BP-ACL-internal-client
    permit 10.2.0.0 0.0.255.255
    ! Összegzett cím, átírandó :D
    exit
    ip access-list standard BP-ACL-external-client
    permit 10.2.150.0 0.0.0.255
    exit

! NAT pools
    ip nat pool BP-internal-client-pool 82.1.79.40 82.1.79.45 netmask 255.255.255.224               ??
    ip nat pool BP-external-client-pool 82.1.79.46 82.1.79.50 netmask 255.255.255.224               ??

! NAT for company devices
    ip nat inside source list BP-ACL-internal-client pool BP-internal-client-pool overload

! NAT for external devices
    ip nat inside source list BP-ACL-external-client pool BP-external-client-pool overload



! Interfaces

! > HQ-MLS1
	interface Xx/x
	    ip address 172.16.2.0 255.255.255.254
	    ip nat inside
		ipv6 enable
	    ipv6 eigrp 100
	    no shutdown

! > HQ-MLS2
	interface Xx/x
	    ip address 172.16.2.2 255.255.255.254
	    ip nat inside
		ipv6 enable
	    ipv6 eigrp 100
	    no shutdown

! ISP
    interface Xx/x
	    ip address 82.1.79.3X 255.255.255.224
	    ip nat outside
        ipv6 address 2a:1dc:7c0:FFFF:82:X:X:X/64
	    ipv6 eigrp 100
	    no shutdown


! EIGRP

router eigrp 100
 ! Redistribute !
 network 82.X.X.X 0.0.0.31
 network 172.16.2.0 0.0.0.3
 network 172.16.2.2 0.0.0.3
 no auto-summary
 router-id 3.3.3.3
 passive-interface default
 no passive-interface GigabitEthernet0/0  # > MLS 1
 no passive-interface GigabitEthernet0/1  # > MLS 2
 no passive-interface GigabitEthernetX/X  # > ISP ??

ipv6 router eigrp 100
	! Redistribute !!
	router-id 3.3.3.3
	passive-interface default
	no passive-interface gig0/0
    no passive-interface gig1/0




! R2

-  [x] Hostname
-  [x] Banner
-  [x] IPv4 EIGRP
-  [ ] IPv6 EIGRP
-  [ ] IP
-  [x] Login, SSH and authentication
-  [ ] NAT
-  [ ] Authentication with RADIUS
-  [ ] DMVPN:
	- [ ] GRE (Packet encapsulation into another packet)
	- [ ] NHRP (Next Hop Resolution Protocol, don't know what this is)
	- [ ] IPsec (Encryption)
	- [ ] Routing protocol (probably EIGRP)

! Banner
    banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
    banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
    banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#


! Hostname
    hostname BP-R2


! Convenience
    no ip domain lookup

! Routing
    ip routing
    ipv6 unicast-routing

 ip route 0.0.0.0 0.0.0.0 gigX/X ?? Different from R1?
 ipv6 route ::/0 gigX/X

! Remote access
    username solaire secret Solar-Dynamics-2025
    crypto key generate rsa general-keys modulus 2048
    line vty 0 15
    login local
    transport input ssh
    ip ssh version 2

! NAT

! ACL
    ip access-list standard BP-ACL-internal-client
    permit 10.2.0.0 0.0.255.255
    ! Összegzett cím, átírandó :D

    exit

            
    ip access-list standard BP-ACL-external-client
    permit 10.2.150.0 0.0.0.255
    exit

! NAT pools
    ip nat pool BP-internal-client-pool 82.1.79.40 82.1.79.45 netmask 255.255.255.224               ??
    ip nat pool BP-external-client-pool 82.1.79.46 82.1.79.50 netmask 255.255.255.224               ??

! NAT for company devices
    ip nat inside source list BP-ACL-internal-client pool BP-internal-client-pool overload

! NAT for external devices
    ip nat inside source list BP-ACL-external-client pool BP-external-client-pool overload

! Interfaces

! > HQ-MLS1
	interface Xx/x
	    ip address 172.16.2.4 255.255.255.254
	    ip nat inside
		ipv6 enable
	    ipv6 eigrp 100
	    no shutdown

! > HQ-MLS2
	interface Xx/x
	    ip address 172.16.2.6 255.255.255.254
	    ip nat inside
		ipv6 enable
	    ipv6 eigrp 100
	    no shutdown

! ISP
    interface Xx/x
	    ip address 82.1.79.3X 255.255.255.224
	    ip nat outside
        ipv6 address 2a:1dc:7c0:FFFF:82:X:X:X/64
        ipv6 eigrp 100
	    no shutdown


! EIGRP

    router eigrp 100
    ! Redistribute !!
    router-id 4.4.4.4
    network 172.16.2.4 0.0.0.3
    network 172.16.2.6 0.0.0.3
    network 82.X.X.X 0.0.0.31   # > ISP
    passive-interface default
    no passive-interface GigabitEthernet0/0  # > MLS 1
    no passive-interface GigabitEthernet0/1  # > MLS 2
    no passive-interface GigabitEhternetX/X  # > ISP ??


    ipv6 router eigrp 100
	router-id 4.4.4.4
	! Redistribute !!
    passive-interface default
    no passive-interface gig0/0
    no passive-interface gig1/0
```



 ! Switches:

 BP-EM1-SW

- [ ] Trunk portok
- [ ] DHCP snooping limit
- [ ] DHCP snooping trust
- [ ] Storm control
- [ ] Nonegotiate
- [ ] Port security
- [ ] Portfast
- [ ] BPDU guard

! Hostname
hostname BP-EM1-SW
ip domain name XX.solardynamics.eu

! Convenience
no ip domain lookup

! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! VTP
vtp domain BUDAP
vtp mode client
vtp version 3
vtp password Solar-Dynamics-2025

! STP Configuration
spanning-tree mode rapid-pvst

! Remote access (SUBJECT TO CHANGE TO RADIUS)
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2

! Management IP
interface vlan 252
	ip address 10.2.254.1 255.255.252.0
	ipv6 address 2a:1dc:7c0:0Xff:10:2:254:1/64
	no shutdown

! > BP-MLS1
interface gigX/X
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

! > BP-MLS2
interface gigX/X
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

interface faX/X
	no shutdown
    switchport mode access 10
	switchport voice vlan 200
	! Security
	switchport port-security
    switchport port-security mac-address sticky
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10

interface faX/X
	no shutdown
    switchport mode access 20
	switchport voice vlan 200
	! Security
	switchport port-security
    switchport port-security mac-address sticky
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10




 BP-EM2-SW

- [ ] Trunk portok
- [ ] DHCP snooping limit
- [ ] DHCP snooping trust
- [ ] Storm control
- [ ] Nonegotiate
- [ ] Port security
- [ ] Portfast
- [ ] BPDU guard

! Hostname
hostname BP-EM2-SW
ip domain name XX.solardynamics.eu

! Convenience
no ip domain lookup

! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! VTP
vtp domain BUDAP
vtp mode client
vtp version 3
vtp password Solar-Dynamics-2025

! STP Configuration
spanning-tree mode rapid-pvst

! Remote access (SUBJECT TO CHANGE TO RADIUS)
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2

! Management IP
interface vlan 252
	ip address 10.2.254.2 255.255.252.0
	ipv6 address 2a:1dc:7c0:0Xff:10:2:254:2/64
	no shutdown

! > BP-MLS1
interface gigX/X
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

! > BP-MLS2
interface gigX/X
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

interface faX/X
	no shutdown
    switchport mode access 30
	switchport voice vlan 200
	! Security
	switchport port-security
    switchport port-security mac-address sticky
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10

interface faX/X
	no shutdown
    switchport mode access 40
	switchport voice vlan 200
	! Security
	switchport port-security
    switchport port-security mac-address sticky
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10

interface faX/X
	no shutdown
    switchport mode access 45
	switchport voice vlan 200
	! Security
	switchport port-security
    switchport port-security mac-address sticky
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10


BP-EM3-SW

- [ ] Trunk portok
- [ ] DHCP snooping limit
- [ ] DHCP snooping trust
- [ ] Storm control
- [ ] Nonegotiate
- [ ] Port security
- [ ] Portfast
- [ ] BPDU guard

! Hostname
hostname BP-EM3-SW
ip domain name XX.solardynamics.eu

! Convenience
no ip domain lookup

! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! VTP
vtp domain BUDAP
vtp mode client
vtp version 3
vtp password Solar-Dynamics-2025

! STP Configuration
spanning-tree mode rapid-pvst

! Remote access (SUBJECT TO CHANGE TO RADIUS)
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2

! Management IP
interface vlan 252
	ip address 10.2.254.3 255.255.252.0
	ipv6 address 2a:1dc:7c0:0Xff:10:2:254:3/64
	no shutdown

! > BP-MLS1
interface gigX/X
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

! > BP-MLS2
interface gigX/X
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

interface faX/X
	no shutdown
    switchport mode access 50
	switchport voice vlan 200
	! Security
	switchport port-security
    switchport port-security mac-address sticky
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10

interface faX/X
	no shutdown
    switchport mode access 55
	switchport voice vlan 200
	! Security
	switchport port-security
    switchport port-security mac-address sticky
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10

interface faX/X
	no shutdown
    switchport mode access 60
	switchport voice vlan 200
	! Security
	switchport port-security
    switchport port-security mac-address sticky
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10


interface faX/X
	no shutdown
    switchport mode access 70
	switchport voice vlan 200
	! Security
	switchport port-security
    switchport port-security mac-address sticky
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10
