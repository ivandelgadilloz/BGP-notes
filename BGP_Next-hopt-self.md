# BGP
## Next hop manipulation

Technique to ensure that the next-hop address check passes without advertising 
peering networks into a routing protocol involves the modification of the next-hop address. 
The next-hop IP address can be modified on inbound or outbound neighbor routing policies. 
Managing next-hop IP addresses in a routing policy can be a complicated task, and the next-hop-self feature modifies the next-hop address in the NLRI for external BGP prefixes in the 
IP address sourcing the BGP session.

The command neighbor ip-address *next-hop-self* [all] is used for each neighbor under the 
address family configuration. The *next-hop-self* feature does not modify the next-hop 
address for iBGP prefixes by default. IOS nodes can append the optional all keyword, which 
modifies the next-hop address on iBGP prefixes, too.
## Topology
![Topology](https://github.com/ivandelgadilloz/BGP-notes/blob/main/assets/images/BGP_Next-hop-self.png?raw=true)

## CLI
# R1
```py
R1#show running-config | sec bgp
router bgp 65100
 bgp log-neighbor-changes
 neighbor 10.12.1.2 remote-as 65200
 !
 address-family ipv4
  network 192.168.1.0
  neighbor 10.12.1.2 activate
 exit-address-family
```
```py
R1#show bgp summary | be Neig
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.12.1.2       4        65200     122     120        5    0    0 01:46:24        3
```

```py
R1#show bgp ipv4 unicast | be Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>   192.168.1.0      0.0.0.0                  0         32768 i
 *>   192.168.2.0      10.12.1.2                0             0 65200 i
 *>   192.168.3.0      10.12.1.2                              0 65200 i
 *>   192.168.4.0      10.12.1.2                              0 65200 65300 i
```
```py
R1#show ip route bgp | be Gate
Gateway of last resort is not set

B     192.168.2.0/24 [20/0] via 10.12.1.2, 01:50:46
B     192.168.3.0/24 [20/0] via 10.12.1.2, 01:50:15
B     192.168.4.0/24 [20/0] via 10.12.1.2, 01:50:15
```
# R2 
```py
R2#show running-config | sec bgp
router bgp 65200
 bgp log-neighbor-changes
 neighbor 10.12.1.1 remote-as 65100
 neighbor 10.23.1.3 remote-as 65200
 !
 address-family ipv4
  network 192.168.2.0
  neighbor 10.12.1.1 activate
  neighbor 10.23.1.3 activate
  neighbor 10.23.1.3 next-hop-self
 exit-address-family
```
```py
R2#show bgp summary | be Neig
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.12.1.1       4        65100     123     125        5    0    0 01:49:24        1
10.23.1.3       4        65200     125     125        5    0    0 01:49:21        2
```

```py
R2#show bgp ipv4 unicast | be Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>   192.168.1.0      10.12.1.1                0             0 65100 i
 *>   192.168.2.0      0.0.0.0                  0         32768 i
 *>i  192.168.3.0      10.23.1.3                0    100      0 i
 *>i  192.168.4.0      10.23.1.3                0    100      0 65300 i
```
```py
R2#show ip route bgp | be Gate
Gateway of last resort is not set

B     192.168.1.0/24 [20/0] via 10.12.1.1, 01:52:15
B     192.168.3.0/24 [200/0] via 10.23.1.3, 01:52:14
B     192.168.4.0/24 [200/0] via 10.23.1.3, 01:52:13
```
# R3
```py
R3#show running-config | sec bgp
router bgp 65200
 bgp log-neighbor-changes
 neighbor 10.23.1.2 remote-as 65200
 neighbor 10.34.1.4 remote-as 65300
 !
 address-family ipv4
  network 192.168.3.0
  neighbor 10.23.1.2 activate
  neighbor 10.23.1.2 next-hop-self
  neighbor 10.34.1.4 activate
 exit-address-family
```
```py
R3#show bgp summary | be Neig
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.23.1.2       4        65200     131     131        5    0    0 01:54:30        2
10.34.1.4       4        65300     130     132        5    0    0 01:54:32        1
```

```py
R3#show bgp ipv4 unicast | be Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>i  192.168.1.0      10.23.1.2                0    100      0 65100 i
 *>i  192.168.2.0      10.23.1.2                0    100      0 i
 *>   192.168.3.0      0.0.0.0                  0         32768 i
 *>   192.168.4.0      10.34.1.4                0             0 65300 i
```
```py
R3#show ip route bgp | be Gate
Gateway of last resort is not set

B     192.168.1.0/24 [200/0] via 10.23.1.2, 01:56:05
B     192.168.2.0/24 [200/0] via 10.23.1.2, 01:56:05
B     192.168.4.0/24 [20/0] via 10.34.1.4, 01:56:05
```
# R4
```py
R4#show running-config | sec bgp
router bgp 65300
 bgp log-neighbor-changes
 neighbor 10.34.1.3 remote-as 65200
 !
 address-family ipv4
  network 192.168.4.0
  neighbor 10.34.1.3 activate
 exit-address-family
```
```py
R4#show bgp summary | be Nei
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.34.1.3       4        65200     137     135        5    0    0 01:58:30        3
```

```py
R4#show bgp ipv4 unicast | be Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>   192.168.1.0      10.34.1.3                              0 65200 65100 i
 *>   192.168.2.0      10.34.1.3                              0 65200 i
 *>   192.168.3.0      10.34.1.3                0             0 65200 i
 *>   192.168.4.0      0.0.0.0                  0         32768 i
```
```py
R4#show ip route bgp | be Gate
Gateway of last resort is not set

B     192.168.1.0/24 [20/0] via 10.34.1.3, 01:58:23
B     192.168.2.0/24 [20/0] via 10.34.1.3, 01:58:23
B     192.168.3.0/24 [20/0] via 10.34.1.3, 01:58:23
```

bio [ivandelgadilloz](https://linktr.ee/idelgadillo)