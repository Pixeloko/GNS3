# Router modes
* ==r1#== exec commands
* ==r1(config)#== modify the config

# Router configuration
*Delete pre-configured startup config*
```bash
erase startup-config 
reload # then reload 
```

*Open an integrated shell to configure FRR Routing daemon*
```console
vtysh
configure terminal
```
*Change the name of the router*
```console
hostname r1
```
*Add ipv4 address for one interface*
```bash
# in config mode
interface eth1
ip address # insert address with subnet mask
no shutdown # permit to send and recieve informations
exit # exit submode config
```
*remove an ip address*
```console
no ip address 10.10.0.1/24
```

Change the function of the router, simulating a host (PC)
```bash
hostname HOST1
no ip routing # will not route
interface [id]
ip address [address][subnet mask]
duplex auto # let the negociation protocol find the appropriate communication method
speed auto
!
ip default-gateway [address]
!
end
```

# Routing protocol
## Static route
```bash
configure terminal
ip route <destination-address><mask><next-hop | exit interface><AD>
# AD = amdinistration distance : reverse of priority level
# gateway = interface (ex: Serial 0/0/0)
ipv6 route destination-address mask gateway AD
# gateway can be global unicast address of the interface, the interface identifier (ex: Serial 0/0/0) or interface identifier + link-local address of the interface (because it is not routed)
```

delete a static route with `no ip route <ip><mask><next-hop>`

# Dynamic routing
## Neighbor Discovery with OSPF
*Only between routers, find the fastest route*
```bash
# 1. check the ospf daemon is enabled
exit # if in vtysh
vi /etc/frr/daemons # change ospf=no by ospf=yes then :wq
/etc/init.d/frr restart # restart daemons

# 2. configure two or more routers (connected with ethernet), do on all routers
# configure the ip4
router ospf
network 10.10.0.0 area 0 # adapt with your network where routers are, area must be the same for neighbors
exit
end
write memory

# 3. display
# in exec mode
show ip ospf neighbor
```

## RIP
Make sure to activate the ripd in /etc/frr/daemons and restart with /etc/init.d/frr restart
```console
configure terminal
router rip
version 2
network
```

# Encapsulation Protocol
## Tunnelling with Generic Routing Encapsulation (GRE)
*in config mode*
```bash
tunnel [1]
tunnel source [interface] # interface the router can physically reach the destination
tunnel destination [ip]
ip adress [ip][mask] # attribute IP for tunnel interface 
```

# Display information
*interfaces*
```bash
show interface brief
show ip interface brief | exclude unassigned # show ip address assigned to interfaces
show ip default-gateway # for router simulating being a host
```
*Show the link-local address of an interface*
```console
show ipv6 interface Serial 0/0/0 | include link-local
```
*routing static table*
```console 
show ip route <destination>
show ip[v6] route static
```
*see the config*
```console
show running-config
```

```console
show tunnel [1]
```

# Server services
## SSH 
Set up a remote access (no-routing router to router)
after having set up :
* interface ip addresses and gateway (for host)
R1
```bash
conf t
username [name] password [password] # can create many as desired
enable secret [password] # password asked when desire to enter in exec mode via host 
line vty 0 4 # enable 5 hosts to remotely access
    transport input ssh
!
ip domain-name lab.local # DNS of the router
crypto key generate rsa modulus 2048 # generate ssh key (2048 bits)
ip ssh version 2
end
```

without user account authentification : 
```bash
line vty 0 4
    password [password] # password for all hosts if no login local (via configured accounts)
    login
!
transport input ssh
```

HOST
```console
ssh -l [name] [dns ip addr]
enable
```

## TFTP
transmission of file
ROUTER
```bash
tftp-server flash:[file]
```

HOST
```bash
copy tftp: [destination] # download destination
```
* type tftp server ip address
* filename

## DNS
ROUTER
```bash
ip domain name [name]
ip dns server
ip host [FQDN] [ip addr] # associate FQDN to one IP
```
FQDN = Fully Qualified Domain Name (host/router name + domain name)
HOST
```bash
ip name-server [router interface ip address]
ip domain-lookup # convert ip address to name
ping [dns host name]
```

## DHCP
ROUTER
```bash
conf t
interface [int]
ip address [ip.254][mask]
no shutdown
!
ip dhcp pool [name]
network [start-ip.0][mask]
default-router [ip]
```
HOST
```console
(config-if)# ip address dhcp
```