# satellite-installation

An ansible playbook and sample configuration for installing Red Hat Satellite.

## Requirements

The following collections must be installed in order for this playbook to work:

- [redhat.satellite_operations](https://console.redhat.com/ansible/automation-hub/collections/published/redhat/satellite_operations/)
- [redhat.rhel_system_roles](https://console.redhat.com/ansible/automation-hub/collections/published/redhat/rhel_system_roles/)
- [community.general](https://galaxy.ansible.com/ui/repo/published/community/general/)
- [ansible.posix](https://galaxy.ansible.com/ui/repo/published/ansible/posix)

For further details about installing these collections see: [installation-requirements.md](installation-requirements.md).

## Prepare the inventory

We can use the same inventory structure we defined in the [main readme](README.md) to tailor the installation to one or more satellites.  The file [lab_inventories/single_org_multi_satellite/host_vars/satellite.london.example.com/satellite_installer.yml](lab_inventories/single_org_multi_satellite/host_vars/satellite.london.example.com/satellite_installer.yml) shows a sample installer configuration.

## Running the installation

The playbook [satellite-installation.yml](satellite-installation.yml) performs the following tasks which can be selected by the tags `os_tasks` and `installer_tasks`.  As per Ansible standards, if tags are not specified then all tasks will be run.

| Tag | Task |
| :---: | :---: |
| os_tasks | Registers the server to Red Hat |
| os_tasks | Sets the required repositories |
| os_tasks | Installs the required packages |
| os_tasks | Configures the firewall |
| os_tasks | Disables Transparent Huge Pages (THP) |
| os_tasks | Updates all packages |
| os_tasks | Reboots the server if package updates or kernel changes require it and `satellite_installer_allow_reboot` is set to `true` |
| installer_tasks | Runs the Satellite installer |
| installer_tasks | Configures the Satellite Cloud Connector |

Sample execution, logging in using SSH as `root` to perform ALL *operating system* and *satellite installer* tasks:

```
ansible-playbook -i inventories satellite-installation.yml --limit satellite.london.example.com -D -u root -k
```

Sample execution, logging in using SSH as `root` to perform the *operating system* tasks:
```
ansible-playbook -i inventories satellite-installation.yml --limit satellite.london.example.com -D -u root -k --tags os_tasks
```
