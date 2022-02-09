# ACSys Proxy

## TLS Certification generation

The command below will generate a private key and CSR.

```bash
ansible-playbook -i hosts.yml gen.yml
```

The output will print the CSR to paste to the service desk.

## Certificate installation

The command below will unzip and copy the Fermi provided certificate zip.

_Note:_ Move the provided zip, star_fnal_gov*.zip, to the files folder.

```bash
ansible-playbook -i host.yml install.yml
```
