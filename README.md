# Disable/Re-enable processes in Ansible Automation Platform

## Description

This project contains playbooks to disable and re-enable all active schedules and instances in AAP 2.4 and above. This can be used for any maintenance tasks against AAP where the system resources would need to be taken down for a period of time. This could include activities like upgrades, system maintenance, etc.

The intent of this project is to disable schedules and instances, etc. to allow for an AAP upgrade, or other system maintenance, and enable the same schedules once the work is complete.

> **_NOTE:_** Any schedules that were previously disabled prior to running `disable_schedules.yml` task will remain disabled after running `enable_schedules.yml`

## Getting Started

### Dependencies

* You must have [ansible-navigator](https://docs.ansible.com/projects/navigator/installation/) installed to run these playbooks
* Required collections:
  * ansible.controller - Included in the EE's
* Required Execution Environment:
  * Based on AAP version:
    * 2.4: registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel9:latest
    * 2.5: registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel9:latest
    * 2.6: registry.redhat.io/ansible-automation-platform-26/ee-supported-rhel9:latest

Example for how to pull the EE from Red Hat Registry using your login name and password:

  ```sh
  podman login registry.redhat.io
  podman pull registry.redhat.io/ansible-automation-platform-26/ee-supported-rhel9:latest
  ```

### Installing

* Once you have pulled down the playbooks, you will need to update the `controller_url` variable in the group_vars/all.yml file with your controller URL.
* In the vaults directory there is an all.yml file. This is where you will update the `controller_token` variable with the API token.
* Next encrypt your vaults/all.yml file (not the full directory) <https://docs.ansible.com/projects/ansible/latest/vault_guide/vault_managing_passwords.html>

```sh
ansible-vault encrypt all.yml
```

* Remember the password you used to encrypt the file.

### Executing program

* Extra variables to pass at execution time:
  * Environment: 
    * `-e "env=dev"` 
    * Where you specify 3-letter abbreviation for the environment
  * Action: 
    * `-e "disable_env=true"`
    * `-e "enable_env=true"`

* The ansible-navigator.yml file specifies the AAP 2.6 execution environment. If you are running this on a lower environment you will need to call the EE during the run command:

* To disable schedules and instances separately in AAP 2.4, run the following:

```sh
ansible-navigator run playbooks/manage_schedules.yml --eei registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel9 -i localhost, -e "env=dev" -e "disable_env=true"

ansible-navigator run playbooks/manage_instances.yml --eei registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel9 -i localhost, -e "env=dev" -e "disable_env=true"
```

* To disable all active schedules and instances in AAP 2.5 and above. 
* IDs of the scheduled jobs will be kept in a file in the playbook directory.

```sh
ansible-navigator run playbooks/manage_schedules.yml --ask-vault-password -i localhost, -e "env=dev" -e "disable_env=true"
ansible-navigator run playbooks/manage_instances.yml --ask-vault-password -i localhost, -e "env=dev" -e "disable_env=true"
```

* Or disable all with one playbook

```sh
ansible-navigator run playbooks/disable_env.yml --ask-vault-password -i localhost, -e "env=dev" -e "disable_env=true"
```

* To re-enable all previously active schedules and instances in AAP 2.5 and above, individually, run the following.

```sh
ansible-navigator run playbooks/manage_schedules.yml --ask-vault-password -i localhost, -e "env=dev" -e "enable_env=true"
ansible-navigator run playbooks/manage_instances.yml --ask-vault-password -i localhost, -e "env=dev" -e "enable_env=true"
```

* Or enable all with one playbook

```sh
ansible-navigator run playbooks/enable_env.yml --ask-vault-password -i localhost, -e "env=dev" -e "enable_env=true"
```

## Authors

* Karyn Cassio (<kcassio@redhat.com>)
* Cara Cannarozzi (<ccannaro@redhat.com>)
