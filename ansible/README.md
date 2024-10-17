# Ansible Playbook for setting up Hydra Home Server

## Setup cloud VM's with

### Setup machine

- [x] Set time zone via `locale.yml`
- [ ] Set hostname
- [x] disable ssh remote root login
- [x] Turn on firewall ufw
- [ ] close ports by default
- [x] Run Ansible inventory
- [ ]
- [ ]
- [ ] Install a Ubuntu desktop
- [ ] Install web browser
- [ ] Remote mount a drive
- [ ] Setup drive rsync

### Setup Users

- [x] create a user
- [x] Install ssh keys
- [x] give user sudo
- [x] Set sudo password
- [ ] install .bashrc & .bash_aliases
- [ ]
- [ ]
- [ ]

### Install software

- [x] Docker
- [ ] Saltstack
- [ ] Ansible
- [ ] nvm and node
- [ ] Setup git
- [ ] Telegraf Metrics Collection
- [ ] InfluxDB
- [ ] Grafana
- [ ] Loki
- [ ] PostgreSQL
- [ ]
- [ ]

Enable/start processes with `systemctl`

### Setup machine

#### After creating or re-init the machine, ssh to it

```bash
ssh root@<ip address of server>
```

You may get this message:

```bash
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:<longFingerprint>
Please contact your system administrator.
Add correct host key in /home/yourUser/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/yourUser/.ssh/known_hosts:13
  remove with:
  ssh-keygen -f "/home/yourUser/.ssh/known_hosts" -R "<ip address of server>"
ECDSA host key for <ip address of server> has changed and you have requested strict checking.
Host key verification failed.
```

```bash
ssh-keygen -f "/home/yourUser/.ssh/known_hosts" -R "<ip address of server>"
```

```bash
# Host <ip address of server> found: line 13
/home/yourUser/.ssh/known_hosts updated.
Original contents retained as /home/yourUser/.ssh/known_hosts.old
```

```bash
ssh root@<ip address of server>
```

Output

```bash
The authenticity of host '<ip address of server> (<ip address of server>)' can't be established.
ECDSA key fingerprint is SHA256:<longFingerprint>.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '<ip address of server>' (ECDSA) to the list of known hosts.
root@<ip address of server>'s password:
```

## set up ip addresses of the inventory host servers

https://docs.ansible.com/ansible/latest/getting_started/get_started_inventory.html

```java
# manually set timezone

sudo timedatectl set-timezone <timeszone>

sudo timedatectl set-timezone America/New_York

# check what users are in the system

cat /etc/passwd

# view all groups

cat /etc/group

# check which users have sudo abilities

grep -Po '^sudo.+:\K.*$' /etc/group

# check what groups your username is in

groups <username>

cd ansible (or wherever the inventory file is located)
ansible-inventory -i inventory --list

ansible-inventory -i inventory.ini --list

# if username on the control node is the SAME on the managed nodes
ansible myhosts -m ping -i inventory.ini --ask-pass

# if username on the control node is different from the managed nodes
ansible myhosts -m ping -i inventory.ini -u root --ask-pass

ansible myhosts -m ping -i inventory.ini --ask-pass

```

### install ansible-lint

https://www.redhat.com/sysadmin/ansible-lint-YAML

```java
python3 -m pip install --user ansible-lint

# Upgrade ansible-lint

pip3 install --user --upgrade ansible-lint

# Upgrade pip

pip install --upgrade pip
```

Verify

```java
ansible-lint --version
```

Output

```java
ansible-lint 6.22.2 using ansible-core:2.16.2 ansible-compat:4.1.11 ruamel-yaml:0.18.5 ruamel-yaml-clib:0.2.8
```

## use ansible-lint to find errors

```java
ansible-lint *.yml
```

Output

```java
count tag                           profile    rule associated tags
     4 jinja[spacing]                basic      formatting (warning)
     1 no-free-form                  basic      syntax, risk
     6 name[missing]                 basic      idiom
     2 yaml[new-line-at-end-of-file] basic      formatting, yaml
    16 yaml[truthy]                  basic      formatting, yaml
    10 name[casing]                  moderate   idiom
     1 risky-file-permissions        safety     unpredictability
     1 no-changed-when               shared     command-shell, idempotency
    45 fqcn[action-core]             production formatting
     5 fqcn[action]                  production formatting
     2 args[module]                             syntax, experimental (warning)

Failed: 87 failure(s), 6 warning(s) on 18 files. Last profile that met the validation criteria was 'min'.
```

## use ansible-lint to fix errors

```java
ansible-lint --fix
```

Output

```java
Modified 50 files.
                  Rule Violation Summary
 count tag                    profile rule associated tags
     5 syntax-check[specific] min     core, unskippable

Failed: 5 failure(s), 0 warning(s) on 140 files.
```

### Determine what version of Ubuntu is installed

```java
lsb_release -a
```

Output

```Java
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.3 LTS
Release:        22.04
Codename:       jammy
```

## Running

See what ansible galaxy collections are installed

```java
ansible-galaxy collection list
```

Remove installed ansible galaxy collections

```java
ansible-galaxy collection remove ansible.posix
ansible-galaxy collection remove community.general
```

This is needed to run `local_gen`

```java
ansible-galaxy collection install community.general
```

or add these rows to requirements.yml

```java
roles:
  - name: community.general
  - name: ansible.posix
```

This one was problematic

```java
  - name: markosamuli.pyenv
```

```java
ansible-galaxy collection install markosamuli.pyenv --force
```

```java
Starting galaxy collection install process
Process install dependency map
ERROR! Failed to resolve the requested dependencies map. Could not satisfy the following requirements:
* markosamuli.pyenv:* (direct request)
Hint: Pre-releases hosted on Galaxy or Automation Hub are not installed by default unless a specific version is requested. To enable pre-releases globally, use --pre.
```

Install them via:

```java
ansible-galaxy collection install -r requirements.yml

ansible-galaxy collection install ansible.posix
ansible-galaxy collection install community.general

ansible-galaxy collection install ansible.posix --force
ansible-galaxy collection install community.general --force

```

## May need to install this for prompting for the root password on the remote hosts

```java
sudo apt-get install sshpass
```

## Imp things to keep in mind

1. rlogin root@ip_address for each server you want to modify, ensure you can log in and add the host fingerprint to the list of known hosts

2. `ansible_ssh_user` for the first run should `root` since there is no user in the instance.
   You must ensure that `bootstrap-nodes` role is first run before continuing. It disables the `root` SSH login to the instance and only
   the `username` supplied in `inventory` has access to SSH.

```java
sudo apt-get install openssh-server

# run on each new vm
scp user@home_vm_ip:~/.ssh/id_rsa ~/.ssh/id_rsa

ansible-playbook -i inventory playbook.yml --tag=bootstrap  --ask-pass

ansible-playbook -i inventory.ini playbook.yml --tag=bootstrap  --ask-pass

# if you want to run it a second time, omit the --ask_pass so that it will use he ssh keys.

ansible-playbook -i inventory playbook.yml --tag=bootstrap
```

3. disable using root login, after this step you cannot use root to rlogin, instead use the username you set up in the inventory file

```java
ansible-playbook -i inventory playbook.yml --tag=disable-root  --ask-pass

ansible-playbook -i inventory playbook.yml --tag=disable-root

ansible-playbook -i inventory.ini playbook.yml --tag=disable-root
```

Now going forward in the next ansible steps you must use the username user you set in the inventory vars section (it will have sudo capability)

If you fail at this step, you need to debug before proceeding.

if Bootstrap works, then in the future omit `-u root --ask-pass`

2. For Tailscale, it is recommended to generate `Pre Authorisation Keys` and encrypt them in vault:

- To encrypt the string `ansible-vault encrypt_string '<AUTH-KEY>' --name 'tailscale_auth_key`
- To run the playbook: `ansible-playbook -i inventory playbook.yml --tag=tailscale --ask-vault-pass`

3. For InfluxDB

- To run the playbook: `ansible-playbook -i inventory playbook.yml --tag=influxdb`

Gives error:

```java
ERROR! couldn't resolve module/action 'locale_gen'. This often indicates a misspelling, missing collection, or incorrect module path.

The error appears to be in 'ansible/roles/bootstrap-node/tasks/locale.yml': line 1, column 3, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:


- name: Ensure US locale exists
  ^ here
```

Fixed via installing

```java
ansible-galaxy collection install community.general
```

4. another InfluxDB playbook

https://www.devopstricks.in/deploy-influxdb-using-ansible-on-ubuntu-22-04-lts/

- To run the playbook: `ansible-playbook -i inventory deploy_influxdb.yml`

Gives error:

```java
ERROR! couldn't resolve module/action 'influxdb_database'. This often indicates a misspelling, missing collection, or incorrect module path.

The error appears to be in 'deploy_influxdb.yml': line 33, column 7, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:


    - name: Create InfluxDB admin user and database
      ^ here
```

5. yet another InfluxDB playbook, this one for InfluxDB 1.8

https://galaxy.ansible.com/ui/standalone/roles/andrewrothstein/influxdb/documentation/

Install role

```java
ansible-galaxy role install andrewrothstein.influxdb
```

- To run the playbook: `ansible-playbook -i inventory playbook.yml --tag=andrewrothstein_influxdb`

Output

```java
TASK [andrewrothstein.influxdb : templatize influxdb.conf] ****************************************************************************************
changed: [srv3] => (item={'f': 'influxdb.conf', 'd': '/usr/local/influxdb-1.8.4-1/etc/influxdb'})
changed: [srv2] => (item={'f': 'influxdb.conf', 'd': '/usr/local/influxdb-1.8.4-1/etc/influxdb'})

PLAY RECAP ****************************************************************************************************************************************
srv2                       : ok=11   changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
srv3                       : ok=11   changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

6. yet another InfluxDB playbook, this one for InfluxDB 2.6.1

https://github.com/andrewrothstein/ansible-influxdb2

Install role

```java
ansible-galaxy role install andrewrothstein.influxdb2
```

- To run the playbook: `ansible-playbook -i inventory playbook.yml --tag=andrewrothstein_influxdb2`

Output

```java
TASK [andrewrothstein.influxdb2 : linking /usr/local/bin/influx to /usr/local/influxdb2-2.6.1/influxdb2-client-2.6.1-linux-amd64/influx] ***
changed: [srv2]
changed: [srv3]

PLAY RECAP *********************************************************************************************************************
srv2                       : ok=19   changed=9    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
srv3                       : ok=19   changed=9    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```

ssh to remote server

start influxdb2 via:

```
/usr/local/bin/influxd
```

output

```java
2024-01-10T04:52:11.700066Z     info    Welcome to InfluxDB     {"log_id": "0mdRGNCl000", "version": "v2.6.1", "commit": "9dcf880fe0", "build_date": "2022-12-29T15:53:07Z", "log_level": "info"}
```
