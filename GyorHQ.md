#cisco #config
MLS
---
### SDM template
You need to change the SDM template so that IPv6 is enabled.

```
en
conf t
sdm prefer dual-ipv4-and-ipv6 default
do reload
```

Hibák: HSRP konfiguráció

### HQ-MLS1

Primary root bridge
-  [x] Hostname
-  [x] Banner
-  [x] VTP
-  [x] LACP
-  [x] STP, Portfast, BPDU guard.
-  [x] IP
-  [x] IPv4 EIGRP
-  [x] IPv6 EIGRP
-  [x] Trunk encapsulation / trunk setting 
-  [ ] DHCP snooping
-  [x] IP helper address 
-  [ ] QOS (voice)
-  [x] FHRP
	- [x] IPv4
	- [x] IPv6
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

! Layer 3 routing links

	! LACP Etherchannel > MLS2
	interface range gig3/0 - 3
	    no switchport
		channel-group 1 mode active
		no shutdown
		
	interface Port-channel 1
		description link to MLS2
		no switchport
		ip address 172.16.0.8 255.255.255.254
		ipv6 enable
		ipv6 eigrp 100
		no shutdown
		! Configs for layer 2 mode, which we don't use anymore
		! switchport trunk encapsulation dot1q
		! switchport mode trunk
		! switchport trunk native vlan 999

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

! VLANs
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
	
	vlan 252
	    name "Management"

! Layer 3 VLAN Interfaces
	interface vlan 10
	    ip address 10.0.10.1 255.255.255.0
	    ipv6 address 2a:1dc:7c0:000A:10:0:10:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 10 version 2
				standby 10 ip 10.0.10.254
				standby 10 priority 90
				standby 2010 preempt
			! IPv6:
				standby 2010 version 2
				standby 2010 preempt
				standby 2010 priority 90
				standby 2010 ipv6 2a:1dc:7c0:000A:10:0:10:254
	    
	    no shutdown
	
	interface vlan 15
	    ip address 10.0.15.1 255.255.255.0
	    ipv6 address 2a:1dc:7c0:000F:10:0:15:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4
				standby 15 version 2
				standby 15 ip 10.0.15.254
				standby 15 priority 90
				standby 15 preempt
			! IPv6	
				standby 2015 version 2
				standby 2015 preempt
				standby 2015 priority 90
				standby 2015 ipv6 2a:1dc:7c0:000F:10:0:15:254
				
	    no shutdown
	
	interface vlan 20
	    ip address 10.0.20.1 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0014:10:0:20:1/64
	    ipv6 eigrp 100
		! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 20 version 2
				standby 20 ip 10.0.20.254
				standby 20 priority 90
				standby 20 preempt
			! IPv6:
				standby 2020 version 2
				standby 2020 preempt
				standby 2020 priority 90
				standby 2020 ipv6 2a:1dc:7c0:0014:10:0:20:254
		no shutdown
	
	interface vlan 25
	    ip address 10.0.25.1 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0019:10:0:25:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 25 version 2
				standby 25 ip 10.0.25.254
				standby 25 priority 90
				standby 25 preempt
			! IPv6:
				standby 2025 version 2
				standby 2025 preempt
				standby 2025 priority 90
				standby 2025 ipv6 2a:1dc:7c0:0019:10:0:25:254
	    no shutdown
	
	interface vlan 50
	    ip address 10.0.50.1 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0032:10:0:50:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 50 version 2
				standby 50 ip 10.0.50.254
				standby 50 priority 90
				standby 50 preempt
			! IPv6:
				standby 2050 version 2
				standby 2050 preempt
				standby 2050 priority 90
				standby 2050 ipv6 2a:1dc:7c0:0032:10:0:50:254
	    no shutdown
	
	interface vlan 51
	    ip address 10.0.51.1 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0033:10:0:51:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 51 version 2
				standby 51 ip 10.0.51.254
				standby 51 priority 90
				standby 51 preempt
			! IPv6:
				standby 2051 version 2
				standby 2051 preempt
				standby 2051 priority 90
				standby 2051 ipv6 2a:1dc:7c0:0033:10:0:51:254
	    no shutdown
	
	interface vlan 70
	    ip address 10.0.70.1 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0046:10:0:70:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 70 version 2
				standby 70 ip 10.0.70.254
				standby 70 priority 110
				standby 70 preempt
			! IPv6:
				standby 2070 version 2
				standby 2070 preempt
				standby 2070 priority 110
				standby 2070 ipv6 2a:1dc:7c0:0046:10:0:70:254
	    no shutdown
	
	interface vlan 100
	    ip address 10.0.100.1 255.255.252.0
	    ipv6 address 2a:1dc:7c0:0064:10:0:100:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 100 version 2
				standby 100 ip 10.0.103.254
				standby 100 priority 90
				standby 100 preempt
			! IPv6:
				standby 2100 version 2
				standby 2100 preempt
				standby 2100 priority 90
				standby 2100 ipv6 2a:1dc:7c0:0064:10:0:103:254
		no shutdown
	
	interface vlan 104
	    ip address 10.0.104.1 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0068:10:0:104:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 104 version 2
				standby 104 ip 10.0.104.254
				standby 104 priority 90
				standby 104 preempt
			! IPv6:
				standby 2104 version 2
				standby 2104 preempt
				standby 2104 priority 90
				standby 2104 ipv6 2a:1dc:7c0:0068:10:0:104:254
	    no shutdown
	
	interface vlan 160
	    ip address 10.0.160.1 255.255.252.0
	    ipv6 address 2a:1dc:7c0:00A0:10:0:160:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 160 version 2
				standby 160 ip 10.0.163.254
				standby 160 priority 90
				standby 160 preempt
			! IPv6:
				standby 2160 version 2
				standby 2160 preempt
				standby 2160 priority 90
				standby 2160 ipv6 2a:1dc:7c0:00A0:10:0:163:254
	    no shutdown
	
	interface vlan 200
	    ip address 10.0.200.1 255.255.255.0
	    ipv6 address 2a:1dc:7c0:00C8:10:0:200:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 200 version 2
				standby 200 ip 10.0.200.254
				standby 200 priority 110
				standby 200 preempt
			! IPv6:
				standby 2200 version 2
				standby 2200 preempt
				standby 2200 priority 110
				standby 2200 ipv6 2a:1dc:7c0:00C8:10:0:200:254
		no shutdown
	
	interface vlan 220
	    ip address 10.0.220.1 255.255.255.0
	    ipv6 address 2a:1dc:7c0:00DC:10:0:220:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 220 version 2
				standby 220 ip 10.0.220.254
				standby 220 priority 90
				standby 220 preempt
			! IPv6:
				standby 2220 version 2
				standby 2220 preempt
				standby 2220 priority 90
				standby 2220 ipv6 2a:1dc:7c0:00DC:10:0:220:254
		no shutdown
	
	interface vlan 252
	    ip address 10.0.253.1 255.255.252.0
	    ipv6 address 2a:1dc:7c0:00FF:10:0:253:1/64
	    ipv6 eigrp 100
	    ! DHCP relay
		ip helper-address 10.0.70.20
		ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
		! HSRP
			! IPv4:
				standby 252 version 2
				standby 252 ip 10.0.255.254
				standby 252 priority 90
				standby 252 preempt
			! IPv6:
				standby 2252 version 2
				standby 2252 preempt
				standby 2252 priority 90
				standby 2252 ipv6 2a:1dc:7c0:00FF:10:0:255:254
	    no shutdown

! Links to access layer switches

	! > HQ-OFFICE-S1
	interface gig0/2
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! All VLANs, less secure
		! switchport trunk allowed vlan 10,15,20,25,50,51,70,100,104,160,200,220,252
		switchport trunk allowed vlan 10,15,100,220,200,252
	
	! > HQ-OFFICE-S2
	interface gig0/3
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! All VLANs, less secure
		! switchport trunk allowed vlan 10,15,20,25,50,51,70,100,104,160,200,220,252
		switchport trunk allowed vlan 20,25,100,160,200,252

	! > HQ-WS-S1
	interface gig1/0
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! All VLANs, less secure
		! switchport trunk allowed vlan 10,15,20,25,50,51,70,100,104,160,200,220,252
		switchport trunk allowed vlan 50,104,200,252

	! > HQ-WS-S2
	interface gig1/1
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! All VLANs, less secure
		! switchport trunk allowed vlan 10,15,20,25,50,51,70,100,104,160,200,220,252
		switchport trunk allowed vlan 51,104,200,252

! Links to servers

	! > SD-HQ-LIN1
	interface gig2/0
		no shutdown
		switchport mode access
		switchport access vlan 70


	! > SD-HQ-WIN1
	interface gig2/1
		no shutdown
		switchport mode access
		switchport access vlan 70

! IPv4 EIGRP Configuration
	router eigrp 100
	    network 10.0.0.0 0.0.255.255
	    network 172.16.0.0 0.0.0.1
	    network 172.16.0.4 0.0.0.1
	    network 172.16.0.8 0.0.0.1
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
-  [x] Trunk encapsulation / trunk setting 
-  [ ] DHCP snooping
-  [x] IP helper address 
-  [ ] QOS (voice)
-  [x] FHRP
	- IPv4 [x]
	- IPv6 [x]
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

! STP Configuration (Secondary for Server and Voice VLANs, primary for all else)
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

! Layer 3 routing links

	! LACP Etherchannel > MLS1
	interface range gig3/0 - 3
		no switchport
		channel-group 1 mode active
		no shutdown
		
	interface Port-channel 1
		description link to MLS1
		no switchport
		ip address 172.16.0.9 255.255.255.254
		ipv6 enable
		ipv6 eigrp 100
		no shutdown
		! Configs for layer 2 mode, which we don't use anymore
		! switchport trunk encapsulation dot1q
		! switchport mode trunk
		! switchport trunk native vlan 999
	
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

! Layer 3 VLAN Interfaces
	interface vlan 10
	    ip address 10.0.10.2 255.255.255.0
	    ipv6 address 2a:1dc:7c0:000A:10:0:10:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 10 version 2
				standby 10 ip 10.0.10.254
				standby 10 priority 110
				standby 10 preempt
			! IPv6:
				standby 2010 version 2
				standby 2010 preempt
				standby 2010 priority 110
				standby 2010 ipv6 2a:1dc:7c0:000A:10:0:10:254
	    no shutdown
	
	interface vlan 15
	    ip address 10.0.15.2 255.255.255.0
	    ipv6 address 2a:1dc:7c0:000F:10:0:15:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 15 version 2
				standby 15 ip 10.0.15.254
				standby 15 priority 110
				standby 15 preempt
			! IPv6:
				standby 2015 version 2
				standby 2015 preempt
				standby 2015 priority 110
				standby 2015 ipv6 2a:1dc:7c0:000F:10:0:15:254
		no shutdown
	
	interface vlan 20
	    ip address 10.0.20.2 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0014:10:0:20:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 20 version 2
				standby 20 ip 10.0.20.254
				standby 20 priority 110
				standby 20 preempt
			! IPv6:
				standby 2020 version 2
				standby 2020 preempt
				standby 2020 priority 110
				standby 2020 ipv6 2a:1dc:7c0:0014:10:0:20:254
	    no shutdown
	
	interface vlan 25
	    ip address 10.0.25.2 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0019:10:0:25:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 25 version 2
				standby 25 ip 10.0.25.254
				standby 25 priority 110
				standby 25 preempt
			! IPv6:
				standby 2025 version 2
				standby 2025 preempt
				standby 2025 priority 110
				standby 2025 ipv6 2a:1dc:7c0:0019:10:0:25:254
	    no shutdown
	
	interface vlan 50
	    ip address 10.0.50.2 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0032:10:0:50:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 50 version 2
				standby 50 ip 10.0.50.254
				standby 50 priority 110
				standby 50 preempt
			! IPv6:
				standby 2050 version 2
				standby 2050 preempt
				standby 2050 priority 110
				standby 2050 ipv6 2a:1dc:7c0:0032:10:0:50:254
	    no shutdown
	
	interface vlan 51
	    ip address 10.0.51.2 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0033:10:0:51:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 51 version 2
				standby 51 ip 10.0.51.254
				standby 51 priority 110
				standby 51 preempt
			! IPv6:
				standby 2051 version 2
				standby 2051 preempt
				standby 2051 priority 110
				standby 2051 ipv6 2a:1dc:7c0:0033:10:0:51:254
	    no shutdown
	
	interface vlan 70
	    ip address 10.0.70.2 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0046:10:0:70:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 70 version 2
				standby 70 ip 10.0.70.254
				standby 70 priority 90
				standby 70 preempt
			! IPv6:
				standby 2070 version 2
				standby 2070 preempt
				standby 2070 priority 90
				standby 2070 ipv6 2a:1dc:7c0:0046:10:0:70:254
	    no shutdown
	
	interface vlan 100
	    ip address 10.0.100.2 255.255.252.0
	    ipv6 address 2a:1dc:7c0:0064:10:0:100:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 100 version 2
				standby 100 ip 10.0.103.254
				standby 100 priority 110
				standby 100 preempt
			! IPv6:
				standby 2100 version 2
				standby 2100 preempt
				standby 2100 priority 110
				standby 2100 ipv6 2a:1dc:7c0:0064:10:0:103:254
	    no shutdown
	
	interface vlan 104
	    ip address 10.0.104.2 255.255.255.0
	    ipv6 address 2a:1dc:7c0:0068:10:0:104:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 104 version 2
				standby 104 ip 10.0.104.254
				standby 104 priority 110
				standby 104 preempt
			! IPv6:
				standby 2104 version 2
				standby 2104 preempt
				standby 2104 priority 110
				standby 2104 ipv6 2a:1dc:7c0:0068:10:0:104:254
	    no shutdown
	
	interface vlan 160
	    ip address 10.0.160.2 255.255.252.0
	    ipv6 address 2a:1dc:7c0:00A0:10:0:160:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 160 version 2
				standby 160 ip 10.0.163.254
				standby 160 priority 110
				standby 160 preempt
			! IPv6:
				standby 2160 version 2
				standby 2160 preempt
				standby 2160 priority 110
				standby 2160 ipv6 2a:1dc:7c0:00A0:10:0:163:254
	    no shutdown
	
	interface vlan 200
	    ip address 10.0.200.2 255.255.255.0
	    ipv6 address 2a:1dc:7c0:00C8:10:0:200:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 200 version 2
				standby 200 ip 10.0.200.254
				standby 200 priority 90
				standby 200 preempt
			! IPv6:
				standby 2200 version 2
				standby 2200 preempt
				standby 2200 priority 90
				standby 2200 ipv6 2a:1dc:7c0:00C8:10:0:200:254
	    no shutdown
	
	interface vlan 220
	    ip address 10.0.220.2 255.255.255.0
	    ipv6 address 2a:1dc:7c0:00DC:10:0:220:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 220 version 2
				standby 220 ip 10.0.220.254
				standby 220 priority 110
				standby 220 preempt
			! IPv6:
				standby 2220 version 2
				standby 2220 preempt
				standby 2220 priority 110
				standby 2220 ipv6 2a:1dc:7c0:00DC:10:0:220:254
	    no shutdown
	
	interface vlan 252
	    ip address 10.0.253.2 255.255.252.0
	    ipv6 address 2a:1dc:7c0:00FF:10:0:253:2/64
	    ipv6 eigrp 100
	    ! DHCP relay
	    ip helper-address 10.0.70.20
	    ipv6 dhcp relay destination 2a:1dc:7c0:0046:10:0:70:20
	    ! HSRP
			! IPv4:
				standby 252 version 2
				standby 252 ip 10.0.255.254
				standby 252 priority 110
				standby 252 preempt
			! IPv6:
				standby 2252 version 2
				standby 2252 preempt
				standby 2252 priority 110
				standby 2252 ipv6 2a:1dc:7c0:00FF:10:0:255:254
	    no shutdown

! Links to access layer switches

	! > HQ-OFFICE-S1
	interface gig0/2
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! All VLANs, less secure
		! switchport trunk allowed vlan 10,15,20,25,50,51,70,100,104,160,200,220,252
		switchport trunk allowed vlan 10,15,100,220,200,252
		
	! > HQ-OFFICE-S2
	interface gig0/3
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! All VLANs, less secure
		! switchport trunk allowed vlan 10,15,20,25,50,51,70,100,104,160,200,220,252
		switchport trunk allowed vlan 20,25,100,160,200,252
	
	! > HQ-WS-S1
	interface gig1/0
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! All VLANs, less secure
		! switchport trunk allowed vlan 10,15,20,25,50,51,70,100,104,160,200,220,252
		switchport trunk allowed vlan 50,104,200,252
	
	! > HQ-WS-S2
	interface gig1/1
		no shutdown
		switchport trunk encapsulation dot1q  
		switchport mode trunk
		switchport nonegotiate
		switchport trunk native vlan 999
		! All VLANs, less secure
		! switchport trunk allowed vlan 10,15,20,25,50,51,70,100,104,160,200,220,252
		switchport trunk allowed vlan 51,104,200,252

! IPv4 EIGRP Configuration
router eigrp 100! Hostname
    network 10.0.0.0 0.0.255.255
    network 172.16.0.2 0.0.0.1
    network 172.16.0.6 0.0.0.1
    network 172.16.0.8 0.0.0.1
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
    

```

Router
---
### HQ-R1
-  [x] Hostname
-  [x] Banner
-  [x] IPv4 EIGRP
-  [x] IPv6 EIGRP
-  [x] Default routes + EIGRP advertisement
	- [x] IPv4
	- [x] IPv6
-  [x] IP
-  [x] Login, SSH and authentication
-  [x] NAT
-  [ ] Authentication with RADIUS
-  [ ] DMVPN:
	- [ ] GRE (Packet encapsulation into another packet)
	- [ ] NHRP (Next Hop Resolution Protocol, don't know what this is)
	- [ ] IPsec (Encryption)
	- [ ] Routing protocol (probably EIGRP)

```
! Hostname
hostname HQ-R1
ip domain name hq.solardynamics.eu

! Convenience
no ip domain lookup

! Routing
ip routing
ipv6 unicast-routing
	! Default routes
	ip route 0.0.0.0 0.0.0.0 gig2/0
	ipv6 route ::/0 gig2/0

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

! Router interfaces

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
	    
	! > ISP
	interface gig2/0
	    ip address 82.136.79.1 255.255.255.224
	    ip nat outside
		ipv6 address 2a:1dc:7c0:FFFF:82:136:79:1/64
	    ipv6 eigrp 100
	    no shutdown

! IPv4 EIGRP Configuration
	router eigrp 100! Hostname
		! Redistribute
		redistribute static metric 100000 1000 255 1 1500
		! Networks
	    network 82.136.79.0 0.0.0.31
	    network 172.16.0.0 0.0.0.1
	    network 172.16.0.2 0.0.0.1
		no auto-summary
		router-id 3.3.3.3
	    passive-interface default
	    no passive-interface gig0/0
	    no passive-interface gig1/0

! IPv6 EIGRP Configuration
	ipv6 router eigrp 100
		redistribute static metric 100000 1000 255 1 1500
		router-id 3.3.3.3
	    passive-interface default
	    no passive-interface gig0/0
	    no passive-interface gig1/0

! NAT configuration
	! SD-HQ-LIN1
	ip nat inside source static 10.0.70.10 82.136.79.3
	! SD-HQ-WIN1
	ip nat inside source static 10.0.70.20 82.136.79.4
	
	! NAT ACL configurations:
		ip access-list standard SD-ACL-internal-client
		permit 10.0.10.0 0.0.0.255
		permit 10.0.15.0 0.0.0.255
		permit 10.0.20.0 0.0.0.255
		permit 10.0.25.0 0.0.0.255
		permit 10.0.50.0 0.0.0.255
		permit 10.0.51.0 0.0.0.255
		permit 10.0.200.0 0.0.0.255
		permit 10.0.220.0 0.0.0.255
		exit
		
		ip access-list standard SD-ACL-external-client
		permit 10.0.100.0 0.0.3.255
		permit 10.0.104.0 0.0.3.255
		permit 10.0.160.0 0.0.3.255
		exit

	! Overload pool for company clients
	ip nat pool SD-internal-client-pool 82.136.79.10 82.136.79.14 netmask 255.255.255.224

	! Pool for publically connected devices
	ip nat pool SD-external-client-pool 82.136.79.15 82.136.79.19 netmask 255.255.255.224

	! NAT for company devices
	ip nat inside source list SD-ACL-internal-client pool SD-internal-client-pool overload

	! NAT for external devices (Or Wifi devices)
	ip nat inside source list SD-ACL-external-client pool SD-external-client-pool overload

```
### HQ-R2
As of 2025.02.23, only a backup router

-  [x] Hostname
-  [x] Banner
-  [x] IPv4 EIGRP
-  [x] IPv6 EIGRP
-  [ ] Default routes + EIGRP advertisement
	- [ ] IPv4
	- [ ] IPv6
-  [x] IP
-  [x] Login, SSH and authentication
-  [ ] NAT
-  [ ] Authentication with RADIUS
-  [ ] DMVPN:
	- [ ] GRE (Packet encapsulation into another packet)
	- [ ] NHRP (Next Hop Resolution Protocol, don't know what this is)
	- [ ] IPsec (Encryption)
	- [ ] Routing protocol (probably EIGRP)

```
! Hostname
hostname HQ-R2
ip domain name hq.solardynamics.eu

! Convenience
no ip domain lookup

! Routing
ip routing
ipv6 unicast-routing
	! Default routes
	ip route 0.0.0.0 0.0.0.0 gig2/0
	ipv6 route ::/0 gig2/0

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
    ip address 82.136.79.2 255.255.255.224
    ip nat outside
	ipv6 address 2a:1dc:7c0:FFFF:82:136:79:2/64
    ipv6 eigrp 100
    no shutdown

! IPv4 EIGRP Configuration
router eigrp 100
    network 82.136.79.0 0.0.0.31
	! Redistribute (with lower value)
    redistribute static metric 1000 1000 255 1 1500
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
	! Redistribute (with lower value)
	redistribute static metric 1000 1000 255 1 1500
    passive-interface default
    no passive-interface gig0/0
    no passive-interface gig1/0



```
Switchek
---
- [x] Trunk portok
- [x] DHCP snooping limit
- [ ] DHCP snooping trust
- [ ] Storm control
- [x] Nonegotiate
- [x] Port security
- [x] Portfast
- [x] BPDU guard

### HQ-OFFICE-S1
```
! Hostname
hostname HQ-OFFICE-S1
ip domain name hq.solardynamics.eu

! Convenience
no ip domain lookup

! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! VTP
vtp domain GyorHQ
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
	ip address 10.0.254.1 255.255.252.0
	ipv6 address 2a:1dc:7c0:00ff:10:0:254:1/64
	no shutdown

! > HQ-MLS-1
interface gig0/0
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

! > HQ-MLS-2
interface gig0/1
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

! Templates for easier configuration and management
template ACCESS
	description Standard Access Port: portfast, BPDU guard, DHCP rate limit, and port-security with restrict. **Sticky and access vlan MUST be configured on the port**
	switchport mode access
	switchport voice vlan 200
	! Security
	switchport port-security
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10

template ACCESS_AP
	description AP Access Port: portfast, BPDU guard, DHCP rate limit with higher limits, no security. **Access vlan MUST be configured on the port**
	switchport mode access
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping with higher limits
	ip dhcp snooping limit rate 50


! VLAN 10: Vezetoseg
interface gig1/0
	no shutdown
	source template ACCESS
	switchport access vlan 10
	switchport port-security mac-address sticky

! VLAN 220: Nyomtatok
interface gig1/1
	no shutdown
	source template ACCESS
	switchport access vlan 220
	switchport port-security mac-address sticky

! VLAN 15: Vezetoseg
interface gig1/2
	no shutdown
	source template ACCESS
	switchport access vlan 15
	switchport port-security mac-address sticky

! VLAN 100: Iroda WLAN
interface gig1/3
	no shutdown
	source template ACCESS_AP
	switchport access vlan 220

```

### HQ-OFFICE-S2
```
! Hostname
hostname HQ-OFFICE-S2
ip domain name hq.solardynamics.eu

! Convenience
no ip domain lookup

! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! VTP
vtp domain GyorHQ
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
	ip address 10.0.254.2 255.255.252.0
	ipv6 address 2a:1dc:7c0:00ff:10:0:254:2/64
	no shutdown
	
! > HQ-MLS-1
interface gig0/0
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.
	
! > HQ-MLS-2
interface gig0/1
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

! Templates for easier configuration and management
template ACCESS
	description Standard Access Port: portfast, BPDU guard, DHCP rate limit, and port-security with restrict. **Sticky and access vlan MUST be configured on the port**
	switchport mode access
	switchport voice vlan 200
	! Security
	switchport port-security
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10

template ACCESS_AP
	description AP Access Port: portfast, BPDU guard, DHCP rate limit with higher limits, no security. **Access vlan MUST be configured on the port**
	switchport mode access
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping with higher limits
	ip dhcp snooping limit rate 50


! VLAN 160: Vendeg WAN
interface gig1/0
	no shutdown
	source template ACCESS_AP
	switchport access vlan 160


! VLAN 100: Iroda WAN
interface gig1/1
	no shutdown
	source template ACCESS_AP
	switchport access vlan 100

! VLAN 20: Marketing
interface gig1/2
	no shutdown
	source template ACCESS
	switchport access vlan 20
	switchport port-security mac-address sticky

! VLAN 25: Ertekesites
interface gig1/3
	no shutdow
	source template ACCESS
	switchport access vlan 25
	switchport port-security mac-address sticky

```

### HQ-WS-S1
```
! Hostname
hostname HQ-WS-S1
ip domain name hq.solardynamics.eu

! Convenience
no ip domain lookup

! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! VTP
vtp domain GyorHQ
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
	ip address 10.0.254.3 255.255.252.0
	ipv6 address 2a:1dc:7c0:00ff:10:0:254:3/64
	no shutdown

! > HQ-MLS-1
interface gig0/0
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

! > HQ-MLS-2
interface gig0/1
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

! Templates for easier configuration and management
template ACCESS
	description Standard Access Port: portfast, BPDU guard, DHCP rate limit, and port-security with restrict. **Sticky and access vlan MUST be configured on the port**
	switchport mode access
	switchport voice vlan 200
	! Security
	switchport port-security
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10

template ACCESS_AP
	description AP Access Port: portfast, BPDU guard, DHCP rate limit with higher limits, no security. **Access vlan MUST be configured on the port**
	switchport mode access
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping with higher limits
	ip dhcp snooping limit rate 50


! VLAN 50: Fejlesztok 1
interface gig1/0
	no shutdown
	source template ACCESS
	switchport access vlan 50
	switchport port-security mac-address sticky


! VLAN 104: Workshop WAN
interface gig1/1
	no shutdown
	source template ACCESS_AP
	switchport access vlan 104


```

### HQ-WS-S2
```
! Hostname
hostname HQ-WS-S2
ip domain name hq.solardynamics.eu

! Convenience
no ip domain lookup

! Banner
banner login # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner incoming # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#
banner exec # WARNING: Unauthorized access is strictly prohibited. This device is the property of the Solar Dynamics corporation and is only for authorized use. Any unauthorized access or attempt to gain access to this device will reported#

! VTP
vtp domain GyorHQ
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
	ip address 10.0.254.4 255.255.252.0
	ipv6 address 2a:1dc:7c0:00ff:10:0:254:4/64
	no shutdown

! > HQ-MLS-1
interface gig0/0
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

! > HQ-MLS-2
interface gig0/1
	no shutdown
	switchport trunk encapsulation dot1q  
	switchport mode trunk
	switchport nonegotiate
	switchport trunk native vlan 999
	! Allowed VLANs are configured on the MLSs for centralized control.

! Templates for easier configuration and management
template ACCESS
	description Standard Access Port: portfast, BPDU guard, DHCP rate limit, and port-security with restrict. **Sticky and access vlan MUST be configured on the port**
	switchport mode access
	switchport voice vlan 200
	! Security
	switchport port-security
	switchport port-security maximum 1
	switchport port-security violation restrict
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping
	ip dhcp snooping limit rate 10

template ACCESS_AP
	description AP Access Port: portfast, BPDU guard, DHCP rate limit with higher limits, no security. **Access vlan MUST be configured on the port**
	switchport mode access
	! STP
	spanning-tree portfast
	spanning-tree bpduguard enable
	! snooping with higher limits
	ip dhcp snooping limit rate 50


! VLAN 51: Fejlesztok 2
interface gig1/0
	no shutdown
	source template ACCESS
	switchport access vlan 51
	switchport port-security mac-address sticky


! VLAN 104: Workshop WAN
interface gig1/1
	no shutdown
	source template ACCESS_AP
	switchport access vlan 104

```
