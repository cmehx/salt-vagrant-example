# A Salt example using Vagrant

Student project to learn how to deploy a salt manager and its minions using
local as master and n vagrants vm for its minions

## Requirements

- Vagrant
- Virtual Box
- Python
- Ubuntu local env as root user / second vagrant vm can also be used as master
  (In this example I used my local env on ubuntu as the salt master because of
  ram issue)
  (You should be able to reproduce the master install in the second vm defined
  on Vagrantfile as your salt master instead of your local machine)

## Getting started

Every step of this installation can be found on the official documentation
website

https://docs.saltproject.io/salt/install-guide/en/latest/topics/bootstrap.html

#### Setup Salt Master

- To install salt master on your local machine run:
```
./master.sh
```

- To ensure installation run:
```
salt --version
```

- To ensure the service is active and running:
```
systemctl status salt-master.service
```

- Create a configuration file for network informations
```
touch /etc/salt/master.d/network.conf
```

- Open this file with you editor then add network informations:

```
interface: [master ip]
# master ip adress
```

#### Setup Salt Minions

Launch Vagrant vm instance on
```
vagrant up salt_client_one
```

connect to your vm cli vi ssh
```
vagrant ssh salt_client_one
```

add master to hosts
```
vi /etc/hosts
```
Add this line
```
[master ip] salt.master
```

clone repository
```
git clone git@gitlab.com:mehhlol/salt-vagrant-minions.git
```

Launch bootstrap installer
```
./minion.sh
```

Give master ip to minion
```
vi /etc/salt/minion.d/master.conf
```
Add
```
master: [master ip]
```

Give id name to the minion
```
vi /etc/salt/minion.d/id.conf
```
Add
```
id: vagrant-salt-minion
```

#### Connect Minion to master

Restart services

form master
```
sudo systemctl stop salt-master.service
sudo systemctl start salt-master.service
```
from minion
```
sudo systemctl stop salt-minion.service
sudo systemctl start salt-minion.service
```

Now check for keys to accept from master

List all keys
```
salt-key -L
```

There should be a minion waiting for his key to be accepted in the list

To add a key. key_name is the name of the key to accept
```
salt-key -a key_name
```

## Final Check

To check if the vagrant minion is correctly added to you local master run:
```
salt '*' test.version
```
Result should be of this kind:
```
vagrant-salt-minion:
    3005.1
```

## Usage Example

### Install a pkg

#### CLI

To install docker to your minions
```
salt '*' pkg.install docker.io
```

#### File

To install a package from file create a file at /srv/salt/emacs.sls
add this inside
```
emacs:
  pkg.installed
```
then run:
```
salt '*' state.sls emacs
```

##### Other tools
```
salt '*' pkg.list_pkgs // list install pkgs
salt '*' sys.doc ssh // get the doc
salt '*' service.status docker // same as systemctl
salt -G 'os:UBUNTU' test.version //to test version by os
```

##### Mysql Example

Salt mysql module offial doc:
https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.mysql.html

To get more information mysql configuration:
https://www.digitalocean.com/community/tutorials/saltstack-infrastructure-creating-salt-states-for-mysql-database-servers

- Install
```
salt '*' pkg.install mysql-server
salt '*' pkg.install python3-mysqldb // require for mysql salt module
salt '*' pkg.install debconf-utils
```

- Restart minion and master services

- Check Mysql install

run:
```
salt '*' mysql.db_create 'test'
salt '*' mysql.db_list
```

result:
```
vagrant-salt-minion:
    - information_schema
    - mysql
    - performance_schema
    - sys
    - test
```

### Grains

Default grains configurations are located at:
```
/etc/salt/grain
```

To display grains uuid info for ubuntu os minions run:
```
salt -G 'os:UBUNTU' grains.item uuid 
```

#### Grains cli ex

```
salt -G 'os:UBUNTU' grains.setval env dev
salt -G 'env:dev' grains.item uuid
vagrant-salt-minion:
    ----------
    uuid:
        05a38cc4-4d2c-4f2c-9cbd-97af5563c3a1
```

#### Grains file ex

```
vi /etc/salt/minion.d/grains.conf
```

add:
```
grains:
  lol:dev
```

Restart services and run:
```
salt -G 'lol:dev' grains.item env
vagrant-salt-minion:
    ----------
    env:
        dev
```
