# Summary
* [Set up](#set-up-the-switch)
* [Display](#display-information)
* [VLAN](#vlan)


# Set up the switch 
*Within Fusion Ware VM, using alpine docker image*

Pull image to create switch emulation
```console
docker pull alpine
docker run -it --name my-switch --privileged alpine
```
Or dowload Ethernet Switch Router template (and select "This is an EtherSwitch")

Add packages 
```bash
apk add bridge-utils iproute2 # brctl for create/handle bridge and iproute to create/modify interface + show MAC table
```

Configure the switch and its interface
*requires to download a template (OpenvSwitch), commands start with ovs*
```bash
for br in $(ovs vsctl list-br); do ovs vsctl del-br $br; done # delete all pre-configured bridges
brctl addbr switch0 | ovs vsctl add-br switch0 # create bridge
ip addr add [ip address] # add ip address for the switch = become gateway to configure PC's address
brctl addif switch0 eth0 | ovs vsctl add-port switch0 eth0 # add interface for this bridge, ⚠️fa0/0 and fa0/1 ar reserved for router use
ip link set eth0 up
ip link set eth0 up
ip link set switch0 up
```

# Display information
```bash
ip link show # see interface and their MAC addresses
ip addr show bridge0 # show their IP Addresses
brctl show | ovs vsctl show # display information about the bridge
brctl showmacs switch0 | ovs appctl fdb/show bridge0 # show forwarding database
show vlan-switch (brief | id [10])
show mac-address table 
show interfaces status
show interface [interface] switchport # info about port mode
```

# VLAN
1. Use the EtherSwitch template, connect 2 PC (or more)
2. Create new VLAN
```console
vlan database
vlan [10] name [name]
```
3. Set up interfaces (⚠️fa0/0 and 0/1 are reserved for router mode)
```bash
configure terminal
interface range fa1/0 - 1 # configure multiple interfaces
switchport mode access # or just switchport, to active switch mode
switchport mode dynamic auto|desirable # for other mode
switchport access vlan [10]
exit
```
4. Set IP addresses for PC (ip [192.168.1.1 255.255.255.0]) and ping between them to verify

## Set up trunk mode on one switch's interface (between two switches)
```bash
(config-if)# switchport mode trunk
switchport trunk encapsulation [dot1q|isl] # config encapsulation type dot1q for 802.1Q or isl
```

## Switch Virtual Interfaces
Give a virtual interface (network layer) for one VLAN
1. Create vlan to create after its SVI (vlan database)
2. 
```bash
configure terminal
interface vlan [20]
no shutdown
ip address [address] # attributes an ip address for the interface of the vlan
exit
```
3. Set the interface to the VLAN
```bash
interface [interface]
switchport mode access
switchport access vlan [id]
```
4. Verify
```console
show ip interface brief | include Vlan
```