---
title: GPT-partícióval rendelkező operációsrendszer-lemez átméretezése
description: Ez a cikk útmutatást nyújt a GUID partíciós tábla (GPT) partícióval rendelkező operációsrendszer-lemezek átméretezéséhez Linux rendszerben.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 76aa18c9724d85b1dd3fb8de3d7d033d40ff95ce
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400233"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>GPT-partícióval rendelkező operációsrendszer-lemez átméretezése

> [!NOTE]
> Ez a cikk csak a GUID partíciós tábla (GPT) partícióval rendelkező operációsrendszer-lemezekre vonatkozik.

Ez a cikk azt ismerteti, hogyan növelhető egy olyan operációsrendszer-lemez mérete, amely egy Linux rendszerű GPT-partícióval rendelkezik. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Annak megállapítása, hogy az operációsrendszer-lemez rendelkezik-e MBR-vagy GPT-partícióval

A `parted` paranccsal azonosíthatja, hogy a lemezpartíció a fő rendszertöltő rekord (MBR) partícióval vagy egy GPT-partícióval lett-e létrehozva.

### <a name="mbr-partition"></a>MBR-partíció

A következő kimenetben a **partíciós tábla** az **MSDOS** értékét jeleníti meg. Ez az érték azonosítja az MBR-partíciót.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT-partíció

A következő kimenetben a **partíciós tábla** a **GPT** értékét jeleníti meg. Ez az érték azonosítja a GPT-partíciót.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Ha a virtuális gép (VM) GPT-partícióval rendelkezik az operációsrendszer-lemezen, növelje az operációsrendszer-lemez méretét.

## <a name="increase-the-size-of-the-os-disk"></a>Az operációsrendszer-lemez méretének növeléséhez

Az alábbi utasítások a Linux által támogatott disztribúciók esetében érvényesek.

> [!NOTE]
> A folytatás előtt készítsen biztonsági másolatot a virtuális gépről, vagy készítsen pillanatképet az operációsrendszer-lemezről.

### <a name="ubuntu"></a>Ubuntu

Az operációsrendszer-lemez méretének növeléséhez az Ubuntu 16-ban. *x* és 18. *x*:

1. Állítsa le a virtuális gépet.
1. Növelje az operációsrendszer-lemez méretét a portálon.
1. Indítsa újra a virtuális gépet, majd jelentkezzen be a virtuális gépre **root** felhasználóként.
1. Győződjön meg arról, hogy az operációsrendszer-lemez megnövelt fájlrendszerbeli mérettel rendelkezik.

A következő példában az operációsrendszer-lemez átméretezi a portálról a 100 GB-ra. A jelenleg csatlakoztatott **/dev/sda1** -fájlrendszer **/** 97 GB-ot jelenít meg.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Az operációsrendszer-lemez méretének növeléséhez a SUSE 12 SP4, SUSE SLES 12 for SAP, SUSE SLES 15 és SUSE SLES 15 for SAP:

1. Állítsa le a virtuális gépet.
1. Növelje az operációsrendszer-lemez méretét a portálon.
1. Indítsa újra a virtuális gépet.

A virtuális gép újraindítása után végezze el a következő lépéseket:

1. Nyissa meg a virtuális gépet **root** -felhasználóként a következő parancs használatával:

   ```
   # sudo -i
   ```

1. A következő parancs használatával telepítse a **growpart** csomagot, amelyet a partíció átméretezéséhez fog használni:

   ```
   # zypper install growpart
   ```

1. A `lsblk` parancs használatával keresse meg a fájlrendszer gyökeréhez csatlakoztatott partíciót ( **/** ). Ebben az esetben azt láthatjuk, hogy az **SDA** eszköz 4-es partíciója a következőre van csatlakoztatva **/** :

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Méretezze át a szükséges partíciót a `growpart` parancs és az előző lépésben meghatározott partíció számának használatával:

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Futtassa `lsblk` újra a parancsot annak megadásához, hogy a partíció megnövelve lett-e.

   A következő kimenet azt mutatja, hogy a **/dev/sda4** partíció mérete 46,5 GB-ra módosult:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Azonosítsa az operációsrendszer-lemezen lévő fájlrendszer típusát a következő `lsblk` paranccsal `-f` :

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. A fájlrendszer típusától függően használja a megfelelő parancsokat a fájlrendszer átméretezéséhez.
   
   A **XFS** használja a következő parancsot:
   
   ```
   #xfs_growfs /
   ```
   
   Példa a kimenetre:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Az **ext4** esetében használja ezt a parancsot:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. A következő parancs használatával ellenőrizze a **DF-th** fájlrendszer méretének növelését:
   
   ```
   #df -Thl
   ```
   
   Példa a kimenetre:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   Az előző példában láthatjuk, hogy az operációsrendszer-lemez fájlrendszerének mérete megnőtt.

### <a name="rhel-with-lvm"></a>RHEL LVM-vel

1. Nyissa meg a virtuális gépet **root** -felhasználóként a következő parancs használatával:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. A `lsblk` paranccsal meghatározhatja, hogy a rendszer melyik logikai kötetet (LV) csatlakoztatja a fájlrendszer gyökeréhez ( **/** ). Ebben az esetben azt láthatjuk, hogy a **rootvg-rootlv** csatlakoztatva van **/** . Ha egy másik fájlrendszerre van szüksége, cserélje le az LV-t és a csatlakoztatási pontot ebben a cikkben.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Győződjön meg arról, hogy van-e szabad hely a főpartíciót tartalmazó LVM-kötet csoportban (VG). Ha van szabad terület, ugorjon a 12. lépésre.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   Ebben a példában a line **Free PE/size** érték azt mutatja, hogy a kötet csoportban a 38,02 GB ingyenes. Nincs szükség a lemez átméretezésére, mielőtt helyet ad hozzá a kötet csoportjához.

1. Az operációsrendszer-lemez méretének növeléséhez a RHEL 7 rendszerben. *x* az LVM:

   1. Állítsa le a virtuális gépet.
   1. Növelje az operációsrendszer-lemez méretét a portálon.
   1. Indítsa el a virtuális gépet.

1. A virtuális gép újraindítása után végezze el a következő lépést:

   - Telepítse a **Cloud-utils-growpart** csomagot a **growpart** parancs megadásához, amely szükséges az operációsrendszer-lemez és a GDisk-kezelő méretének növeléséhez a GPT-lemezek elrendezéséhez. Ezek a csomagok a legtöbb Piactéri lemezképen előre telepítve vannak.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Határozza meg, hogy melyik lemez és partíció tartalmazza az LVM fizikai kötetet vagy köteteket (PV) a **rootvg** nevű kötetben a `pvscan` parancs használatával. Jegyezze fel a szögletes zárójelek (**[** és **]**) között megjelenő méretet és szabad területet.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Ellenőrizze a partíció méretét a használatával `lsblk` . 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Bontsa ki a PV-t tartalmazó partíciót `growpart` a használatával, az eszköz nevét és a partíció számát. Ezzel a művelettel kibontja a megadott partíciót, hogy az összes szabad folytonos területet használhassa az eszközön.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. A parancs ismételt használatával ellenőrizze, hogy a partíció mérete átméretezi-e a várt méretre `lsblk` . Figyelje meg, hogy a példában a **sda4** 63 GB-ról 95 GB-ra változott.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. A PV kibontásával az újonnan kibontott partíció hátralévő részét használhatja:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Győződjön meg arról, hogy a PV új mérete a várt méret, az eredeti **[méret/szabad]** értékekkel összehasonlítva:

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Bontsa ki a kívánt logikai kötetet (LV) a kívánt mennyiséggel. Az összegnek nem kell az összes szabad területnek lennie a kötet csoportban. A következő példában a **/dev/Mapper/rootvg-rootlv** mérete 2 GB-ról 12 GB-ra van átméretezve (10 GB-os növekedés). A parancs átméretezi a fájlrendszert is.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Példa a kimenetre:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. A `lvresize` parancs automatikusan meghívja a megfelelő átméretezési parancsot az LV fájlrendszerben. Győződjön meg arról, hogy a **/dev/Mapper/rootvg-rootlv**, amely csatlakoztatva van **/** , megnövelt fájlrendszer-mérettel rendelkezik a következő parancs használatával:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Példa a kimenetre:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Ha ugyanezt az eljárást szeretné használni az egyéb logikai kötetek átméretezéséhez, módosítsa a 12. lépésben szereplő LV-nevet.

### <a name="rhel-raw"></a>RHEL NYERS

Az operációsrendszer-lemez méretének növeléséhez egy RHEL nyers partícióban:

1. Állítsa le a virtuális gépet.
1. Növelje az operációsrendszer-lemez méretét a portálon.
1. Indítsa el a virtuális gépet.

A virtuális gép újraindítása után végezze el a következő lépéseket:

1. A következő parancs használatával érheti el a virtuális gépet **root** felhasználóként:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. A virtuális gép újraindítása után végezze el a következő lépést:

   - Telepítse a **Cloud-utils-growpart** csomagot a **growpart** parancs megadásához, amely szükséges az operációsrendszer-lemez és a GDisk-kezelő méretének növeléséhez a GPT-lemezek elrendezéséhez. Ez a csomag a legtöbb Piactéri lemezképen előre telepítve van.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. A **lsblk-f** parancs használatával ellenőrizze a gyökér () partíciót tároló partíciót és fájlrendszer típusát **/** :

   ```bash
   [root@vm-dd-cent7 ~]# lsblk -f
   NAME    FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1  xfs          2a7bb59d-6a71-4841-a3c6-cba23413a5d2 /boot
   ├─sda2  xfs          148be922-e3ec-43b5-8705-69786b522b05 /
   ├─sda14
   └─sda15 vfat         788D-DC65                            /boot/efi
   sdb
   └─sdb1  ext4         923f51ff-acbd-4b91-b01b-c56140920098 /mnt/resource
   ```

1. Az ellenőrzéshez először a **GDisk** tartalmazó SDA-lemez partíciós tábláját listázva kell megkeresni. Ebben a példában egy 48 GB-os lemezt látunk a 2. partícióval a 29,0 GiB-on. A lemez a Azure Portalban 30 GB-ról 48 GB-ra bővült.

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 62914526
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 6076 sectors (3.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048        62912511   29.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. Bontsa ki a gyökérhez tartozó partíciót, ebben az esetben a sda2 a **growpart** parancs használatával. Ezzel a paranccsal kibonthatja a partíciót a lemezen található összes összefüggő terület használatára.

   ```bash
   [root@vm-dd-cent7 ~]# growpart /dev/sda 2
   CHANGED: partition=2 start=2050048 old: size=60862464 end=62912512 new: size=98613214 end=100663262
   ```

1. Most nyomtassa ki újra az új partíciós táblát a **GDisk** .  Figyelje meg, hogy a 2. partíció 47,0 GiB-ra bővült:

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 100663262
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 4062 sectors (2.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048       100663261   47.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. Bontsa ki a fájlrendszert a partíción **xfs_growfs**, amely megfelelő a piactér által generált szabványos RedHat rendszerhez:

   ```bash
   [root@vm-dd-cent7 ~]# xfs_growfs /
   meta-data=/dev/sda2              isize=512    agcount=4, agsize=1901952 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=7607808, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3714, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7607808 to 12326651
   ```

1. Ellenőrizze, hogy az új méret megjelenik-e a **DF** parancs használatával:

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
