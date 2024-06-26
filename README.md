
# LinuxKing : Linux (commands) Put Together
> I suffixed the names of chapters with `Byte` which symbolizes the concise nature of the material provided.

### Command Index
1. [Linux Navigation Byte](#linux-navigation-commands)
2. [Files & Directories Byte](#files-and-directories)
3. [SSH Byte](#ssh-command)
3a. [Zip Byte](#zip-command)
4. [SCP Byte](#scp-command)
5. [Sed Byte](#sed-command)
6. [Grep Byte](#grep-command)
6a. [Awk Byte](#awk-command)
7. [Searching Files](#search-for-files)
8. [Netstat Byte](#netstat-command)
9. [Lsof Byte](#lsof-command)
10. [Curl Byte](#curl-command)
11. [Wget Byte](#wget-command)
12. [Tcpdump Byte](#TCPDUMP)
13. [Dig Byte](#dig-command)
14. [Iptables Byte](#IPTABLES)
15. [Ufw Byte](#UFW)
16. [Ipaddr Byte](#IPADDR)


### Stuff you should to Know
1. [Redirection](#redirection)
2. [Process Substitution](#process-substitution)
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
## ZIP Command
> To make .zip from folders we use this utility. It does not preserve ownership information. To preserve it, we should use `tar` utility.
```shell
## SYNTAX
## zip OPTIONS ARCHIVE_NAME FILES
zip files.zip file1 file2

# suppress the output
zip -q files.zip file1 file2 file3

# archive a directory
zip -r files.zip filesDir/

# add other files to the zip as well
zip -r files.zip filesDir/ file1.txt ../shoaib/files.txt

## By default compression method used is : deflate
## SUPPORTED METHODS:
## 1. deflate
## 2. bzip2
# if a file cannot be compress,it stored as is
zip -r -Z bzip2 filesDir/

# specify compression level from 0-9
# default is 6 and 0 is no compression
zip -9 -r hello.zip filesDir/

# password protect zipfile
zip -e hello.zip filesDir/

# split zip into equal size zip for upload restrictions
# will create 1GB zip files for dir/ if size exceeds 1GB.
zip -s 1g -r arch.zip dir/
```
## SCP Command
> SCP or Secure Copy is used to copy files to and from a machine over an ssh connection. Files are encrypted while transfer so, its safe. SCP uses ':' to differentiate between 
>
>With scp, you can copy a file or directory:
>
>1. From your local system to a remote system.
>
>2. From a remote system to your local system.
>
>3. Between two remote systems from your local system.

```shell
# the basic structure of the command is like
scp [OPTION] [user@]SRC_HOST:]file1 [user@]DEST_HOST:]file2

# copy a file to a remote system from local system
scp -i keyFile /home/file.txt shoaib@10.1.1.2:/root/user/files

# change the name at destination
scp /home/file.txt shoaib@10.1.1.2:/root/user/files/newfile.txt

# if ssh is on non default port
scp -P 2342 file.txt dev:/root/user/files    # dev is configured in config

# copy a directory and all its files
scp -r /home/files dev:/root/files

# when using wildcards to avoid shell expansion, wrap in ""
scp "~/Projects/*" dev:/user/files
```

> Copy a remote file to local system.
```shell
# copy to local system from a remote system
scp dev:/remote/file.txt /home/shoaib/txt1.txt
```

> Copy files from one remote system to another remote system.
```shell
# take file from host1 to host2
scp user1@host1.com:/files/file.txt user2@host2.com:/files

# to route the traffic from local machine where command was executed use '-3'
scp -3 user1@host1.com:/files/file.txt user2@host2.com:/files
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
## AWK Command
> Awk is a general-purpose scripting language designed for advanced text processing.

### Working of AWK
> For AWK, a text file or stream contains `records` and `fields`. AWK processes each `record` until finished.
>
> Records are seperated by `RS` (default `\n`) and fields are seperated by `FS` (default `space`).
>
> Fields are referenced using `$` and a number starting from 1. `$0` references entire record.

#### Built-in Variables
Built-in Variables
Awk has a number of built-in variables that contain helpful information and allow you to control how the program is processed. Below are some of the most common built-in Variables:

`NF` - The number of fields in the record.

`NR` - The number of the current record.

`FILENAME` - The name of the input file that is currently processed.

`FS` - Field separator.

`RS` - Record separator.

`OFS` - Output field separator.

`ORS` - Output record separator.

#### Common Awk Statements
Awk supports different types of statements, including expressions, conditionals, input, output statements, and more. The most common awk statements are:

`exit` - Stops the execution of the whole program and exits.

`next` - Stops processing the current record and moves to the next record in the input data.

`print` - Print records, fields, variables, and custom text.

`printf` - Gives you more control over the output format, similar to C and bash printf .

### Working with AWK
```shell
# inline awk program
awk 'program' file.txt                           # 'program' is awk code
awk -f program.txt file.txt                      # read awk code from file

# execute actions on every record
awk '{ print $1, $2 }' pops.txt
# find a record and execute an action on it
awk '/shoaib/ {print $2}' pops.txt
# action before beginning the program
awk 'BEGIN { print "STARTED" }; END { print "ENDING" }' filex.txt
# change builtin variables
awk 'BEGIN { FS="." }; { print $2 }' pops.txt
# using variables to do stuff
awk '{ sum += $3 } END { printf "%d\n", sum }' teams.txt
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
## Wget Command
> Used to download files over HTTP, HTTPs and FTP.

```shell
# download a file to current directory
wget https://download.com/helo.txt
# turn off the output
wget -q https://.../
# save with a different name
wget -O my.txt https://download.com/helo.txt
# save to a specific directory with default name
wget -P /tmp/files https://download.com/helo.txt
# limit the download speed [1k, 1m, 1g]
wget --limit-rate=1m https://.../
# resume a download
wget -c https://microsoft.com/win10.iso
# run in background, output will be redirected to
# wget-log file in current directory
wget -b https://.../

# change user agent, as some website may block wget user agent
wget --user-agent="Mozilla ....." https://blocked.com/site.pdf

# download multiple files from links stored in a text file
wget -i files.txt

# read urls from STDIN
wget -i - <(echo "https://google.com/")

# download files from FTP server
wget --ftp-user=FTP_USERNAME --ftp-password=FTP_PASSWORD ftp://ftp.example.com/filename.tar.gz
```
## Dig Command
> This command is used to find DNS records.

```shell
# command format
dig @<dns_ip> <domain> <record_type>

# find A(ipv4) record of facebook.com
dig facebook.com A
# find AAAA(ipv6) record of google.com from cloudflare dns
dig @1.1.1.1 facebook.com AAAA

# types of records
# - A > ipv4
# - AAAA > ipv6
# - CNAME > canonical names
# - MX > mail servers
# - SOA > Start of Authority
# - TXT > TXT records
```
## Redirection

> Redirect Stdout to some other file or redirect stdin to some otherfile. 
```shell
# redirect stdout
ls / > output.txt
# redirect stdin
cat < file.txt
# redirect stderr
find / 2> /dev/null
# redirect stdout and stderr
find / 2> /dev/null 2>&1
# in short we may use this to redirect both
find / &> /dev/null


# truncate a file or create a new one
> newfile.txt
# append to a file
cat hi.txt >> greet.txt
```

> To redirect output of one command to input of another, we use piping `|`.
```shell
ls / | grep root
```
* We cannot do this using `>` because, it writes to a file descriptor and `grep` takes in a file descriptor to read from. To make it work, we may use process substitution.

> Commands which are executed in pipes, are actually executed in subshells, with each command in its own subshell. To doing any variable assignments in the environment will not persist, rather be lost.
```shell
ls / | grep root | sed -i 's/hi/hello/g'
```

> To overcome this limitation, we can use [Process Substitution](#process-substitution).
## Process Substitution
> What process substitution does is, it executes a command in subshell, and returns the stdout of that subshell as a file descriptor. We can make the file descriptor either readable or writable.

```shell
# read something from substituion
read < <(echo "HELLo")       # provides a readable fd
cat <(echo "hey" "whatsup")

# write something to it
cat file.txt > >(cat)
```
