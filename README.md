### 1 Enable epel repositories
```bash
sudo yum install epel-release
```

### 2 Install drbd 
```bash
yum install drbd-utils.x86_64
```
### 3 Create meta data storage
```bash
dd if=/dev/zero of=/var/drbd-meta bs=1M count=128
losetup /dev/loop0 /var/drbd-meta
```
### 4 Configure drbd(both nodes)
```bash
vi /etc/drbd.d/drbd_res0.res
```
resource drbd_res0 {
syncer {rate 50M;}
device     /dev/drbd0;
disk       /dev/xvdf;
meta-disk  /dev/loop0[0];
on linux1 {
    address    172.31.45.186:7788;
}
on linux2 {
    address    172.31.34.113:7788;
}
}

### 5 Create the meta-data disk for our specific resource(both nodes)
```bash
drbdadm create-md drbd_res0
```

### 5.1 Start drbd (on both nodes)
```bash
service drbd start 
```

### 5.2 Setup drbd primary node !!!this needs to be done initially if data is inconsistent
```bash
drbdadm --overwrite-data-of-peer primary drbd_res0
```
### Otherwise
```bash
drbdadm primary drbd_res0
```

### 6 Filesystem and mounting
```bash
yum install xfsprogs
mkfs.ext4 /dev/drbd0
```

### 6.1 Create directory and mount drbd device
```bash
mkdir /data
mount /dev/drbd0 /data
```


### TEST
### On the primary node:
```bash
echo "This is a test" > /data/test.txt
umount  /mnt/data
drbdadm secondary drbd_res0
```

### On the secondary node:
```bash
drbdadm primary drbd_res0
mkdir /mnt/data
mount /dev/drbd0 /mnt/data
cat /data/test.txt
```

### Resizing drbd
### 1 change all drbd volume size primary/secondary
### 2 Resize drbd partition
```bash
sudo resize2fs /dev/drbd0
```

### 3 Resize drbd 
```bash
drbdadm resize drbd_res0
```
