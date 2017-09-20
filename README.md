# SSH keybased authentication setup

## Summary
This role manages local users and SSH-keys in a centralized way.

## Prerequisite
A team should store their public SSH-keys in any Git repo which is accessible without authorization from Ansible host. All the keys filenames should match the mask _*_*.pub_.

## Variables
| Variable             | Required  | Default | Description                                 |
|----------------------|-----------|---------|---------------------------------------------|
| keys_git_repo        | yes       | —       | Git URL pointing to a repo with public keys |
| keys_git_repo_branch | yes       | master  | Git repo branch                             |
| user_groups          | no        | —       | List of groups users will be added to       |
| default_home_folder  | yes       | /home   | Default location of user "home" folder      |

## Dependencies
None.

In some cases, you should manually install _python2_ (absent in Ubuntu 16.04) and _libselinux-python_. Example playbook:
```
---
- hosts: all
  become: yes
  gather_facts: no
  tasks:
    - name: install python2
      raw: test -e /usr/bin/python || (apt update && apt install -y python-minimal)
      changed_when: false
    - name: gather facts
      setup:
    - name: install libselinux-python binary for ansible to work
      yum: name=libselinux-python state=present
      when: ansible_pkg_mgr == 'yum' and not ansible_selinux

- hosts: all
  roles:
    - ansible-role-ssh-keybased-auth
```

### Usage
Consider running in a dry-run mode first to see the expected changes.
```
$ ansible-playbook -i "<host>," playbook.yml --check --diff
```

## Platforms
All supported platforms are listed in _molecule/default/molecule.yml_.
