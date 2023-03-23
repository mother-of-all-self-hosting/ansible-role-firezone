# Firezone Ansible Role

![Firezone Logo](assets/firezone-logo.png)

![Lint badge](https://woodpecker.hyteck.de/api/badges/moan0s/role-firezone/status.svg)

Firezone is a self-hosted VPN server and Linux firewall. This role helps you to set up firezone:

- with everything run in [Docker](https://www.docker.com/) containers
- powered by [the official Firezone container image](https://hub.docker.com/r/dxflrs/garage)


## Installing

To configure and install Firezone on your own server(s), you should use a playbook like [Mother of all self-hosting](https://github.com/mother-of-all-self-hosting/mash-playbook) or write your own.

# Configuring this role for your playbook

```
##############
## FIREZONE ##
##############

firezone_enabled: true
firezone_hostname: example.org

firezone_default_admin_email: "user@invalid.org"
firezone_default_admin_password: "<securepassword>"

# Generate this with `openssl rand -base64 32`
firezone_database_encryption_key: "<secret>"
```

## Support


- Github issues: [moan0s/role-firezone/issues](https://github.com/moan0s/role-firezone/issues)
