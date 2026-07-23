---
name: Bug report
about: Report a problem with the role
title: ''
labels: bug
assignees: ''
---

## Describe the bug

A clear and concise description of what the bug is.

## To reproduce

Playbook / role invocation and any non-default variables:

```yaml
- hosts: all
  roles:
    - role: iamenr0s.ansible_role_pkg_management
```

## Expected behavior

What you expected to happen.

## Actual behavior

What actually happened. Include the relevant Ansible output (run with `-v` if possible):

```
paste output here
```

## Environment

- Target OS and version (e.g. Debian 12, AlmaLinux 9):
- Ansible version (`ansible --version`):
- Role version:

## Additional context

Anything else that might help (custom `pkg_*` variables, proxy, air-gapped mirror, etc.).
