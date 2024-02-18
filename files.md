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