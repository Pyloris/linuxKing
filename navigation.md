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