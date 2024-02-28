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