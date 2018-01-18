# Rename multiple file (or do anything to them) at once  with `sed` and shell pipline.

### What `sed` does?

`sed` is a shell command that can replace input base on regular expression.

tldr (with literal `tldr` command)
```bash
sed

Run replacements based on regular expressions.

- Replace the first occurrence of a string in a file, and print the result:
    sed 's/find/replace/' filename

- Replace all occurrences of an extended regular expression in a file:
    sed -E 's/regex/replace/g' filename

- Replace all occurrences of a string in a file, overwriting the file (i.e. in-place):
    sed -i '' 's/find/replace/g' filename

- Replace only on lines matching the line pattern:
    sed '/line_pattern/s/find/replace/' filename

- Apply multiple find-replace expressions to a file:
    sed -e 's/find/replace/' -e 's/find/replace/' filename

- Replace separator / by any other character not used in the find or replace patterns, e.g., #:
    sed 's#find#replace#' filename
```



### Find target files with `sed`

```bash
ls | sed -E -n 's/regex/replace/p'
```

- `-E` tell  `sed` to use regular expression,
- `-n` prevents `sed` printing any lines it run through.
- The tailing `p` prints out any matching lines (with replace result.)



### Feed `sed` result to shell pipline with `sh`

```bash
# Let's say I want to print out all matching file
ls | sed -E -n 's/(regex)/echo \1/p' | sh
```

- `()` create a regex group.
- `\1` repersents the 1st group in regex.



### Now, the real deal! Rename the files.

```bash
~/Junks took 2s
➜ ls
daily-log-20180115.md  daily-log-20180117.md  daily-log 20180118.md  work-log-template.md
daily-log-20180116.md  daily-log-template.md  work-log-20180118.md

~/Junks
➜ ls | sed -n -E 's/(daily-log-([0-9]+)\.md)/mv \1 work-log-\2\.md/p' | sh

~/Junks
➜ ls
daily-log-template.md  work-log-20180115.md  work-log-20180117.md  work-log-template.md
daily-log 20180118.md  work-log-20180116.md  work-log-20180118.md
```









