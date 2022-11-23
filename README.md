# salt-vagrant-minions

Student project to learn how to deploy a salt manager and its minions using
local as master and n vagrants vm for its minions

# Requirements

- Vagrant
- Virtual Box
- Python
- Ubuntu local env as root user

## Getting started

Official documentation
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
