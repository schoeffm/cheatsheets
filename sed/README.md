# sed

## Print lines of a file

The basic command for printing goes like this 

    sed -n 'ADDRESS'p filename
    
where `ADDRESS` can be a number, a pattern or a range. `-n` will prevent the normal behaviour of `sed` which is printing all inputs to stdout (which is the opposite of what we want when using `p`):

    sed -n 1p /etc/passwd                   # prints the first line - like 'head -1 /etc/passwd'
    sed -n '3,5'p /etc/passwd               # prints line 3-5 of /etc/passwd
    sed -n '3,$p' /etc/passwd               # prints from 3 till the end
    sed -n /schoeffm/p /etc/passwd          # prints the line which contains 'schoeffm'
    sed -n '3,/schoeffm/'p /etc/passwd      # prints from 3 till the one which contains 'schoeffm' 
    sed -n '/icon/,/astri/'p /etc/passwd    # prints from matching line to matching line

    # next two doesn't work on mac but on linux (docker)
    sed -n '/icon/,+2'p /etc/passwd         # prints matching line and the next 2 ones
    sed -n '1~2'p /etc/passwd               # prints just every second line starting at line 1

## Delete lines of an input

Basically the same format as above - but this time we'd like to omit `-n` since we explicitly want non-matching lines to be printed to stdout.

    # when deleting we'd like to print everything to stdout (hence no -n) 
    # matching lines will be 'deleted' -> thus they won't be printed to sdtout 
    sed '1,5'd /etc/passwd    # skips the first 5 lines
    sed '3,5'd /etc/passwd    # prints all but line 3-5 will be skipped
    sed '3,$'d /etc/passwd    # only prints line 1-2 (rest is skipped)
    sed '3,+3'd /etc/passwd   # will skip line 3-6
    sed '/^$/'d /some/input   # deletes all empty lines from the file 

## Replacing lines in a file

The basic command for replacing stuff goes like this:

    sed 'ADDRESSs/REGEXP/REPLACEMENT/FLGAS' filename

where addressing lines is the same as above but this time the operation `s` is a bit more involved (and needs additional configuration)

Flags:

- `g` Replace all the instances of REGEXP with REPLACEMENT (global)
- `n` Just a number - replace the nth instance of REGEXP with REPLACEMENT
- `p` just print the matched/changed lines to stdout (only sensible in combination with `-n`)
- `i` matches REGEXP in a case-insensitive way
- `w file` will write the result into a new file
- notice: sometimes it's easier to use a different REGEXP-delimiter (also allowed are `#`, `@` or `%`)

Examples:

    # without explicit addressing it will be applied to the whole file
    sed 's/dev/div/' /some/input.txt             # will replace the first "dev" in each line 
                                                 # with "div" in the whole file
    sed 's/dev/div/g' /some/input.txt            # more likely what you want - replace all "dev"s 
                                                 # with "div", not just the first one
    sed 's/dev/div/2' /some/input.txt            # will replace the second "dev" in each line 
                                                 # with "div" in the whole file 
    sed -n 's/dev/div/gp' /some/input.txt        # only changed lines will be printed

    # explicit addressing will restrict the scope of the substitution
    sed '/root/s/rw/rwx/g' /some/input.txt       # replace "rw" with "rwx" in all lines that contain "root"

    sed 's/...$//' /some/input.txt               # just deletes the last 3 chars from each line
    sed '5,20s/^/# /' /some/input.txt            # will comment out line 5-20

    # applying more than one substitution
    sed -e 's/#.*//;/^$/d' /some/input.txt       # will remove comments (first substitution-pattern) and 
                                                 # empty lines (second one)
    sed 's/.$//' /some/dos-file.txt              # replaces DOS line-endings (CR/LF) to unix format

    # the inplace-substition
    sed -i 's/.$//' /some/dos-file.txt           # replaces DOS line-endings (CR/LF) directly in the input-file
                                                 # so this doesn't print to sdtout but will change the given
                                                 # file in-place!!
    sed -i.bak 's/.$//' /some/dos-file.txt       # does the same in-place replacement but will create a 
                                                 # 'dos-file.txt.bak' file which contains the original input -
                                                 # just in case

## Execute multiple sed commands

    sed -e '4d' -e '2d' /some/input                 # will delete the 2nd and the 4th line in that file
    sed -n -e '/root/p' -e '/nist/p' /some/input    # will print lines matching either of the two pattern
