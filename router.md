# Router modes
* ==r1#== exec commands
* ==r1(config)#== modify the config

# Router configuration
*Open an integrated shell to configure FRR Routing daemon*
```bash
vtysh
configure terminal
```
*Change the name of the router*
```bash
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
```bash
no ip address 10.10.0.1/24
```

# Routing protocol
## Static route
```bash
ip route destination-address mask gateway AD
# AD = amdinistration distance : reverse of priority level
# mask indicate the bits we need to match for the destination
# gateway = interface (ex: Serial 0/0/0)
ipv6 route destination-address mask gateway AD
# gateway can be global unicast address of the interface, the interface identifier (ex: Serial 0/0/0) or interface identifier + link-local address of the interface (because it is not routed)
```

# Display information
*Active interface and routeur address on the physical link*
```bash
show interface brief
```
*Show the link-local address of an interface*
```bash
show ipv6 interface Serial 0/0/0 | include link-local
*routing static table*
```bash 
show ipv6 route static
```

# Neighbor Discovery with OSPF
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