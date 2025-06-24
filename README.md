<!--
SPDX-FileCopyrightText: 2023 Julian-Samuel Gebühr
SPDX-FileCopyrightText: 2023 Nikita Chernyi
SPDX-FileCopyrightText: 2023 Slavi Pantaleev
SPDX-FileCopyrightText: 2025 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Firezone Ansible role

This is an [Ansible](https://www.ansible.com/) role which installs [Firezone](https://www.firezone.dev/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

This role *implicitly* depends on:

- [`com.devture.ansible.role.playbook_help`](https://github.com/devture/com.devture.ansible.role.playbook_help)
- [`com.devture.ansible.role.systemd_docker_base`](https://github.com/devture/com.devture.ansible.role.systemd_docker_base)

Check [defaults/main.yml](defaults/main.yml) for the full list of supported options.

## Configuration

```
########################################################################
#                                                                      #
# firezone                                                             #
#                                                                      #
########################################################################

firezone_enabled: true
firezone_hostname: example.org

firezone_default_admin_email: "user@invalid.org"
firezone_default_admin_password: "<securepassword>"

# Generate this with `openssl rand -base64 32`
firezone_database_encryption_key: "<secret>"

########################################################################
#                                                                      #
# /firezone                                                            #
#                                                                      #
########################################################################
```

## Exposed tags

| Tag | Usage |
| --- | --- |
| `install-all` | Install Firezone and possible other services (bit faster than setup) |
| `install-all` | Install only Firezone (bit faster than setup) |
| `setup-all` | (Un-)Install Firezone and possible other services |
| `setup-firezone` | (Un-)Install Firezone (only)|
| `firezone-create-or-reset-admin` | Create the configured admin account or reset the password to the password set in `vars.yml` |
