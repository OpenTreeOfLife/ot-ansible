# Dev notes

## Playbook inventory

All playbook files have naming convention `playbk-*.yml`

build-otc-ws
- builds the otcetera webservices
- roles: fetch-and-install-restbed, fetch-otcetera-ws, configure-otcetera-ws, build-otcetera-ws

build-synth-pipeline
- gets everything ready to build synth tree (otcetera CLI, propinquity, inputs) but does not actually build synth tree
- roles: fetch-otcetera-cli, configure-otcetera-cli, build-otcetera-cli, fetch-peyotl-synth, fetch-monophyly-tests, fetch-propinquity, fetch-synth-inputs

configure-apache
- configures and restarts apache (single configure-apache role only)

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
  - served with otcetera

- studies (curated input phylogenies)
  - canonical source is phylesystem repo on github
  - indexed / served by otindex (find methods) and phylesystem-api (get methods)

- amendments
  - source github repo
  - served by phylesystem-api

- taxonomy (OTT)
  - build using reference-taxonomy
  - served by otcetera (taxonomy, TNRS), smasher (taxonomy browser)

- collections (lists of trees used as inputs for synthesis)
  - source github repo
  - served by phylesystem-api


### APIs and web applications

- otcetera : (C++) serves synthetic tree, taxonomy, taxonomic name resolution
- otindex : (python Pyramid) serves indexed studies; has own ansible deployment
- phylesystem-api : (python web2py) serves studies and collections
- opentree : (python web2py) front-end tree browser and study curator
- ws_wrapper : (python Pyramid) routes api calls to correct place
