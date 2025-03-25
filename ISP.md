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
    ip address  82.1.79.30 255.255.255.224
    ipv6 address 2a:1dc:7c0:00FF:82:1:79:30/64
    ip nat inside
    no shutdown

! SzF-R
interface gig1/0
    ip address 82.1.79.142 255.255.255.240
    ipv6 address 2a:1dc:7c0:01FF:82:1:79:142/64
    ipv6 enable
    ip nat inside
    no shutdown

! > TB-R
interface gig2/0
    ip address 82.1.79.158 255.255.255.240
    ipv6 address 2a:1dc:7c0:03FF:82:1:79:158/64
    ip nat inside
    no shutdown

! > BP-R
interface gig3/0
    ip address  82.1.79.62 255.255.255.224
    ipv6 address 2a:1dc:7c0:02FF:82:1:79:62/64
    ip nat inside
    no shutdown

! > Backup ISP
ip route 85.16.100.2 255.255.255.254 172.20.0.2

interface gig5/0
	ip address 172.20.0.1 255.255.255.252
	no shutdown

    
! > Internet
interface gig6/0
    ip address dhcp
    ipv6 enable
    ip nat outside
    no shutdown
    
! NAT
ip access-list standard INSIDE-NET
	permit 82.1.79.0 0.0.0.255

ip nat inside source list INSIDE-NET interface gig6/0
    
    
    

```


Backup ISP
---

```
en
conf t
hostname BackupISP

! Default routes
ip route 0.0.0.0 0.0.0.0 gig6/0

! > HQ-R2
interface gig2/0 
    ip address  85.16.100.2 255.255.255.254
    ip nat inside
    no shutdown

! > Internet
interface gig6/0
    ip address dhcp
    ip nat outside
    no shutdown


! > ISP
ip route 82.1.79.0 255.255.255.0 172.20.0.1

interface gig5/0
	ip address 172.20.0.2 255.255.255.252
	no shutdown




! NAT
ip access-list standard INSIDE-NET
	permit 85.16.100.3 0.0.0.1

ip nat inside source list INSIDE-NET interface gig6/0    
```
