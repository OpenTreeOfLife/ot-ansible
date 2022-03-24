# Dev notes


## Playbook inventory

List of playbooks and their function, prior to Spring 2020 re-factoring. All playbook files have naming convention `playbk-*.yml`

build-otc-ws
- builds the otcetera webservices
- roles: fetch-and-install-restbed, fetch-otcetera-ws, configure-otcetera-ws, build-otcetera-ws

build-synth-pipeline
- gets everything ready to build synth tree (otcetera CLI, propinquity, inputs) but does not actually build synth tree
- roles: fetch-otcetera-cli, configure-otcetera-cli, build-otcetera-cli, fetch-peyotl-synth, fetch-monophyly-tests, fetch-propinquity, fetch-synth-inputs

install-javadev
- install and setup dir structure for neo4j / taxomachine

install-web2py
 - downloads and installs web2py
 - role: unpack-web2py

install-webapp
  - installs the webapp (opentree browser & curator)
  - roles: refresh-opentree, restart-apache

put-server-in-maintenance-mode
 - single put-apache-into-maintenance-mode role to put server in maintenance mode

rebuild-otc-cli
 - builds the otcetera command line tools
 - roles: fetch-otcetera-cli, build-otcetera-cli

rebuild-synth-tree
 - builds a new version of the synthetic tree
 - role: build-synth-tree

rebuild-taxomachine-plugins
 - builds taxomachine plugins (including ot-base and jade)
 - role: build-taxomachine

refresh-synth-runtime
 - sets up everything to run synth (peyotl, propinquity, phylesystem, collections)
 - roles: fetch-peyotl-synth, fetch-monophyly-tests, fetch-propinquity, fetch-synth-inputs

serve-next-tree
 - copies synth tree and OTT to served location, launches ws_wrapper
 - roles: add-ots-ws-links-to-tree-and-taxonomy, launch-otc-ws-app

setup
 - installs os packages, creates opentree user, sets up python envs, creates directory structure
 - roles: users, init, cplusplusdev

take-server-out-of-maintenance-mode
 - takes server out of maintenance mode by writing the non-maintenance apache conf and restarting
 - identical to configure-apache playbook
 - role: configure-apache role

## Components

### Data

- synthetic tree :
  - built using otcetera + propinquity
  - source = flat files on files.opentreeoflife.org
  - served with otcetera

- studies (curated input phylogenies)
  - source = phylesystem repo on github
  - indexed / served by otindex (find methods) and phylesystem-api (get methods)

- amendments
  - source = github repo
  - served by phylesystem-api

- taxonomy (OTT)
  - build using reference-taxonomy
  - source = flat files on files.opentreeoflife.org
  - served by otcetera (taxonomy, TNRS), smasher (taxonomy browser)

- collections (lists of trees used as inputs for synthesis)
  - source = github repo
  - served by phylesystem-api


### APIs and web applications

- otcetera : (C++) serves synthetic tree, taxonomy, taxonomic name resolution
- otindex : (python Pyramid) serves indexed studies; has own ansible deployment
- phylesystem-api : (python web2py) serves studies and collections
- opentree : (python web2py) front-end tree browser and study curator
- ws_wrapper : (python Pyramid) routes api calls to correct place


## Hosts

**Named hosts**

Each of these accessible at `*.opentreeoflife.org`:

- tree / devtree = opentree
- api / devapi = ws_wrapper, otcetera; routes calls to other services
- phylesystemapi / devphylesystemapi = phylesystemapi
- otindex / devotindex = otindex
- nexttree = opentree, ws_wrapper, otcetera

**Host groups**

Production = tree + api + otindex + phylesystemapi
Development = devtree + devapi + devotindex + devphylesystemapi
Staging = nexttree (no curation; api and webapp on same server)

Development is where we test new software, and nexttree is production software but with new synthesis data (tree and underlying taxonomy)


## Actions

* build synthetic tree
* deploy synthetic tree
* deploy taxonomy


## Setting up a new AWS machine
Everything other than “tinytree:
Debian Buster
m4. Large
ADD 256 GB storage in AWS
Use OpenTree security group
EJM Used her personal keypair
Login.
Add Mark’s public key to authorized keys

To add swap (following BenRI) run:

sudo swapon --show
sudo fallocate -l 16G /mnt/16GB-manually-added.swap
sudo chmod 600 /mnt/16GB-manually-added.swap
sudo mkswap /mnt/16GB-manually-added.swap
sudo swapon /mnt/16GB-manually-added.swap
sudo swapon --show
sudo cp /etc/fstab /etc/fstab.bak
echo '/mnt/16GB-manually-added.swap none swap sw 0 0' | sudo tee -a /etc/fstab
Authkeys
Copy from admin@ot50.opentreeoflife.org:~/.ssh/authorized_keys  to get the latest and greatest public keys



Making deploy user:

login as admin

apt-get update
sudo adduser deploy sudo
sudo passwd -d deploy
sudo visudo
    add
        deploy     ALL=(ALL) NOPASSWD:ALL

sudo su deploy
cd
mkdir .ssh
chmod 700 ~/.ssh
sudo cp /home/admin/authorized_keys ./.ssh
chmod 644 .ssh/authorized_keys



## Germinator notes

In install-otcetera script :

```
APPS=$HOME/Applications
mkdir -p $APPS

OPENTREE=$APPS/OpenTree
mkdir -p $OPENTREE

TAX_FILE=${TAX_URL##*/}
TAX_DIR=${TAX_FILE%.*}
OTT=$OPENTREE/$TAX_DIR
mkdir -p $OTT

SYNTHPARENT=$OPENTREE/synth-par
mkdir -p $SYNTHPARENT

mkdir -p $APPS/restbed
cd $APPS/restbed
git clone https://github.com/corvusoft/restbed.git

mkdir -p $APPS/otcetera
cd $APPS/otcetera
git clone --recursive https://github.com/OpenTreeOfLife/otcetera
```

peyotl cloned in in `$HOME/repo/peyotl/` and ws_wrapper in `$HOME/repo/ws_wrapper`

In install-web2py-apps script :

```
WEBAPP=opentree
APPROOT=repo/$WEBAPP
```
