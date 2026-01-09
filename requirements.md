# Requirements

The following collection must be installed in order for the satellite configuration playbook to run.

- [redhat.satellite](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/)

Generally speaking there are two ways to install this, both of which are documented below.

* Installation via RPM

* Installation via Ansible Automation Hub

# Preferred method - installing redhat.satellite using Ansible Automation Hub and requirements.yml

The easiest method for installing the required collection is to specify Ansible Automation Hub in [ansible.cfg](ansible.cfg) and then use the [requirements.yml](requirements.yml) file:

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

`ansible-galaxy collection install -r requirements.yml`

By default, this will install into `~/.ansible/collections/ansible_collections/redhat/satellite/`

If the above is not possible, you can install the collection from RPM.

# Installing the redhat collection from RPM

Install the `ansible-collection-redhat-satellite` RPM on the server where you are running Ansible.  The RPM is available in the Satellite repository, for example on Satellite 6.18 it's in the `satellite-6.18-for-rhel-9-x86_64-rpms` repo.
