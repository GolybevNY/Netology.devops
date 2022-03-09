1. Узнайте о sparse (разряженных) файлах.

Выполнено, прочитана статья википедии.


2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?

Ответ:

Нет не могут. Жесткая ссылка и файл, для которой она создавалась имеют одинаковые inode. Поэтому жесткая ссылка имеет те же права доступа, владельца и время последней модификации, что и целевой файл. Различаются только имена файлов. Фактически жесткая ссылка это еще одно имя для файла. Жесткие ссылки нельзя создавать для директорий. Жесткая ссылка не может указывать на несуществующий файл.


3. Сделайте vagrant destroy на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.provider :virtualbox do |vb|
    lvm_experiments_disk0_path = "/tmp/lvm_experiments_disk0.vmdk"
    lvm_experiments_disk1_path = "/tmp/lvm_experiments_disk1.vmdk"
    vb.customize ['createmedium', '--filename', lvm_experiments_disk0_path, '--size', 2560]
    vb.customize ['createmedium', '--filename', lvm_experiments_disk1_path, '--size', 2560]
    vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk0_path]
    vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk1_path]
  end
end

Данная конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2.5 Гб.

Выполнено:

vagrant@vagrant:~$ lsblk

NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT

loop0                       7:0    0 70.3M  1 loop /snap/lxd/21029

loop2                       7:2    0 55.4M  1 loop /snap/core18/2128

loop3                       7:3    0 55.5M  1 loop /snap/core18/2284

loop4                       7:4    0 43.6M  1 loop /snap/snapd/14978

loop5                       7:5    0 61.9M  1 loop /snap/core20/1361

loop6                       7:6    0 67.9M  1 loop /snap/lxd/22526

sda                         8:0    0   64G  0 disk

├─sda1                      8:1    0    1M  0 part

├─sda2                      8:2    0    1G  0 part /boot

└─sda3                      8:3    0   63G  0 part

  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
  
sdb                         8:16   0  2.5G  0 disk

sdc                         8:32   0  2.5G  0 disk


4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.

Выполнено:

vagrant@vagrant:~$ sudo fdisk /dev/sdb


Welcome to fdisk (util-linux 2.34).

Changes will remain in memory only, until you decide to write them.

Be careful before using the write command.


Device does not contain a recognized partition table.

Created a new DOS disklabel with disk identifier 0x8e664ab6.


Command (m for help): m


Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table


Command (m for help): n

Partition type

   p   primary (0 primary, 0 extended, 4 free)
   
   e   extended (container for logical partitions)
   
Select (default p): p

Partition number (1-4, default 1): 1

First sector (2048-5242879, default 2048): 2048

Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-5242879, default 5242879): +2G


Created a new partition 1 of type 'Linux' and of size 2 GiB.


Command (m for help): n

Partition type

   p   primary (1 primary, 0 extended, 3 free)
   
   e   extended (container for logical partitions)
   
Select (default p): p

Partition number (2-4, default 2): 2

First sector (4196352-5242879, default 4196352): 4196352

Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-5242879, default 5242879): 5242879


Created a new partition 2 of type 'Linux' and of size 511 MiB.


Command (m for help): w

The partition table has been altered.

Calling ioctl() to re-read partition table.

Syncing disks.


Проверим:

vagrant@vagrant:~$ lsblk

NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0 70.3M  1 loop /snap/lxd/21029
loop2                       7:2    0 55.4M  1 loop /snap/core18/2128
loop3                       7:3    0 55.5M  1 loop /snap/core18/2284
loop4                       7:4    0 43.6M  1 loop /snap/snapd/14978
loop5                       7:5    0 61.9M  1 loop /snap/core20/1361
loop6                       7:6    0 67.9M  1 loop /snap/lxd/22526
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
└─sdb2                      8:18   0  511M  0 part
sdc                         8:32   0  2.5G  0 disk

5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.

Выполнено:

Сохраним дамп (описание компоновки устройства в текстовый файл) диска. Формат дампа подходит для последующего ввода на sfdisk:

vagrant@vagrant:~$ sudo sfdisk --dump /dev/sdb > sdb.dump


Проверим наличие файла дампа:

vagrant@vagrant:~$ ls -lAh | grep dump

-rw-rw-r-- 1 vagrant vagrant  182 Mar  7 13:52 sdb.dump


Восстановим бэкап на диск /dev/sdc:

vagrant@vagrant:~$ sudo sfdisk /dev/sdc < sdb.dump

Checking that no-one is using this disk right now ... OK


Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors

Disk model: VBOX HARDDISK

Units: sectors of 1 * 512 = 512 bytes

Sector size (logical/physical): 512 bytes / 512 bytes

I/O size (minimum/optimal): 512 bytes / 512 bytes


>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Created a new DOS disklabel with disk identifier 0x8e664ab6.

/dev/sdc1: Created a new partition 1 of type 'Linux' and of size 2 GiB.

/dev/sdc2: Created a new partition 2 of type 'Linux' and of size 511 MiB.

/dev/sdc3: Done.


New situation:

Disklabel type: dos

Disk identifier: 0x8e664ab6


Device     Boot   Start     End Sectors  Size Id Type

/dev/sdc1          2048 4196351 4194304    2G 83 Linux

/dev/sdc2       4196352 5242879 1046528  511M 83 Linux


The partition table has been altered.

Calling ioctl() to re-read partition table.

Syncing disks.


Проверим результат:

vagrant@vagrant:~$ sudo fdisk -l | grep sd

Disk /dev/sda: 64 GiB, 68719476736 bytes, 134217728 sectors

/dev/sda1     2048      4095      2048   1M BIOS boot

/dev/sda2     4096   2101247   2097152   1G Linux filesystem

/dev/sda3  2101248 134215679 132114432  63G Linux filesystem

Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors

/dev/sdb1          2048 4196351 4194304    2G 83 Linux

/dev/sdb2       4196352 5242879 1046528  511M 83 Linux

Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors

/dev/sdc1          2048 4196351 4194304    2G 83 Linux

/dev/sdc2       4196352 5242879 1046528  511M 83 Linux


6. Соберите mdadm RAID1 на паре разделов 2 Гб.

Выполнено:

vagrant@vagrant:~$ sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sd[bc]1

mdadm: Note: this array has metadata at the start and

    may not be suitable as a boot device.  If you plan to
    
    store '/boot' on this device please ensure that
    
    your boot-loader understands md/v1.x metadata, or use
    
    --metadata=0.90
    
mdadm: size set to 2094080K

Continue creating array? yes

mdadm: Defaulting to version 1.2 metadata

mdadm: array /dev/md0 started.

Просмотрим информацию о созданном массиве:

vagrant@vagrant:~$ sudo mdadm --query --detail /dev/md0

/dev/md0:

           Version : 1.2
     Creation Time : Mon Mar  7 14:39:37 2022
        Raid Level : raid1
        Array Size : 2094080 (2045.00 MiB 2144.34 MB)
     Used Dev Size : 2094080 (2045.00 MiB 2144.34 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Mon Mar  7 14:39:48 2022
             State : clean
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

Consistency Policy : resync


              Name : vagrant:0  (local to host vagrant)
              UUID : e0d2661c:02aa658a:d1bf05d6:e1c4c090
            Events : 17

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       1       8       33        1      active sync   /dev/sdc1



7. Соберите mdadm RAID0 на второй паре маленьких разделов.

vagrant@vagrant:~$ sudo mdadm --create --verbose /dev/md1 --level=0 --raid-devices=2 /dev/sd[bc]2

mdadm: chunk size defaults to 512K

mdadm: Defaulting to version 1.2 metadata

mdadm: array /dev/md1 started.


Просмотрим информацию о созданном массиве:

vagrant@vagrant:~$ sudo mdadm --query --detail /dev/md1

/dev/md1:

           Version : 1.2
     Creation Time : Mon Mar  7 14:46:23 2022
        Raid Level : raid0
        Array Size : 1042432 (1018.00 MiB 1067.45 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Mon Mar  7 14:46:23 2022
             State : clean
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

            Layout : -unknown-
        Chunk Size : 512K

Consistency Policy : none


              Name : vagrant:1  (local to host vagrant)
              UUID : 33a7727c:7e282f83:dc485195:10fddc38
            Events : 0

    Number   Major   Minor   RaidDevice State
       0       8       18        0      active sync   /dev/sdb2
       1       8       34        1      active sync   /dev/sdc2


8. Создайте 2 независимых PV на получившихся md-устройствах.

Выполнено:

vagrant@vagrant:~$ sudo pvcreate /dev/md0 /dev/md1

  Physical volume "/dev/md0" successfully created.
  
  Physical volume "/dev/md1" successfully created

Создайте общую volume-group на этих двух PV.

Выполнено:

vagrant@vagrant:~$ sudo vgcreate vg1 /dev/md1 /dev/md0

  Volume group "vg1" successfully created
  
Получилось:

vagrant@vagrant:~$ sudo pvdisplay

  --- Physical volume ---
  PV Name               /dev/sda3
  VG Name               ubuntu-vg
  PV Size               <63.00 GiB / not usable 0
  Allocatable           yes
  PE Size               4.00 MiB
  Total PE              16127
  Free PE               8063
  Allocated PE          8064
  PV UUID               sDUvKe-EtCc-gKuY-ZXTD-1B1d-eh9Q-XldxLf

  --- Physical volume ---
  PV Name               /dev/md1
  VG Name               vg1
  PV Size               1018.00 MiB / not usable 2.00 MiB
  Allocatable           yes
  PE Size               4.00 MiB
  Total PE              254
  Free PE               254
  Allocated PE          0
  PV UUID               yuuxB8-Z3xz-lOug-VXLX-gJiM-P4lq-j2skDA

  --- Physical volume ---
  PV Name               /dev/md0
  VG Name               vg1
  PV Size               <2.00 GiB / not usable 0
  Allocatable           yes
  PE Size               4.00 MiB
  Total PE              511
  Free PE               511
  Allocated PE          0
  PV UUID               KfPSB6-i1hU-msBu-9qBu-YpTx-Uk9N-o7yKpq
  

9. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.

Выполнено:

vagrant@vagrant:$ sudo lvcreate -L 100M vg1 /dev/md1

  Logical volume "lvol0" created.
  
Проверяем:

vagrant@vagrant:$ sudo lvs

  LV        VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  
  ubuntu-lv ubuntu-vg -wi-ao----  31.50g
  
  lvol0     vg1       -wi-a----- 100.00m
  

10. Создайте mkfs.ext4 ФС на получившемся LV.

Выполнено:

vagrant@vagrant:~$ sudo mkfs.ext4 /dev/vg1/lvol0

mke2fs 1.45.5 (07-Jan-2020)

Creating filesystem with 25600 4k blocks and 25600 inodes


Allocating group tables: done

Writing inode tables: done

Creating journal (1024 blocks): done

Writing superblocks and filesystem accounting information: done


Проверяем:

vagrant@vagrant:$ sudo lsblk -f
NAME                      FSTYPE            LABEL     UUID                                   FSAVAIL FSUSE% MOUNTPOINT
loop0                     squashfs                                                                 0   100% /snap/lxd/21029
loop2                     squashfs                                                                 0   100% /snap/core18/2128
loop3                     squashfs                                                                 0   100% /snap/core18/2284
loop4                     squashfs                                                                 0   100% /snap/snapd/14978
loop5                     squashfs                                                                 0   100% /snap/core20/1361
loop6                     squashfs                                                                 0   100% /snap/lxd/22526
sda
├─sda1
├─sda2                    ext4                        6062f85a-eb61-4c49-900d-65a91b7edafb    802.6M    11% /boot
└─sda3                    LVM2_member                 sDUvKe-EtCc-gKuY-ZXTD-1B1d-eh9Q-XldxLf
  └─ubuntu--vg-ubuntu--lv ext4                        4855fb55-feed-4397-8ed6-ad6ccc89ef59     25.6G    12% /
sdb
├─sdb1                    linux_raid_member vagrant:0 e0d2661c-02aa-658a-d1bf-05d6e1c4c090
│ └─md0                   LVM2_member                 KfPSB6-i1hU-msBu-9qBu-YpTx-Uk9N-o7yKpq
└─sdb2                    linux_raid_member vagrant:1 33a7727c-7e28-2f83-dc48-519510fddc38
  └─md1                   LVM2_member                 yuuxB8-Z3xz-lOug-VXLX-gJiM-P4lq-j2skDA
    └─vg1-lvol0           ext4                        7e57fcc9-3f43-4de4-8758-3d1c88c30c04
sdc
├─sdc1                    linux_raid_member vagrant:0 e0d2661c-02aa-658a-d1bf-05d6e1c4c090
│ └─md0                   LVM2_member                 KfPSB6-i1hU-msBu-9qBu-YpTx-Uk9N-o7yKpq
└─sdc2                    linux_raid_member vagrant:1 33a7727c-7e28-2f83-dc48-519510fddc38
  └─md1                   LVM2_member                 yuuxB8-Z3xz-lOug-VXLX-gJiM-P4lq-j2skDA
    └─vg1-lvol0           ext4                        7e57fcc9-3f43-4de4-8758-3d1c88c30c04


11. Смонтируйте этот раздел в любую директорию, например, /tmp/new.

Выполнено:

vagrant@vagrant:$ mkdir /tmp/new

vagrant@vagrant:$ sudo mount /dev/vg1/lvol0 /tmp/new

Проверяем:

vagrant@vagrant:$ sudo mount -l | grep lvol0

/dev/mapper/vg1-lvol0 on /tmp/new type ext4 (rw,relatime,stripe=256)


12. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.

Выполнено:

vagrant@vagrant:~$ sudo wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.

--2022-03-07 15:18:40--  https://mirror.yandex.ru/ubuntu/ls-lR.gz

Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183

Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.

HTTP request sent, awaiting response... 200 OK

Length: 22173228 (21M) [application/octet-stream]

Saving to: ‘/tmp/new/test.gz.’


/tmp/new/test.gz.                    100%[======================================================================>]  21.15M  5.99MB/s    in 3.6s


2022-03-07 15:18:43 (5.94 MB/s) - ‘/tmp/new/test.gz.’ saved [22173228/22173228]


vagrant@vagrant:~$ ls -lAh /tmp/new/

total 22M

drwx------ 2 root root 16K Mar  7 15:06 lost+found

-rw-r--r-- 1 root root 22M Mar  7 13:13 test.gz.


13. Прикрепите вывод lsblk.
Выполнено:

vagrant@vagrant:~$ sudo lsblk

NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT

loop0                       7:0    0 70.3M  1 loop  /snap/lxd/21029

loop2                       7:2    0 55.4M  1 loop  /snap/core18/2128

loop3                       7:3    0 55.5M  1 loop  /snap/core18/2284

loop4                       7:4    0 43.6M  1 loop  /snap/snapd/14978

loop5                       7:5    0 61.9M  1 loop  /snap/core20/1361

loop6                       7:6    0 67.9M  1 loop  /snap/lxd/22526

sda    	                     8:0    0   64G  0 disk

├─sda1       	               8:1    0    1M  0 part

├─sda2      	                8:2    0    1G  0 part  /boot

└─sda3      	                8:3    0   63G  0 part

  └─ubuntu--vg-ubuntu--lv 253:0 	   0 31.5G  0 lvm   /
	
  
	
  
sdb	                         8:16   0  2.5G  0 disk

├─sdb1 	                     8:17   0    2G  0 part

│ └─md0	                     9:0    0    2G  0 raid1

└─sdb2 	                     8:18   0  511M  0 part

  └─md1 	                    9:1    0 1018M  0 raid0
	
    └─vg1-lvol0 	          253:1    0  100M  0 lvm   /tmp/new
		
		
    
sdc 	                        8:32   0  2.5G  0 disk

├─sdc1  	                    8:33   0    2G  0 part

│ └─md0 	                    9:0    0    2G  0 raid1

└─sdc2 	                     8:34   0  511M  0 part

  └─md1 	                    9:1    0 1018M  0 raid0
	
    └─vg1-lvol0 	          253:1    0  100M  0 lvm   /tmp/new
		
    

14. Протестируйте целостность файла:


root@vagrant:# gzip -t /tmp/new/test.gz

root@vagrant:# echo $?

0

Выполнено:

vagrant@vagrant:$ gzip -t /tmp/new/test.gz. && echo $?

0


15. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.

Выполнено:

vagrant@vagrant:~$ sudo pvmove /dev/md1

  /dev/md1: Moved: 16.00%
  
  /dev/md1: Moved: 100.00%
  

Проверим:

vagrant@vagrant:~$ sudo lsblk

NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT

loop0                       7:0    0 70.3M  1 loop  /snap/lxd/21029

loop2                       7:2    0 55.4M  1 loop  /snap/core18/2128

loop3                       7:3    0 55.5M  1 loop  /snap/core18/2284

loop4                       7:4    0 43.6M  1 loop  /snap/snapd/14978

loop5                       7:5    0 61.9M  1 loop  /snap/core20/1361

loop6                       7:6    0 67.9M  1 loop  /snap/lxd/22526

sda      	                   8:0    0   64G  0 disk

├─sda1  	                    8:1    0    1M  0 part

├─sda2   	                   8:2    0    1G  0 part  /boot

└─sda3   	                   8:3    0   63G  0 part

  └─ubuntu--vg-ubuntu--lv 	253:0    0 31.5G  0 lvm   /
	
  
sdb             	            8:16   0  2.5G  0 disk

├─sdb1           	           8:17   0    2G  0 part

│ └─md0  	                   9:0    0    2G  0 raid1

│   └─vg1-lvol0    	       253:1    0  100M  0 lvm   /tmp/new

└─sdb2         	             8:18   0  511M  0 part

  └─md1       	              9:1    0 1018M  0 raid0
	

sdc    	                     8:32   0  2.5G  0 disk

├─sdc1   	                   8:33   0    2G  0 part

│ └─md0  	                   9:0    0    2G  0 raid1

│   └─vg1-lvol0 	          253:1    0  100M  0 lvm   /tmp/new

└─sdc2  	                    8:34   0  511M  0 part

  └─md1  	                   9:1    0 1018M  0 raid0
  

16. Сделайте --fail на устройство в вашем RAID1 md.

Выполнено:

vagrant@vagrant:~$ sudo mdadm /dev/md0 -f /dev/sdb1

mdadm: set /dev/sdb1 faulty in /dev/md0


Проверим:

vagrant@vagrant:~$ sudo mdadm -D /dev/md0

/dev/md0:

           Version : 1.2
     Creation Time : Mon Mar  7 14:39:37 2022
        Raid Level : raid1
        Array Size : 2094080 (2045.00 MiB 2144.34 MB)
     Used Dev Size : 2094080 (2045.00 MiB 2144.34 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Mon Mar  7 15:32:54 2022
             State : clean, degraded
    Active Devices : 1
   Working Devices : 1
    Failed Devices : 1
     Spare Devices : 0

Consistency Policy : resync

              Name : vagrant:0  (local to host vagrant)
              UUID : e0d2661c:02aa658a:d1bf05d6:e1c4c090
            Events : 19

    Number   Major   Minor   RaidDevice State
       -       0        0        0      removed
       1       8       33        1      active sync   /dev/sdc1

       0       8       17        -      faulty   /dev/sdb1
       

17. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.

Выполнено:

vagrant@vagrant:~$ dmesg |grep md0

[13174.375156] md/raid1:md0: not clean -- starting background reconstruction

[13174.375160] md/raid1:md0: active with 2 out of 2 mirrors

[13174.375203] md0: detected capacity change from 0 to 2144337920

[13174.381156] md: resync of RAID array md0

[13184.971734] md: md0: resync done.

[16371.294914] md/raid1:md0: Disk failure on sdb1, disabling device.

               md/raid1:md0: Operation continuing on 1 devices.
               

18. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:


root@vagrant:# gzip -t /tmp/new/test.gz

root@vagrant:# echo $?

0

Проверено:

vagrant@vagrant:~$ gzip -t /tmp/new/test.gz. && echo $?

0

19. Погасите тестовый хост, vagrant destroy.

Выполнено.


