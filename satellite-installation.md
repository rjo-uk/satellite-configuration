# satellite-configuration

An ansible playbook and sample configuration for installing Red Hat Satellite.

## Requirements

The [redhat.satellite_operations](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite_operations/) collection MUST be installed in order for this playbook to work.

Generally speaking there are two ways to install this collection:

* Install the `ansible-collection-redhat-satellite` RPM which is available in the Satellite repository
* Install from Ansible Automation Hub by:
1.  Update your [ansible.cfg](ansible.cfg) file to include:

```
[galaxy]
server_list = automation_hub

[galaxy_server.automation_hub]
url=https://console.redhat.com/api/automation-hub/content/published/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=CHANGEME
```

2. Replace `CHANGEME` with a valid token which can be obtained at the following URL: https://console.redhat.com/ansible/automation-hub/token.  See also see [Getting started with Red Hat APIs](https://access.redhat.com/articles/3626371)

3. Install the collection as the current user:

`ansible-galaxy collection install redhat.satellite_operations`

By default, this will install into `~/.ansible/collections/ansible_collections/redhat/satellite_operations/`

## SETUP - TODO

See [lab_inventories/single_org_multi_satellite/host_vars/satellite.london.example.com/satellite_installer.yml](lab_inventories/single_org_multi_satellite/host_vars/satellite.london.example.com/satellite_installer.yml) for sample configuration.

See [satellite-installation.yml](satellite-installation.yml) for details.  The playbook does the following:

* Registers the server to Red Hat
* Sets the required repositories
* Installs the required packages
* Configures the firewall
* Updates all packages
* Runs the Satellite installer
* Configures the Satellite Cloud Connector

## Running the installer

Sample execution, logging in using SSH as `root` to perform the installation and prompting for the password.

```
ansible-playbook -i inventories satellite-installation.yml --limit satellite.london.example.com -D -u root -k 
```
