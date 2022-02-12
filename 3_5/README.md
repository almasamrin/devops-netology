# 1.
Интересно :)

# 2.
Жесткие ссылки имею одинаковые права и владельца, так как это указатель/интерфейс на один и тот же файл.

# 3.
vagrant destroy

Vagrant.configure("2") do |config|
 	config.vm.box = "bento/ubuntu-20.04"
	config.vm.network "forwarded_port", guest: 19999, host: 19999
	config.vm.provider "virtualbox" do |vb|
		vb.memory = 2048
		vb.cpus = 2
		lvm_experiments_disk0_path = "/tmp/lvm_experiments_disk0.vmdk"
		lvm_experiments_disk1_path = "/tmp/lvm_experiments_disk1.vmdk"
		vb.customize ['createmedium', '--filename', lvm_experiments_disk0_path, '--size', 2560]
		vb.customize ['createmedium', '--filename', lvm_experiments_disk1_path, '--size', 2560]
		vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk0_path]
		vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk1_path]
  end
 end
vagrant up
vagrant ssh

# 4. 
vagrant@vagrant:/dev$ sudo fdisk /dev/sdb

Command (m for help): F
Unpartitioned space /dev/sdb: 2.51 GiB, 2683305984 bytes, 5240832 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes

Start     End Sectors  Size
 2048 5242879 5240832  2.5G

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-5242879, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-5242879, default 5242879): +2G

Created a new partition 1 of type 'Linux' and of size 2 GiB.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p):
Using default response p.
Partition number (2-4, default 2):
First sector (4196352-5242879, default 4196352):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-5242879, default 5242879):

Created a new partition 2 of type 'Linux' and of size 511 MiB.

Command (m for help): p
Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x5cd12ff4

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1          2048 4196351 4194304    2G 83 Linux
/dev/sdb2       4196352 5242879 1046528  511M 83 Linux

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

# 5. 
sudo sfdisk -d /dev/sdb | sudo sfdisk /dev/sdc

Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Created a new DOS disklabel with disk identifier 0x5cd12ff4.
/dev/sdc1: Created a new partition 1 of type 'Linux' and of size 2 GiB.
/dev/sdc2: Created a new partition 2 of type 'Linux' and of size 511 MiB.
/dev/sdc3: Done.

New situation:
Disklabel type: dos
Disk identifier: 0x5cd12ff4

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1          2048 4196351 4194304    2G 83 Linux
/dev/sdc2       4196352 5242879 1046528  511M 83 Linux

The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

# 6. 
sudo mdadm --create --verbose /dev/md1 --level=mirror --raid-devices=2 /dev/sdb1 /dev/sdc1 --metadata=0.90
mdadm: size set to 2097088K
mdadm: array /dev/md1 started.

# 7.
sudo mdadm --create --verbose /dev/md2 --level=0 --raid-devices=2 /dev/sdb2 /dev/sdc2
mdadm: chunk size defaults to 512K
mdadm: partition table exists on /dev/sdb2
mdadm: partition table exists on /dev/sdb2 but will be lost or
       meaningless after creating array
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md2 started.

# 8.
sudo pvcreate /dev/md1
  Physical volume "/dev/md1" successfully created.
sudo pvcreate /dev/md2
  Physical volume "/dev/md2" successfully created.

# 9.
sudo vgcreate vgmd1 /dev/md1 /dev/md2
  Volume group "vgmd1" successfully created

# 10.
sudo lvcreate -n my_custom_lv -L 100M vgmd1 /dev/md2
  Logical volume "my_custom_lv" created.

# 11.
sudo mkfs.ext4 -F /dev/vgmd1/my_custom_lv
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done

# 12.
```
sudo mkdir -p /mnt/vgmd1/my_custom_lv
sudo mount /dev/vgmd1/my_custom_lv /mnt/vgmd1/my_custom_lv
# chown someuser:somegroup /mnt/vgmd1/my_custom_lv
```

# 13.
sudo wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /mnt/vgmd1/my_custom_lv/test.gz
--2022-02-11 17:19:39--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 22278842 (21M) [application/octet-stream]
Saving to: ‘/mnt/vgmd1/my_custom_lv/test.gz’

/mnt/vgmd1/my_custom_lv/test.gz                             100%[========================================================================================================================================>]  21.25M  1.90MB/s    in 18s

2022-02-11 17:19:57 (1.20 MB/s) - ‘/mnt/vgmd1/my_custom_lv/test.gz’ saved [22278842/22278842]

# 14.
lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 32.3M  1 loop  /snap/snapd/12704
loop1                       7:1    0 55.4M  1 loop  /snap/core18/2128
loop2                       7:2    0 70.3M  1 loop  /snap/lxd/21029
loop3                       7:3    0 43.4M  1 loop  /snap/snapd/14549
loop4                       7:4    0 55.5M  1 loop  /snap/core18/2284
loop5                       7:5    0 61.9M  1 loop  /snap/core20/1328
loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md1                     9:1    0    2G  0 raid1
└─sdb2                      8:18   0  511M  0 part
  └─md2                     9:2    0 1018M  0 raid0
    └─vgmd1-my_custom_lv  253:1    0  100M  0 lvm   /mnt/vgmd1/my_custom_lv
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md1                     9:1    0    2G  0 raid1
└─sdc2                      8:34   0  511M  0 part
  └─md2                     9:2    0 1018M  0 raid0
    └─vgmd1-my_custom_lv  253:1    0  100M  0 lvm   /mnt/vgmd1/my_custom_lv

# 15.
vagrant@vagrant:~$ gzip -t /mnt/vgmd1/my_custom_lv/test.gz
vagrant@vagrant:~$ echo $?
0

# 16.
sudo pvmove /dev/md2 /dev/md1
  /dev/md2: Moved: 12.00%
  /dev/md2: Moved: 100.00%

lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 32.3M  1 loop  /snap/snapd/12704
loop1                       7:1    0 55.4M  1 loop  /snap/core18/2128
loop2                       7:2    0 70.3M  1 loop  /snap/lxd/21029
loop3                       7:3    0 43.4M  1 loop  /snap/snapd/14549
loop4                       7:4    0 55.5M  1 loop  /snap/core18/2284
loop5                       7:5    0 61.9M  1 loop  /snap/core20/1328
loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md1                     9:1    0    2G  0 raid1
│   └─vgmd1-my_custom_lv  253:1    0  100M  0 lvm   /mnt/vgmd1/my_custom_lv
└─sdb2                      8:18   0  511M  0 part
  └─md2                     9:2    0 1018M  0 raid0
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md1                     9:1    0    2G  0 raid1
│   └─vgmd1-my_custom_lv  253:1    0  100M  0 lvm   /mnt/vgmd1/my_custom_lv
└─sdc2                      8:34   0  511M  0 part
  └─md2                     9:2    0 1018M  0 raid0

# 17.
sudo mdadm --fail /dev/md1 /dev/sdc1
mdadm: set /dev/sdc1 faulty in /dev/md1

# 18.
dmesg
md/raid1:md1: Disk failure on sdc1, disabling device.
md/raid1:md1: Operation continuing on 1 devices.

# 19.
vagrant@vagrant:~$ gzip -t /mnt/vgmd1/my_custom_lv/test.gz
vagrant@vagrant:~$ echo $?
0

# 20.