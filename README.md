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

2. Replace `CHANGEME` with a valid token which can be obtained at the following URL: https://console.redhat.com/ansible/automation-hub/token.  See also see [Getting started with Red Hat APIs](https://access.redhat.com/articles/3626371)

3. Install the collection as the current user:

`ansible-galaxy collection install redhat.satellite`

By default, this will install into `~/.ansible/collections/ansible_collections/redhat/satellite/`

## Inventory structure

The inventory is currently setup for my lab environment which consists of two servers, roughly mirroring a 'production' environment at `satellite.london.example.com` and a 'development' environment at `satellite.nyc.example.com`.  Using the standard [Ansible precedence](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#understanding-variable-precedence) rules, configuration can be performed for common settings via [inventories/group_vars/Example_Organization](inventories/group_vars/Example_Organization/) and individual configuration via [inventories/host_vars](inventories/host_vars/).  For example, my development server could match production but mirror a subset of the repositories and host a subset of the content views.  The development environment overrides would be set via  [inventories/host_vars](inventories/host_vars/) in the appropriate inventory directory.

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

Satellite login credentials can be placed in [inventories/inventory.yml](inventories/inventory.yml).  These can be unencrypted (not recommended) or encrypted with ansible-vault.  The quickest way to generate an encrypted record is via the `ansible-vault encrypt_string` command.

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

To eliminate the need to provide the Vault password each run, a vault password file can be defined in [ansible.cfg](ansible.cfg) and secured locally via filesystem permissions.  This repository contains a sample entry in [ansible.cfg](ansible.cfg) for achieving this.


## Running the playbook against a single server

`ansible-playbook --limit satellite.london.example.com -i inventories/ satellite-configuration.yml`

## Naming Conventions

Although not required, a standard naming convention for Satellite resources provides support teams with a consistent experience and allows automation and scripting tools to use pattern matching and regular expressions to audit and manipulate the environment.  The PDF guide [10 Steps to Build an SOE: How Red Hat Satellite 6 Supports Setting up a Standard Operating Environment](https://access.redhat.com/articles/1585273) suggests a possible naming convention.  This repository uses some naming conventions from that guide along with some opinionated modifications.

### Overview

Avoid special characters such as `@ ! " ' , ? # : ;` in object names, as this allows objects to be manipulated easily in CSV and YAML formats.  If there is an established naming convention (eg for a product) consider replicating it.  Although Satellite objects can often be renamed, you may find that items such as `labels` cannot be changed after they are created.  As an example, if a Content View is initially created as `RHEL 9` (the name) with label `RHEL_9` and the name is subsequently updated in the UI, you might end up with a name `cv-rhel-9` but with label `RHEL_9`.  This can cause confusion.  It is advisable to avoid this if possible by configuring your resources in the yaml files and performing some analysis before implementing the configuration.

### Product Name

| Naming Convention | Examples |
|       :---:       |  :---:   |
| < vendor or upstream project > - < product name or purpose > [ - < RHEL release > ] | MariaDB |
| | EPEL |
| | HPE |

Note that it is now possible to have repositories with different RHEL releases and different GPG keys under a single Product.  This means, for example, that it's no longer required to have an EPEL-8 product and an EPEL-9 product.  As such, the 'RHEL release' may not be required.

### GPG Keys

| Naming Convention | Examples |
|       :---:       |  :---:   |
| RPM-GPG-KEY- <vendor or product>  [ - < version or release > ] | RPM-GPG-KEY-EPEL-8 |
| | RPM-GPG-KEY-EPEL-9 |
| | RPM-GPG-KEY-MariaDB |
| | RPM-GPG-KEY-SPP |

This above naming convention is used (with uppercase characters) to mirror filenames typically placed in `/etc/pki/rpm-gpg`.

### Repository Label
| Naming Convention | Examples |
|       :---:       |  :---:   |
< vendor > [ - < product >  ] [ - < product version > ] - for -< os version > - < architecture > - < repo type > | mariadb-11-2-for-rhel-8-x86_64-rpms |
| | mariadb-11-4-for-rhel-8-x86_64-rpms |
| | epel-8-for-rhel-8-x86_64-rpms |
| | epel-9-for-rhel-9-x86_64-rpms |
| | spp-gen9-for-rhel-8-x86_64-rpms |
| | spp-gen10-for-rhel-8-x86_64-rpms |
| | spp-gen11-for-rhel-8-x86_64-rpms |
| | spp-gen9-for-rhel-9-x86_64-rpms |
| | spp-gen10-for-rhel-9-x86_64-rpms |
| | spp-gen11-for-rhel-9-x86_64-rpms |

The naming standard here attempts to mirror the RHEL repo naming standards as of RHEL 8 and RHEL 9.

### Lifecycle Environment

| Naming Convention | Examples |
|       :---:       |  :---:   |
| dev-test/stage/prod-dr | dev-test |
| | stage |
| | prod-dr |

These names should match the environments used in your organization.

### Content-View

| Naming Convention | Examples |
|       :---:       |  :---:   |
| cv - < os/app > - < profile name > [ - < version or release > ] | cv-os-rhel-6 |
| | cv-os-rhel-7 |
| | cv-os-rhel-8 |
| | cv-os-rhel-9 |
| | cv-os-rhel-monthly |
| | cv-app-mariadb |
| | cv-app-spp |

The naming convention described depends on whether Content Views will be consumed by clients directly, or whether Composite Content Views are used.  The use of `os` and `app` allows for operating system views to be deployed at a different cadence to application ones.  A common approach may to be have a monthly or quarterly `os` patching content view.  Organizations may have different opinions as to whether applications should be updated at the same time.

### Composite-Content-View

| Naming Convention | Examples |
|       :---:       |  :---:   |
| ccv - < biz/infra > - < role name > [ - < version or release > ] | ccv-biz-soe-monthly |
| | ccv-biz-database |
| | ccv-infra-containerhost |

###  Activation-Key

| Naming Convention | Examples |
|       :---:       |  :---:   |
| act - < lifecycle environment > - < biz/infra/os > - < role name > | act-dev-test-biz-soe |
| | act-stage-biz-soe |
| | act-prod-dr-biz-soe |
| | act-dev-test-biz-database |
| | act-dev-test-biz-database |
| | act-prod-dr-biz-database |
| | act-stage-infra-containerhost |

### Host-Group


### Provisioning Template

| Naming Convention | Examples |
|       :---:       |  :---:   |
| < org > < provisioning template name > | Example Organization Kickstart Default |

### Partition Tables

| Naming Convention | Examples |
|       :---:       |  :---:   |
| ptable - < org > - < ptable name > | ptable-example-organization-gitserver |

### Resources

| Naming Convention | Examples |
|       :---:       |  :---:   |
| < org > - < compute resource name > - < location > | example-organisation-rhelosp-london |
