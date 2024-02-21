
# LinuxKing : Linux (commands) Put Together
> I suffixed the names of chapters with `Byte` which symbolizes the concise nature of the material provided.

### Command Index
1. [Linux Navigation Byte](#linux-navigation-commands)
2. [Files & Directories Byte](#files-and-directories)
3. [SSH Byte](#ssh-command)
4. [Sed Byte](#sed-command)
5. [Grep Byte](#grep-command)
6. [Searching Files](#search-for-files)
7. [Netstat Byte](#NETSTAT)
8. [Curl Byte](#curl-command)
9. [Wget Byte](#wget-command)
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
```shell
# Generate a public-private keypair
ssh-keygen -t rsa -b 4096 -C "shoaibwani@gmail.com"

# send the public key to a remote server
ssh-copy-id -i ~/.ssh/previousKey username@server

# now use new key
ssh -i ~/.ssh/id_rsa username@server
```


### Local Port Forwarding
```shell
# Forward a remote port to a local port
# remote_host:remote_port is resolved with respect to ssh server (hostname)
# -N means not to execute a remote command
# -f means run in background
ssh -L localp:remote_host:remote_port -N -f username@hostname

# like this example
ssh -L 8080:localhost:80 -N -f shoaib@ssh.wani.com        # forwards localhost:80 on ssh.wani.com to my machines 8080
```


### Remote Port Forwarding
```shell
# This is done via the client, SERVER -> Client -> Dest
ssh -R remote_host:remote_port:dest_host:dest_port -N -f username@hostname

# example
# here ssh.shoaib.com is resolved from the client machine.
ssh -R localhost:80:ssh.shoaib.com:8080 -N -f shoaib@ssh.wani.com
```


### Dynamic Port Forwarding
```shell
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
```shell
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
## SED Command
> SED is a stream editor. It can perform basic text manipulation on files and input streams such as pipelines.

```shell
# do inplace search and replace
sed -i 's/shoaib/Anonymous/g' file.txt
# In the string :
# 1. s - it means substitute
# 2. g - means globally | all occurances in the line

# same as above, but creates a backup file with given extension
sed -i.bak 's/shoaib/anonymous/g' file.txt       # backup file => file.txt.bak

# for case insensitive matching
sed -i 's/foo/bar/gI' file.txt

# '&' represents the matched string, and can be used to surround etc the matched part
# add {} around number 5
sed -i 's/5/{&}/g' file.txt

# search and replace recursively
find . -type f -iname 'Shoaibwani.txt' -exec sed -i 's/shoaib/foo/g' '{}' +
```
## Grep Command
> This command is used to that searchs for text strings in one or more files.
> It prints out each line on which a match is found.

```shell
# search for a line in a file
grep root /etc/passwd
# print lines that dont match the pattern
grep -v root /etc/passwd
# perform a recursive search on a directory
grep -r linux.com /etc
# to follow symbolic links as well use
grep -R linux.com /etc
# print only file names where match is found
grep -l linux /etc/apt/*.conf
# perform a case insenitive search
grep -i "shoaib wani" /etc/passwd

# to search for a word (delimited by non word characters)
grep -w wani /etc/passwd
```

> Word characters include alphanumeric characters (a-z, A-Z, and 0-9) and underscores (_). All other characters are considered as non-word characters.

```shell
# print 5 lines before matched line
grep -B 5 -E 'shoaib|hassan' /etc/profile
# print 5 lines after the matched line
grep -A 5 -E 'shoaib|wani' /etc/profile

# print only the matching part of the string
grep -o threat /etc/config

# use extended regular expressions
grep -E 'shoaib|hassan|wani' /etc/profile

# count no of matches found
grep -c countme /usr/file

# show line numbers with matches (prefixed)
grep -n tcp /etc/services
```
## Search for Files
> This section introduces commands used to search files and directories in a linux filesystem.

```shell
# to search based on just name of the file/directory
locate bin/zip

# search for a file owned by shoaib with permissions 600
find ~ -type f -user shoaib -perm 600
# search for files which are empty and dont belong to valid group
find / -type d -empty -nogroup
# using a name -iname is case insensitive
find / -type f -name "Shoaib.txt"
# conditions supported
# 1. -cmin +n/-n [n minutes]
# 2. -ctime +n/-n [n days]
# 3. -empty
# 4. -group
# 5. -newer
# 6. -nouser


# using operators to combine above conditions -and, -or, -not
find / \( -type f -and -perm 600 -not -empty\) -or \( -type d -and -perm 600 \)

# action based on search results (predefined)
find / -type f -size +1G -delete         # delete found files
# supported ones are:
# -print (default) if none is specified
# -delete
# -ls
# -quit (quit once matches are made)

# actions (user-defined) on search results
find / -type f -perm 620 -user shoaib -size -1G -cnewer ~/.ssh/config -exec rm -rf '{}' ';'   # they have special meaning to shell
# for interactive execution of commands
find / -type f -nogroup -ok rm '{}' ';'

# to execute a single command for entire search result
find / -type f -name "*.BAK" -exec rm '{}' ';' +
```
## CURL Command
> It is used to transfer files over a network. Supports Authentication , Encryption & it can also resume interrupted Transfers.


```shell
# download files while keeping original name
curl -O https://hacker.com/pdf1.pdf -O https://hacker.com/pdf2.pdf
# save file to a custom path
curl https://hacker.com/file.pdf -o /home/shoaib/mypdf.pdf
# to get a progress bar as the download progresses use '-#'
curl https://domain.com/file.pdf -#

# use proxy
curl -x "http://shoaib:paswd@198.8.8.1:8080" https://domain.com/file.pdf -o ./file1

# POST request
curl -X POST https://domain.com/login --data-urlencode username="shoaib" --data-urlencode password="itsme" -d key="hello"

# upload a file to HTTP server
curl --upload-file /path/to/file http://ip:port/

# upload to FTP server in anonymous mode
curl -T /home/file ftp://addr/
# provide credentials
curl -u username:pass -T /path/to/file ftp://ip
```
