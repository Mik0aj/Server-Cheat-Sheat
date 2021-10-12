# Server-Cheat-Sheat

1. [SSH](README.md#SSH)

    * [Key authentication](README.md#Key-authentication)

2. [Proxmox](README.md#Proxmox)

    * [Ip change](README.md#Ip-change)
    * [GPU passthrough](README.md#GPU-passthrough)
    * [Remote desktop](README.md#Remote-desktop)

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

#### Remote desktop
I tested few remote desktop clients. It isn't as good as having everything hooked up directly to a server. You will encounter some artifacts while using it. For best expirience use wired connection or at least 5GHz wifi.

Low intensity streaming(desktop configuration):

1. Proxmox VNC - web clien in proxmox has console tab. It uses VNC to stream desktop. 
2. Remote Desktop Viewer - installed by default on Ubuntu it's okay to quickly connect.
3. Remmina - similar to previous one has a lot more options.

High intensity streaming(mainly games):

1. Rainway - good for games, can also stream desktop. High refresh rate make it almost as good as sitting in front of machine. Requires free account. I had a lot of problems connecting to host. Has web client.
2. Parsec - same story as Rainway. Good for games and requires account exept it works flawlessly. Has web client.
3. Steam remote play - main inted with this program is to stream games. Although with some workarounds you can stream desktop too. I didn't have much luck with it. Encountered "steam input error", I managed to succesfully stream my desktop once. On pair with parsec and rainway. Also requires free account.
