# BGP 
## Conceptos BGP

- BGP es usado para proveer un enrutamiento interdominio
- Garantiza Loop-free routing information
- BGP esta basado en policy-based routing
- Control path using **path attributes** 
- Anuncia el path que debe seguir para llegar al destino
- AS-Path is Always loop-free
- Usa TCP, no se envia trafico hasta que se establesca la conexion TCP port 179

## Tipos de bases de datos
**1 Neighbor table**: Usado para listar los BGP Neighbors

**2 BGP Table**: Lista de networks BGFP aprendidos desde cada neighbor

**3 IP Routing table**: Lista de mejores caminos para llegar al destino

## RESUMEN
- **Protocol type**: Path Vector

- **Type**: EGP (External Gateway Protocol)

- **Packet Types**: Open, Update, KeepAlive, Notification

- **Administrative Distance**: eBGP: 20; iBGP: 200

- **Transport**: TCP port 179

- **Neighbor States**: Idle -> Active -> Connect -> Open Sent -> Open Confirm -> Established

1 – Idle: the initial state of a BGP connection. In this state, the BGP speaker is waiting for a BGP start event, generally either the establishment of a TCP connection or the re-establishment of a previous connection. Once the connection is established, BGP moves to the next state.

2 – Connect: In this state, BGP is waiting for the TCP connection to be formed. If the TCP connection completes, BGP will move to the OpenSent stage; if the connection cannot complete, BGP goes to Active

3 – Active: In the Active state, the BGP speaker is attempting to initiate a TCP session with the BGP speaker it wants to peer with. If this can be done, the BGP state goes to OpenSent state.

4 – OpenSent: the BGP speaker is waiting to receive an OPEN message from the remote BGP speaker

5 – OpenConfirm: Once the BGP speaker receives the OPEN message and no error is detected, the BGP speaker sends a KEEPALIVE message to the remote BGP speaker

6 – Established: All of the neighbor negotiations are complete. You will see a number, which tells us the number of prefixes the router has received from a neighbor or peer group.

- **Path Selection Attributes**: (highest) Weight > (highest) Local Preference > Originate > (shortest) AS Path > Origin > (lowest) MED > External > IGP Cost > eBGP Peering > (lowest) Router ID
(Originate: prefer routes that it installed into BGP by itself over a route that another router installed in BGP)

- **Authentication**: MD5

- **BGP Origin codes**: i – IGP (injected by “network” statement), e – EGP, ? – Incomplete

- **AS number range**: Private AS range: 64512 – 65535, Globally (unique) AS: 1 – 64511

- **BGP Keepalive**: 60 Seg and dead time 180 seg, configurable


>texto citado
>> texto citado

+ prueba
    + prueba


```py
print("BGP")
```

Mas informacion puedes encontrar en mi bio [ivandelgadilloz](https://linktr.ee/idelgadillo)

![Topologia](https://github.com/ivandelgadilloz/BGP-notes/tree/main/assets/images/Topologia-BGP-full.png?raw=true)










