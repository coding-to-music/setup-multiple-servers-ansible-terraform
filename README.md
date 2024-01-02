# setup-multiple-servers-ansible-terraform

# 🚀 Setup multiple servers with New Users and SSH Key Auth and various software using Ansible and Terraform 🚀

https://github.com/coding-to-music/setup-multiple-servers-ansible-terraform


From / By https://github.com/mr-karan/homelab

My point-in-time cloned version is https://github.com/coding-to-music/hydra

This repo version is with many enhancements so is not forked, it is cloned and modified

## GitHub

```java
git init
git add .
git remote remove origin
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:coding-to-music/setup-multiple-servers-ansible-terraform.git
git push -u origin main
```

## Environment variables:

```java
# See file terraform/env.sample

DIGITALOCEAN_TOKEN=
CLOUDFLARE_API_TOKEN=
TF_VAR_cloudflare_caddy_api_token=
TF_VAR_shynet_postgresql_password=
TF_VAR_shynet_django_secret_key=
TF_VAR_gitea_secret_key=
TF_VAR_gitea_internal_token=
TF_VAR_gitea_lfs_jwt_secret=
TF_VAR_gitea_oauth2_jwt_secret=
TF_VAR_restic_b2_account_id=
TF_VAR_restic_b2_account_key=
TF_VAR_restic_repository=
TF_VAR_restic_password=

# See file terraform/variables.tf

# See file terraform/providers.tf

# See ansible/

# See file ansible/

edit hosts file


```

## URL's and domain names

see TODO.md

the original project uses these domains:

`nomad.mrkaran.dev` 
`consul.mrkaran.dev`
`shynet.mrkaran.dev`

```java
DOMAIN           = git.mrkaran.dev
SSH_DOMAIN       = koadings.mrkaran.dev
ROOT_URL         = https://git.mrkaran.dev/
```

## Ports 


```java
gitea
HTTP_PORT        = 3000
```


<!-- PROJECT LOGO -->
<br />
<p align="center">
  <h2 align="center">hydra</h2>
  <p align="center">
<i>Setup scripts for my homelab</i>
  </p>
  <img src="docs/calvin.jpg" alt="Calvin and Hobbes">
</p>

---

## Overview

- Single node [Nomad](https://www.nomadproject.io/) server for running workloads.
- [Consul](https://www.consul.io/) agent co-located for service discovery. 
- [Ansible](https://www.ansible.com/) scripts to boostrap the node.
- [Terraform](https://www.terraform.io/) modules for managing the following services:
  - Nomad jobs
  - Cloudflare DNS
  - DigitalOcean Infra
- [Tailscale VPN](https://tailscale.com/) for connectivity to internal services.
- [Caddy](https://caddyserver.com/) as a reverse proxy for all web services.

## Services Running

- [Pihole](https://pi-hole.net/)
- [Gitea](https://gitea.io/)
- [Shynet](https://github.com/milesmcc/shynet)
- [Syncthing](https://syncthing.net/)

## Blog Posts

Here's a collection of posts I've written which shows how Hydra has evolved over the years:

- **2021-02-14**: [Home Server with Nomad](https://mrkaran.dev/posts/home-server-nomad/)
- **2020-04-23**: [Home Server Updates](https://mrkaran.dev/posts/home-server-updates/)
- **2019-09-22**: [Home Server Setup](https://mrkaran.dev/posts/home-server-setup/)

## Setup Instructions

Visit [SETUP.md](./docs/SETUP.md) for following instructions on setting up Nomad and Consul.
