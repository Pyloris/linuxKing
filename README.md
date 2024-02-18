# LinuxKing : Linux (commands) Put Together
> I suffixed the names of chapters with `Byte` which symbolizes the concise nature of the material provided.

### Command Index
1. [Linux Navigation Byte](#LinuxNav)
2. [SSH Byte](#SSH)
3. [Sed Byte](#SED)
4. [Grep Byte](#GREP)
5. [Find Byte](#FIND)
6. [Netstat Byte](#NETSTAT)
7. [Curl Byte](#CURL)
8. [Wget Byte](#WGET)
9. [Tcpdump Byte](#TCPDUMP)
10. [Dig Byte](#DIG)
11. [Iptables Byte](#IPTABLES)
12. [Ufw Byte](#UFW)
13. [Ipaddr Byte](#IPADDR)


### Stuff you should to Know
1. [Redirection](#REDIRECTION)
2. [Process Substitution](#PROCESS_SUBSTITUTION)
3. [Bash Fu](#BASHFU)
## SSH Command
> SSH (Secure Shell) is a network communication protocol that runs on TCP. Its default port is 22.
>
> Note: A client can establish as many connection as it wants and same for the Server, it can accept as many as it receives (as long as CPU doesnt giveup).


### Jump into Terminal
```shell
# connect to this host using SSH with username being same as the username on your local machine (will ask for password)
ssh ssh.wani.com

# to login as a different user (2 ways)
ssh -l username hostname
ssh username@hostname

# use a private key to login
ssh -i keyFile username@hostname

# to use a non-default port (other than 22)
ssh -p 4422 -l username hostname

# facing issues logging in, use -v to enable debug messages (increase verbosity -vv -vvv)
ssh -v hostname
```


### Using PK Auth
> Private Key is stored on the client & Public key is to transferred to each server to which we want to connect.
```text
# Generate a public-private keypair
ssh-keygen -t rsa -b 4096 -C "shoaibwani@gmail.com"

# send the public key to a remote server
ssh-copy-id -i ~/.ssh/previousKey username@server

# now use new key
ssh -i ~/.ssh/id_rsa username@server
```


### Local Port Forwarding
```text
# Forward a remote port to a local port
# remote_host:remote_port is resolved with respect to ssh server (hostname)
# -N means not to execute a remote command
# -f means run in background
ssh -L localp:remote_host:remote_port -N -f username@hostname

# like this example
ssh -L 8080:localhost:80 -N -f shoaib@ssh.wani.com        # forwards localhost:80 on ssh.wani.com to my machines 8080
```


### Remote Port Forwarding
```text
# This is done via the client, SERVER -> Client -> Dest
ssh -R remote_host:remote_port:dest_host:dest_port -N -f username@hostname

# example
# here ssh.shoaib.com is resolved from the client machine.
ssh -R localhost:80:ssh.shoaib.com:8080 -N -f shoaib@ssh.wani.com
```


### Dynamic Port Forwarding
```text
# It turns ssh client into a SOCKS proxy allowing us to tunnel all traffic through a remote server.
# APP(on local machine) -> localhost:port -> ssh.wani.com
ssh -D port username@ssh.wani.com

# It should be allowed by the server (make sure).
```


### Learn SSH config File
> This comes into picture when you are connecting to multiple hosts on a daily basis. Its hard to remember the hostname, username, port and etc.
>
>We put all this info into the per-user Config file which is located in `~/.ssh/`. Depending on the logged in user, the corresponding config is read.
>
>We can create a system wide config for ssh in `/etc/ssh/ssh_config`.

Everything you will ever need to use in an ssh config file
```text
# Global options
Host *
  # Use stronger ciphers for encryption
  Ciphers aes256-ctr,aes128-ctr
  # Enable compression for smaller data transfers
  Compression yes

# Host-specific configurations

# Server1 with password authentication
Host server1
  Hostname 192.168.1.10
  User shoaibwani
  Password itsmeyaar

# Server2 with public key authentication
Host server2
  Hostname example.com
  IdentityFile ~/.ssh/id_rsa
  Port 2222 # Non-standard port forwarding

# configure multiple hosts in single section
Host 192.168.* *.shoaib.com *.wani.in
    User shoaib
    Password letmein

# Group configuration for all servers in "dev" group
Match Group dev
  User devuser
  ForwardAgent yes

# Group definition for servers in "dev" group
Group dev
  server1
  server2

# Comments explaining specific options
# Port forwarding for web server on server2
Host webserver
  Hostname server2
  LocalForward 8080:localhost:80 # Expose local port 8080 to remote port 80
```
