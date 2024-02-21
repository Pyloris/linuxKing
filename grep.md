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