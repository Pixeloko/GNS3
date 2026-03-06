# 1.Floating static route
Acquired skills :
* Configuration of PC and routers ip and static routes with administrative distance higher than OSPF (110)
* Distribution of adresses in (sub)networks
* Testing the different routes

![Topology](/media/floating_route.png)

## Address 
|     Router1       |        Router2     |        PC1            |            PC2        |
|-------------------|--------------------|-----------------------|-----------------------|
|eth-010.10.10.1/24 |eth0-10.10.10.2/24  |IP/MASK-192.162.1.10/24|IP/MASK-192.162.2.10/24|
|eth1-192.162.3.1/24|eth1-192.162.3.2/24 |GATEWAY-192.162.1.1    |GATEWAY-192.162.2.1    |
|eth2-192.162.1.1/24|eth2-192.162.2.1/24 |


## Configuration console
#### Routers 1|2
```bash
interface eth0
ip address 10.10.10.1|2/24
!
interface eth1
ip address 192.162.3.1|2/24
!
interface eth2
ip address 192.162.1|2.1/24
!
router ospf
network 192.162.1|2.0/24 area 0 # let know the network (include PC)
network 10.10.10.0/24 area 0 # communication between routers
!
ip route 192.162.2|1.0/24 192.162.3.2|1 150 # syntaxe= ip route [neighbor network/cdr][neighbor router gateway]
!
```

### PC1
```console
ip 192.162.1.10/24 192.162.1.1
```

#### PC2
```console
ip 192.162.2.10/24 192.162.2.1
```

## Testing
### R1 or R2
```console
interface eth0
shutdown
```

#### PC2
```console
ping 192.162.1.10
```
