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

# Deploying the synthetic tree

A very rough overview of deploying the synthetic tree looks like this:

1. To download a new copy of OTT:
    * `ansible-playbook --limit=nexttree playbk-refresh-synth-runtime.yml`
    * This is downloaded to `~/synth/ott/ott<version>/`
2. To build the synth tree:
    * `ansible-playbook --limit=nexttree playbk-rerebuilt-synth-tree.yml`
    * This is built in `~/synth/in_progress_tree_builds/` and is copied to `~/synth/tree_builds/`
3. To start serving the synth tree
    * `ansible-playbook --limit=nexttree playbk-serve-next-tree.yml`
    * This involves copying the tree and taxonomy to `~/ws/otc_ws_data`
    * The webservices are run using an instance of otcetera in `~/ws/local/bin/`

