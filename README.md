vagrant-coprhd-scaleio
---------------

# Description
This repo provides a [Vagrant](https://www.vagrantup.com/) environment for CoprHD and ScaleIO.  It's intended for Developers/Users to get up and running quickly with CoprHD and a storage backend that can be discovered, provisioned, and used for block volume operations.  The Vagrantfile describes the four Virtual Machines that will be created under VirtualBox.  One VM is for CoprHD and three VMs are for the ScaleIO Cluster.


# Prerequisites
* [Vagrant](https://www.vagrantup.com/)
  * Nice-to-have: vagrant-cachier for caching packages - really speeds up subsequent installs
  * `vagrant plugin install vagrant-cachier`
  * NOTE: If you're installing behind a proxy, you'll need vagrant-proxyconf:
  * `vagrant plugin install vagrant-proxyconf`
* [Virtualbox](https://www.virtualbox.org/)

# Usage
* Modify the Vagrantfile for the Host-only network you want to create/use for all VMs
* Launch/Provision all VMs
  * `vagrant up`
* Launch only a subset of VM(s)
  * `vagrant up [VM]`
  * [VM] is one or more of: tb, mdm1, mdm2, coprhd
  * e.g. To launch only CoprHD: `vagrant up coprhd`
  * e.g. To launch only the ScaleIO Cluster: `vagrant up tb mdm1 mdm2`
  * NOTE: For ScaleIO Cluster provisioning on the first boot, you MUST have mdm2 as the last in the list as it does all the cluster creation during provisioning so the other nodes must be running.

# Access the VMs
* `vagrant ssh [VM]`
  * [VM] is one of tb, mdm1, mdm2, coprhd
  * NOTE: Default ssh user is 'vagrant'.  CoprHD sets up services using a user named 'storageos'.
  * You can either `su storageos` after SSH'ing into CoprHD or you can use this command:
  * `vagrant ssh coprhd -- -l storageos` with the password 'vagrant' to login to CoprHD

# Modify CoprHD Source and Recompile:
* Login to the CoprHD VM:
  * `vagrant ssh coprhd -- -l storageos`  # P: vagrant
* Change to root:
  * `su -` # P: ChangeMe
* Stop CoprHD services
  * `sudo /etc/storageos/storageos stop`
* Optional: If you want to wipe the database and log files:
	* `/vagrant/clean_slate_coprhd.sh`
* CoprHD source code is in:
	* 	`cd /tmp/coprhd-controller/`
* Checked out branch is: master
* Make changes and then recompile:
  * `sudo make clobber BUILD_TYPE=oss rpm`

# [CoprHD Cli Scripts](https://github.com/curtbruns/coprhd_cli_scripts)
# CLI to configure CoprHD with ScaleIO as backend

## Location
* coprhd_cli_scripts are cloned in /opt/storageos/coprhd_cli_scripts

##Execution Flow
* Make sure coprhd_settings matches your environment (Passwords, URLs, etc)
* Source the coprhd_settings file

## Check CoprHD Setup
* ./coprhd -c

## Setup CoprHD and ScaleIO
* ./coprhd -s
* This will add: ScaleIO as a Storage Provider/Backend, ScaleIO network, Virtual Array and Create a ThickSATA Virtual Pool

## Tear Everything Down
* ./coprhd -d
* This will delete all traces of CoprHD setup (remove Auth provider, VPool, Varray, Project, Tenant)
