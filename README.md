# Project Title

Disable and Re-Enable AAP Schedules

## Description

This project contains a playbook to disable all active schedules in AAP 2.4 and a playbook to re-enable those schedules in AAP 2.6

The intent of this project is to disable schedules to allow for an AAP 2.4 to 2.6 upgrade and enable them once the upgrade is complete

Note that any schedules that were already disabled prior to running `disable_schedules.yml` will stay disabled after running `enable_schedules.yml`

## Getting Started

### Dependencies

* You must have [ansible-navigator](https://docs.ansible.com/projects/navigator/installation/) installed to run these playbooks

### Installing

* Once you have pulled down the playbooks, you will need to update the `controller_url` and `controller_token` vars in both playbooks with your URL and API token, respectively

### Executing program

* To disable all active schedules
```
ansible-navigator run disable_schedules.yml
```
* To re-enable all schedules disabled in the previous step
```
ansible-navigator run enable_schedules.yml
```

## Authors

Karyn Cassio and Cara Cannarozzi
