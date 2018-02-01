CONSUL SERVER
=========

Ansible role for latest consul server agent in docker container. Tested on:
 - Ubuntu 14.04 Trusty
 - Ubuntu 16.04 Xenial

Requirements
------------

Installed OS:
 - Ubuntu 14.04 Trusty
 - Ubuntu 16.04 Xenial

Role Variables
--------------

- CONSUL_DIR: /etc/consul.d # Directory of consul config files, will be mounted to container
- CONSUL_PORT: 8500 # Default consul port
- NGINX_DEFAULT_USER: admin # Nginx default user
- NGINX_DEFAULT_PASSWD: admin # Nginx default password
- USE_TLS: True # Force to use TLS, can be True or False
- CONSUL_TLS_DIR: "{{ CONSUL_DIR }}/tls" # Directory for TLS files (certificates and keys)
- CONSUL_CA_DIR: "{{ CONSUL_TLS_DIR }}/CA" # Directory for TLS Certificate Authority files (certificates, keys, CSRs)
- FIRST_CA_SERIAL: 000a # First serial number of certificate
- CERT_DATABASE_FILENAME: certindex # Database of signed certificates
- CERT_SERIAL_FILENAME: serial # Database of signed certificates serial numbers
- CA_NAME: af_consul_ca # Certificate Authority config file name (without extension)
- CA_CSR_FILENAME: ca.csr # Certificate Authority CSR file name
- CA_CERT_NAME: ca.cert # Certificate Authority self signed certificate file name
- CA_PRIVKEY_NAME: ca_privkey.key # Certificate Authority private key file name
- CA_KEYTYPE: rsa:2048 # Certificate Authority private key type
- CA_EXPIRE: 3650 # Period, after which Certificate Authority self signed certificate will expire (days)
- CA_COUNTRY: US # Certificate Authority self signed certificate subject - Country
- CA_STATE: CA # Certificate Authority self signed certificate subject - State
- CA_LOCALITY: Some City # Certificate Authority self signed certificate subject - Locality
- CA_ORG_NAME: Some Company # Certificate Authority self signed certificate subject - Organization
- CA_ORG_UNIT_NAME: Some dept # Certificate Authority self signed certificate subject - Organization unit name
- CA_COMMON_NAME: Consul Staging Server # Certificate Authority self signed certificate subject - Common name
- CA_EMAIL: dept@company.com # Certificate Authority self signed certificate subject - E-mail
- CSR_COUNTRY: US # Certificate Authority private key subject - Country
- CSR_STATE: CA # Certificate Authority private key subject - State
- CSR_LOCALITY: Some City # Certificate Authority private key subject - Locality
- CSR_ORG_NAME: Some Company # Certificate Authority private key subject - Organization
- CSR_ORG_UNIT_NAME: Some dept # Certificate Authority private key subject - Organization unit name
- CSR_COMMON_NAME: "\*.company.com" # Certificate Authority private key subject - Common name, can be wildcard
- CSR_EMAIL: dept@company.com # Certificate Authority private key subject - Email
- CONSUL_CSR_FILENAME: consul.csr # Consul server CSR filename
- CONSUL_CERT_FILENAME: consul.cert # Consul server certificate filename
- CONSUL_KEY_FILENAME: consul.key # Consul server private key filename
- CONSUL_KEYTYPE: rsa:1024 # Consul server private key type
- CONSUL_DC: global # Consul server Datacenter
- CONSUL_CONF_FILENAME: config.json # Consul server cofig filename

Dependencies
------------

Depends on latest Docker-CE installed:
 - winmasta.docker-latest
 - winmasta.nginx

Example Playbook
----------------

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
