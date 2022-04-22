# Ansible for Open Tree configuration management

## Local installation

    sudo apt install ansible

or see http://docs.ansible.com/ansible/latest/installation_guide

If you've installed a minimal version of Ansible (eg, `python3 -m pip install
ansible-core`), you should add our required Ansible modules thusly:

    ansible-galaxy install -r requirements.yml


## new host prep

Ansible connects and runs using the `ansibleuser` set in
`group_vars/all.yml`. All playbooks assume this user exists, you can
log into the server with this user using ssh keys, and that the user has
sudo privileges.

MTH has an `ot-private` repo that interacts with a fresh AWS instance to create an
"admin" account with sudo privileges and the open tree keys.
Other the user account creation, the only initial steps applied to the 
OS boil down to:

    apt update
    apt upgrade -y
    apt install python-dev -y

Then you have to add the hostname to the hosts file in this repo.

## Usage

# Thanks

Some links that were helpful:
  * Previous Open Tree deployment system:
    https://github.com/OpenTreeOfLife/germinator/tree/master/deploy
  * Previous Open Tree component with ansible installation:
    https://github.com/OpenTreeOfLife/otindex_ansible
  * https://andidog.de/blog/2017-04-24-ansible-best-practices

# Setting role variables

Variables for roles are set in `role/defaults`, not `role/vars`. This makes
it easy to override role defaults either in `group_vars` (when the   
setting is specific for a host) or with `include_role` (when a setting is
specific for a playbook / another role). See the
[ansible docs on variable priority](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable) for the gory details on how
variable priority works.

# Deploying the synthetic tree

A very rough overview of deploying the synthetic tree looks like this:

1. To download a new copy of OTT:
    * `ansible-playbook --limit=nexttree playbk-refresh-synth-runtime.yml`
    * This is downloaded to `~/synth/ott/ott<version>/`
2. To build the synth tree:
    * `ansible-playbook --limit=nexttree playbk-rebuild-synth-tree.yml`
    * This is built in `~/synth/in_progress_tree_builds/` and is copied to `~/synth/tree_builds/`
3. To start serving the synth tree
    * `ansible-playbook --limit=nexttree playbk-serve-next-tree.yml`
    * This involves copying the tree and taxonomy to `~/ws/otc_ws_data`
    * The webservices are run using an instance of otcetera in `~/ws/local/bin/`

# Local testing with Vagrant

You can test any of the playbooks locally using Vagrant and VirtualBox (or other provider like VMWare, Hyper-V). Once you have vagrant and virtualbox installed, running `vagrant up` will start the virtual host and run the playbook `opentree.yml`. To run a different playbook, edit the `ansible.playbook` option in the Vagrantfile.

See the [Ansible documentation on using Vagrant](https://docs.ansible.com/ansible/2.3/guide_vagrant.html) for more info.

# Troubleshooting

## Apache logs

$ ssh ot58 'sudo tail /var/log/apache2/error.log'
$ ssh ot58 'sudo tail /var/log/apache2/access.log'
$ ssh ot58 'sudo tail /var/log/apache2/ssl_error.log'

## Web2py tickets

If you see the dreaded web2py Internal error, or a 500 server error, look in `/home/deploy/ws_dir/repos/opentree/webapp/errors` for logs.
