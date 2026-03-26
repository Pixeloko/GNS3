# Summary
* [Set up](#set-up-the-switch)
* [Display](#display-information)
* [Spanning-Tree](#spantree-protocol-stp)
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
show vlan (brief | id [10])
show mac-address # add dynamic or static to only see mac address learnt with a specific mode
show mac-address aging-time # after this indicated time, SW forget MAC address of a host which has gone silent
show interfaces status
show interface [interface] switchport # info about port mode
show running-config all | include spanning-tree mode
show spanning-tree vlan [1] (root) # show the priority of the root bridge + SPT protocol
show spanning-tree bridge details # bridge id (priority, VLAN sys-id, MAC)
debug spanning-tree events # see events hapenning in the topology
debug spanning-tree bpdu # view sent & received BPDUs
show spanning-tree mst configuration | sh span mst con # show MST regions info
show spanning-tree mst # vlan mapping + root
show spanning-tree mst interface [id] | begin Interface # show port type according to instances (containing range of vlans)
```

# SpanTree Protocol STP
Draw topology, helps switches to elect root bridge an set root/designated/blocking ports.
Connect a SW to another device (router/switch), to see steps of port state:
1. tap the debug command
```console
debug spanning-tree events
```
2. shutdown and set up the interface to see blocking->listening->learning->forwarding

Lower the priority (to choose the root bridge)
```bash
configure t
spanning-tree vlan 1 priority [number, default is 32768] # by default, all switches are in vlan 1
exit
show spanning-tree vlan 1 root # check ports configuration
```
or, automatic priority 
```bash
spanning-tree vlan [1] root primary|secondary 
```
Higher the path cost (to urge for blocked port)
```console
(config-if)# spanning-tree cost [new cost]
```

## SPT features
PORTFAST
prevent TCN sending for topology change on an interface connected to a single passive device
```console
SW(config-if)# spanning-tree portfast
```

ROOT GUARD
prevent a root bridge from losing its status by enabling Root Guard on all its DSG ports
```console
(config-if)#spanning-tree guard root
```

BPDU FILTER
Prevent transmission+reception of BPDU on portfast port (global mode)
```console
spanning-tree portfast bpdufilter default
```
on a specific port (local mode)
```console
spanning-tree bpdufilter enable
```

BPDU GUARD
Prevent BPDU reception. 
```bash
spanning-tree portfast bpduguard default # global
spanning-tree bpduguard enable # local
```

# Rapid SpanTree Protocol
enable RSPT per VLAN on SW
```console
(config)#spanning-tree mode rapid-pvst
```
⚠️Some IOL image does not support rapid PVST, to solve the problem
1. Download an appropriate image (`i86bi-linux-adventerprisek9-15.1a.bin` works perfectly fine)
2. Generate a valid license key. To do so, create a file in /tmp `license.py` (in gns3 VM) and paste
```python
#!/usr/bin/python
print("\n*********************************************************************")
print("Cisco IOU License Generator - Kal 2011, python port of 2006 C version")
import os
import socket
import hashlib
import struct
# get the host id and host name to calculate the hostkey
hostid = os.popen("hostid").read().strip()
hostname = socket.gethostname()
ioukey = int(hostid, 16)
for x in hostname:
    ioukey = ioukey + ord(x)
print("hostid=" + hostid + ", hostname=" + hostname + ", ioukey=" + hex(ioukey)[2:])
# create the license using md5sum
iouPad1 = b'\x4B\x58\x21\x81\x56\x7B\x0D\xF3\x21\x43\x9B\x7E\xAC\x1D\xE6\x8A'
iouPad2 = b'\x80' + b'\x00' * 39
md5input = iouPad1 + iouPad2 + struct.pack('!L', ioukey) + iouPad1
iouLicense = hashlib.md5(md5input).hexdigest()[:16]
with open('/opt/unetlab/addons/iol/bin/iourc', 'w') as f:
    f.write("[license]\n" + hostname + " = " + iouLicense + ";\n")
# add license info to $HOME/.iourc
print("\n*********************************************************************")
print("Create the license file $HOME/.iourc with this command:")
print("\nThe command adds the following text to $HOME/.iourc:")
print("[license]\n" + hostname + " = " + iouLicense + ";")
# disable phone home feature
print("\n*********************************************************************")
print("Disable the phone home feature with this command:")
print(" grep -q -F '127.0.0.1 xml.cisco.com' /etc/hosts || echo '127.0.0.1 xml.cisco.com' | sudo tee -a /etc/hosts")
print("\nThe command adds the following text to /etc/hosts:")
print("127.0.0.1 xml.cisco.com")
print("\n*********************************************************************")
```
execute it and copy paste license key in GNS2 GUI>Preferences>"IOS on Unix"

# Multiple spanning tree protocol (MST)
enable it with `(config)#spanning-tree mode mst'
enter MST configuration mode
```bash
spanning-tree mst configuration
name [name] # optional
revision [id] # optional
instance [id] vlan [id],[id] # map vlans in it
```
influence root bridge election for one instance with priority of the switch
```bash
spanning-tree mst 1 priority 4096 # switch will have this priority in instance 1
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

## Voice VLAN (for IP Phone)
1. Create a VLAN via the SW 
2. Go to the interface andd attribute that VLAN
```bash
switchport voice vlan [id] # turn into a voice vlan
show interface [interface id]switchport # verify
```

## Inter-VLAN routing
### Router on a stick technique
Requisites : 
* R1
* SW1 with multiple VLANs
* trunk link between R1 and SW1

Enable the trunk on SW1:
```bash
configure t
interface fa1/3 # the link between R1 and SW1
switchport trunk encapsulation [dot1q|isl] # config encapsulation type dot1q for 802.1Q or isl
switchport mode trunk
switchport trunk allowed vlan [id, id] # to add specific vlan to the default ones : ...allowed vlan add [id]
```

Create sub-interfaces for each vlan on R1:
```bash
interface faO/0.10 # creates a virtual sub-interface on the physical port fa0/0
encapsulation dot1q 10
ip address 192.168.10.254 255.255.255.0 # will be the default gateway for all devices in VLAN 10
show ip route # verify
```
Repeat for each vlan

### Switch Virtual Interfaces
Give a virtual interface (network layer) for one VLAN
1. Create vlan to create after its SVI (vlan database)
2. For every vlans
```bash
configure terminal
ip routing # to start building the routing table 
interface vlan [20]
no shutdown
ip address [address][subnetmask] # attributes an ip address for the interface of the vlan
exit
```
3. Set the interface to the VLAN
```bash
interface [id]
switchport mode access
switchport access vlan [id]
```
4. Verify
```bash
show ip interface brief | include Vlan # or show ip interface vlan [id]
```

If all interfaces are down, the SVI protocol must be down. To exclude one interface from influencing the state of the SVI:
```console
SW1(config-if)#switchport autostate exclude
```

### Routed port
```console
(config-if)#no switchport
ip address [ip, subnet mask]
```