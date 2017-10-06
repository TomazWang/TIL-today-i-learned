# Ack - the other (maybe better) grep

## tl;dr

__ack__
A search tool like grep, optimized for programmers.

- Find files containing "foo":
```bash
ack foo
```

- Find files in a specific language:
```bash
ack --ruby each_with_object
```

- Count the total number of matches for the term "foo":
```bash
ack -ch foo
```

- Show the file names containing "foo" and number of matches in each file:
```bash
ack -cl foo
```

- Find all file names like...
```bash
ack -f | ack 'pattern'
# also
ack -g 'pattern'
```

- Search in files names like...
```bash
ack -g 'file_name_pattern' | ack -x 'pattern'
```
`-x `  accepts a list of filenames via standard input; this list is the list of filenames that will be used for the search.


### For More 
Visits the [document page](https://beyondgrep.com/documentation/).