<!--
SPDX-FileCopyrightText: 2020 - 2024 MDAD project contributors
SPDX-FileCopyrightText: 2020 - 2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024 - 2025 Suguru Hirahara
SPDX-FileCopyrightText: 2025 Nicola Murino

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up Firezone

This is an [Ansible](https://www.ansible.com/) role which installs [Firezone](https://www.firezone.dev/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

Firezone is a self-hosted VPN server based on [WireGuard](https://www.wireguard.com/) with a web UI.

See the project's [documentation](https://www.firezone.dev/kb) to learn what Firezone does and why it might be useful to you.

## Prerequisites

To run a Firezone instance it is necessary to prepare a [Postgres](https://www.postgresql.org) database server.

If you are looking for an Ansible role for it, you can check out [this role (ansible-role-postgres)](https://github.com/mother-of-all-self-hosting/ansible-role-postgres) maintained by the [Mother-of-All-Self-Hosting (MASH)](https://github.com/mother-of-all-self-hosting) team.

### Open ports

You may need to open the following ports on your server:

- `51820` over **UDP**, controlled by `firezone_wireguard_bind_port` — used for [Wireguard](https://www.wireguard.com/) connections

Docker automatically opens these ports in the server's firewall, so you likely don't need to do anything. If you use another firewall in front of the server, you may need to adjust it.

## Adjusting the playbook configuration

To enable Firezone with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# firezone                                                             #
#                                                                      #
########################################################################

firezone_enabled: true

########################################################################
#                                                                      #
# /firezone                                                            #
#                                                                      #
########################################################################
```

### Set the hostname

To enable Firezone you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
firezone_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

### Create the first admin account with environment variables (optional)

To use Firezone, you need to create an admin account by adding the following configuration to your `vars.yml` file:

```yaml
firezone_default_admin_email: ADMIN_EMAIL_ADDRESS_HERE
firezone_default_admin_password: ADMIN_PASSWORD_HERE
```

Replace `ADMIN_EMAIL_ADDRESS_HERE` and `ADMIN_PASSWORD_HERE` with your own values.

### Set 32-byte base64 string for secret key

You also need to specify a **32-byte base64 string** to encrypt integration secrets on the database. To do so, add the following configuration to your `vars.yml` file. The value can be generated with `openssl rand -base64 32` or in another way.

```yaml
firezone_database_encryption_key: YOUR_SECRET_KEY_HERE
```

>[!NOTE]
> Other type of values such as one generated with `pwgen -s 64 1` does not work.

### Extending the configuration

There are some additional things you may wish to configure about the component.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `firezone_environment_variables_additional_variables` variable

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, the Firezone instance becomes available at the URL specified with `firezone_hostname`. With the configuration above, the service is hosted at `https://example.com`.

To get started, open the URL with a web browser, and log in to the service with the credentials set to `firezone_default_admin_email` and `firezone_default_admin_password`.

Refer to the [official documentation](https://www.firezone.dev/docs/user-guides/add-devices/) to figure out how to add devices, etc.

### Create or reset admin account

You can run the command below to create the admin account or reset the password to the current password specified on `vars.yml`:

```sh
just run-tags firezone-create-or-reset-admin
```

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu firezone` (or how you/your playbook named the service, e.g. `mash-firezone`).
