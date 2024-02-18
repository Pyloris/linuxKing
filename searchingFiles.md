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