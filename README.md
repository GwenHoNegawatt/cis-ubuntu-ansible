# Install CIS for Ubuntu 14.04 - NU-Logger

## Before installation
Add 'universe' at the end of apt sources list:
```bash
vim /etc/apt/sources.list
```

## Install Python Pip

```bash
apt update
apt install python-pip
```

## installation

Install dependencies on your host (on Ubuntu 14.04):

```bash
apt-get install python-pip git python-dev
pip install ansible markupsafe
```

Create a placeholder to describe your machine:

```bash
mkdir -p ansible/roles-ubuntu/roles
cd ansible/roles-ubuntu
git clone https://github.com/fishong/cis-ubuntu-ansible roles/cis
```

Create a playbook in the _roles-ubuntu_ folder:

```bash
$ cat >>  playbook.yml << 'EOF'
---
- hosts: all
  roles:
    - cis
EOF
```

Before run, remove 'universe' at /etc/apt/sources.list.

## run
```bash
$ ansible-playbook -b -i 'localhost,' playbook.yml --connection=local
```

## Change ssh setting
```bash
$ vim /etc/ssh/sshd_config

 Permit RootLogin Yes 
 

ufw status
ufw disable
reboot
```

After ssh login again, remove node modules in logger file
```bash
cd logger-app        //or cd bacnet-logger
cd server
rm -rf node_modules
```

install nvm
```bash
git clone https://github.com/nvm-sh/nvm.git .nvm
cd .nvm
git checkout <tag>
. nvm.sh
nvm          //for testing
```

install node.js for v8
```bash
# install node 8.x
nvm install v8.15.1 
```

Run yarn
```bash
yarn
yarn start
pm2 update
pm2 save
```

## useful command
```bash
$ufw enable
$ufw allow ssh
$ufw allow http
$ufw allow 27017
$ufw allow 3000
```

## Check RTC Setting
```bash
#check time status
systemctl status ntp
```

## Edit RTC Setting
```bash
timedatectl
sh /home/etc/sh

#
crontab -e
# comment sh /home/etc/sh

#  diable ntp
systemctl disable ntp 
```
=======================================================================================================================================

## Prerequisites

The role is focused on hardening an Ubuntu 14.04 system. However it has been successfully tested on other Debian based systems (Debian 8, Raspbian). The minimum requirements of the targeted system are `ssh`, `aptitude` and `python2`; `ansible>=1.9` is required on your local system.

## Usage

### One liner installation & execution

The following will automatically install Ansible, download and run the playbook on your local system.
```
$ \curl -sSL http://git.io/vZw8S > /tmp/cis.sh && bash /tmp/cis.sh
```
To apply the playbook on a remote system:
```
$ IP=[remote host's IP] USER=[remote user] \curl -sSL http://git.io/vZw8S | bash
```

### Manual installation

Install dependencies on your host (on Ubuntu 14.04):

```bash
$ sudo apt-get install python-pip git python-dev
$ sudo pip install ansible markupsafe
```

Create a placeholder to describe your machine:

```bash
$ mkdir -p ansible/roles-ubuntu/roles
$ cd ansible/roles-ubuntu
$ git clone https://github.com/awailly/cis-ubuntu-ansible.git roles/cis
```

Create a playbook in the _roles-ubuntu_ folder:

```bash
$ cat >>  playbook.yml << 'EOF'
---
- hosts: all
  roles:
    - cis
EOF
```

### Tuning the environment

You have to tune the environment to match your security requirements. The default is very restrictive and will perform strong modifications on the system. All requirements are enabled and may not work. For example the rsyslog server address have to be defined to respect the CIS rule.

*Read `default/main.yml` file and set your variables in `vars/main.yml`*

For the CI tests we only create specific files for the environment (see `tests/travis_defaults.yml`) in the `vars/` directory.

### Running the role

Replace the target information (USER, IPADDRESS) and run the playbook with a version of ansible higher than 1.8:

    $ ansible-playbook -b -u USER -i 'IPADDRESS,' playbook.yml

Note that this command will perform modifications on the target. Add the `-C` option to only check for modifications and audit the system. However, some tasks cannot be audited as they need to register a variable on the target and thus modify the system.

If the user you are using is not privileged you have to use the `-b` (`become`) option to perform privilege escalation. The password required to become superuser can be specified with the `--ask-become-pass` option.

### Optimizations

Ansible come with some great options that can improve your operations:

- Add the `-e "pipelining=True"` option to the command line to speed up the hardening process.
- Specify the private key to use with the `--private-key=~/.ssh/id_rsa` option.
- The conventional method to specify hosts in ansible is to create an `inventory` file and feed it with a group of hosts to process.

## Documentation

The details of each tasks operated on the target system is available in the [online documentation](http://cis-ubuntu-ansible.readthedocs.org/en/latest/). It is build on every commit based on the `docs/` repository content.

## Contributing

We accept modifications through pull requests. Please note that CI tests and code coverage are being performed automatically. All tests have to pass before accepting the contribution.

Issues are welcome too, and we expect reproductible steps to have efficient discussions.

## License

This project is under [GPL license](LICENSE).

## Contact

We have a dedicated IRC channel for the project on chat.freenode.net. Join us on ##cis-ansible or with the [direct link](https://kiwiirc.com/client/irc.freenode.net/?nick=GuestAnsib|?##cis-ansible).

