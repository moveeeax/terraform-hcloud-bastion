# terraform-hcloud-bastion

> One way in, from your CIDRs only — there is no default that says 0.0.0.0/0.

**Status:** 🚧 In development

## Overview

Terraform module that provisions a hardened Hetzner Cloud bastion host: public IP, firewall restricted to caller-supplied CIDRs (no world-open default), SSH-key-only login and a private-network route to the rest of the fleet.

## Features

- `allowed_ssh_cidrs` is a required input with no default, so a world-open bastion is not something you can get by omission
- Firewall rejects `0.0.0.0/0` and `::/0` on port 22 at plan time, whatever is passed in
- Cloud-init hardening: `PasswordAuthentication no`, `PermitRootLogin prohibit-password`, no host keys baked into the image
- Attached to the private network with a route so the rest of the fleet needs no public IP at all
- Optional fail2ban and an idle `ClientAliveInterval`, both toggled by input rather than hand-edited later
- Outputs the public address plus a ready-to-paste `ProxyJump` snippet for `~/.ssh/config`

## Stack

Terraform + the hetznercloud/hcloud provider.

## Usage

```hcl
module "bastion" {
  source = "github.com/moveeeax/terraform-hcloud-bastion"

  name        = "bastion-01"
  server_type = "cx22"
  image       = "debian-12"
  location    = "nbg1"

  network_id   = module.network.network_id
  private_ipv4 = "10.0.1.4"

  ssh_key_names     = ["ops-team"]
  allowed_ssh_cidrs = ["203.0.113.0/24"]

  enable_fail2ban = true
}
```

## License

MIT
