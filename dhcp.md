# DHCP configuration
```console
ip dhcp pool <name> # name of the pool of addresses to distribute
network <network><mask> # pool of addreses
default-router <gateway-ip> # gateway address for clients to send traffic outside their network
ip helper-address <dhcp-address> # if clients & dhcp server not in the same network, configure the router as the dhcp relay agent
dns-server <ip> # sent to the clients
lease <days><hours>min>
ip dhcp excluded-address <start><end>
```

# Display
*bindings between one dhcp server and its clients*
```console
show ip dhcp binding
```
*see messages between clients and dhcp server*
```console
debug ip dhcp server packet
```