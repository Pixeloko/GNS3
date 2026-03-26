# Multiple Spanningtree Protocol
## Requirements
* 3 Switches/1 MST region
* 1 PVST switch
* 5 trunks

## Navigate
1. [MST](#set-up-mst-region)
1. [PVST + MST](#config-2)
1. [Verification commands](#commands-for-verification)

## Config 1 - MST
![first-config](/media/mst-pvst-1.png)

## MST region configuration
enable MST (SW1,SW2,SW3)
```console
spanning-tree mst
```
trunks on all interfaces
```console
(config-if)# switchport trunk encappsulation dot1q
(config-if)# switchport mode trunk 
exit
```
create identical VLANs
```console
vlan database 10 name right
vlan database 20 name left
```
set up instances, attribute VLAN
```console
spanning-tree mst configuration
    name [region name]
    revision [region id]
    instance [1] vlan [id]
    instance [2] vlan [id]
    exit
```

## Differentiate root bridges/instances
SW2
```bash
config t
spanning-tree mst 1 priority 4096 # change the priority of SW2 for instance 1
```
SW3
```console
spanning-tree mst 2 priority 4096
```

## Config 2 - PVST & MST
![second-config](/media/mst-pvst-2.png)

## PVST + MST
1. Add new switch
1. Set up trunks on both sides
2. Create identical vlans
```bash
spanning-tree vlan 10
spanning-tree vlan 20
``` 
see root bridge never changing direction

## Commands for verification
```bash
show interface status # check trunk mode
show vlan brief # check VLANs
show span mst conf # check VLANs mapped in instances
show spanning-tree mst [1] | begin Interface # check interfaces/instances
show spanning-tree vlan [id] # check vlan root bridge, interfaces, SPT mode 
```