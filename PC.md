# VPC
## PC ip adress configuration
```console
ip <address><cidr><gateway>
```

create traffic
```bash
ping [address] -t
(config)# ip sla [id] # to continue to run in the background
(config-ip-sla)# icmp-echo [ip addr]
(config-ip-sla-echo)# frequency [seconds]
!
ip sla [id] start-time [now] life [seconds] # set ip sla responder for the router to respond request
no ip sla [1] # end sla
```

## Display
```console
show ip
show ip sla stat
```

# Linux (Alpine)
```bash
ip addr add [ip address]/[cidr] dev [interface]
ip route add default via [gateway address]
ip link set [interface] up
```

## Display
```bash
ip route show # show the gateway
```


