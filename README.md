# SSH

This roles is for installing and configuring sshd.

## Requirements

- ansible: version 2 and higher

## Role Variables

### SSH configuration directives
#### Global config

in the variable `sshd_config` defined in `defaults/main.yml` are defined basic values as key/value. You have to define yous values.  TI use this approach to be more flexible in **group_vars/all**

```
sshd_config_base:
  - Port 22

ssh_config_host: {}
ssh_config: "{{ sshd_config_base|combine(ssh_config_host) }}"
```

There are some OS specific variables, which are defined in variables `__sshd_config_<name>` in `vars/os-<distribution>`.

#### Match directive

Match directives are defined as a list in variable `sshd_config_match`

```
sshd_config_match:
  - match:
      User: ['upload']
    value:
      AllowAgentForwarding: 'yes'

```

For valid keys for **match** see the man page (should be **User**, **Group**, **Host**, **LocalAddress**, **LocalPort**, **Address**, values are always lists.

For valid keys for **value** see the `sshd_config_match_directives` variable defined in `vars/main.yml`. For each variable there is **name** and **type**.

#### HostKey
Will generate hosts kayes gived type and create **HostKey** attrbutes in the configuration files. If the host keys files exists, we do not touc them. If you do not change any configuration value, the OS defaults are used.

- `sshd_host_keys_dir`: define directory where the ssh host keys should be placed, default is  based on distribution type.
- `sshd_host_keys_prefix`: define prefix for each algorithm, default is based on distribution type
- `sshd_host_keys_suffix`: define prefix for each algorithm, default is based on distribution type
- `sshd_host_keys_perm`: define host key permission to set, default is based on distribution type
- `sshd_host_keys_group`: define host key group to set, default is based on distribution type
- `sshd_host_keys_list`: is list of the keys to generate, default is based on distribution, the list contains keys
  - **type**: defined alg. type, like dsa, rsa, ecdsa, ed25519
  - **agrs** additional args to set, like bits


Dependencies
------------

- roles:
  - hudecof.filters

License
-------

BSD

Author Information
------------------

Peter Hudec
