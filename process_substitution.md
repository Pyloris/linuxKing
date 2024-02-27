## Process Substitution
> What process substitution does is, it executes a command in subshell, and returns the stdout of that subshell as a file descriptor. We can make the file descriptor either readable or writable.

```shell
# read something from substituion
read < <(echo "HELLo")       # provides a readable fd
cat <(echo "hey" "whatsup")

# write something to it
cat file.txt > >(cat)
```