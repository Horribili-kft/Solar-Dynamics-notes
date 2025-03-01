BP-MLS1

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
    ! EIGRP
    no shu

! > R2
interface gig0/1
    no sw
    ip address 172.16.2.5 255.255.255.254
    ipv6 enable
    ! EIGRP
    no shu

! EtherChannel
interface Port-channel1
 no switchport
 ! ip

interface Xx/x
 no switchport
 channel-group 1 mode active

interface Xx/x
 no switchport
 channel-group 1 mode active


! STP

spanning-tree vlan 10-70 root primary
spanning-tree vlan 100-199 root secondary


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
    standby 10 ip 10.2.10.1
    standby 10 priority 110
    standby 10 preempt

    interface Vlan20
    ip address 10.2.20.2 255.255.255.0
    standby 20 ip 10.2.20.1
    standby 20 priority 110
    standby 20 preempt

    interface Vlan30
    ip address 10.2.30.2 255.255.255.0
    standby 30 ip 10.2.30.1
    standby 30 priority 110
    standby 30 preempt

    interface Vlan40
    ip address 10.2.40.2 255.255.255.0
    standby 40 ip 10.2.40.1
    standby 40 priority 110
    standby 40 preempt

    interface Vlan45
    ip address 10.2.45.2 255.255.255.0
    standby 45 ip 10.2.45.1
    standby 45 priority 110
    standby 45 preempt

    interface Vlan50
    ip address 10.2.50.2 255.255.255.0
    standby 50 ip 10.2.50.1
    standby 50 priority 110
    standby 50 preempt

    interface Vlan55
    ip address 10.2.55.2 255.255.255.0
    standby 55 ip 10.2.55.1
    standby 55 priority 110
    standby 55 preempt

    interface Vlan60
    ip address 10.2.60.2 255.255.255.0
    standby 60 ip 10.2.60.1
    standby 60 priority 110
    standby 60 preempt

    interface Vlan70
    ip address 10.2.70.2 255.255.255.0
    standby 70 ip 10.2.70.1
    standby 70 priority 110
    standby 70 preempt

! Link to switches

! EIGRP

router eigrp 100
 network 172.16.2.0 0.0.0.1  # MLS 1 <-> Router 1
 network 172.16.2.4 0.0.0.1  # MLS 1 <-> Router 2
 network 10.2.0.0 0.0.255.255  # Összegzett címek
 ! passive-interface default
 no passive-interface GigabitEthernet0/0 # R1
 no passive-interface GigabitEthernet0/1  # R2
 no passive-interface Port-channel1  # EtherChannel





BP-MLS-2

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
vtp mode client
vtp password Solar-Dynamics-2025

! Remote access
username solaire secret Solar-Dynamics-2025
crypto key generate rsa general-keys modulus 2048
line vty 0 15
login local
transport input ssh
ip ssh version 2

! EtherChannel
interface Port-channel1
 no switchport
 ! ip

interface Xx/x
 no switchport
 channel-group 1 mode active

interface Xx/x
 no switchport
 channel-group 1 mode active


! STP
spanning-tree vlan 10-70 root secondary
spanning-tree vlan 100-199 root primary


! SVI


interface Vlan10
 ip address 10.2.10.3 255.255.255.0
 standby 10 ip 10.2.10.1
 standby 10 priority 100
 standby 10 preempt

interface Vlan20
 ip address 10.2.20.3 255.255.255.0
 standby 20 ip 10.2.20.1
 standby 20 priority 100
 standby 20 preempt

interface Vlan30
 ip address 10.2.30.3 255.255.255.0
 standby 30 ip 10.2.30.1
 standby 30 priority 100
 standby 30 preempt

interface Vlan40
 ip address 10.2.40.3 255.255.255.0
 standby 40 ip 10.2.40.1
 standby 40 priority 100
 standby 40 preempt

interface Vlan45
 ip address 10.2.45.3 255.255.255.0
 standby 45 ip 10.2.45.1
 standby 45 priority 100
 standby 45 preempt

interface Vlan50
 ip address 10.2.50.3 255.255.255.0
 standby 50 ip 10.2.50.1
 standby 50 priority 100
 standby 50 preempt

interface Vlan55
 ip address 10.2.55.3 255.255.255.0
 standby 55 ip 10.2.55.1
 standby 55 priority 100
 standby 55 preempt

interface Vlan60
 ip address 10.2.60.3 255.255.255.0
 standby 60 ip 10.2.60.1
 standby 60 priority 100
 standby 60 preempt

interface Vlan70
 ip address 10.2.70.3 255.255.255.0
 standby 70 ip 10.2.70.1
 standby 70 priority 100
 standby 70 preempt

! EIGRP

router eigrp 100
 network 172.16.2.3 0.0.0.0  # MLS 2 <-> Router 1
 network 172.16.2.7 0.0.0.0  # MLS 2 <-> Router 2
 network 10.2.0.0 0.0.255.255  # Összegzett címek
 ! passive-interface default
 no passive-interface GigabitEthernet0/0  # R1
 no passive-interface GigabitEthernet0/1  # R2
 no passive-interface Port-channel1  # EtherChannel







! R1

! EIGRP

router eigrp 100
 network 172.16.2.0 0.0.0.3
 network 172.16.2.2 0.0.0.3
 ! passive-interface default
 no passive-interface GigabitEthernet0/0  # > MLS 1
 no passive-interface GigabitEthernet0/1  # > MLS 2




! R2

! EIGRP

router eigrp 100
 network 172.16.2.4 0.0.0.3
 network 172.16.2.6 0.0.0.3
 ! passive-interface default
 no passive-interface GigabitEthernet0/0  # > MLS 1
 no passive-interface GigabitEthernet0/1  # > MLS 2