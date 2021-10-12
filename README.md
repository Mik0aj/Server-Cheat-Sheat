# Server-Cheat-Sheat

1. SSH

    * [Key authentication](https://github.com/Mik0aj/Server-Cheat-Sheat/edit/main/README.md#Key-authentication)

2. Proxmox

    * Ip change
    * GPU passthrough

### SSHs

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

#### GPU passthrough
