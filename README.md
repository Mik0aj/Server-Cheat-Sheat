# Server-Cheat-Sheat

1. [SSH](README.md#SSH)

    * [Key authentication](README.md#Key-authentication)

2. [Proxmox](README.md#Proxmox)

    * [Ip change](README.md#Ip-change)
    * [GPU passthrough](README.md#GPU-passthrough)

### SSH

#### Key authentication
1. Prepare key pair. SSH will ask for passphrase, it's not nessercary.
```
ssh-keygen -t rsa

```
2. Copy the public half of the key pair to server
```
ssh-copy-id -i ~/.ssh/id_rsa.pub user@server
```

### Proxmox

#### Ip change
By default proxmox don't have `ifconfig`. It doesn't really matter because `ip addr` won't work either. To change ip in proxmox you'll have to modify correct file and reboot.
```
nano /etc/network/interfaces
```


#### GPU passthrough
