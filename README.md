# Server-Cheat-Sheat

1. [SSH](README.md#SSH)

    * [Key authentication](README.md#Key-authentication)
    * [Bandwidth test](README.md#Bandwidth-test)

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

#### Bandwidth test

SSH command to test the speed beetween two computers. The command prints a 1GB dummy file full of zeros to stdout on the remote server, which is printed (transferred) via SSH to stdout of the local server and then locally piped to /dev/null. Requires only SSH so you don't need to install any additional packages.
```
ssh user@server 'dd if=/dev/zero bs=1GB count=1 2>/dev/null' | dd of=/dev/null status=progress
```

### Proxmox

#### Ip change
By default proxmox don't have `ifconfig`. It doesn't really matter because `ip addr` won't work either. To change ip in proxmox you'll have to modify correct file and reboot.
```
nano /etc/network/interfaces
```

#### GPU passthrough

Tested setup:

* Proxmox VE 7.0-11

* Gigabyte H410M S2

* Intel i5-10400F

* NVIDIA GeForce GTX 1050 Ti

* 16GB of RAM

* 1TB ssd

As far as I know to passthrough GPU, your cpu have to support VT-x and VT-d. VT-x is necessary for Intel systems in order to run virtualization with KVM. VT-d makes direct access to a PCI device (such as GPU) possible for guest systems with the help of the Input/Output Memory Management Unit (IOMMU). Before attempting check of your hardware supports these technologies. I would also recommend chosing motherboard with wake on lan feature (WoL). Unfortunetly mine does not support WoL so I have to turn on my server manually.

1. Make sure that VT-x, VT-d technologies are enabled in motherboard's BIOS.
2. You'll have to modify `/etc/default/grub`
    ```
    GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on intel_iommu=pt video=efifb:off video=vesafb:off"
    ```
    Let's break it down:
    
    * `quiet` shows only splash screen while booting
    
    * `intel_iommu=on` enables IOMMU
    
    * `intel_iommu=pt` allows for SR-IOV, which lets you split your GPU into smaller virtual GPUs, currently i am not using it, and probably never will. By default GTX 10xx family doesn't support it. There are workarounds but with 1050ti I came to conclusion that it is not worth even trying.
    * `video=efifb:off` and `video=vesafb:off` make sure that the GPU framebuffer is not being used. If I understand it correctly it disables drivers for primary GPU. So that host wont use GPU while booting. **IMPORTANT Make sure that these two kernel parameters look like this "video=efifb:off video=vesafb:off" otherwise the may not work**

3. Run `update-grub` and Reboot. **IMPORTANT Make sure that changes took affect**. It is very important to check if grub actually updated. to do that run ```cat /proc/cmdline``` If you get output similar to this ```BOOT_IMAGE=/boot/vmlinuz-5.11.22-4-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on intel_iommu=pt video=efifb:off video=vesafb:off``` everything is working correctly.
4. You'll have to modify `/etc/modules` and add ```vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd```
5. You'll have to create `/etc/modprobe.d/iommu_unsafe_interrupts.conf` with `options vfio_iommu_type1 allow_unsafe_interrupts=1` inside.
6. You'll have to create `/etc/modprobe.d/kvm.conf` with `options kvm ignore_msrs=1` inside.
7. Now it's time to blacklist GPU drivers. Create `/etc/modprobe.d/blacklist.conf` and add 'blacklist driver'. It should look simillar to this.```blacklist radeon
blacklist nouveau
blacklist nvidia
blacklist snd_hda_intel```

#### Remote desktop
I tested few remote desktop clients. It isn't as good as having everything hooked up directly to a server. You will encounter some artifacts while using it. For best expirience use wired connection or at least 5GHz wifi.

Low intensity streaming(desktop configuration):

1. Proxmox VNC - web client in proxmox has console tab. It uses VNC to stream desktop. 
2. Remote Desktop Viewer - installed by default on Ubuntu it's okay to quickly connect.
3. Remmina - similar to previous one has a lot more options.

High intensity streaming(mainly games):

1. Rainway - good for games, can also stream desktop. High refresh rate make it almost as good as sitting in front of machine. Requires free account. I had a lot of problems connecting to host. Has web client.
2. Parsec - same story as Rainway. Good for games and requires account exept it works flawlessly. Has web client.
3. Steam remote play - main inted with this program is to stream games. Although with some workarounds you can stream desktop too. I didn't have much luck with it. Encountered "steam input error", I managed to succesfully stream my desktop once. On pair with parsec and rainway. Also requires free account.
