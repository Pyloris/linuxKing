## LSOF Command
> This command is used to get information about the files which have been opened by processes.
> In linux everything is a FILE.

```shell
# list all the files being accessed by the system at the moment
lsof
# user specific opened files
lsof -u username
# list all the processes current using a certain port
lsof -i TCP:80
# can supply a range of ports as well
lsof -i TCP:1-1000
# processes using ipv4
lsof -i 4
# ipv6
lsof -i 6
# all network connections currently
lsof -i
```