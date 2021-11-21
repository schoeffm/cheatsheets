# awk

Much of this is based on this [excellent article about AWK][awk-examples] learnings. But without writing it down ... I hardly remember 'em.

## Useful one-liners
```bashc
$ docker rm $(docker ps -a | awk '$2~/httpbin/ {print $1}')
```

## Field separator
Default separator is `space` - which is not very handy when one column contains sentences (like a log-statement):
```bash
$ awk '{ print $6 }' # will split each line using 'space'
```

You can change this using the `-F` option:
```bash
  # splits on tab-signs and prints the 6th column
$ awk -F '\t' '{ print $6 }'
```
## Special variables-references
> `NF` is a variable holding the _Number of Fields_ in a record. So I’m just using its value as an index to refer to the last field.<br/>
> Another variable is `NR`, the number of records so far. NR is handy when I need to print line numbers

```bash
$ awk -F '\t' '{ print NR " " $(NF-2) }' bookreviews.tsv | head  
1 review_headline
2 Great Twist on Zombie Mythos
3 Helpful and Practical
4 Paul
5 Direction and Destiny in the Birth Chart
...
```

## Matching

An awk action, like `{print $4}`, can be preceded by a pattern, like `/regex/`. Without the pattern, the action runs on all lines - with the pattern it only runs on matching lines.

```bash
# will look for 'hello' _anywhere_ in each respective line 
$ awk '/hello/ { print "This line contains hello", $0}'

# will look for 'hello' only within the 4th column 
$ awk '$4~/hello/ { print "This field contains hello", $4}'

# will look for exact matches of 'hello' (in the 4th column) 
$ awk '$4 == "hello" { print "This field is hello:", $4}'
```

## Built-in functions

If you need to print out a table, Awk lets you use `printf` and built-ins like `substr` to format your output.
```bash
$ awk '{ printf "%s \t %-5s", $1, substr($2,1,5) }'
  #     └────────┬────────┘  └┬─┘ └─────┬──────┘
  #              │            │         └─> Only 1-5 of the 2nd column - used as
  #              │            │             2nd input parameter for printf
  #              │            └─> references the 1st column in each row - used as
  #              │                1st input parameter for printf
  #              └─> Defines the output format using the built-in printf function
```

## `BEGIN` and `END` variables
Awk provides two special patterns, `BEGIN` and `END`. You can use them to run actions before and after processing the records.
```bash
  # general arrangement looks like this
$ awk '
BEGIN { print "start up" }
      { print "line match" }
END   { print "tear down" }'


  # concrete example (notice also the usage of variables like 'total' or 'NR') 
$ awk -F '\t' '
BEGIN { print "Calculating Average ..." } 
      { total = total + $8 }
END   { print "Average book review:", total/NR, "stars" }'
Calculating Average ...
Average book review is 4.24361 stars
```
Especially `END` looks very useful for counting, averages etc. 

[awk-examples]:https://earthly.dev/blog/awk-examples/