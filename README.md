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

MTH has an `ot-private` repo on bitbucket that interacts with a fresh AWS instance to create an
"deploy" account with sudo privileges and the open tree keys.
Other the user account creation, the only initial steps applied to the 
OS boil down to:

    apt update
    apt upgrade -y
    apt install python-dev -y

Then you have to add the hostname to the hosts file in this repo.

## Usage
Not all of the playbooks have been tested recently.
Below are the commands that have been used recently.

### Installing custom synthesis on ot38

    ansible-playbook -v --limit=ot38 playbk-build-synth-pipeline.yml
    ansible-playbook -v --limit=ot38 playbk-build-otc-ws.yml


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

# Local testing with Vagrant

You can test any of the playbooks locally using Vagrant and VirtualBox (or other provider like VMWare, Hyper-V). Once you have vagrant and virtualbox installed, running `vagrant up` will start the virtual host and run the playbook `opentree.yml`. To run a different playbook, edit the `ansible.playbook` option in the Vagrantfile.

See the [Ansible documentation on using Vagrant](https://docs.ansible.com/ansible/2.3/guide_vagrant.html) for more info.

# Troubleshooting

## Apache logs

```
$ ssh ot58 'sudo tail /var/log/apache2/error.log'
$ ssh ot58 'sudo tail /var/log/apache2/access.log'
$ ssh ot58 'sudo tail /var/log/apache2/ssl_error.log'
```
or, alternately

`$ ssh ot58 'sudo tail -f /var/log/apache2/*.log'`

## Web2py tickets

If you see the dreaded web2py Internal error, or a 500 server error, look in `/home/deploy/ws_dir/repos/opentree/webapp/errors` for logs.
