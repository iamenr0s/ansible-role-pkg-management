# Changelog

All notable changes to this project are documented in this file.

## [1.0.1] - 2025-11-12
### Added
- Cross-platform repository management variables:
  - `pkg_apt_keys`, `pkg_apt_repositories` for Debian/Ubuntu.
  - `pkg_yum_repositories` for RHEL/Fedora.
- Tasks to manage repositories:
  - Create `/etc/apt/keyrings`, fetch APT keyrings, add APT repositories.
  - Add YUM/DNF repositories via `yum_repository` (supports `exclude`).
- `pkg_disable_excludes` to pass `disable_excludes` for YUM/DNF installs/upgrades.
- README examples for adding Kubernetes repositories on Debian/Ubuntu and RHEL/Fedora.

### Changed
- Split install and upgrade operations per package manager (`apt`, `dnf`, `yum`) to apply `disable_excludes` precisely and improve clarity.

## [1.0.0] - 2025-11-02
### Added
- Role to manage packages across Debian/Ubuntu and RHEL-family (Rocky/Alma/Fedora).
- Update cache tasks:
  - `apt` cache refresh honoring `pkg_cache_valid_time`.
  - `dnf`/`yum` cache refresh.
- Install and remove packages via unified variables:
  - `pkg_install` with `pkg_install_state` (`present`, `latest`).
  - `pkg_remove` for removals.
- Upgrade all packages:
  - `apt` upgrades using `pkg_upgrade_debian_type` (`safe`, `yes`, `dist`, `full`).
  - `dnf`/`yum` upgrades to `latest`.
- Defaults and README documenting variables and basic usage.
- Molecule scenario for basic convergence testing.
- Supported platform list (Ubuntu, Debian, EL/Rocky/Alma, Fedora).

## Format
This changelog follows a simple, readable format. Future versions may adopt the
Keep a Changelog style and semantic versioning.
