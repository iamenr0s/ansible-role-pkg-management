[![Molecule](https://github.com/iamenr0s/ansible-role-pkg-management/actions/workflows/molecule.yml/badge.svg)](https://github.com/iamenr0s/ansible-role-pkg-management/actions/workflows/molecule.yml) [![Release](https://github.com/iamenr0s/ansible-role-pkg-management/actions/workflows/release.yml/badge.svg)](https://github.com/iamenr0s/ansible-role-pkg-management/actions/workflows/release.yml) ![Ansible Role](https://img.shields.io/ansible/role/d/iamenr0s/ansible_role_pkg_management) [![CodeFactor](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-pkg-management/badge)](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-pkg-management)

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

Repository management:
- `pkg_apt_keys` (list, default: `[]`): APT keyrings to fetch. Each item supports `url`, `dest`.
- `pkg_apt_repositories` (list, default: `[]`): APT repositories to add. Each item supports `repo`, optional `filename`, `codename`, `state`, `update_cache`.
- `pkg_yum_repositories` (list, default: `[]`): YUM/DNF repositories to add. Each item supports `name`, `description`, `baseurl`, optional `enabled`, `gpgcheck`, `gpgkey`, `state`.
 - `pkg_disable_excludes` (str|null, default: `null`): For YUM/DNF operations, pass `disable_excludes` (values: `all`, `main`, or a repo ID). When `null`, it is omitted.

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
 
## Add Kubernetes Repositories

Debian/Ubuntu example (v1.30):

```yaml
- hosts: debian_hosts
  gather_facts: true
  become: true
  roles:
    - role: iamenr0s.ansible_role_pkg_management
      vars:
        pkg_apt_keys:
          - url: https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key
            dest: /etc/apt/keyrings/kubernetes-apt-keyring.gpg
        pkg_apt_repositories:
          - repo: "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /"
            filename: kubernetes.list
            state: present
            update_cache: true
        pkg_install:
          - kubelet
          - kubeadm
          - kubectl
```

RHEL/Fedora example (v1.30):

```yaml
- hosts: rhel_hosts
  gather_facts: true
  become: true
  roles:
    - role: iamenr0s.ansible_role_pkg_management
      vars:
        pkg_yum_repositories:
          - name: kubernetes
            description: Kubernetes
            baseurl: https://pkgs.k8s.io/core:/stable:/v1.30/rpm/
            enabled: true
            gpgcheck: true
            gpgkey: https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key
            state: present
        # Ignore repo-level excludes during installs/upgrades when needed
        pkg_disable_excludes: all
        pkg_install:
          - kubelet
          - kubeadm
          - kubectl
```
