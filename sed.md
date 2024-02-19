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