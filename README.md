ca
=========

[![Build Status](https://travis-ci.org/robertdebock/ansible-role-ca.svg?branch=master)](https://travis-ci.org/robertdebock/ansible-role-ca)

Install and configure a certificate authority on your system.

Example Playbook
----------------

This example is taken from `molecule/default/playbook.yml`:
```yaml
---
- name: Converge
  hosts: all
  gather_facts: no
  become: yes

  vars:
    ca_requests:
      - name: my_ca.example.com
        passphrase: WoNtT3L1
        cipher: aes256
        country_name: NL
        email_address: robert@meinit.nl
        organization_name: Some Corporation
        organizational_unit_name: Department X

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.buildtools
    - role: robertdebock.epel
    - role: robertdebock.python_pip
    - role: robertdebock.httpd
    - role: robertdebock.ca

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

ca_requests:
  - name: certificate1.example.com
    passphrase: S3creT
    cipher: aes256
  - name: certificate2.example.com

# Where to publish the certificates, normally a webserver location.
# If not specified, certificates will not be published.
# {{ httpd_data_directory }} is inheritted from the role robertdebock.httpd.
ca_publication_location: "{{ httpd_data_directory | default('/tmp') }}/pub"

# Where do the certificates need to be stored? By default the distribution
# preferred locations are used (see `vars/main.yml`, under `_ca_openssl_path`.
# If you need a CA certificate somewhere else, simple use something like this:
# ca_openssl_path: /my/preferred/path
ca_openssl_path: "{{ _ca_openssl_path[ansible_distribution] | default(_ca_openssl_path['default']) }}"

# The names of the generated files are set to a sane default, but if you need
# to change the for your application, you can overwrite the values. These paths
# are relative to `ca_openssl_path`.
ca_private_path: private
ca_certs_path: certs
ca_privatekey_path: "{{ ca_private_path }}/ca.pem"
ca_csr_path: ca.csr
ca_certificate_path: "{{ ca_certs_path }}/ca.crt"

# To update packages this role places on the system, set `ca_package_state` to `latest`.
ca_package_state: present

# Some Docker containers do not allow managing services, rebooting and writing
# to some locations in /etc. The role skips tasks that will typically fail in
# Docker. With this parameter you can tell the role to -not- skip these tasks.
ca_ignore_docker: yes

```

Requirements
------------

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the last 3 release of Ansible.)

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

This role has been tested against the following distributions and Ansible version:

|distribution|ansible 2.6|ansible 2.7|ansible devel|
|------------|-----------|-----------|-------------|
|alpine-edge*|yes|yes|yes*|
|alpine-latest|yes|yes|yes*|
|archlinux|yes|yes|yes*|
|centos-6|no|no|no*|
|centos-latest|yes|yes|yes*|
|debian-latest|yes|yes|yes*|
|debian-stable|yes|yes|yes*|
|debian-unstable*|yes|yes|yes*|
|fedora-latest|yes|yes|yes*|
|fedora-rawhide*|yes|yes|yes*|
|opensuse-leap|yes|yes|yes*|
|opensuse-tumbleweed|yes|yes|yes*|
|ubuntu-devel*|yes|yes|yes*|
|ubuntu-latest|yes|yes|yes*|
|ubuntu-rolling|yes|yes|yes*|

A single star means the build may fail, it's marked as an experimental build.

Testing
-------

[Unit tests](https://travis-ci.org/robertdebock/ansible-role-ca) are done on every commit and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-ca/issues)

To test this role locally please use [Molecule](https://github.com/metacloud/molecule):
```
pip install molecule
molecule test
```

To test on Amazon EC2, configure [~/.aws/credentials](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/credentials.html) and `export AWS_REGION=eu-central-1` before running `molecule test --scenario-name ec2`.

There are many specific scenarios available, please have a look in the `molecule/` directory.

Run the [ansible-galaxy](https://github.com/ansible/galaxy-lint-rules) and [my](https://github.com/robertdebock/ansible-lint-rules) lint rules if you want your change to be merges:

```shell
git clone https://github.com/ansible/ansible-lint.git /tmp/ansible-lint
ansible-lint -r /tmp/ansible-lint/lib/ansiblelint/rules .

git clone https://github.com/robertdebock/ansible-lint /tmp/my-ansible-lint
ansible-lint -r /tmp/my-ansible-lint/rules .
```

License
-------

Apache-2.0


Author Information
------------------

[Robert de Bock](https://robertdebock.nl/) <robert@meinit.nl>
