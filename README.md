CONSUL SERVER
=========

Ansible role for latest consul server agent in docker container. UI behind nginx with basic authorization. Tested on:
  - Ubuntu 14.04 Trusty
  - Ubuntu 16.04 Xenial

Requirements
------------

Installed OS:
 - Ubuntu 14.04 Trusty
 - Ubuntu 16.04 Xenial

Role Variables
--------------

  - USE_TLS: True # Force to use TLS, can be True or False
  - CONSUL_DIR: /etc/consul.d # Directory of consul config files, will be mounted to container
  - CONSUL_CONF_FILENAME: config.json # Consul server cofig filename
  - CONSUL_PORT: 8500 # Default consul port
  - CONSUL_DC: global # Consul server Datacenter

Dependencies
------------

Depends on:
 - winmasta.docker-latest
 - winmasta.nginx
 - winmasta.CA

 Using winmasta.CA role is optionally if TLS required.

Example Playbook
----------------

Prequesites:

In order to use TLS for consul connections follow next steps:

  1. Ensure that variable `USE_TLS: True` in `defaults/main.yml` is set to `True`
  2. Uncomment `- src: winmasta.CA` in `requirements.yml` file.

If using TLS is not required do steps above viceversa:

  1. Ensure that variable `USE_TLS: False` in `defaults/main.yml` is set to `False`
  2. Comment `- src: winmasta.CA` in `requirements.yml` file.

To use this role:

  - create folder (in user $HOME folder in example below) and install role with dependencies from ansible-galaxy

```bash
cd ~/
mkdir consul-server
cd consul-server
ansible-galaxy install winmasta.consul-server --roles-path .
```

  - as soon as ansible-galaxy doesn't install role dependencies yet, you should do it manually

```bash
ansible-galaxy install -r winmasta.consul-server/requirements.yml --roles-path .
```

  - create file `hosts`, containing hostname(s) or IP address(es) of host(s), where you want to deploy consul server

```bash
echo "ENTER HOSTNAME OR IP" > hosts
```

  - create file `ansible.cfg` in current folder

```bash
cat > ansible.cfg << EOF
[defaults]
remote_user = root
host_key_checking = False
EOF
```

  - create playbook in current folder `main.yml` with content

```bash
cat > main.yml << EOF
---
- hosts: all
  gather_facts: no

  pre_tasks:

  - name: Install required packages
    raw: sudo apt-get update -y && sudo apt-get -y install python-simplejson python-pip
    changed_when: False

  - setup:

  roles:
    - winmasta.docker-latest
    - winmasta.nginx
    - { role: winmasta.CA, when: USE_TLS == True }
    - winmasta.consul-server

EOF
```

  - execute playbook `main.yml`

```bash
ansible-playbook -i hosts main.yml
```

License
-------

MIT
