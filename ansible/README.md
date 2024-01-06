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

ansible-inventory -i inventory --list

# if username on the control node is the SAME on the managed nodes
ansible myhosts -m ping -i inventory

# if username on the control node is different from the managed nodes
ansible myhosts -m ping -i inventory -u root --ask-pass

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

## Imp things to keep in mind

1) `ansible_ssh_user` for the first run should `root` since there is no user in the instance.
You must ensure that `bootstrap-nodes` role is first run before continuing. It disables the `root` SSH login to the instance and only
the `username` supplied in `inventory` has access to SSH.

**Bootstrap**: `ansible-playbook -i inventory playbook.yml --tag=bootstrap`

or with  --ask-pass

**Bootstrap**: `ansible-playbook -i inventory playbook.yml --tag=bootstrap -u root --ask-pass`

If you fail at this step, you need to debug before proceeding.

if Bootstrap works, then in the future omit `-u root --ask-pass`

2) For Tailscale, it is recommended to generate `Pre Authorisation Keys` and encrypt them in vault:

- To encrypt the string `ansible-vault encrypt_string '<AUTH-KEY>' --name 'tailscale_auth_key`
- To run the playbook: `ansible-playbook -i inventory playbook.yml --tag=tailscale --ask-vault-pass`

3) For InfluxDB

- To run the playbook: `ansible-playbook -i inventory playbook.yml --tag=influxdb`

