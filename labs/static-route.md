# Static route
Acquired skills :
* Configuration of PC and routers ip and static routes
* Distribution of adresses in (sub)networks

![Topology](/media/static_route.png)

## Address 
|     Router1       |        Router2     |      Router3       |        PC1           |           PC2        |
|-------------------|--------------------|--------------------|----------------------|----------------------|
|eth0-192.168.0.1/30|eth0--192.168.0.2/30|eth0--192.168.1.2/30|IP/MASK-192.168.3.2/24|IP/MASK-192.168.2.2/24 |
|eth1-192.168.1.1/30|eth1--192.168.3.1/24|eth1--192.168.2.1/24|GATEWAY-192.168.3.1   |GATEWAY-192.168.2.1   |


## Configuration console
#### Router 1
```console
interface eth0
ip address 192.168.0.1/30
!
interface eth1
ip address 192.168.1.1/30
!
ip route 192.168.3.0/24 192.168.0.2
ip route 192.168.2.0/24 192.168.1.2
!
```

#### Router 2
```console
interface eth0
ip address 192.168.0.2/30
!
interface eth1
ip address 192.168.3.1/24
!
ip route 192.168.2.0/24 192.168.0.1
!
```

#### Router 3
```console
interface eth0
ip address 192.168.1.2/30
!
interface eth1
ip address 192.168.2.1/24
!
ip route 192.168.3.0/24 192.168.1.1
!
```

### PC1
```console
ip 192.168.3.2/24 192.168.3.1
```

#### PC2
```console
ip 192.168.2.2/24 192.168.2.1/24 
```

## Testing
#### PC1
```console
ping 192.168.2.2
```

#### PC2
```console
ping 192.168.3.2
```