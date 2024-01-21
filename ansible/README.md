# Ansible Playbook for setting up Hydra Home Server

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

ansible-inventory -i inventory.ini --list

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
```

Verify

```java
ansible-lint --version
```

Output

```java
ansible-lint 6.22.1 using ansible-core:2.16.2 ansible-compat:4.1.10 ruamel-yaml:0.18.5 ruamel-yaml-clib:0.2.8
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

This is needed to run `local_gen`

```java
ansible-galaxy collection install community.general
```

or add these rows to requirements.yml

```java
roles:
  - name: markosamuli.pyenv
  - name: community.general
```

Install them via:

```java
ansible-galaxy collection install -r requirements.yml
```

## May need to install this for prompting for the root password on the remote hosts

```java
sudo apt-get install sshpass
```

## Imp things to keep in mind

1. `ansible_ssh_user` for the first run should `root` since there is no user in the instance.
   You must ensure that `bootstrap-nodes` role is first run before continuing. It disables the `root` SSH login to the instance and only
   the `username` supplied in `inventory` has access to SSH.

**Bootstrap**: `

```java
ansible-playbook -i inventory playbook.yml --tag=bootstrap  --ask-pass
```

or with --ask-pass

**Bootstrap**: `ansible-playbook -i inventory playbook.yml --tag=bootstrap -u root --ask-pass`

Succeeded, then ran it again, complains that I am not root and so it cant write to /etc

```java
TASK [bootstrap-node : add ssh banner info] *******************************************************************************************************
ok: [srv3]
ok: [srv2]

TASK [bootstrap-node : update ssh banner] *********************************************************************************************************
fatal: [srv3]: FAILED! => {"changed": false, "checksum": "f9ecc78f4f3efb2e7e65668a0362009fbb42155d", "msg": "Destination /etc not writable"}
fatal: [srv2]: FAILED! => {"changed": false, "checksum": "5399406a965030a7fe153df685acb9ac4029e14d", "msg": "Destination /etc not writable"}

PLAY RECAP ****************************************************************************************************************************************
srv2                       : ok=16   changed=2    unreachable=0    failed=1    skipped=2    rescued=0    ignored=0
srv3                       : ok=16   changed=2    unreachable=0    failed=1    skipped=2    rescued=0    ignored=0
```

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
