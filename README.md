# VPN
Ansible role to setup VPN (Iptables/OpenVPN)

> Tested only with Ubuntu 22

### Prerequsites

Create instance with Ubuntu 22 LTS with enough resources like: 1 CPU / 2GB memory and public IP.
Allow security group with rules
- tcp/22
- tcp/443 (default mode of OpenVPN server from role)

### How to:
- Create a base `ansible.cfg`
```
[defaults]
private_key_file              = ...
host_key_checking             = False
roles_path                    = roles:./roles
remote_user                   = root
become                        = True
become_method                 = sudo
allow_world_readable_tmpfiles = True
retry_files_enabled           = False
force_valid_group_names       = ignore

[ssh_connection]
pipelining = True
ssh_args   = -o ControlMaster=auto -o ControlPersist=30m -o ConnectionAttempts=100 -o UserKnownHostsFile=/dev/null
```
- Create inventory file with group of hosts `vpn`:

```
[vpn]
<server_name> ansible_host=1.2.3.4 ansible_python_interpreter=/usr/bin/python3

```

- Create group vars to override default roles vars, for example

> To help a little obfuscate openvpn traffic from assholes, run it in TCP/443 mode like a simple TLS web server

```
---
openvpn_server_name: "awesome_vpn"
openvpn_server_addr: "{{ ansible_host }}"
openvpn_server_conf_port: 443
openvpn_server_conf_proto: tcp
```

- Run playbook
```
ansible-playbook playbooks/vpn.yml -i inventory
```
