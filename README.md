# cisco-ucs

From:  https://github.com/CiscoUcs/ucsm-ansible/blob/master/README.md
sudo pip install ucsmsdk

# This came from the workbook
cd /var/tmp
git clone https://github.com/ciscoucs/ucsm_apis
cd ucsm_apis
make install
cd /var/tmp
rm -rf ucsm_apis
# End of workbook info

cd /usr/share
git clone https://github.com/ciscoucs/ucsm-ansible
chown -R awx.awx ucsm-ansible

# This came from the workbook
cd ucsm-ansible
python install.py
# End of workbook info

Add the following to /etc/ansible/ansible.cfg in the [defaults] block

# Customization for Cisco UCS modules
library = /usr/share/ucsm-ansible/library
module_utils = /usr/share/ucsm-ansible/utils
# Customization for Cisco UCS modules

From:  https://galaxy.ansible.com/CiscoUcs/ucs/

git clone https://github.com/ciscoucs/ansible-role-ucs CiscoUcs.ucs

# Tower setup

Create a new Project
Name: UCS
Organization: Default
SCM Type: Git
SCM URL: <url>
SCM Update Options Checked:
Clean
Update on Launch

Create a new Inventory
Name: UCS Emulator
Organization: Default

Create a new Source within the UCS Emulator inventory.  This will reference the inventory file in the project
and create the corresponding group and host(s).
Name: UCS github project
Source: Sourced from a Project
Project: UCS
Inventory File: inventory
Update Options Checked:
Overwrite
Overwrite Variables
Update on Project Change

Create a new Credential
Name: UCS Vault Credential
Organization: Default
Credential Type: Vault
Vault Password: <password>

Create a new Job Template
Name: UCS - NTP Configuration
Inventory: UCS Emulator
Project: UCS
Playbook: ntp.yml
Credentials:
Type Machine - Demo Credential (Blank)
Type Vault - UCS Vault Credential
