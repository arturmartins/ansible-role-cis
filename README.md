# [cis](#cis)

Apply and/or check recommendations from the CIS benchmarks.

|Travis|GitHub|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-cis.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-cis)|[![github](https://github.com/robertdebock/ansible-role-cis/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-cis/actions)|[![quality](https://img.shields.io/ansible/quality/49856)](https://galaxy.ansible.com/robertdebock/cis)|[![downloads](https://img.shields.io/ansible/role/d/49856)](https://galaxy.ansible.com/robertdebock/cis)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-cis.svg)](https://github.com/robertdebock/ansible-role-cis/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.cis
      # 1.1.5 Ensure noexec option set on /tmp partition (Scored)
      # `noexec` can't be set in a container.
      cis_tmp_noexec: no

      # 1.1.6 Ensure separate partition exists for /var (Scored)
      # Can't create a partition in CI.
      cis_var_partition: no

      # 1.1.7 Ensure separate partition exists for /var/tmp (Scored)
      # Can't create a partition in CI.
      cis_var_tmp_partition: no

      # 1.1.11 Ensure separate partition exists for /var/log (Scored)
      # Can't create a partition in CI.
      cis_var_log_partition: no

      # 1.1.12 Ensure separate partition exists for /var/log/audit (Scored)
      # Can't create a partition in CI.
      cis_var_log_audit_partition: no

      # 1.1.13 Ensure separate partition exists for /home (Scored)
      # Can't create a partition in CI.
      cis_home_partition: no

      # 1.5.1 Ensure permissions on bootloader config are configured (Scored)
      # No bootloaders in a container.
      cis_permissions_bootloader: yes
```

The machine may need to be prepared using `molecule/resources/prepare.yml`:
```yaml
---
- name: prepare
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.cron
```

For verification `molecule/resources/verify.yml` run after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: yes

  tasks:
    # Molecules verifier does not load variables in default and vars. These
    # tasks load the variables explicitly.
    - name: load vars/main.yml
      include_vars: ../../vars/main.yml

    - name: load defaults/main.yml
      include_vars: ../../defaults/main.yml

    # In verify (in CI), certain overwrites must occur because not all resources
    # are available in a container, like kernel modules or partitions.
    - name: load verifier overwrites
      include_vars: defaults.yml

    - name: include distribution specific version
      include: "{{ ansible_distribution ~ '-' ~ ansible_distribution_major_version }}/verify.yml"
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for cis

# The CIS guidelines determines many settings of a system. The values used in
# this file will make a system compliant to the CIS specifications.
# There are many reasons why you do not want to adhere to one or more specific
# rules. You can overwrite values in you group_vars, host_vars, inventory or
# playbook.

# 1.1.1.1 Ensure mounting of cramfs filesystems is disabled (Scored)
cis_cramfs_disabled: yes

# 1.1.1.2 Ensure mounting of vFAT filesystems is limited (Not Scored)
cis_vfat_disabled: yes

# 1.1.1.3 Ensure mounting of squashfs filesystems is disabled (Scored)
cis_squashfs_disabled: yes

# 1.1.1.4 Ensure mounting of udf filesystems is disabled (Scored)
cis_udf_disabled: yes

# 1.1.2 Ensure /tmp is configured (Scored)
cis_tmp_configured: yes

# 1.1.3 Ensure nodev option set on /tmp partition (Scored)
cis_tmp_nodev: yes

# 1.1.4 Ensure nosuid option set on /tmp partition (Scored)
cis_tmp_nosuid: yes

# 1.1.5 Ensure noexec option set on /tmp partition (Scored)
cis_tmp_noexec: yes

# 1.1.6 Ensure separate partition exists for /var (Scored)
cis_var_partition: yes

# 1.1.7 Ensure separate partition exists for /var/tmp (Scored)
cis_var_tmp_partition: yes

# 1.1.8 Ensure nodev option set on /var/tmp partition (Scored)
cis_var_tmp_nodev: yes

# 1.1.9 Ensure nosuid option set on /var/tmp partition (Scored)
cis_var_tmp_nosuid: yes

# 1.1.10 Ensure noexec option set on /var/tmp partition (Scored)
cis_var_tmp_noexec: yes

# 1.1.11 Ensure separate partition exists for /var/log (Scored)
cis_var_log_partition: yes

# 1.1.12 Ensure separate partition exists for /var/log/audit (Scored)
cis_var_log_audit_partition: yes

# 1.1.13 Ensure separate partition exists for /home (Scored)
cis_home_partition: yes

# 1.1.14 Ensure nodev option set on /home partition (Scored)
cis_home_nodev: yes

# 1.1.15 Ensure nodev option set on /dev/shm partition (Scored)
cis_dev_shm_nodev: yes

# 1.1.16 Ensure nosuid option set on /dev/shm partition (Scored)
cis_dev_shm_nosuid: yes

# 1.1.17 Ensure noexec option set on /dev/shm partition (Scored)
cis_dev_shm_noexec: yes

# 1.1.18 Ensure nodev option set on removable media partitions (Not Scored)
cis_removable_media_nodev: yes

# 1.1.19 Ensure nosuid option set on removable media partitions (Not Scored)
cis_removable_media_nosuid: yes

# 1.1.20 Ensure noexec option set on removable media partitions (Not Scored)
cis_removable_media_noexec: yes

# 1.1.21 Ensure sticky bit is set on all world-writable directories (Scored)
cis_fix_sticky_bit: yes

# 1.1.22 Disable Automounting
cis_disable_automount: yes

# 1.1.23 Disable USB Storage (Scored)
cis_usb_storage_disabled: yes

# 1.2.1 Ensure GPG keys are configured (Not Scored)
cis_gpg_keys_configured: yes

# 1.2.2 Ensure gpgcheck is globally activated (Scored)
cis_gpgcheck_enabled: yes

# 1.2.3 Ensure package manager repositories are configured (Not Scored)
cis_repositories_configured: yes

# 1.3.1 Ensure sudo is installed (Scored)
cis_sudo_installed: yes

# 1.3.2 Ensure sudo commands use pty (Scored)
cis_sudo_use_pty: yes

# 1.3.3 Ensure sudo log file exists (Scored)
cis_sudo_logfile: yes

# 1.4.1 Ensure AIDE is installed (Scored)
cis_aide_installed: yes

# 1.4.2 Ensure filesystem integrity is regularly checked (Scored)
cis_filesystem_integrity_checked: yes

# 1.5.1 Ensure permissions on bootloader config are configured (Scored)
cis_permissions_bootloader: yes

# 1.5.2 Ensure bootloader password is set (Scored)
cis_bootloader_password_set: yes
cis_bootloader_password: changeme

# 1.5.3 Ensure authentication required for single user mode (Scored)
cis_authentication_single_user_mode: yes
```

## [Requirements](#requirements)

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.cron

```

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/cis.png "Dependency")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|el|8|
|ubuntu|focal|

The minimum version of Ansible required is 2.8 but tests have been done to:

- The previous version, on version lower.
- The current version.
- The development version.



## [Testing](#testing)

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-cis) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-cis/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

## [License](#license)

Apache-2.0

## [Contributors](#contributors)

I'd like to thank everybody that made contributions to this repository. It motivates me, improves the code and is just fun to collaborate.

- [arturmartins](https://github.com/arturmartins)

## [Author Information](#author-information)

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
