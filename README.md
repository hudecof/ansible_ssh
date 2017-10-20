# SSH

This roles is for installing and configuring sshd.

## Requirements

- ansible: version 2 and higher

## Role Variables

### SSH configuration directives
#### Global config

in the variable `sshd_config_directives` defined in `vars/main.yml` are defined all supported `sshd_config` variables with reasonable defautls. For each variable there is **name**, **type** and **default value**. 

To override the the default value set varibale `sshd_config_<name>`.

If the type is

- **s** / **scalar**: use value enclosed in double quote, since values like **yes** and **no** have special meaning.
- **l** / **list**: use YAML list syntax, like `['val1', 'val2']`

There are some OS specific variables, which are defined in variables `__sshd_config_<name>` in `vars/os-<distribution>` but still could be overwriten.

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
