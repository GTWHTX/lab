# lab

Ansible base setup for ten Kubuntu lab machines (`lab-01` … `lab-10`). `lab-01` is the NFS and management node.

## Requirements

- Ansible on the control machine
- SSH key at `~/.ssh/id_ed25519`, already on each host
- User `kse` with passwordless sudo (the playbook installs this)
- `hosts` listing each machine as `IP name`

`ansible.cfg` points at `inventory.ini`, disables host-key checking, and runs 15 forks.

## Base setup

```bash
ansible-playbook setup.yml                 # all machines
ansible-playbook setup.yml --limit lab-07  # one machine
```

`setup.yml` does the following:

- Sets the hostname to the inventory name
- Writes `hosts` into `/etc/hosts`
- Grants `kse` passwordless sudo
- Installs git, curl, wget, vim, htop, tmux, nfs-common, figlet, lolcat, neofetch, openssh-server, and ufw
- Stops waiting for the network at boot
- Sets swappiness and inotify limits
- Mounts `lab-01:/opt/soft` read-only on every machine except the server
- Sources `/opt/soft/lab.sh` from `/etc/bash.bashrc` when that file exists
- Forces Chromium into incognito and installs uBlock Origin Lite
- Allows SSH through ufw and enables the firewall (default deny)

## Extra packages

`install.yml` runs only the tasks whose variables you pass:

```bash
ansible-playbook install.yml -e pkg=htop
ansible-playbook install.yml -e deb=some-package.deb   # from /opt/soft/
ansible-playbook install.yml -e cmd='echo hello'
```
