# Ansible for Open Tree configuration management

## Installation

    sudo apt install ansible

or see http://docs.ansible.com/ansible/latest/installation_guide


## new host prep

Ansible connects and runs using the `ansibleuser` set in
`group_vars/all.yml`. All playbooks assume this user exists, you can
log into the server with this user using ssh keys, and that the user has
sudo privileges.

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

# Role variables

Variables for roles are set in `role/defaults`, not `role/vars`. This makes is easy to override role defaults using variables for hosts in `group_vars`.  

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

# Local testing with Vagrant

You can test any of the playbooks locally using Vagrant and VirtualBox (or other provider like VMWare, Hyper-V). Once you have vagrant and virtualbox installed, running `vagrant up` will start the virtual host and run the playbook `opentree.yml`. To run a different playbook, edit the `ansible.playbook` option in the Vagrantfile.

See the [Ansible documentation on using Vagrant](https://docs.ansible.com/ansible/2.3/guide_vagrant.html) for more info.
