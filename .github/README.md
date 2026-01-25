# ans_role_config_tailscale

Configure machine as a user-facing device on a Tailnet.

[![Release](https://img.shields.io/github/release/digimokan/ans_role_config_tailscale.svg?label=release)](https://github.com/digimokan/ans_role_config_tailscale/releases/latest "Latest Release Notes")
[![License](https://img.shields.io/badge/license-MIT-blue.svg?label=license)](LICENSE.md "Project License")

## Table Of Contents

* [Purpose](#purpose)
* [Supported Operating Systems](#supported-operating-systems)
* [Quick Start](#quick-start)
    * [Tailnet Bootstrapping](#tailnet-bootstrapping)
        * [Create Tailnet](#create-tailnet)
        * [Create Tags](#create-tags)
        * [Add Access Policies](#add-access-policies)
        * [Add Auth Keys](#add-auth-keys)
    * [Use From Playbook](#use-from-playbook)
* [Role Options](#role-options)
* [Contributing](#contributing)

## Purpose

* Install and configure [Tailscale](https://tailscale.com/).
* Start and enable the `tailscaled` service to run as `root`.
* Add the host to the Tailnet as a _user-facing device_.

Note that there are two kind of devices on a Tailnet: _user-facing devices_, and
_service-providing_ devices (see
[docs](https://tailscale.com/kb/1068/tags#use-cases).
_User-facing devices_ associate with an authenticated Tailnet user, and
_service-providing_ devices associate with a
[tag](https://tailscale.com/kb/1068/tags) (and optionally use
[auth keyss](https://tailscale.com/kb/1085/auth-keys) for connecting to the
Tailnet).

## Supported Operating Systems

* Arch Linux
* FreeBSD

## Quick Start

### Tailnet Bootstrapping

In this stage, you have a Tailscale account, but no hosts on the tailnet.

#### Create Tailnet

1. Install the `tailscale` package on a FreeBSD machine.

2. Start and enable `tailscaled` on the FreeBSD machine.

3. Join the tailnet with `tailscale up` on the FreeBSD machine.

    * Use the provided URL (in any browser, on any machine) to authenticate
      and add the FreeBSD machine.

4. Repeat steps 1 to 3 on a second FreeBSD machine.

5. You can remove these machines later. They're required to initialize the
   Tailnet.

#### Create Tags

For the purpose of adding a service-providing device to the Tailnet (i.e.
a server), define a `server` tag.

1. Go to the [Web UI Tags Page](https://login.tailscale.com/admin/acls/visual/tags).

2. Add a tag with name `server` and owner `autogroup:owner`.

#### Add Access Policies

1. Go to the [Access Control Page](https://login.tailscale.com/admin/acls/visual/tailscale-ssh).

2. Add a new rule with source `autogroup:member`, destination `tag:server`,
   users `root`+`autogroup:nonroot`, action `check`, and `checkPeriod` `12h0m0s`.

#### Add Auth Keys

1. Go to the [Keys Page](https://login.tailscale.com/admin/settings/keys).

2. Generate a new auth key, with `Reusable` enabled (for user-facing devices).

3. Generate a new auth key, with `Reusable` enabled, with tag `tag:server`.

### Use From Playbook

1. Create `requirements.yml` in ansible project root, and add this content:

   ```yaml
   # requirements.yml
   - src: https://github.com/digimokan/ans_role_config_tailscale
   ```

2. From the project root directory, install/download the role:

   ```shell
   $ ansible-galaxy install --role-file requirements.yml --roles-path ./roles --force-with-deps
   ```

   * _NOTE:_ `--force-with-deps` _ensures subsequent calls download updates_

3. Include the role like any local role, from the project playbook:

   ```yaml
   # playbook.yml
   - hosts: localhost
     connection: local
     tasks:
       - name: "Configure machine as a user-facing device on a Tailnet"
         ansible.builtin.include_role:
           name: ans_role_config_tailscale
         vars:
           cfg_tailscale_login_authkey: "tskey-abcdef123456789"
   ```

## Role Options

Vars that must be defined when including the role in the playbook:

  * [dependencies](../defaults/main/dependencies/main.yml)

Vars with default values, which can be overridden in the playbook:

  * [overridable](../defaults/main/overridable)

## Contributing

* Feel free to report a bug or propose a feature by opening a new
  [Issue](https://github.com/digimokan/ans_role_config_tailscale/issues).
* Follow the project's [Contributing](CONTRIBUTING.md) guidelines.
* Respect the project's [Code Of Conduct](CODE_OF_CONDUCT.md).

