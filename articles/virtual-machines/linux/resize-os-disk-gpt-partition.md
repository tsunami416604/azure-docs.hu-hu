---
title: GPT-partícióval rendelkező operációsrendszer-lemez átméretezése | Microsoft Docs
description: Ez a cikk a GPT-partícióval rendelkező operációsrendszer-lemezek átméretezésére vonatkozó utasításokat tartalmazza.
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
ms.openlocfilehash: 7c408e8e29b3f9ac423a6104c40242f11f93a171
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651087"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>GPT-partícióval rendelkező operációsrendszer-lemez átméretezése

> [!NOTE]
> Ez a forgatókönyv csak a GUID partíciós tábla (GPT) partícióval rendelkező operációsrendszer-lemezekre vonatkozik.

Ez a cikk azt ismerteti, hogyan növelhető egy olyan operációsrendszer-lemez mérete, amely egy Linux rendszerű GPT-partícióval rendelkezik. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Annak megállapítása, hogy az operációsrendszer-lemez rendelkezik-e MBR-vagy GPT-partícióval

Az elválasztott **paranccsal** azonosíthatja, hogy a lemezpartíció egy fő rendszertöltő REKORDDAL (MBR) vagy egy GPT-partícióval lett-e létrehozva.

### <a name="mbr-partition"></a>MBR-partíció

A következő kimenetben a **partíciós tábla** az **MSDOS**értékét jeleníti meg. Ez az érték azonosítja az MBR-partíciót.

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

A következő kimenetben a **partíciós tábla** a **GPT**értékét jeleníti meg. Ez az érték azonosítja a GPT-partíciót.

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

Az operációsrendszer-lemez méretének növeléséhez Ubuntu 16. x és 18. x esetén:

1. Állítsa le a virtuális gépet.
1. Növelje az operációsrendszer-lemez méretét a portálon.
1. Indítsa újra a virtuális gépet, majd jelentkezzen be a virtuális gépre **root** felhasználóként.
1. Győződjön meg arról, hogy az operációsrendszer-lemez megnövelt fájlrendszerbeli mérettel rendelkezik.

Ahogy az az alábbi példában is látható, az operációsrendszer-lemez átméretezi a portálról a 100 GB-ra. A jelenleg csatlakoztatott **/dev/sda1** -fájlrendszer **/** 97 GB-ot jelenít meg.

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

Ha a virtuális gép újraindult, hajtsa végre a következő lépéseket:

   1. A következő parancs használatával érheti el a virtuális gépet **root** felhasználóként:
   
      `#sudo su`

   1. A következő parancs használatával telepítse a **gptfdisk** csomagot, amely az operációsrendszer-lemez méretének növeléséhez szükséges:

      `#zypper install gptfdisk -y`

   1. A lemezen elérhető legnagyobb szektor megtekintéséhez futtassa a következő parancsot:

      `#sgdisk -e /dev/sda`

   1. Méretezze át a partíciót anélkül, hogy törölné a következő parancs használatával. Az átnevezett parancs **resizepart** nevű kapcsolóval rendelkezik, **hogy a partíció** törlése nélkül átméretezi a partíciót. A 4. számú **resizepart** azt jelzi, hogy a negyedik partíció átméretezése megtörténjen.

      `#parted -s /dev/sda "resizepart 4 -1" quit`

   1. Futtassa a **#lsblk** parancsot, és győződjön meg arról, hogy a partíció növelése megtörtént-e.

      A következő kimenet azt mutatja, hogy a **/dev/sda4** partíció mérete 98,5 GB-ra van méretezve.

      ```
      user@myvm:~ # lsblk
      NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda      8:0    0  100G  0 disk
      ├─sda1   8:1    0    2M  0 part
      ├─sda2   8:2    0  512M  0 part /boot/efi
      └─sda4   8:4    0 98.5G  0 part /
      sdb      8:16   0   20G  0 disk
      └─sdb1   8:17   0   20G  0 part /mnt/resource
      ```
      
   1. Azonosítsa az operációsrendszer-lemezen lévő fájlrendszer típusát a következő parancs használatával:

      `blkid`

      Példa a kimenetre:

      ```
      #blkid

      user@myvm:~ # blkid
      /dev/sda1: PARTLABEL="p.legacy" PARTUUID="0122fd4c-0069-4a45-bfd4-98b97ccb6e8c"
      /dev/sda2: SEC_TYPE="msdos" LABEL_FATBOOT="EFI" LABEL="EFI" UUID="00A9-D170" TYPE="vfat" PARTLABEL="p.UEFI" PARTUUID="abac3cd8-949b-4e83-81b1-9636493388c7"
      /dev/sda3: LABEL="BOOT" UUID="aa2492db-f9ed-4f5a-822a-1233c06d57cc" TYPE="xfs" PARTLABEL="p.lxboot" PARTUUID="dfb36c61-b15f-4505-8e06-552cf1589cf7"
      /dev/sda4: LABEL="ROOT" UUID="26104965-251c-4e8d-b069-5f5323d2a9ba" TYPE="xfs" PARTLABEL="p.lxroot" PARTUUID="50fecee0-f22b-4406-94c3-622507e2dbce"
      /dev/sdb1: UUID="95239fce-ca97-4f03-a077-4e291588afc9" TYPE="ext4" PARTUUID="953afef3-01"
      ```

   1. A fájlrendszer típusától függően használja a megfelelő parancsokat a fájlrendszer átméretezéséhez.

      A **XFS**használja a következő parancsot:

      ` #xfs_growfs /`

      Példa a kimenetre:

      ```
      user@myvm:~ # xfs_growfs /
      meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
               =                       sectsz=512   attr=2, projid32bit=1
               =                       crc=1        finobt=1 spinodes=0 rmapbt=0
               =                       reflink=0
      data     =                       bsize=4096   blocks=7470331, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=3647, version=2
               =                       sectsz=512   sunit=0 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 7470331 to 25820172
      ```

      Az **ext4**esetében használja a következő parancsot:

      ```#resize2fs /dev/sda4```

   1. A következő parancs használatával ellenőrizze a **DF-th**fájlrendszer méretének növelését:

      `#df -Th`

      Példa a kimenetre:

      ```
      user@myvm:~ # df -Th
      Filesystem     Type      Size  Used Avail Use% Mounted on
      devtmpfs       devtmpfs  306M  4.0K  306M   1% /dev
      tmpfs          tmpfs     320M     0  320M   0% /dev/shm
      tmpfs          tmpfs     320M  8.8M  311M   3% /run
      tmpfs          tmpfs     320M     0  320M   0% /sys/fs/cgroup
      /dev/sda4      xfs        99G  1.8G   97G   2% /
      /dev/sda3      xfs      1014M   88M  927M   9% /boot
      /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
      /dev/sdb1      ext4       20G   45M   19G   1% /mnt/resource
      tmpfs          tmpfs      64M     0   64M   0% /run/user/1000
      user@myvm:~ #
      ```

Az előző példában láthatjuk, hogy az operációsrendszer-lemez fájlrendszerének mérete megnőtt.

### <a name="rhel"></a>RHEL

Az operációsrendszer-lemez méretének növeléséhez az RHEL 7. x és az LVM használatával:

1. Állítsa le a virtuális gépet.
1. Növelje az operációsrendszer-lemez méretét a portálon.
1. Indítsa el a virtuális gépet.

Ha a virtuális gép újraindult, hajtsa végre a következő lépéseket:

   1. A következő parancs használatával érheti el a virtuális gépet **root** felhasználóként:
   
      `#sudo su`

   1. Telepítse a **gptfdisk** csomagot, amely az operációsrendszer-lemez méretének növeléséhez szükséges.

      `#yum install gdisk -y`

   1. A lemezen elérhető legnagyobb szektor megtekintéséhez futtassa a következő parancsot:

      `#sgdisk -e /dev/sda`

   1. Méretezze át a partíciót anélkül, hogy törölné a következő parancs használatával. Az átnevezett parancs **resizepart** nevű kapcsolóval rendelkezik, **hogy a partíció** törlése nélkül átméretezi a partíciót. A 4. számú **resizepart** azt jelzi, hogy a negyedik partíció átméretezése megtörténjen.

      `#parted -s /dev/sda "resizepart 4 -1" quit`
    
   1. Futtassa a következő parancsot a partíció növelésének ellenőrzéséhez:

      `#lsblk`

      A következő kimenet azt mutatja, hogy a **/dev/sda4** partíció mérete 99 GB-ra van méretezve.

      ```
      [user@myvm ~]# lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      fd0                 2:0    1    4K  0 disk
      sda                 8:0    0  100G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot/efi
      ├─sda2              8:2    0  500M  0 part /boot
      ├─sda3              8:3    0    2M  0 part
      └─sda4              8:4    0   99G  0 part
      ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
      ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
      ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
      ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
      ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
      └─rootvg-rootlv   253:5    0    2G  0 lvm  /
      sdb                 8:16   0   50G  0 disk
      └─sdb1              8:17   0   50G  0 part /mnt/resource
      ```

   1. A fizikai kötet (PV) átméretezéséhez használja a következő parancsot:

      `#pvresize /dev/sda4`

      A következő kimenet azt mutatja, hogy a PV mérete 99,02 GB-ra módosult.

      ```
      [user@myvm ~]# pvresize /dev/sda4
      Physical volume "/dev/sda4" changed
      1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      [user@myvm ~]# pvs
      PV         VG     Fmt  Attr PSize   PFree
      /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
      ```

   1. A következő példában a **/dev/Mapper/rootvg-rootlv** 2 GB-ról 12 GB-ra (a 10 GB-os növekedésre) átméretezi a következő parancs segítségével. A parancs átméretezi a fájlrendszert is.

      `#lvresize -r -L +10G /dev/mapper/rootvg-rootlv`

      Példa a kimenetre:

      ```
      [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
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
         
   1. A következő parancs használatával ellenőrizze, hogy a **/dev/Mapper/rootvg-rootlv** rendelkezik-e a fájlrendszer megnövekedett méretével:

      `#df -Th /`

      Példa a kimenetre:

      ```
      [user@myvm ~]# df -Th /
      Filesystem                Type  Size  Used Avail Use% Mounted on
      /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
      [user@myvm ~]#
      ```

   > [!NOTE]
   > Ha ugyanezt az eljárást szeretné használni az egyéb logikai kötetek átméretezéséhez, módosítsa a 7. lépésben szereplő **lv** -nevet.

## <a name="next-steps"></a>További lépések

- [Lemez átméretezése](expand-disks.md)