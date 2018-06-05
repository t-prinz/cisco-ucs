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


