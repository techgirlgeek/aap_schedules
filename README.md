# Disable/Re-enable processes in Ansible Automation Platform

## Description

This project contains playbooks to disable and re-enable all active schedules and instances in AAP 2.4 and above. This can be used for any maintenance tasks against AAP where the system resources would need to be taken down for a period of time. This could include activities like upgrades, system maintenance, etc.

The intent of this project is to disable schedules and instances, etc. to allow for an AAP upgrade, or other system maintenance, and enable the same schedules once the work is complete.

> **_NOTE:_** Any process that was previously disabled when the disabling scripts are run will remain disabled when the enabling scripts are run

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

  # The following command only needs to happen once.
  # Or it will be pulled during the playbook run.
  podman pull registry.redhat.io/ansible-automation-platform-26/ee-supported-rhel9:latest
  ```

### Installing

* Once you have pulled down the playbooks, you will need to update the `controller_url` variable in the inventories/{{ env }}/hosts.yml file with your controller URL.
* In the vaults directory there is an all.yml file as well as a file for different environments. This is where you will update the `controller_token` variable with the API token.
* Next encrypt your vaults/dev.yml (or applicable environment) file (not the full directory)
<https://docs.ansible.com/projects/ansible/latest/vault_guide/vault_managing_passwords.html>

```sh
ansible-vault encrypt all.yml
```

* Remember the password you used to encrypt the file.

## Executing playbooks

### Managing Schedules and Instances

#### Variables and Information

* Extra variables to pass at execution time:
  * Action only used when running manage-instances or manage-schedules playbooks:
    * `-e "disable_env=true"`
    * `-e "enable_env=true"`
  * When disabling schedules, if there are more than 250 enabled schedules, use the following extra variable.
    * `-e "aap_schedules_count=500"`
    * Where 500 is an example of how many enabled schedules were found.

* The ansible-navigator.yml file specifies the AAP 2.6 execution environment. If you are running this on a lower environment you will need to call the EE during the run command:
  * `--eei registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel9`

* IDs of the scheduled jobs will be kept in a file in the playbook directory.

* In the below examples replace `{{ env }}` with the environment you are working against.

#### Ansible Automation Platform 2.4

To disable schedules and instances, individually, in AAP 2.4 run the following:

```sh
ansible-navigator run playbooks/manage_schedules.yml --eei registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel9 --ask-vault-password -i inventories/{{ env }}/hosts.yml, -e "disable_env=true"

ansible-navigator run playbooks/manage_instances.yml --eei registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel9 --ask-vault-password -i inventories/{{ env }}/hosts.yml, -e "disable_env=true"
```

#### Ansible Automation Platform 2.5 and above

To disable all active schedules and instances, individually, in AAP 2.5 and above

```sh
ansible-navigator run playbooks/manage_schedules.yml --ask-vault-password -i inventories/{{ env }}/hosts.yml -e "disable_env=true"

ansible-navigator run playbooks/manage_instances.yml --ask-vault-password -i inventories/{{ env }}/hosts.yml -e "disable_env=true"
```

Or disable all with one playbook

```sh
ansible-navigator run playbooks/disable_env.yml --ask-vault-password -i inventories/{{ env }}/hosts.yml
```

To re-enable all previously active schedules and instances in AAP 2.5 and above individually, run the following.

```sh
ansible-navigator run playbooks/manage_schedules.yml --ask-vault-password -i inventories/{{ env }}/hosts.yml -e "enable_env=true"

ansible-navigator run playbooks/manage_instances.yml --ask-vault-password -i inventories/{{ env }}/hosts.yml -e "enable_env=true"
```

Or enable all with one playbook

```sh
ansible-navigator run playbooks/enable_env.yml --ask-vault-password -i inventories/{{ env }}/hosts.yml
```

### Managing Replicas

Default replicas is set to 0, to scale down replicas.
Be sure to take note of how many replicas were running before disabling.

#### Replicas - Ansible Automation Platform 2.4

```sh
# Ansible Automation Platform 2.4
# Scales down existing web and task pods
ansible-navigator run playbooks/manage_replicas.yml -i inventories/{{ env }}/hosts.yml --ask-vault-password
```

```sh
# Ansible Automation Platform 2.4
# Scales replicas back up.
ansible-navigator run playbooks/manage_replicas.yml -i inventories/{{ env }}/hosts.yml -e "spec_replicas=1" -e "task_replicas=1" --ask-vault-password
```

#### Set Idle - Ansible Automation Platform 2.5 and above

* Default value is `idle_aap=true`

```sh
# Ansible Automation Platform 2.5 and above
# Set AAP to idle. Scales down existing web and task pods
ansible-navigator run playbooks/manage_replicas.yml -i inventories/{{ env }}/hosts.yml
```

```sh
# Ansible Automation Platform 2.5 and above
# Unset idle AAP. Scales replicas back up.
ansible-navigator run playbooks/manage_replicas.yml -i inventories/{{ env }}/hosts.yml -e "idle_aap=false" --ask-vault-password
```

## Authors

* Karyn Cassio (<kcassio@redhat.com>)
* Cara Cannarozzi (<ccannaro@redhat.com>)
