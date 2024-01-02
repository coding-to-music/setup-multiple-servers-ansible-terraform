# Ansible Playbook for setting up Hydra Home Server

## set up ip addresses of the inventory host servers

https://docs.ansible.com/ansible/latest/getting_started/get_started_inventory.html


```java
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

## Running

## Imp things to keep in mind

1) `ansible_ssh_user` for the first run should `root` since there is no user in the instance.
You must ensure that `bootstrap-nodes` role is first run before continuing. It disables the `root` SSH login to the instance and only
the `username` supplied in `inventory` has access to SSH.

**Bootstrap**: `ansible-playbook -i inventory playbook.yml --tag=bootstrap`

or with  --ask-pass

**Bootstrap**: `ansible-playbook -i inventory playbook.yml --tag=bootstrap -u root --ask-pass`

If you fail at this step, you need to debug before proceeding.


2) For Tailscale, it is recommended to generate `Pre Authorisation Keys` and encrypt them in vault:

- To encrypt the string `ansible-vault encrypt_string '<AUTH-KEY>' --name 'tailscale_auth_key`
- To run the playbook: `ansible-playbook -i inventory playbook.yml --tag=tailscale --ask-vault-pass`
