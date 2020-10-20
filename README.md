# [ca](#ca)

Install and configure a certificate authority on your system.

|Travis|GitHub|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-ca.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-ca)|[![github](https://github.com/robertdebock/ansible-role-ca/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-ca/actions)|[![quality](https://img.shields.io/ansible/quality/35543)](https://galaxy.ansible.com/robertdebock/ca)|[![downloads](https://img.shields.io/ansible/role/d/35543)](https://galaxy.ansible.com/robertdebock/ca)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-ca.svg)](https://github.com/robertdebock/ansible-role-ca/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.ca
```

The machine may need to be prepared using `molecule/resources/prepare.yml`:
```yaml
---
- name: Prepare
  hosts: all
  gather_facts: no
  become: yes

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.buildtools
    - role: robertdebock.epel
    - role: robertdebock.python_pip
    - role: robertdebock.openssl
      openssl_items:
        - name: apache-httpd
          common_name: "{{ ansible_fqdn }}"
    - role: robertdebock.httpd
```

For verification `molecule/resources/verify.yml` runs after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.ca
      ca_requests:
        - "{{ ansible_fqdn }}"
        - name: my_ca.example.com
          passphrase: WoNtT3L1
          cipher: aes256
          country_name: NL
          email_address: robert@meinit.nl
          organization_name: Some Corporation
          organizational_unit_name: Department X
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for ca

# set ca_init: 'yes' to create CA
ca_init: yes

# ca_own_root: 'yes' if you want to have yout own root CA.
# if no, set ca_certificate_path manually
ca_own_root: yes

# A passphrase for the CA key.
ca_passphrase: SuP3rS3creT

# The common name for the CA.
ca_common_name: example.com

# Other details for the CA.
ca_country_name: NL
ca_email_address: robert@meinit.nl
ca_organization_name: Very little
ca_organizational_unit_name: Even less
ca_state_or_province_name: Utrecht
ca_locality_name: Utrecht

# There are two formats to request a key and certificate:
# 1. With details: (Includes `name:`)
# ca_requests:
#   - name: certificate1.example.com
#     passphrase: S3creT
#     cipher: aes256
#
# 2. Without details: (Does not include `name:`)
# ca_requests:
#   - "{{ ansible_fqdn }}"

# You can also mix these formats:
# ca_requests:
#   - name: certificate1.example.com
#     passphrase: S3creT
#     cipher: aes256
#   - "{{ ansible_fqdn }}"

# Where to publish the certificates, normally a webserver location.
# If not specified, certificates will not be published.
# {{ httpd_data_directory }} is inheritted from the role robertdebock.httpd.
ca_publication_location: "{{ httpd_data_directory | default('/tmp') }}/pub"

# Where do the certificates need to be stored? By default the distribution
# preferred locations are used (see `vars/main.yml`, under `_ca_openssl_path`.
# If you need a CA certificate somewhere else, simple use something like this:
# ca_openssl_path: /my/preferred/path
ca_openssl_path: "{{ _ca_openssl_path[ansible_os_family] | default(_ca_openssl_path['default']) }}"
```

## [Requirements](#requirements)

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.buildtools
- robertdebock.epel
- robertdebock.httpd
- robertdebock.openssl
- robertdebock.python_pip

```

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/ca.png "Dependency")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|alpine|all|
|el|7, 8|
|debian|buster, bullseye|
|fedora|31, 32|
|opensuse|all|
|ubuntu|focal, bionic, xenial|

The minimum version of Ansible required is 2.9, tests have been done to:

- The previous version.
- The current version.
- The development version.

## [Exceptions](#exceptions)

Some variarations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| Alpine | Depencency ansible-role-httpd does not work on Alpine. |


## [Testing](#testing)

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-ca) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-ca/issues)

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

- [grzs](https://github.com/grzs)

## [Author Information](#author-information)

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
