# SSH

Ansible role for installing and configuring sshd.

## Requirements

- Ansible 2.9 or higher
- Role dependency: `hudecof.filters`

## Role Variables

### Service Variables

| Variable | Default | Description |
| --- | --- | --- |
| `sshd_packages` | OS-specific | Packages to install |
| `sshd_service` | OS-specific | Service name |
| `sshd_file_config` | OS-specific | Path to `sshd_config` |
| `sshd_dir_config` | OS-specific | SSH config directory |
| `sshd_binary` | OS-specific | Path to the `sshd` binary |
| `sshd_backup` | `true` | Back up config files before overwriting |
| `sshd_service_enabled` | `true` | Enable service on boot |
| `sshd_service_state` | `started` | Desired service state |
| `sshd_test` | `false` | Skip service restart (useful in CI/molecule) |

### Main Configuration — `sshd_config`

The `sshd_config` dictionary drives the generated `sshd_config` file. Values can be strings, lists, or `null`. OS-specific defaults are set via `__sshd_config_<name>` variables in `vars/os-<OS>.yml`.

`sshd_config_order` controls which keys are emitted first (before the alphabetically-sorted remainder):

```yaml
sshd_config_order:
  - AddressFamily
  - Port
  - ListenAddress
```

Override example:

```yaml
sshd_config:
  AddressFamily: any
  Port: 22
  ListenAddress: ['0.0.0.0', '::']
  PasswordAuthentication: 'no'
  PubkeyAuthentication: 'yes'
  Subsystem: "sftp /usr/lib/openssh/sftp-server"
```

### Match Directives — `sshd_config_match`

Match blocks are defined as a list. Each entry has a `match` dict and a `value` dict:

```yaml
sshd_config_match:
  - match:
      User: ['upload']
    value:
      AllowAgentForwarding: 'yes'
      ForceCommand: '/usr/lib/openssh/sftp-server'
```

Valid `match` keys: `User`, `Group`, `Host`, `LocalAddress`, `LocalPort`, `Address` — values are always lists.

### Host Keys — `sshd_host_keys_list`

Generates host key files and adds the corresponding `HostKey` directives. Existing key files are left untouched.

| Variable | Default | Description |
| --- | --- | --- |
| `sshd_host_keys_dir` | OS-specific | Directory for host key files |
| `sshd_host_keys_prefix` | OS-specific | Filename prefix (e.g. `ssh_host_`) |
| `sshd_host_keys_suffix` | OS-specific | Filename suffix (e.g. `_key`) |
| `sshd_host_keys_perm` | OS-specific | File permission |
| `sshd_host_keys_group` | OS-specific | File group |
| `sshd_host_keys_list` | OS-specific | List of key types to generate |

```yaml
sshd_host_keys_list:
  - type: rsa
    args: '-b 4096'
  - type: ecdsa
    args: '-b 521'
  - type: ed25519
```

### Drop-in Files — `sshd_config_d`

Files can be placed in `sshd_config.d/` (included by the main config via the `Include` directive). Each entry in the dict becomes a `.conf` file; the value is a regular sshd directive dict with the same rules as `sshd_config`.

| Variable | Default | Description |
| --- | --- | --- |
| `sshd_config_d_dir` | `{{ sshd_dir_config }}/sshd_config.d` | Drop-in directory |
| `sshd_config_d` | `{}` | Dict of `filename → directives` — files to create |
| `sshd_config_d_disabled` | `[]` | List of filenames to disable (renames `.conf` → `.conf.disabled`) |
| `sshd_config_d_absent` | `[]` | List of filenames to remove entirely (deletes `.conf` and `.conf.disabled`) |

The directory is created automatically when `sshd_config_d` is non-empty.

To disable a file, add its name to `sshd_config_d_disabled` — the active `.conf` is renamed to `.conf.disabled` (content preserved, sshd ignores it). To re-enable, move the name back to `sshd_config_d` and remove it from `sshd_config_d_disabled`. To delete completely, add the name to `sshd_config_d_absent`.

**Create example** — generates `/etc/ssh/sshd_config.d/hardening.conf`:

```yaml
sshd_config_d:
  hardening:
    MaxAuthTries: 3
    LoginGraceTime: 30
    AllowAgentForwarding: 'no'
    AllowTcpForwarding: 'no'
```

Match blocks are supported via an optional `match` key inside the file entry (same structure as `sshd_config_match`):

```yaml
sshd_config_d:
  sftp:
    Subsystem: "sftp internal-sftp"
    match:
      - match:
          User: ['upload']
        value:
          ChrootDirectory: /srv/upload
          ForceCommand: internal-sftp
          AllowTcpForwarding: 'no'
```

**Disable example** — renames `hardening.conf` to `hardening.conf.disabled`:

```yaml
sshd_config_d_disabled:
  - hardening
```

**Remove example** — deletes `hardening.conf` and any `.disabled` marker:

```yaml
sshd_config_d_absent:
  - hardening
```

## Example Playbook

```yaml
- hosts: all
  vars:
    sshd_config:
      Port: 22
      PasswordAuthentication: 'no'
      PubkeyAuthentication: 'yes'
      Subsystem: "sftp /usr/lib/openssh/sftp-server"
    sshd_config_d:
      hardening:
        MaxAuthTries: 3
        LoginGraceTime: 30
        X11Forwarding: 'no'
  roles:
    - hudecof.ssh
```

## Dependencies

- `hudecof.filters`

## License

BSD

## Author Information

Peter Hudec
