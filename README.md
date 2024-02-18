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
# LinuxKing : Linux (commands) Put Together
> I suffixed the names of chapters with `Byte` which symbolizes the concise nature of the material provided.

### Command Index
1. [Linux Navigation Byte](#linux-navigation-commands)
2. [Files & Directories Byte](#files-and-directories)
3. [SSH Byte](#ssh-command)
4. [Sed Byte](#SED)
5. [Grep Byte](#GREP)
6. [Find Byte](#FIND)
7. [Netstat Byte](#NETSTAT)
8. [Curl Byte](#CURL)
9. [Wget Byte](#WGET)
10. [Tcpdump Byte](#TCPDUMP)
11. [Dig Byte](#DIG)
12. [Iptables Byte](#IPTABLES)
13. [Ufw Byte](#UFW)
14. [Ipaddr Byte](#IPADDR)


### Stuff you should to Know
1. [Redirection](#REDIRECTION)
2. [Process Substitution](#PROCESS_SUBSTITUTION)
3. [Bash Fu](#BASHFU)
## Linux Navigation Commands
> This section contains commands related to moving through a linux system via command line.
>
> Without further adu, lets get started (launch your terminal)

```shell
# Everyone knows what directories/folders are in a computer.
# so the folder you are in currently is called Current Working Directory
# To find the current working directory do
pwd   
#output:  /home/shoiab/

# to change current working directory to some other directory
cd /path/to/directory

# shortcuts
cd    # change cwd to you home directory
cd -   # change cwd to previous working directory
cd ~username    # change cwd to user with username home directory


# list contents of cw directory (which is files & other directories)
ls 
# list some other directory
ls /root
# in long format (shows other details too)
ls -l
# see details about a directory not its contents
ls -ld /root
# all the files including ones that start with .(period)
ls -a
# size in human readable format
ls -lh
# reverse the order
ls -r
# sort results
ls -S  # by size
ls -t  # by Modification time


# determine a files type (extensions are not that great)
file /root/unknownFile

# view text files using a pager
less /etc/passwd
# search for something in less
/characters # n for next match & q to quit less
```
## Files and Directories
> This section deals with commands used to manuplate files & directories.

```shell
# create directory
mkdir dir1 /home/dir2 /dir3

# copy files and directories
cp file1 file2 dir1
# copy dir1 recursively into /home/dir2
cp -r dir1 /home/dir2
# only copy files that dont exist or are newer
cp -u dir1/* /home/dir2
# copy files while preserving their permission & attributes (after copy they usually take on default values for user performing copy)
cp -a file1 dir1
# prompt before overwriting existing files
cp -i dir1/* /home/dir2

# move files and directories, options mean same as for cp
mv file1 file2 dir1
mv -u dir1/* /home/dir2
mv -i dir1/* /dir3

# remove files & directories
rm file1
rm -i file2  # before delete prompt user
rm -r dir1
rm -f dir1 dir2  # ignore non existant files & directories (continue silently)


# create a soft link for a file/directory
ln -s /home/file /bin/link    # /bin/link points to /home/file

# create a hard link for a file/directory
ln /home/file /bin/hlink
```
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
