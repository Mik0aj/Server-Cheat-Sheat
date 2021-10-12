# Server-Cheat-Sheat

1. SSH

    1. [Key authentication](README.md#Key authentication)

2. Proxmox

    1. Ip change
    2. GPU passthrough

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