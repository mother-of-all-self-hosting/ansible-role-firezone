# Firezone Ansible Role

![Firezone Logo](assets/firezone-logo.png)

![Lint badge](https://woodpecker.hyteck.de/api/badges/mother-of-all-self-hosting/ansible-role-firezone/status.svg)

Firezone is a self-hosted VPN server and Linux firewall. This role helps you to set up firezone:

- with everything run in [Docker](https://www.docker.com/) containers
- powered by [the official Firezone container image](https://hub.docker.com/r/firezone/firezone)


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

# Exposed tags

| Tag | Usage |
| --- | --- |
| `install-all` | Install Firezone and possible other services (bit faster than setup) |
| `install-all` | Install only Firezone (bit faster than setup) |
| `setup-all` | (Un-)Install Firezone and possible other services |
| `setup-firezone` | (Un-)Install Firezone (only)|
| `firezone-create-or-reset-admin` | Create the configured admin account or reset the password to the password set in `vars.yml` |

## Support


- Github issues: [mother-of-all-self-hosting/ansible-role-firezone/issues](https://github.com/mother-of-all-self-hosting/ansible-role-firezone/issues)
