# Spot CORE

## Prep

### Ethernet connection

Use outer-most Ethernet port meant for lidar for the local connection. I used the MacOS feature of sharing my internet connection with another NIC. On the CORE, the lidar port will need to be switched to DHCP to allow the host computer to assign an IP.

### ssh access

Start by copying a public key to the `spot` user `.ssh/authorized_keys` using `cat .ssh/id_rsa.pub | ssh spot@192.168.2.2 'sudo cat >> ~spot/.ssh/authorized_keys'`. Using password authentication with Ansible requires a third-party package that is not trivially installed.

### Enable root access

To enable root access for Ansible, we need to allow the spot user to sudo without a password.

```bash
    sudo visudo
```

```text
    spot ALL=(ALL) NOPASSWD: ALL
```

## Tasks

```bash
ansible-playbook -i hosts.yml main.yml
```

The IP is likely to be the same, but it's good verify that the IP in `hosts.yml` is what is assigned to the CORE.

### System update

Make sure the system has the latest updates including distribution updates.

## Prevent disapproved access

Disable VNC access to the CORE.

## Enable Kerberos access

__Note:__ We should consider how to sync time. CORE uses Spot as an NTP server to keep systems in sync, but Kerberos requires the time to be close to the KDC time. We could possibly use CORE to rebroadcast Fermi NTP broadcast, it depends on if Spot will listen to CORE's NTP broadcasts.

This installed the Kerberos user libraries and downloads the Fermi `krb5.conf` file to `/etc/krb5.conf`.

It also ensures the timezone is set to America/Chicago.

### Install keytab

Installing the keytab should be done manually since it requires root to use an auto-generated one-time use password.

*AS ROOT*:

```bash
kadmin -q "ktadd host/spot.dhcp.fnal.gov" -p host/spot.dhcp.fnal.gov
```
