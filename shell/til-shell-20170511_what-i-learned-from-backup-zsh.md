# What I Learned from Writing Backup Script For DotFiles

## Preface:

I'm tried to manage my system enviroment by version controling my dotfiles with
git and github. However, I have almost zero knowledge about shell script. Thus,
I've been though a quite pleasing learning journey.


**NOTE**: Things I've learned in this project is very basic.

> See also: [Learn Bash in Y minutes](https://learnxinyminutes.com/docs/bash/)

## Basic Shell Script

```sh
#!/bin/bash
# Add '!/bin/bash' at the first line.

# Comments begin with hashtag

# Print things on screen.
echo "Text is surrond with double quote"
echo 'Single quote can do the job as well'

# Declare a variable by naming it.
# NOTE: There cannot be any space between variable and '=' sign.
Var=""
Bool=true
Dec=1

# However, using variable as a 'value' requires a '$' before it.
echo "get Var: $Var"
echo $Var


# Put the result of some expresion in a variable like this.
timestampStr=$(date "+%Y%m%d_%H:%M:%S")
# This will put string "YYYYmmdd_HH:MM:SS" in timestampStr


# Conditions
if bool ; then
    # do something
fi

# bool can be a single boolean variabel. 
# Or, use [] to return a boolean.
# Make sure to put spaces around [ and ] sign.

if [ $Foo == $Bar ]; then
    # do something
fi

# || and && can be use as well.


# Check type and permission of an item. Using POSIX.

[ -d "path/to/some/dir" ] # check if path/to/some/dir is exist and a directory.
[ -f "path/to/some/file.file" ] # check if path/to/some/file.file is exist and 

# See: http://pubs.opengroup.org/onlinepubs/9699919799/utilities/test.html


# Switch-Case 
case $1 in
    
    case1   )       # do things
                    ;;

    case2   )       # do things 2
                    ;;

    *       )       # default
                    ;;
esac


# Iterate
for d in "${dirs_needed[@]}"; do

    if ! is_dir "$d" ; then
        mkdir $d
        echo '[OK]... create tmp dir: '${d}
    fi

done


# Declare a function
function fn_name(){}
this_fn_would_work_as_well(){}


```

Inside the script, use any command as in shell.










