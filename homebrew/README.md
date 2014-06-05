## Install a specific Version

    brew update
    brew versions FORMULA
    cd `brew --prefix`
    git checkout HASH Library/Formula/FORMULA.rb  # use output of "brew versions"
    brew install FORMULA
    brew switch FORMULA VERSION
    git checkout -- Library/Formula/FORMULA.rb    # reset formula

for example to install a different (in this case older) version of docker

    $ brew update
    $ brew versions docker
    Warning: brew-versions is unsupported and may be removed soon.
    Please use the homebrew-versions tap instead:
      https://github.com/Homebrew/homebrew-versions
      0.11.1   git checkout eefa235 /usr/local/Library/Formula/docker.rb
      0.11.0   git checkout f4560d2 /usr/local/Library/Formula/docker.rb
      0.10.0   git checkout c26c37d /usr/local/Library/Formula/docker.rb
      0.9.1    git checkout 6bd6532 /usr/local/Library/Formula/docker.rb
      0.9.0    git checkout a90b40e /usr/local/Library/Formula/docker.rb
      0.8.1    git checkout 4ee0389 /usr/local/Library/Formula/docker.rb
      0.8.0    git checkout 9880ace /usr/local/Library/Formula/docker.rb

    $ cd `brew --prefix`
    $ git checkout c26c37d /usr/local/Library/Formula/docker.rb
    $ brew install docker
    $ brew switch docker 0.10.0

    $ git checkout -- Library/Formula/docker.rb    # reset formula
