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