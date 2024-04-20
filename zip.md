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
```