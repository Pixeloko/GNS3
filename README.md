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
# In config mode
interface eth1
ip address # Insert address with subnet mask
no shutdown # Permit to send and recieve informations
exit # Exit submode config
```

# Display information
*Active interface and routeur address on the physical link*
```bash
show interface brief
```