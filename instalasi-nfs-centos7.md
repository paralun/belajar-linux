Prepare
-------

- VM1 : 192.168.133.6 --> server
- VM2 : 192.168.133.7 --> client

#### edit /etc/hosts
```
192.168.133.6   server
192.168.133.7   client
```

### Installation Server/Client

```
# yum install nfs-utils nfs-utils-lib
# systemctl enable rpcbind
# systemctl enable nfs-server
# systemctl enable nfs-lock
# systemctl enable nfs-idmap
# systemctl start rpcbind
# systemctl start nfs-server
# systemctl start nfs-lock
# systemctl start nfs-idmap
```

### Config NFS Server

```
# firewall-cmd --permanent --zone=public --add-service=nfs
# firewall-cmd --permanent --zone=public --add-service=mountd
# firewall-cmd --permanent --zone=public --add-service=rpc-bind
# firewall-cmd --reload
# mkdir /nfs
# chmod -R 775 /nfs
# chown -R nfsnobody:nfsnobody /nfs
```
#### edit /etc/exports
```
<DIRECTORY> <IP CLIENT>(rw,sync,no_root_squash,no_all_squash)
/nfs 192.168.133.7(rw,sync,no_root_squash,no_all_squash)
/nfs 192.168.133.7(sync,wdelay,hide,no_subtree_check,sec=sys,rw,secure,no_root_squash,all_squash)
/nfs *(ro,no_root_squash,async,no_subtree_check)

# exportfs -a
# exportfs -v 
```
#### NFS Options
```
ro : read only
rw : read write
sync :
async :
root_squash :
no_root_squash :
all_squash :
no_all_squash :
wdelay :
no_wdelay :
no_subtree_check :
```

### Config NFS Client

```
# mkdir -p /nfsdata
# showmount -e 192.168.133.6
# mount -t nfs 192.168.133.6:/nfs /nfsdata
# mount -t nfs -o rw,nosuid 192.168.133.6:/nfs /nfsdata
# df -h
```
#### Mounting NFS Shares at Boot Time
```
# vi /etc/fstab
192.168.133.6:/nfs  /nfsdata nfs default 0   0
192.168.133.6:/nfs /nfsdata nfs nosuid,rw,sync,hard,intr 0 0
# reboot
```
#### Disconnect Mounting
```
# umount /nfs
# umount -f /nfs
# umount -l /nfs
```