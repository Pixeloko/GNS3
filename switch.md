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
```bash
brctl addbr switch0 # create bridge
brctl addif switch0 eth0 # add interface for this bridge
ip link set eth0 up
ip link set eth0 up
ip link set switch0 up
```

Display information
```bash
ip link show # see interface and their MAC addresses
ip addr show # show their IP Addresses
brctl show # display information about the bridge
brctl showmacs switch0 # show forwarding database
```