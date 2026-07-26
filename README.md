[![CI](https://github.com/guidugli/ansible-role-selinux/actions/workflows/CI.yml/badge.svg)](https://github.com/guidugli/ansible-role-selinux/actions/workflows/CI.yml)
[![Release](https://img.shields.io/github/v/release/guidugli/ansible-role-selinux?sort=semver)](https://github.com/guidugli/ansible-role-selinux/releases)
[![Galaxy](https://img.shields.io/badge/galaxy-guidugli.selinux-blue.svg)](https://galaxy.ansible.com/ui/standalone/roles/guidugli/selinux/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

# Ansible Role: selinux

Install distribution-specific SELinux support packages and configure SELinux state and policy on supported Linux hosts.

The role remains flexible by default and provides optional CIS-oriented validation through `selinux_cis_profile`. By default (`none`), the role allows all SELinux states and policies supported by the underlying platform. CIS validation can be enabled without changing the role's core behavior.

## Requirements

- Ansible Core 2.14 or newer.
- `ansible.posix` collection 1.5.4 or newer.
- External privilege escalation (`become: true`) when managing packages, SELinux state, or reboots on real hosts.
- Supported Molecule platforms:
  - Ubuntu 26.04, 24.04
  - Debian 13, 12
  - Fedora 44, 43

## Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `selinux_state` | `enforcing` | Desired SELinux mode (`disabled`, `permissive`, `enforcing`). |
| `selinux_policy` | OS-dependent | Desired SELinux policy. Debian-family defaults to `default`; RedHat-family defaults to `targeted`. |
| `selinux_reboot` | `true` | Permit the reboot handler when SELinux changes require a reboot. |
| `selinux_cis_profile` | `none` | CIS validation profile: `none`, `level1`, or `level2`. |

### CIS Profiles

`selinux_cis_profile` controls validation strictness without removing role flexibility.

| Profile | Behavior |
|-----------|----------|
| `none` | Baseline role validation only. |
| `level1` | Rejects disabled SELinux, validates CIS-approved RedHat SELinux policies, validates host configuration where available. |
| `level2` | Includes Level 1 checks and requires enforcing mode. |

## Example Playbook

### Standard Usage

```yaml
- name: Configure SELinux
  hosts: linux
  become: true
  roles:
    - role: guidugli.selinux
```

### CIS Level 1

```yaml
- name: Configure SELinux with CIS validation
  hosts: linux
  become: true
  vars:
    selinux_cis_profile: level1
    selinux_policy: targeted
  roles:
    - role: guidugli.selinux
```

### CIS Level 2

```yaml
- name: Configure SELinux with CIS Level 2 validation
  hosts: linux
  become: true
  vars:
    selinux_cis_profile: level2
    selinux_state: enforcing
    selinux_policy: targeted
  roles:
    - role: guidugli.selinux
```

## Molecule Testing

```bash
ansible-galaxy collection install -r requirements.yml
molecule test -s default
molecule test -s systemd
```

The shared verify play validates:

- Expected SELinux packages.
- Variable correctness.
- Optional CIS profile constraints.
- SELinux configuration and runtime state when available.
- RedHat-specific CIS checks where applicable.

## Execution Notes

### Privilege Model

The role never sets `become`, `become_user`, or `become_method`. Privilege escalation must be provided externally.

### Container Behavior

Container connections are treated as package-validation environments. Runtime SELinux management and host bootloader checks are skipped when the connection type is container-based.

### Systemd Behavior

The role does not manage services or unit files. The only system-level action is the optional reboot handler.
