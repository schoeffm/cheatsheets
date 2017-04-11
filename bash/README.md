## Quoting your variables with ${}

        mv $MYVAR $MYVAR__bak       # wrong
        mv $MYVAR ${MYVAR}__bak     # right
        
## for loops

        for i in {1..10}            # {1..10}== `seq 1 10`
        do
            echo "$i"
        done
        
        # you can write this also in one line
        for i in {1..10}; do echo "$i"; done
        
## Interpolate Command-output

        OUTPUT=`command`        # can be written as
        OUTPUT=$(command)
        
## if statements
- there have to be spaces after/before the sqzare brakets (otherwise it wont work)
- `[[` is actual bash sytax - `[` is a program in `/usr/bin` (will also work, but with some caveats). always use `[[`

        [ 3 < 4 ] && echo "true"        # no such file
        [[ 3 < 4 ]] && echo true"       # true

        # complete example
        if [[ "bla" = "foo" ]]; then
            echo "this is true"
        else
            echo "this is false"
        fi

- you can also use the return code of another program (and get ride of `[[` completly)
    
        if grep peanuts food-list.txt
        then
            echo "found"
        fi

## return codes, `&&` and `||`
Every Unix program has a "return code" which is an integer from `0` to `127` (`0` means success - everthing else is an failure). 

- `&&`: Combine several commands with `&&` => only if the first command returns `0` the second one will be executed. The cmd-chain will stop as soon as the first command returns != `0`
- `||`: When combined with `||` the second command will be executed _only_ when the first fails (like a default to be used if the first one isn't possible).

        create_user; make_home_dir              # will execute both commands, no matter what the rc is
        create_user && make_home_dir            # will execute the 2nd cmd only if the first was successful
        create_user || make_home_dir            # will execute the 2nd only if the first one fails

## Scripting flags

- `set -e`: By default, when a program fails, bash will just keep running. 

        python do_sth.py            # assume, this cmd fails miserably 
        echo "done"                 # ... you'll still get this msg printed :(

    `set -e` will make the script stop and hopefully prevent any (further) damage.

        set -e

        python do_sth.py            # again, this one fails
        echo "done"                 # you won't see this message :)

- `set -u`: In most programming languages I use, I get an error if I try to use an unset variable. Not in Bash! By default, unset variables are just evaluated as if they were the empty string. The result of this is:

        rm -rf "$DIRECTORY/*"

    will actually run `rm -rf /*` if `$DIRECTORY` is unset. If you use `set -u` bash will stop and fail if you try to use an unset variable.
- `set -x`: will make bash print out every command it runs before running it. This is really useful for debugging.

Taken all these together your script might start with `set -eu` or even `set -eux` to be save and more predictable.
