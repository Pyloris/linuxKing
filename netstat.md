## Netstat Command
> It is used to check for listening ports on a linux machine.
```shell
# check for all tcp & udp ports that are being listened on
netstat -tulnp
# -t -> show tcp ports
# -u -> show udp ports
# -n -> show numerical addresses instead of resolving hosts
# -l -> show only listening ports
# -p -> show pid & name of process listening
```