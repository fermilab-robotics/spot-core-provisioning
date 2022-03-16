# Spot CORE

## Prep

The following instructions assume you are starting from a fresh install from a Boston Dynamics Spot CORE Ubunutu image.

### Ethernet connection

Use outer-most Ethernet port meant for lidar for the local connection. I used the MacOS feature of sharing my internet connection with another NIC. On the CORE, the lidar port will need to be switched to DHCP to allow the host computer to assign an IP like a router.

### ssh access

_Note_: This must be done by hand before using Ansible. Password login doesn't work for Ansible.

Start by testing ssh access to the Core using password login. While you are logged in, set the permission on `~spot/.ssh/authorized_keys` to allow a new key to be added; `sudo chown spot:spot ~spot/.ssh`.

Start by copying a public key from your host system to the `spot` user `.ssh/authorized_keys` on the Core using `cat ~/.ssh/id_rsa.pub | ssh spot@[spot ip] 'cat >> ~spot/.ssh/authorized_keys'`. Using password authentication with Ansible requires a third-party package that is not trivially installed.

### Enable root access without a password

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

### Prevent disapproved access

Disable VNC access to the CORE.

### Enable Kerberos access

__Note:__ We should consider how to sync time. CORE uses Spot as an NTP server to keep systems in sync, but Kerberos requires the time to be close to the KDC time. We could possibly use CORE to rebroadcast Fermi NTP broadcast, it depends on if Spot will listen to CORE's NTP broadcasts.

This installed the Kerberos user libraries and downloads the Fermi `krb5.conf` file to `/etc/krb5.conf`.

It also ensures the timezone is set to America/Chicago.

#### Request keytab

Fill out the SNOW form for [Access to Kerberized Machines](https://fermi.servicenowservices.com/wp?id=evg_sc_cat_item&sys_id=9a0304dee4809000863885ce73245e77).

You will receive an email and a phone call with instructions on how to generate the keytab.

#### Install keytab

_Note_: This requires Kerberos utilities to be installed. You need to have the `kadmin` command line tool available.

Installing the keytab should be done manually since it requires root to use an auto-generated one-time use password.

_AS ROOT_:

```bash
kadmin -q "ktadd host/spot.dhcp.fnal.gov" -p host/spot.dhcp.fnal.gov
```

This will prompt for the password the service desk provided.
