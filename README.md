ca
=========

<img src="https://docs.ansible.com/ansible-tower/3.2.4/html_ja/installandreference/_static/images/logo_invert.png" width="10%" height="10%" alt="Ansible logo" align="right"/>
<a href="https://travis-ci.org/robertdebock/ansible-role-ca"><img src="https://travis-ci.org/robertdebock/ansible-role-ca.svg?branch=master" alt="Build status" align="left"/></a>

Install and configure a certificate authority on your system.

<img src="https://img.shields.io/ansible/role/d/35543"/>
<img src="https://img.shields.io/ansible/quality/35543"/>

Example Playbook
----------------

This example is taken from `molecule/resources/playbook.yml`:
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  vars:
    ca_requests:
      - "{{ ansible_fqdn }}"
      - name: my_ca.example.com
        passphrase: WoNtT3L1
        cipher: aes256
        country_name: NL
        email_address: robert@meinit.nl
        organization_name: Some Corporation
        organizational_unit_name: Department X

  roles:
    - robertdebock.ca
```

The machine you are running this on, may need to be prepared.
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
    - role: robertdebock.httpd
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

Role Variables
--------------

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for ca

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

# There are two formats to request a key and certificate:
# 1. With details: (Includes `name:`)
# ca_requests:
#   - name: certificate1.example.com
#     passphrase: S3creT
#     cipher: aes256

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

# The names of the generated files are set to a sane default, but if you need
# to change the for your application, you can overwrite the values. These paths
# are relative to `ca_openssl_path`.
ca_private_path: private
ca_certs_path: certs
ca_privatekey_path: "{{ ca_private_path }}/ca.pem"
ca_csr_path: ca.csr
ca_certificate_path: "{{ ca_certs_path }}/ca.crt"
```

Requirements
------------

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.buildtools
- robertdebock.epel
- robertdebock.python_pip
- robertdebock.httpd

```

Context
-------

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/ca.png "Dependency")


Compatibility
-------------

This role has been tested on these [container images](https://hub.docker.com/):

|container|tag|allow_failures|
|---------|---|--------------|
|alpine|latest|no|
|alpine|edge|yes|
|debian|stable|yes|
|debian|unstable|yes|
|debian|latest|no|
|centos|7|no|
|centos|latest|no|
|fedora|latest|no|
|fedora|rawhide|yes|
|opensuse|latest|no|
|ubuntu|rolling|yes|
|ubuntu|devel|yes|
|ubuntu|latest|no|

This role has been tested on these Ansible versions:

- ansible~=2.8
- ansible~=2.9
- git+https://github.com/ansible/ansible.git@devel

The indicator '\~=' means [compatible with](https://www.python.org/dev/peps/pep-0440/#compatible-release). For example 'ansible\~=2.8' would pick the latest ansible-2.8, for example ansible-2.8.6.




Testing
-------

[Unit tests](https://travis-ci.org/robertdebock/ansible-role-ca) are done on every commit, pull request, release and periodically.

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

Modules
-------

This role uses the following modules:
```yaml
---
- copy
- file
- openssl_certificate
- openssl_csr
- openssl_privatekey
- package
- pip
```

License
-------

Apache-2.0


Author Information
------------------

[Robert de Bock](https://robertdebock.nl/)
