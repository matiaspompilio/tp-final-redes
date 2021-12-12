## Steps to follow

1. Open the topology file in COREEMU
2. Create the RJ45 connection (TODO create new topology file with RJ45 added)
3. Creating dummy interface in host machine (Steps to follow below)
5. Run the COREEMU
6. Connect COREEMU to host machine. (Steps to follow below)
7. Kill bgp in Core router and re-run with `-M bmp` option added (This step will delete the bgp configuration)
8. Restore topology configuration with restore.sh script
9. Run openbmp all in one
10. Route node to bmp collector (Step to follow below)
11. Configure BPM in Core router


### Run openbmp All in one

`docker run -it -d -e --name=openbmp.localdomain -e REINIT_DB=1 -e OPENBMP_BUFFER=64 -e MEM=15 \
     -v /var/openbmp/mysql:/data/mysql \
     -p 3306:3306 -p 2181:2181 -p 9092:9092 -p 5000:5000 -p 8001:8001 \
     openbmp/aio`

### Creating dummy interface in host machine
```
host>> ip link add dummy type dummy
In core assign dummy interface to rj45.
```

### Connect coreemu router to host machine (using rj45 tool)

```
#show bridget interfaces and copy dummy dev
host>> brctl show
host>> ip add add <new-ip/mask> dev <paste-bridge-name>

host>> iptables -t nat -A POSTROUTING -j MASQUERADE
host>> echo 1 > /proc/sys/net/ipv4/ip_forward
```

### Route node to bmp collector

``` 
#routing node (necessary for routing to openbmp collector)
core-router>>ip route add <ip> via <new-ip-host>
n6(config-bgp-bmp)# bmp connect <ip> port 5000 min-retry 100 
```

### Useful commands

```
#show bmp connection in core router
core-router>>sh bmp
http://localhost:8001/db_rest/v1/routers (user: openbmp, password: CiscoRA)
Check bmp logs: docker exec <container-name> tail -f /var/log/openbmpd.log
```
