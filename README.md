Ansible Role: selinux
=========

An Ansible Role that install and configure selinux on RHEL/CentOS, Fedora and Debian/Ubuntu.

Requirements
------------

Operating system running on bare metal or on a hypervisor virtualization. On conteinerized systems selinux is disabled.

Role Variables
--------------

**Available variables are listed below, along with default values (see defaults/main.yml):**

    selinux_state: enforcing

The state, either disabled, permissive or enforcing.

    selinux_policy: "{{ _selinux_policy[ansible_os_family] | default(_selinux_policy['default']) }}"

The policy, default: see vars/main.yml.
The policy differs per distribution, mostly because Debian and Ubuntu use 'default' and other distributions use 'targeted'.

    selinux_reboot: yes

Should the machine be rebooted after changes?

**The variables below don't need any change for targeted operating systems (see vars/main.yml):**

    selinux_requirements:

This variable contains the packages needed to be installed based on Linux distribution and version.


Dependencies
------------

No dependencies.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      vars:
        - selinux_reboot: false
      roles:
         - { role: guidugli.ansible }

License
-------

MIT / BSD

Author Information
------------------

This role was created in 2020 by Carlos Guidugli.
