# cisco-ucs

This repository includes a couple of examples showing how to setup and use Ansible/Ansible Tower with
the Cisco UCS modules.

# Reference Material

For the purposes of these examples, the Cisco UCS Emulator was used, which can be obtained from this site:

https://communities.cisco.com/docs/DOC-71877

This site includes the User Guide:

https://communities.cisco.com/servlet/JiveServlet/download/71877-10-141193/b_Cisco_UCS_Platform_Emulator_User_Guide_Release_3_1_2e_PE1.pdf

The following Cisco Communities site has a useful Ansible Lab Guide that provides the basis for the examples:

Cisco Communities site: https://communities.cisco.com/docs/DOC-73997

Ansible Lab Guide (link also on Communities site): https://communities.cisco.com/servlet/JiveServlet/download/73997-5-148749/Ansible%20Lab%20Guide.docx

Cisco UCS Ansible Roles can be obtained from this site:

https://galaxy.ansible.com/CiscoUcs/ucs/

```
git clone https://github.com/ciscoucs/ansible-role-ucs CiscoUcs.ucs
```

# Ansible/Ansible Tower Control System Preparation
References:
https://github.com/CiscoUcs/ucsm-ansible
https://github.com/CiscoUcs/ucsm_apis/blob/master/docs/installation.rst

```
sudo pip install ucsmsdk

cd /var/tmp
git clone https://github.com/ciscoucs/ucsm_apis
cd ucsm_apis
make install
cd /var/tmp
rm -rf ucsm_apis
```

# Test connectivity

```
# python
Python 2.7.14 (default, Apr 27 2018, 14:31:56) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-11)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> from ucsmsdk import ucshandle
>>> handle = ucshandle.UcsHandle(ip='http://192.168.122.34', username='admin', password='password')
>>> handle.login()
True
```

# Install Cisco UCS Ansible modules

```
cd /usr/share
git clone https://github.com/ciscoucs/ucsm-ansible
chown -R awx.awx ucsm-ansible
cd ucsm-ansible
python install.py
```

Add the following to /etc/ansible/ansible.cfg in the [defaults] block

```
library = /usr/share/ucsm-ansible/library
module_utils = /usr/share/ucsm-ansible/utils
```

# Using the example playbooks

Create a directory and copy the example roles directory

```
mkdir cisco-ucs
cd cisco-ucs
cp -r /usr/share/ucsm-ansible/roles .
```

Modify the inventory file (inventory)

```
[ucs]
ucs-emulator ucs_ip=X.X.X.X
```

Remove the existing vault file and create a new one (vars/svars)

```
rm vars/svars
ansible-vault create vars/svars

Contents:
---
ucs_username: admin
ucs_password: <password>
```

Update NTP server IP and timezone information (vars/default)

```
---
ucs_state: present
ntp_server: X.X.X.X
ucs_timezone: "America/Chicago"
```

Run the playbook to add a NTP server

```
ansible-playbook --ask-vault -i inventory ntp-add-server.yml
```

# Using the examples with Ansible Tower

Create a new Project

```
Name: UCS
Organization: Default
SCM Type: Git
SCM URL: <url>
SCM Update Options Checked:
  Clean
  Update on Launch
```

Create a new Inventory

```
Name: UCS Emulator
Organization: Default
```

Create a new Source within the UCS Emulator inventory.  This will reference the inventory file in the project
and dynamically create the corresponding group and host(s).

```
Name: UCS github project
Source: Sourced from a Project
Project: UCS
Inventory File: inventory
Update Options Checked:
  Overwrite
  Overwrite Variables
  Update on Project Change
```

Create a new Credential

```
Name: UCS Vault Credential
Organization: Default
Credential Type: Vault
Vault Password: <password>
```

Create a new Job Template

```
Name: UCS - NTP Configuration
Inventory: UCS Emulator
Project: UCS
Playbook: ntp.yml
Credentials:
  Type Machine - Demo Credential (Blank)
  Type Vault - UCS Vault Credential
```
