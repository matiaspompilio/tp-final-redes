### Run openbmp All in one

`docker run -it -d -e --name=openbmp.localdomain -e REINIT_DB=1 -e OPENBMP_BUFFER=64 -e MEM=15 \
     -v /var/openbmp/mysql:/data/mysql \
     -p 3306:3306 -p 2181:2181 -p 9092:9092 -p 5000:5000 -p 8001:8001 \
     openbmp/aio`

### Show openbmp logs

`docker exec <container-name> tail -f /var/log/openbmpd.log`


### Connect coreemu router to host machine (using rj45 tool)

```
#Creating dummy interface in host machine
host>> ip link add dummy type dummy

#show bridget interfaces and copy dummy dev
host>> brctl show
host>> ip add add <new-ip/mask> dev <paste-dev-name>

host>> iptables -t nat -A POSTROUTING -j MASQUERADE
host>> echo 1 > /proc/sys/net/ipv4/ip_forward

#routing node (necessary for routing to openbmp collector)
core-node>>ip route add <ip> via <new-ip-host>
```
