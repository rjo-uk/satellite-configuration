# satellite-configuration

Sample configuration for Red Hat Satellite

## Requirements

The [redhat.satellite](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/) collection MUST be installed in order for this playbook to work.

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

2. Replace `CHANGEME` with a valid token which can be obtained at the following URL: https://console.redhat.com/ansible/automation-hub/token.  See also see [access article](https://access.redhat.com/articles/3626371)


## Inventory structure

The inventory is currently setup for my lab environment which consists of two servers, roughly mirroring a 'production' environment at `satellite.london.example.com` and a 'development' environment at `nyc.london.example.com`.  Using the standard [Ansible precedence](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#understanding-variable-precedence) rules, configuration can be performed for common configuration via [inventories/group_vars/Example_Organization](inventories/group_vars/Example_Organization/) and individual configuration via [inventories/host_vars](inventories/host_vars/).  For example, my development server could match production but only mirror a subset of the repositories and host a subset of the content views.  The development environment overrides would be set via  [inventories/host_vars](inventories/host_vars/) in the appropriate inventory directory.

```
.
├── .vault_pass.txt
├── README.md
├── ansible.cfg
├── inventories
│   ├── group_vars
│   │   └── Example_Organization
│   │       ├── satellite_activation_keys.yml
│   │       ├── satellite_auth_sources_ldap.yml
│   │       ├── satellite_compute_profiles.yml
│   │       ├── satellite_compute_resources.yml
│   │       ├── satellite_content_credentials.yml
│   │       ├── satellite_content_view_version_cleanup_keep.yml
│   │       ├── satellite_content_views.yml
│   │       ├── satellite_convert_to_rhel.yml
│   │       ├── satellite_domains.yml
│   │       ├── satellite_hostgroups.yml
│   │       ├── satellite_lifecycle_environments.yml
│   │       ├── satellite_locations.yml
│   │       ├── satellite_manifest.yml
│   │       ├── satellite_operatingsystems.yml
│   │       ├── satellite_organizations.yml
│   │       ├── satellite_products.yml
│   │       ├── satellite_provisioning_templates.yml
│   │       ├── satellite_settings.yml
│   │       ├── satellite_subnets.yml
│   │       └── satellite_sync_plans.yml
│   ├── host_vars
│   │   ├── satellite.london.example.com
│   │   │   ├── satellite_activation_keys.yml
│   │   │   └── satellite_hostgroups.yml
│   │   └── satellite.nyc.example.com
│   │       ├── satellite_content_views.yml
│   │       ├── satellite_hostgroups.yml
│   │       ├── satellite_locations.yml
│   │       ├── satellite_products.yml
│   │       └── satellite_sync_plans.yml
│   └── inventory.yml
└── satellite-configuration.yml
```

## Satellite login credentials

Satellite login credentials can be placed in [inventories/inventory.yml](inventories/inventory.yml).  These can be unenrcrypted (not recommended) or encrypyted with ansible-vault.  The quickest way to generate an encypted record is via the `ansible-vault encrypt_string` command.

For example:
```
ansible-vault encrypt_string mysecretpassword
New Vault password:
Confirm New Vault password:
Encryption successful
!vault |
          $ANSIBLE_VAULT;1.1;AES256
          37616631303933386465363436386166643136643130353635326664623532393462363233316331
          3131303633646239373832653939613963666265653462390a393463656239616136626565326364
          37666535356339366236663266386334643566343765663838396564323664663262646634333464
          6232613066616639660a393838613635633338643962333430363963646339613935343936343162
          30323163393836356132616431653930323031636566353936653238396166353763
```

To eliminate the need to provide the Vault password each run, a vault password file can be defined in [ansible.cfg](ansible.cfg) and secured locally via filesystem permissions.  This repository contains a sample entry in (ansible.cfg)[ansible.cfg] for achieving this.


## Running the playbook against a single server

`ansible-playbook --limit satellite.london.example.com -i inventories/ satellite-configuration.yml`
