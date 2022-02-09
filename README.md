# Spot CORE

## Prep

### Ethernet connection

Use outer-most Ethernet port meant for lidar for the local connection. I used the MacOS feature of sharing my internet connection with another NIC. On the CORE, the lidar port will need to be switched to DHCP to allow the host computer to assign an IP.

### ssh access

Start by copying a public key to the `spot` user `.ssh/authorized_keys` using `cat .ssh/id_rsa.pub | ssh spot@192.168.2.2 'sudo cat >> ~spot/.ssh/authorized_keys'`. Using password authentication with Ansible requires a third-party package that is not trivially installed.

## System update

```bash
ansible-playbook -i hosts.yml main.yml
```

The IP is likely to be the same, but it's good verify that the IP in `hosts.yml` is what is assigned to the CORE.
