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

This is an [Ansible](https://www.ansible.com/) role which installs [Firezone](https://github.com/drakkan/firezone/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

Firezone is a full-featured and highly configurable event-driven file transfer solution. It supports SFTP, HTTP/S, FTP/S and WebDAV, and can connect to storage backends including local filesystem, S3 (compatible) Object Storage, Google Cloud Storage, Azure Blob Storage, and other SFTP servers.

See the project's [documentation](https://docs.firezone.com/latest/) to learn what Firezone does and why it might be useful to you.

>[!NOTE]
> There are two versions of Firezone provided by the developer: the free (as in speech) version released under [AGPL 3.0 license](https://github.com/drakkan/firezone/blob/main/LICENSE) and the nonfree ["Enterprise" version](https://docs.firezone.com/enterprise/#enterprise-edition). This role makes it possible for you to install the former (the free version). See [this page](https://firezone.com/compliance.html) for the official information about licensing.

## Prerequisites

### Open ports

You may need to open the following ports on your server:

- `2022` — port number for SFTP service
- `10080` — port number for WebDAV service

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

### Specify data driver

You also need specify "data driver" for Firezone. It is possible to use a driver like Postgres and MySQL (MariaDB), as well as SQLite and CockroachDB.

To use Postgres, add the following configuration to your `vars.yml` file:

```yaml
firezone_environment_variables_data_provider_driver: postgresql
```

Please note that it is necessary to add environment variables manually for database other than Postgres and MySQL (MariaDB).

Refer to [this section](https://docs.firezone.com/latest/config-file/#data-provider) on the official documentation for options to be configured.

### Enable interfaces for WebAdmin and WebClient (optional)

Because the theme used in WebAdmin and WebClient user interfaces is proprietary (see [this section](https://docs.firezone.com/latest/#licensing) for additional information), this role disables them by default.

To enable them, add the following configuration to your `vars.yml` file:

```yaml
firezone_environment_variables_http_bindings_enable_web_admin: true
firezone_environment_variables_http_bindings_enable_web_client: true
```

### Create the first admin account with environment variables (optional)

To use Firezone, you need to create an admin account. If you enable WebAdmin and open it, you can follow the set up wizard to create the first admin account.

Alternatively, you can create it by adding the following configuration to your `vars.yml` file:

```yaml
firezone_environment_variables_data_provider_create_default_admin: true
firezone_environment_variables_firezone_default_admin_username: ADMIN_USERNAME_HERE
firezone_environment_variables_firezone_default_admin_password: ADMIN_PASSWORD_HERE
```

Replace `ADMIN_USERNAME_HERE` and `ADMIN_PASSWORD_HERE` with your own values.

>[!NOTE]
> Changing those values after creating the user does not update the login credential.

### Enable WebDAV server (optional)

You can enable WebDAV server by adding the following configuration to your `vars.yml` file:

```yaml
firezone_environment_variables_webdav_enabled: true
```

>[!NOTE]
> By default the connection to the WebDAV server is not encrypted with HTTPS. To enable encryption, it is necessary to install a TLS certificate and its private key. See [`defaults/main.yml`](../defaults/main.yml) and [this page](https://docs.firezone.com/latest/config-file/#webdav-server) on the official documentation to check how it should be set up.

<!-- TODO: Have Traefik (ansible-role-traefik) manage the TLS certificate and the private key. -->

### Extending the configuration

There are some additional things you may wish to configure about the component.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `firezone_environment_variables_additional_variables` variable

See [this page](https://docs.firezone.com/latest/config-file/) for a complete list of Firezone's config options that you could put in `firezone_environment_variables_additional_variables`.

For example, you can enable OpenID Connect (OIDC) for the web interfaces by adding environment variables such as `SFTPGO_HTTPD__BINDINGS__0__OIDC__CONFIG_URL=https://example.com` to it. See [this section](https://docs.firezone.com/latest/config-file/#http-server) for details.

>[!NOTE]
> You can check [this page on the documentation](https://docs.firezone.com/latest/env-vars/) for the conversion rule of settings into environment variables.

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, Firezone becomes available at the specified hostname like `https://example.com`. You can connect to the SFTP server with a SFTP client on the port `2022`.

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu firezone` (or how you/your playbook named the service, e.g. `mash-firezone`).
