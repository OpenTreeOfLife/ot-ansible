# Ansible for Open Tree configuration management

## Installation

    sudo apt install ansible

or see http://docs.ansible.com/ansible/latest/installation_guide


## new host prep

MTH has an `ot-private` repo that interacts with a fresh AWS instance to create an
"admin" account with sudo privileges and the open tree keys (the SSH private keys are 
not in that repo, developers need to be given those). Other than the user account
creation, the only initial steps applied to the OS (currently ubuntu 16.04) boil down
to:

    apt update
    apt upgrade
    apt install python-dev

Then you have to add the hostname to the hosts file in this repo.

# Thanks

Some links that were helpful:
  * Previous Open Tree deployment system: 
    https://github.com/OpenTreeOfLife/germinator/tree/master/deploy
  * Previous Open Tree component with ansible installation:
    https://github.com/OpenTreeOfLife/otindex_ansible
  * https://andidog.de/blog/2017-04-24-ansible-best-practices