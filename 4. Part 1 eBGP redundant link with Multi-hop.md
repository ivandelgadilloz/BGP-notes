# BGP
## eBGP with redundant link 

When an eBGP connection is made between two different ASNs with redundant physical connections, the eBGP peer is made with the loopbacks of the peer routers.

For this example we will use two physical links where a static routing will be created to reach the Loopback interfaces to make the eBGP connection but at this point the connection is not established because the eBGP rule indicates that the TTL = 1, for this reason we will modify the TTL to reach 2 hops, the physical interface and the Loopback interface.

## Topology
![Topology](https://github.com/ivandelgadilloz/BGP-notes/blob/main/assets/images/BGP-2-link.png?raw=true)

# CLI
## CORE-1
static routing to reach PE-6 Loopback 0 over two links
```py
CORE-1#show running-config | in ip route
ip route 6.6.6.6 255.255.255.255 10.1.26.6 name PRIMARY_AS600
ip route 6.6.6.6 255.255.255.255 10.2.26.6 10 name BACKUP_600
```
## PE-2
static routing to reach CORE-1 Loopback 0 over two links
```py
PE-6#show run | in ip route 
ip route 100.1.1.1 255.255.255.255 10.1.26.1 name PRIMARY_AS1010
ip route 100.1.1.1 255.255.255.255 10.2.26.1 10 name BACKUP_AS1010
```
## BGP CORE-1
```py
CORE-1#show running-config | sec bgp
router bgp 1010
 bgp router-id 100.1.1.1
 bgp log-neighbor-changes
 no bgp default ipv4-unicast
 neighbor 6.6.6.6 remote-as 600
 neighbor 6.6.6.6 update-source Loopback0
 neighbor 2000:6:6::6 remote-as 600
 neighbor 2000:6:6::6 update-source Loopback0
 !
 address-family ipv4
  network 100.1.1.1 mask 255.255.255.255
  neighbor 6.6.6.6 activate
 exit-address-family
 !
 address-family ipv6
  network 2000:100:1:1::1/128
  neighbor 2000:6:6::6 activate
 exit-address-family
```
## BGP PE-6
```py
PE-6#show running-config | sec bgp
router bgp 600
 bgp log-neighbor-changes
 no bgp default ipv4-unicast
 neighbor 2000:100:1:1::1 remote-as 1010
 neighbor 2000:100:1:1::1 update-source Loopback0
 neighbor 100.1.1.1 remote-as 1010
 neighbor 100.1.1.1 update-source Loopback0
 !
 address-family ipv4
  network 6.6.6.6 mask 255.255.255.255
  neighbor 100.1.1.1 activate
 exit-address-family
 !
 address-family ipv6
  network 2000:6:6::6/128
  neighbor 2000:100:1:1::1 activate
 exit-address-family
```
## Modify TTL on eBGP
at this point the bgp session is not established, the rule indicates that to establish the eBGP session the TTL=1, as the session is configured with the loopback the TTL=2 must be set to be reachable.

## CORE-1
```py
CORE-1#show run partition router bgp 1010 | in ebgp|router
router bgp 1010
 bgp router-id 100.1.1.1
 neighbor 6.6.6.6 ebgp-multihop 2
 neighbor 2000:6:6::6 ebgp-multihop 2
```
## PE-6
```py
PE-6#show running-config partition router bgp 600 | in ebgp|router
router bgp 600
 neighbor 2000:100:1:1::1 ebgp-multihop 2
 neighbor 100.1.1.1 ebgp-multihop 2
```
## Verification
```py
CORE-1#ping 6.6.6.6 source loopback 0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 6.6.6.6, timeout is 2 seconds:
Packet sent with a source address of 100.1.1.1 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms

CORE-1#show bgp ipv4 unicast summary | in Neighbor|6.6.6.6
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
6.6.6.6         4          600     441     447        5    0    0 06:32:25        1


PE-6#ping 100.1.1.1 source loopback 0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 100.1.1.1, timeout is 2 seconds:
Packet sent with a source address of 6.6.6.6 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms

PE-6#show bgp ipv4 unicast summary | in Neighbor|100.1.1.1
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
100.1.1.1       4         1010     446     440        9    0    0 06:31:30        3

```

bio [ivandelgadilloz](https://linktr.ee/idelgadillo)