# cisco-ucs

This repository includes a couple of examples showing how to setup and use Ansible/Ansible Tower with
the Cisco UCS modules.

# Reference Material

For the purposes of these examples, the Cisco UCS Emulator was used, which can be obtained here:

https://communities.cisco.com/docs/DOC-71877

Download the User Guide from this site:

https://communities.cisco.com/servlet/JiveServlet/download/71877-10-141193/b_Cisco_UCS_Platform_Emulator_User_Guide_Release_3_1_2e_PE1.pdf

The following Cisco Communities site has a useful Ansible Lab Guide:

Cisco Communities site: https://communities.cisco.com/docs/DOC-73997

Ansible Lab Guide (link also on Communities site): https://communities.cisco.com/servlet/JiveServlet/download/73997-5-148749/Ansible%20Lab%20Guide.docx

# Ansible/Ansible Tower Control System Preparation

```
sudo pip install ucsmsdk
cd /var/tmp
git clone https://github.com/ciscoucs/ucsm_apis
cd ucsm_apis
make install
cd /var/tmp
rm -rf ucsm_apis
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

# Cisco UCS Ansible Roles

Reference:  https://galaxy.ansible.com/CiscoUcs/ucs/

```
git clone https://github.com/ciscoucs/ansible-role-ucs CiscoUcs.ucs
```

# Ansible Tower Setup

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
and create the corresponding group and host(s).

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
