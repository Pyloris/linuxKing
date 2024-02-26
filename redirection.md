## Redirection

> Redirect output to stdin of another command using `>` and to redirect standard input, we use `<`.
```shell
cat file.txt > file2.txt
cat < file.txt
# redirect stderr
find / 2> /dev/null
# redirect stdout and stderr
find / 2> /dev/null 2>&1
```

> To redirect output of one command to input of another, we use piping `|`.
```shell
ls / | grep root
```
* We cannot do this using `>` because, it writes to a file descriptor and `grep` takes in a file descriptor to read from. To make it work, we may use process substitution.