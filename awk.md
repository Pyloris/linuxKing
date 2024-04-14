## AWK Command
> Awk is a general-purpose scripting language designed for advanced text processing.

### Working of AWK
> For AWK, a text file or stream contains `records` and `fields`. AWK processes each `record` until finished.
>
> Records are seperated by `RS` (default `\n`) and fields are seperated by `FS` (default `space`).
>
> Fields are referenced using `$` and a number starting from 1. `$0` references entire record.

#### Built-in Variables
Built-in Variables
Awk has a number of built-in variables that contain helpful information and allow you to control how the program is processed. Below are some of the most common built-in Variables:

`NF` - The number of fields in the record.

`NR` - The number of the current record.

`FILENAME` - The name of the input file that is currently processed.

`FS` - Field separator.

`RS` - Record separator.

`OFS` - Output field separator.

`ORS` - Output record separator.

#### Common Awk Statements
Awk supports different types of statements, including expressions, conditionals, input, output statements, and more. The most common awk statements are:

`exit` - Stops the execution of the whole program and exits.

`next` - Stops processing the current record and moves to the next record in the input data.

`print` - Print records, fields, variables, and custom text.

`printf` - Gives you more control over the output format, similar to C and bash printf .

### Working with AWK
```shell
# inline awk program
awk 'program' file.txt                           # 'program' is awk code
awk -f program.txt file.txt                      # read awk code from file

# execute actions on every record
awk '{ print $1, $2 }' pops.txt
# find a record and execute an action on it
awk '/shoaib/ {print $2}' pops.txt
# action before beginning the program
awk 'BEGIN { print "STARTED" }; END { print "ENDING" }' filex.txt
# change builtin variables
awk 'BEGIN { FS="." }; { print $2 }' pops.txt
# using variables to do stuff
awk '{ sum += $3 } END { printf "%d\n", sum }' teams.txt
```