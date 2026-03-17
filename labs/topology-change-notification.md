# Topology Change Notification
See lesson on [Topology Change Notification](/switch-vlan.md#spantree-protocol-stp)

![Topology](/media/tcn.png)

## Manipulation the election of root bridge
Enable debug on all switches
```console
debug spanning-tree events
```

Lower priority on SW3 to turn it into root bridge
```bash
configure t
spanning-tree vlan 1 priority 4086
```

Increase path cost on interface fa1/2 of SW2 to make it a blocked port
```bash
configure t
interface fa1/2
spanning-tree cost 50
end
```

# Test the topology
Set ip address on PCs and connect them to the network
PC1
```console
ip 192.168.1.1
```
PC2
```console
ip 192.168.1.2
```

Verify port type on each switch with
```console
show spanning-tree vlan 1
```

Verify Interfaces brief (on swicth connected to one PC)
```console
show ip interface brief
```
⚠️Note : if the interface is physically up but the protocol is down (old CISCO device) force
SW
```bash
configure t
speed [100]
duplex full # force the same communication on both side
shutdown 
no shutdown # restart
end
show ip interface brief # interface should but up/up
```

## Provoke a Topology Chage 
Launch an infinite ping
PC1
```console
ping 192.168.1.2 -t
```

Shutdown one interface on root port and see the debug output + the timeout ping
SW1
```console
(config-if)# shutdown
```

Verify new ports configuration on each switch
```console
show spanning-tree vlan 1
```
After the learning phase see the ping reaching its target again
