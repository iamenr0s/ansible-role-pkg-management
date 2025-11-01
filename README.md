[![Molecule](https://github.com/iamenr0s/ansible_role_pkg_management/actions/workflows/molecule.yml/badge.svg)](https://github.com/iamenr0s/ansible_role_pkg_management/actions/workflows/molecule.yml) [![Release](https://github.com/iamenr0s/ansible_role_pkg_management/actions/workflows/release.yml/badge.svg)](https://github.com/iamenr0s/ansible_role_pkg_management/actions/workflows/release.yml) ![Ansible Role](https://img.shields.io/ansible/role/d/iamenr0s/ansible_role_upgrade) [![CodeFactor](https://www.codefactor.io/repository/github/iamenr0s/ansible_role_pkg_management/badge)](https://www.codefactor.io/repository/github/iamenr0s/ansible_role_pkg_management)

# Ansible Role: Package Management

This role manages packages across common Linux distributions, supporting Debian/Ubuntu and RHEL-family systems (Rocky Linux, AlmaLinux, Fedora). It provides idempotent tasks for updating package caches, installing and removing packages, and performing upgrades using the native package managers (`apt`, `dnf`, `yum`) behind a consistent interface.

## Requirements

- Ansible `>= 2.9`
- Python 3 on managed hosts

## Supported Platforms

- Ubuntu 20.04 (focal), 22.04 (jammy)
- Debian 11 (bullseye), 12 (bookworm)
- Enterprise Linux 8, 9 (RHEL-compatible)
- Rocky Linux 8, 9
- AlmaLinux 8, 9
- Fedora 38, 39

## Role Variables

- `pkg_update_cache` (bool, default: `true`): Refresh package cache.
- `pkg_cache_valid_time` (int, default: `3600`): Cache validity for `apt` in seconds.
- `pkg_install` (list, default: `[]`): Packages to install.
- `pkg_install_state` (str, default: `present`): State for installed packages (`present`, `latest`).
- `pkg_remove` (list, default: `[]`): Packages to remove.
- `pkg_upgrade` (bool, default: `false`): Upgrade all packages.
- `pkg_upgrade_debian_type` (str, default: `dist`): Upgrade type for Debian/Ubuntu (`safe`, `yes`, `dist`, `full`).

## Example Playbook

Basic usage:

```yaml
- hosts: all
  gather_facts: true
  become: true

  roles:
    - role: iamenr0s.ansible_role_pkg_management
      vars:
        pkg_update_cache: true
        pkg_install:
          - curl
          - tree
```

Upgrade all packages:

```yaml
- hosts: debian_hosts
  gather_facts: true
  become: true

  roles:
    - role: iamenr0s.ansible_role_pkg_management
      vars:
        pkg_upgrade: true
        pkg_upgrade_debian_type: dist
```

Remove packages:

```yaml
- hosts: rhel_hosts
  gather_facts: true
  become: true

  roles:
    - role: iamenr0s.ansible_role_pkg_management
      vars:
        pkg_remove:
          - telnet
```

## Dependencies

None.

## License

MIT

## Author Information

Author: iamenr0s
Galaxy: `iamenr0s.ansible_role_pkg_management`

## Contributing

Contributions are welcome! Please open an issue or PR.
