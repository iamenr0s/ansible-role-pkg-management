# Contributing

Thanks for taking the time to contribute to `iamenr0s.ansible_role_pkg_management`!

## Getting started

1. Fork the repository and create your branch from `main`.
2. Install the development dependencies:

   ```bash
   pip3 install ansible molecule molecule-plugins[docker] docker cryptography yamllint ansible-lint
   ```

## Making changes

- Keep changes small and focused — one topic per pull request.
- Follow the existing YAML style; lint rules live in `.yamllint` and `.ansible-lint`.
- If you add or change a variable, document it in `README.md` and set a sane default in `defaults/main.yml`.

## Testing

Before opening a pull request, make sure lint and molecule pass:

```bash
yamllint .
ansible-lint

# Full test against the default distro (almalinux10)
molecule test

# Or against a specific distro
MOLECULE_DISTRO=ubuntu2404 molecule test
```

Molecule uses Podman locally and Docker in CI. The CI matrix covers Debian, Ubuntu, AlmaLinux, Rocky Linux, and Fedora — changes must not break any supported distro.

## Submitting a pull request

1. Ensure lint and tests pass locally.
2. Fill in the pull request template.
3. A maintainer will review your PR; CI must be green before merge.

## Reporting bugs and requesting features

Use the issue templates — they ask for the details (distro, Ansible version, role variables) needed to reproduce a problem.

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating you agree to abide by it.
