Ansible Role: Shibboleth
============================

![molecule](https://github.com/elan-ev/shibboleth/actions/workflows/molecule.yml/badge.svg)

This Ansible role installs Shibboleth and optional Nginx Shibboleth module. Nginx Shibboleth module needs Shibboleth with FastCGI support. Prebuild packages for RedHat based systems has not enabled it by default. This is the reason, why we rebuild the Shibboleth rpm package. This role will take care of it by enabling the [ELAN e.V. Shibboleth repository](https://elan-ev.github.io/shibboleth-nginx-repo/) and installing `shibboleth-fastcgi` package (instead of `shibboleth`) if needed. The build Nginx Shibboleth module is also living in this repository.

## Dependencies

This role has no dependencies.

## Role Variables

- `shibboleth_fastcgi_support_enable`
    - Enable FastCGI support for Shibboleth by setting the value to true. Shibboleth FastCGI is used by the Nginx Shibboleth module at least.
    - Default: false
- `shibboleth_nginx_module_install`
    - Should the Nginx Shibboleth module be installed? Then set the value to true, otherwise to false. Shibboleth Nginx module needs Shibboleth with FastCGI support. Enbaling this option implicit set shibboleth_fastcgi_support_enable to true.
    - Default value: false

## Example Playbook

Example of how to configure and use the role:

```yaml
- hosts: servers
  become: true
  tasks:
    - name: Install Nginx
      ansible.builtin.package:
        name: nginx
      handlers: Restart nginx

    - ansible.builtin.include_role:
        name: elan.shibboleth
      vars:
        shibboleth_nginx_module_install: true

    - name: Configure Shibboleth
      ansible.builtin.copy:
        args...
      notify: Restart shibboleth
    
    - name: Configure Nginx
      ansible.builtin.copy:
        args...
      notify: Reload nginx
```

## Development Environment

For linting and role development you can use the tools defined in [development requirements](.dev_requirements.txt).
You can quickly install them in a python virtual environment like this:

```sh
# Create a virtual environment
python -m venv venv
# Activate the virtual environment
. venv/bin/activate
# Install the dependencies
pip install -r .dev_requirements.txt
```

E.g. you can then install the ansible requirements or run the linter (`yamllint -c .yamllint . && ansible-lint`).

For development and testing you can use [molecule](https://molecule.readthedocs.io/).
With podman as driver you can install it like this – preferably in a virtual environment:

```bash
pip install -r .dev_requirements.txt
```

Then you can run the tests with:

```bash
molecule test
```

You may find some usefull arguments like `-p <plattform>` or `--destroy never` in the molecule documentation by running

```bash
molecule test --help
```

## License
[BSD-3-Clause](LICENSE)

## Author
[ELAN e.V.](https://elan-ev.de)