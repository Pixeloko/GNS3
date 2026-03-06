# Set up the switch 
*Within Fusion Ware VM, using alpine docker image*

Pull image to create switch emulation
```console
docker pull alpine
docker run -it --name my-switch --privileged alpine
```

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
brctl addif switch0 eth0 | ovs vsctl add-port switch0 eth0 # add interface for this bridge
ip link set eth0 up
ip link set eth0 up
ip link set switch0 up
```

Display information
```bash
ip link show # see interface and their MAC addresses
ip addr show bridge0 # show their IP Addresses
brctl show | ovs vsctl show # display information about the bridge
brctl showmacs switch0 | ovs appctl fdb/show bridge0 # show forwarding database
```