#### NFS Server Example
```bash
NFS Server: 172.17.30.22
NFS client: 172.17.30.21
```

### Setup NFS Server
#### 1. Install nfs-server packages
```bash
yum install nfs-utils
```

#### 2. Create the shared folder and change the permissions
```bash
mkdir /nfs
chmod -R 755 /nfs
chown nfsnobody:nfsnobody /nfs
```

#### 3. Create the configuration file which has share details.
```bash
vi /etc/exports
/nfs    172.17.30.0/24(rw,sync,no_root_squash,no_all_squash)
``` 
##### You can add multiple shares in the same configuration file below is an example
```bash
/backup    172.17.30.0/24(rw,sync,no_root_squash,no_all_squash)
/data    172.17.30.0/24(rw,sync,no_root_squash,no_all_squash)
```
#### 4. Restart the nfs-server service to make the configuration effective.
```bash
service nfs-server restart
```

###Setup NFS client
#### 1. Install the NFS client package
```bash
yum install nfs-utils
```
#### 2. Create a folder where you want to mount the NFS share, In my case I’m going to create a folder called /nfs
```bash
mkdir -p /nfs
```
#### 3. Mount the nfs share
```bash
mount -t nfs 172.17.30.22:/mnt/data /mnt/data
```
#### 4. Configure fstab
```bash
vi /etc/fstab
172.17.30.22:/backup /nfs nfs defaults 0 0
```
