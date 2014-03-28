SSH
========

This roles is for installing and configuring sshd.

Requirements
------------

This role requires Ansible 1.4 or higher, and platform requirements are listed in the metadata file. It should work also with lower version, but I have never tested it.

Role Variables
--------------

Please readme the descriptions in the

    defautls/main.yml
    vars/main.yml

The variables in the defautls/main.yml could be overriden in the host_vars/group_vars. The variables in the vars/main.yml are global for all managed servers.

Dependencies
------------

None

License
-------

BSD

Author Information
------------------

Peter Hudec
