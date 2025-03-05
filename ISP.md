This is a configuration for the ISP. In pratctice we wouldn't be configuring this ourselves. This would be the job of the ISP.

ISP
---

```
en
conf t

hostname ISP

! Default routes
ip route 0.0.0.0 0.0.0.0 gig6/0
ipv6 route ::/0 gig6/0

! Other IPv4 are routes inferred by interface configurations


! IPv6 routes configured manually, because link-local is used
ipv6 route 2a:1dc:7c0:0000::/56 gig0/0
ipv6 route 2a:1dc:7c0:0100::/56 gig1/0
ipv6 route 2a:1dc:7c0:0200::/56 gig2/0
ipv6 route 2a:1dc:7c0:0300::/56 gig3/0


! > HQ-R1
interface gig0/0 
    ip address  82.136.79.30 255.255.255.224
    ipv6 enable
    ip nat inside
    no shutdown

! SzF-R
interface gig1/0
    ip address 82.136.79.142 255.255.255.240
    ipv6 enable
    ip nat inside
    no shutdown

! > TB-R
interface gig2/0
    ip address 82.136.79.158 255.255.255.240
    ipv6 enable
    ip nat inside
    no shutdown

! > BP-R
interface gig3/0
    ip address  82.136.79.62 255.255.255.224
    ipv6 enable
    ip nat inside
    no shutdown
    
    
! > Internet
interface gig6/0
    ip address dhcp
    ipv6 enable
    ip nat outside
    no shutdown
    
! NAT
ip access-list standard INSIDE-NET
	permit 82.136.79.0 0.0.0.255

ip nat inside source list INSIDE-NET interface gig6/0
    
    
    

```
