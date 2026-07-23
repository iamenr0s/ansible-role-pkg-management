[![Molecule](https://github.com/iamenr0s/ansible-role-pkg-management/actions/workflows/molecule.yml/badge.svg)](https://github.com/iamenr0s/ansible-role-pkg-management/actions/workflows/molecule.yml) ![Ansible Role](https://img.shields.io/ansible/role/d/iamenr0s/ansible_role_pkg_management) [![CodeFactor](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-pkg-management/badge)](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-pkg-management)

# Ansible Role: Package Management

This role manages packages across common Linux distributions, supporting Debian/Ubuntu and RHEL-family systems (Rocky Linux, AlmaLinux, Fedora). It provides idempotent tasks for updating package caches, installing and removing packages, and performing upgrades using the native package managers (`apt`, `dnf`, `yum`) behind a consistent interface.

## Features

- Updates the package cache (`apt`, `dnf`) with configurable cache validity
- Installs and removes packages via a distro-agnostic interface
- Upgrades all packages (`apt`, `dnf`, `yum`)
- Manages APT keyrings and repositories (Debian/Ubuntu)
- Manages YUM/DNF repositories, including repo excludes (RHEL/Fedora)
- Treats `dnf5` the same as `dnf` for newer Fedora releases

## Requirements

- Ansible `>= 2.9`
- Python 3 on managed hosts

## Supported Platforms

- Ubuntu 22.04 (jammy), 24.04 (noble)
- Debian 12 (bookworm), 13 (trixie)
- Enterprise Linux 8, 9, 10 (RHEL-compatible)
- Rocky Linux 8, 9, 10
- AlmaLinux 8, 9, 10
- Fedora 42, 43, 44

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

## CI & Release (maintainers)

A single workflow (`.github/workflows/molecule.yml`) runs lint and the full Molecule distro matrix on pushes to `main`, PRs, and `v*` tags. On `v*` tags, a `release` job publishes to Ansible Galaxy after all tests pass.

The Galaxy API key lives in the `galaxy` GitHub environment, which only `v*` tags may target. One-time setup:

```bash
# Galaxy publishing key (environment-scoped, get it from galaxy.ansible.com/ui/token)
gh secret set GALAXY_API_KEY --env galaxy --repo iamenr0s/ansible-role-pkg-management

# Code scanning notifications (Slack webhook URL; for Discord append /slack to the webhook URL)
gh secret set SECURITY_ALERT_WEBHOOK --env galaxy --repo iamenr0s/ansible-role-pkg-management
```

`.github/workflows/code-scanning-notify.yml` polls the code-scanning API every 6 hours and posts new or updated open alerts to that webhook (GitHub Actions cannot trigger on `code_scanning_alert` directly).

To release: tag a commit `vX.Y.Z` and push the tag — CI gates the Galaxy publish.

 
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

## Contributing

Contributions are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md) for the
local pipeline commands and pull request checklist. This project follows the
[Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).

## Security

See [SECURITY.md](SECURITY.md) — GitHub private vulnerability reporting, no
public issues for security bugs.

## License

This project is licensed under the [MIT License](LICENSE).

## Author Information

Author: iamenr0s
Galaxy: `iamenr0s.ansible_role_pkg_management`