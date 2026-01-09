# Satellite installation requirements

The following collections must be installed in order for the satellite installer playbook to run.

- [redhat.satellite_operations](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite_operations/)
- [redhat.rhel_system_roles](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/rhel_system_roles/)
- [community.general](https://galaxy.ansible.com/ui/repo/published/community/general/)
- [ansible.posix](https://galaxy.ansible.com/ui/repo/published/ansible/posix)

Generally speaking there are two ways to install redhat.rhel_system_roles, both of which are documented below:

* Installation via RPM

* Installation via Ansible Automation Hub

The redhat.satellite_operations collection is available from Ansible Automation Hub.

The community.general and ansible.posix collections are available from Ansible Galaxy.

# Prefered method - installing redhat.satellite_operations, redhat.rhel_system_roles, community.general and ansible.posix collections using requirements-install.yml

The easiest method for installing the required collections is to specify both Ansible Automation Hub and Ansible Galaxy in the same [ansible.cfg](ansible.cfg)  configuration file and install all the collections in one go using the [requirements-install.yml](requirements-install.yml) file:

1.  Update your [ansible.cfg](ansible.cfg) file to include:

```
[galaxy]
server_list = automation_hub,galaxy

[galaxy_server.automation_hub]
url=https://console.redhat.com/api/automation-hub/content/published/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=CHANGEME

[galaxy_server.galaxy]
url=https://galaxy.ansible.com/
```

2. Replace `CHANGEME` with a valid token which can be obtained at the following URL: https://console.redhat.com/ansible/automation-hub/token.  See also see [Getting started with Red Hat APIs](https://access.redhat.com/articles/3626371)

3. Install the collections as the current user:

`ansible-galaxy collection install -r requirements-install.yml`


By default, this will install into `~/.ansible/collections/ansible_collections` directory.

If the above is not possible, you can install the collections individually using any of the combinations below.

# Alternative Methods for installing the collections

## Installing the redhat collections from RPM

### redhat.rhel_system_roles

Install the `rhel-system-roles` RPM on the server where you are running Ansible.  The RPM is available in the appstream repository, for example on RHEL 9 it's in the `rhel-9-for-x86_64-appstream-rpms` repo.

## Installing the redhat collections from Ansible Automation Hub

To install from Ansible Automation Hub by performing the following three tasks.

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

3. Install the collections as the current user:

`ansible-galaxy collection install redhat.satellite_operations redhat.rhel_system_roles`

By default, this will install into `~/.ansible/collections/ansible_collections/redhat/satellite_operations` and `~/.ansible/collections/ansible_collections/redhat/rhel_system_roles`

## Installing the community.general and ansible.posix collections from Ansible Galaxy

To install from Ansible Automation Hub by performing the following two tasks.

1.  Update your [ansible.cfg](ansible.cfg) file to include:

```
[galaxy]
server_list = galaxy

[galaxy_server.galaxy]
url=https://galaxy.ansible.com/
```

2. Install the collections as the current user:

`ansible-galaxy collection install community.general and ansible.posix`

By default, this will install into `~/.ansible/collections/ansible_collections/community/general` and `~/.ansible/collections/ansible_collections/ansible/posix`
