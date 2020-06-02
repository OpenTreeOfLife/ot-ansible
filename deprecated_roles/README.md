# README

Directories for roles deprecated during the refactor, but kept around
for reference. Not used in any playbooks.

In most cases, these are deprecated because of consolidation of roles.
Instead of separate fetch, configure, build roles, we instead have a
single role for each piece of software. In addition, rather than generic
setup roles that run with a sudo-enabled user and install all of the things,
we move installation steps close to where we need them and only install
what we need for the software on each server. 
