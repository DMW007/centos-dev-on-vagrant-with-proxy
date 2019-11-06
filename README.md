# CentOS Dev/Testing environment on Vagrant with Proxy

Vagrantfile to setup a ready-to-go CentOS Linux VM on Vagrant using VirtualBox. Supports usage behind corporate proxys. It's designed to do some quick tests out of the box, without to spend a lot of manual work.

## Get started

Copy the example vars file: `cp vars-example.yml vars.yml`
Now customize the file to your needs (see next section).

The VM could be created with `vagrant up`.

## Customize `vars.yml`

### `private_ip`

The IP on your internal network interface from VirtualBox. You shood look at _File_ > _Host-only network manager_ to choose an ip outside of DHCPs range. You can use this IP to connect on this machine using SSH or access services running on the machine.

### `vm_memory` and `vm_cpu_cores`

Specifies the memory in MB and the amount of cores. Should be adjusted, depending on how much memory the host have and the requirement of your test-setup.

### `proxy_enabled` and `proxy_cert_host`

Only required when you're using an proxy server that uses MITM to inspect https traffic. By setting ` proxy_enabled`` to true, specify any reachable https server in your network in `proxy_cert_host`. We will fetch the root certificate from this server to insert it in CentOS system-wide store.

## Re-build

If you made any changes to the `Vagrantfile` or the `vars.yml`, a rebuild is required. Please make sure that the (may existing) VM doesn't contain any data you need that is not stored outside the machine. Then run the following commands to delete and re-create the VM:

```bash
vagrant destroy -f; vagrant up --provision
```
