# Disable/Re-enable Schedules in Ansible Automation Platform

## Description

This project contains a playbook to disable all active schedules in AAP 2.4 and above and a playbook to re-enable those schedules after upgrades, system maintenance, etc.

The intent of this project is to disable schedules to allow for an AAP upgrade, or other system maintenance, and enable the same schedules once the work is complete.

> **_NOTE:_** Any schedules that were already disabled prior to running `disable_schedules.yml` will stay disabled after running `enable_schedules.yml`

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

### Installing

* Once you have pulled down the playbooks, you will need to update the `controller_url` and `controller_token` vars in the group_vars/all.yml file with your controller URL and API token, respectively

### Executing program

* The ansible-navigator.yml file specifies the AAP 2.6 execution environment. If you are running this on a lower environment you will need to call the EE during the run command:

```sh
ansible-navigator run disable_schedules.yml --eei registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel9
```

* To disable all active schedules

```sh
ansible-navigator run disable_schedules.yml
```

* To re-enable all schedules disabled in the previous step

```sh
ansible-navigator run enable_schedules.yml
```

## Authors

* Karyn Cassio (<kcassio@redhat.com>)
* Cara Cannarozzi (<ccannaro@redhat.com>)
