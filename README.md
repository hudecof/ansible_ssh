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

Dependencies
------------

None

License
-------

BSD

Author Information
------------------

Peter Hudec
