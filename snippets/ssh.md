# SSH setup

## ssh keygen

see all ssh keys
  - `ls -al ~/.ssh`

generate default ssh key
  - `ssh-keygen -t rsa -C "pravatpandey@gmail.com"`

generate custom ssh key to a file 
  - `ssh-keygen -t rsa -C "pravat.neesum@gmail.com" -f "~/.ssh/id_rsa_neesum"`

then add the .pub key generated to corresponding github accounts

~/.ssh/config
  ```
  # Personal account: nepali-prabhat
  Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa
    
  # Work account: neesum
  Host neesum.github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_neesum

  ```

## Adding a remote ssh

see current remotes
  - `git remote -v`

add remote ssh
  - `git remote add origin http://git@neesum.github.com:dev-neesum/customer`

**Note** how we have `neesum.github.com` and not just `github.com`. The host name must match with the one that we have in config

## Git config 
    
see all config
    - `git config --list`

get a specifig config active in the repo
    - `git config user.email` or `git config --get <<user.email>>`

set a config
    - `git config user.email "pandeypravat@gmail.com"`
