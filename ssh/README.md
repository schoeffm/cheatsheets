# SSH

## ssh-config format

Create a file called `~/.ssh/config` and configure your usual destinations there:

    # by using 'ssh gateway' you are entering the given machin as user 'cloud'
    # you also will forward your identity when jumping from that host further on
    # and you will forward your local port 9906 to 443 at the remote host
    Host gateway
        HostName 160.46.188.153
        User cloud
        IdentityFile ~/.ssh/id_rsa
        ForwardAgent yes
        LocalForward 9906 127.0.0.1:443
        

    # this one will first jump to 160.46.188.153 and subsequently will jump on
    # to 10.135.0.20 (internal IP address which is not even accessible directly)
    Host monitoring
        ProxyCommand ssh cloud@160.46.188.153 -W %h:%p
        HostName 10.135.0.20
        User cloud
        IdentityFile ~/.ssh/id_rsa
        ForwardAgent yes


## `ssh-add` complains that it cannot connect

    eval "$(ssh-agent)"

See here for an explanation - but you'll have to start the `ssh-agent`
