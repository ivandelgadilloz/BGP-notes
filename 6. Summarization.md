# BGP
## eBGP TTL Security

TTL safety is another option for using multi-hop, 
in this case the inverse is used as a decrement of the maximum TTL of 255, for the configuration of 2 hops the subtraction of TTL=255 - 2 the TTL should be 253.

## Topology
![Topology](https://github.com/ivandelgadilloz/BGP-notes/blob/main/assets/images/BGP-2-link.png?raw=true)


## Wireshark with Multi-hop
![Topology](https://github.com/ivandelgadilloz/BGP-notes/blob/main/assets/images/TTL_multihop.png?raw=true)


# CLI
## BGP CORE-1
```py
CORE-1#show running-config | sec bgp
router bgp 1010
 bgp router-id 100.1.1.1
 bgp log-neighbor-changes
 no bgp default ipv4-unicast
 neighbor 6.6.6.6 ebgp-multihop 2
 neighbor 6.6.6.6 remote-as 600
 neighbor 6.6.6.6 update-source Loopback0
  neighbor 2000:6:6::6 ebgp-multihop 2
 neighbor 2000:6:6:6::6 remote-as 600
 neighbor 2000:6:6:6::6 update-source Loopback0
 !
 address-family ipv4
  network 100.1.1.1 mask 255.255.255.255
  neighbor 6.6.6.6 activate
 exit-address-family
 !
 address-family ipv6
  network 2000:100:1:1::1/128
  neighbor 2000:6:6:6::6 activate
 exit-address-family
```

## Change Multi-hop with TTL Security 
```py
CORE-1(config)#router bgp 1010
CORE-1(config-router)#no neighbor 6.6.6.6 ebgp-multihop 2     
CORE-1(config-router)#no  neighbor 2000:6:6:6::6 ebgp-multihop 2
```
```py
## TTL=255-2 -->> TTL=253
CORE-1(config)#router bgp 1010
CORE-1(config-router)#neighbor 6.6.6.6 ttl-security hops 2
CORE-1(config-router)#neighbor 2000:6:6:6::6 ttl-security hops 2
```

## BGP PE-6
```py
PE-6(config)#do show running-config | sec bgp
router bgp 600
 bgp log-neighbor-changes
 no bgp default ipv4-unicast
 neighbor 10.67.67.7 remote-as 700
 neighbor 2000:100:1:1::1 remote-as 1010
 neighbor 2000:100:1:1::1 ebgp-multihop 2
 neighbor 2000:100:1:1::1 update-source Loopback0
 neighbor 100.1.1.1 remote-as 1010
 neighbor 100.1.1.1 ebgp-multihop 2
 neighbor 100.1.1.1 update-source Loopback0
 !
 address-family ipv4
  network 6.6.6.6 mask 255.255.255.255
  neighbor 10.67.67.7 activate
  neighbor 100.1.1.1 activate
 exit-address-family
 !
 address-family ipv6
  network 2000:6:6::6/128
  neighbor 2000:100:1:1::1 activate
 exit-address-family
```

## Change Multi-hop with TTL Security 
```py
PE-6(config)#router bgp 600
PE-6(config-router)#no neighbor 2000:100:1:1::1 ebgp-multihop 2
PE-6(config-router)#no neighbor 100.1.1.1 ebgp-multihop 2
```
```py
## TTL=255-2 -->> TTL=253
PE-6(config-router)#neighbor 100.1.1.1 ttl-security hops 2 
PE-6(config-router)#neighbor 2000:100:1:1::1 ttl-security hops 2
*Aug  1 00:47:04.901: %BGP-5-ADJCHANGE: neighbor 100.1.1.1 Up
```

## Verification
At this point, the router only receives data at minimum TTL 253.
```py
CORE-1#show bgp ipv4 unicast neighbors 6.6.6.6 | in TTL
Connection is ECN Disabled, Mininum incoming TTL 253, Outgoing TTL 255

PE-6#show bgp ipv4 unicast neighbors 100.1.1.1 | in TTL
Connection is ECN Disabled, Mininum incoming TTL 253, Outgoing TTL 255
```

bio [ivandelgadilloz](https://linktr.ee/idelgadillo)