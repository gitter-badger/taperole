# Infrastructure Management

[Trello](https://trello.com/b/4sOCutfn/smashingboxer)

## Why?

Our old Capistrano system is too much of a ballache to add new tasks to.  Ansible is being used here for infrastructure mgmt since Capistrano isn't even the right tool for the job.

## Configuring your app

1. Add `smashing_boxer` to your gemfile
2. `bundle install`
3. Create inventory file (see hosts.example)
4. `smashing_boxer ansible everything -i hosts` from your project root

## The `smashing_boxer` tool

This tool provides a basic wrapper around the ansible scripts, and also contains a `qemu` module which is useful for testing ansible scripts locally.

###Setup
####Installing roles from ansible galaxy
```
ansible-galaxy install -r Rolefile
```

###Testing
####With vagrant

1. `smashing_boxer vagrant create`
2. `smashing_boxer vagrant start`
3. `smashing_boxer ansible everything -i vagrant`

####With QEMU

1. `smashing_boxer qemu create --name fe_test`
2. `smashing_boxer qemu start --name fe_test -p2255`
3. `ssh-add ./id_rsa_sb_basebox`
4. `echo 'localhost:2255' >test_hosts`
5. `smashing_boxer ansible everything -i test_hosts`
 
Run `smashing_boxer -h` for a quick rundown of the tool's modules and options.

## Playbooks

### deploy

This does the basic deployment for both FE/BE portions of the application.  Handles code checkout, building, migrating, seeding and server reloading.

### omnibox

This is the setup right now.  This just puts everything on all the hosts, turning them all into single-machine deployments

## Roles

### General

General encapsulates basic stuff every box should have like unattended upgrades and swap space.

### Deployer User

This user needs to have some SSH keys so all the devs on the project can ssh to machines and do things.  Deployer user keys will live under roles/deployer\_user/files/keys.

### Web

Basic nginx installation, configured to work with unicorns running on the same box

This looks for the `fe_app` variable, and either configures nginx to serve `./public` from a rails app or `./dist` from an angular app at the root of the site.

### app\_server

Runs unicorns executing app code

### legacy

Used to migrate from old capistrano-managed boxes to the new infrastructure
