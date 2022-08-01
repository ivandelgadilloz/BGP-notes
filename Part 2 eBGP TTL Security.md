# BGP
## eBGP TTL Security

TTL safety is another option for using multi-hop, 
in this case the inverse is used as a decrement of the maximum TTL of 255, for the configuration of 2 hops the subtraction of TTL=255 - 2 the TTL should be 253.

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


bio [ivandelgadilloz](https://linktr.ee/idelgadillo)