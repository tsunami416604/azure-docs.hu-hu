---
title: Kromát-környezet Linux rendszerű mentési virtuális gépen.
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a kromát-környezetet a virtuális gép (VM) használatával a Linux rendszerben.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: d6605aa77cfa746f60fc3b23e120267a2614f7b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82864681"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Kromát-környezet Linux rendszerű mentési virtuális gépen

Ez a cikk azt ismerteti, hogyan lehet elhárítani a kromát-környezetet a virtuális gép (VM) használatával a Linux rendszerben.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16. x && Ubuntu 18. x

1. Az érintett virtuális gép leállítása vagy lefoglalása.
1. Hozzon létre egy azonos operációsrendszer-verzióval rendelkező mentési virtuálisgép-lemezképet ugyanazon az erőforráscsoport-(RSG-) és egy felügyelt lemezt használó helyen.
1. A Azure Portal segítségével készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről.
1. Hozzon létre egy lemezt a pillanatképből az operációsrendszer-lemezről, és csatolja a mentési virtuális géphez.
1. Miután létrehozta a lemezt, hárítsa el a virtuális gép rendszerindítási környezetét.

   1. A következő parancs használatával érheti el a virtuális gépet root felhasználóként:

      `#sudo su -`

   1. Keresse meg a lemezt a használatával `dmesg` (az új lemez felderítéséhez használt módszer változhat). Az alábbi példa a **dmesg-et** használja az **SCSI** -lemezek szűrésére:

      `dmesg | grep SCSI`

      A kimenet a következő példához hasonló lesz. Ebben a példában a **SDC** lemezt szeretnénk használni:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. A kromát-környezet eléréséhez használja az alábbi parancsokat:

      ```
      #mkdir /rescue
      #mount /dev/sdc1 /rescue
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. A kromát-környezet hibáinak megoldása.

   1. A kromát-környezetből való kilépéshez használja az alábbi parancsokat:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > Ha ezt a hibaüzenetet kapja `unable to unmount /rescue` , adja hozzá az-l kapcsolót a umount parancshoz.
      >
      > Például: `umount -l /rescue`

1. Válassza le a lemezt a mentési virtuális gépről, és hajtson végre egy lapozófájlt az eredeti virtuális géppel.
1. Indítsa el az eredeti virtuális gépet, és vizsgálja meg a kapcsolatát.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/CentOS/Oracle 6. x && Oracle 8. x && RHEL/CentOS 7. x nyers partíciókkal

1. Az érintett virtuális gép leállítása vagy lefoglalása.
1. Hozzon létre egy azonos operációsrendszer-verzióval rendelkező mentési virtuálisgép-lemezképet ugyanazon az erőforráscsoport-(RSG-) és egy felügyelt lemezt használó helyen.
1. A Azure Portal segítségével készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről.
1. Hozzon létre egy lemezt a pillanatképből az operációsrendszer-lemezről, és csatolja a mentési virtuális géphez.
1. Miután létrehozta a lemezt, hárítsa el a virtuális gép rendszerindítási környezetét.

   1. A következő parancs használatával érheti el a virtuális gépet root felhasználóként:

      `#sudo su -`

   1. Keresse meg a lemezt a használatával `dmesg` (az új lemez felderítéséhez használt módszer változhat). Az alábbi példa a **dmesg-et** használja az **SCSI** -lemezek szűrésére:

      `dmesg | grep SCSI`

      A kimenet a következő példához hasonló lesz. Ebben a példában a **SDC** lemezt szeretnénk használni:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. A kromát-környezet eléréséhez használja az alábbi parancsokat:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. A kromát-környezet hibáinak megoldása.

   1. A kromát-környezetből való kilépéshez használja az alábbi parancsokat:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > Ha ezt a hibaüzenetet kapja `unable to unmount /rescue` , adja hozzá az-l kapcsolót a umount parancshoz.
      >
      > Például: `umount -l /rescue`

1. Válassza le a lemezt a mentési virtuális gépről, és hajtson végre egy lapozófájlt az eredeti virtuális géppel.
1. Indítsa el az eredeti virtuális gépet, és vizsgálja meg a kapcsolatát.

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/CentOS 7. x és LVM

   > [!NOTE]
   > Ha az eredeti virtuális gép tartalmazza a logikai kötet-kezelőt (LVM) az operációsrendszer-lemezen, hozza létre a mentési virtuális gépet az operációsrendszer-lemezen található nyers partíciókkal.

1. Az érintett virtuális gép leállítása vagy lefoglalása.
1. Hozzon létre egy azonos operációsrendszer-verzióval rendelkező mentési virtuálisgép-lemezképet ugyanazon az erőforráscsoport-(RSG-) és egy felügyelt lemezt használó helyen.
1. A Azure Portal segítségével készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről.
1. Hozzon létre egy lemezt a pillanatképből az operációsrendszer-lemezről, és csatolja a mentési virtuális géphez.
1. Miután létrehozta a lemezt, hárítsa el a virtuális gép rendszerindítási környezetét.

   1. A következő parancs használatával érheti el a virtuális gépet root felhasználóként:

      `#sudo su -`

   1. Keresse meg a lemezt a használatával `dmesg` (az új lemez felderítéséhez használt módszer változhat). Az alábbi példa a **dmesg-et** használja az **SCSI** -lemezek szűrésére:

      `dmesg | grep SCSI`

      A kimenet a következő példához hasonló lesz. Ebben a példában a **SDC** lemezt szeretnénk használni:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. A logikai kötet csoport aktiválásához használja a következő parancsot:

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. `lsblk`Az LVM-nevek beolvasásához használja a parancsot:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. A kromát-környezet eléréséhez használja az alábbi parancsokat:

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/mapper/rootvg-optlv /rescue/opt
      #mount /dev/sdc2 /rescue/boot/
      #mount /dev/sdc1 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. A kromát-környezet hibáinak megoldása.

   1. A kromát-környezetből való kilépéshez használja az alábbi parancsokat:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue/opt
      #umount /rescue
      ```

      > [!NOTE]
      > Ha ezt a hibaüzenetet kapja `unable to unmount /rescue` , adja hozzá az-l kapcsolót a umount parancshoz.
      >
      > Például: `umount -l /rescue`

1. Válassza le a lemezt a mentési virtuális gépről, és hajtson végre egy lapozófájlt az eredeti virtuális géppel.
1. Indítsa el az eredeti virtuális gépet, és vizsgálja meg a kapcsolatát.

## <a name="rhel-8x-with-lvm"></a>RHEL 8. x és LVM

   > [!NOTE]
   > Ha az eredeti virtuális gép tartalmazza a logikai kötet-kezelőt (LVM) az operációsrendszer-lemezen, hozza létre a mentési virtuális gépet az operációsrendszer-lemezen található nyers partíciókkal.

1. Az érintett virtuális gép leállítása vagy lefoglalása.
1. Hozzon létre egy azonos operációsrendszer-verzióval rendelkező mentési virtuálisgép-lemezképet ugyanazon az erőforráscsoport-(RSG-) és egy felügyelt lemezt használó helyen.
1. A Azure Portal segítségével készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről.
1. Hozzon létre egy lemezt a pillanatképből az operációsrendszer-lemezről, és csatolja a mentési virtuális géphez.
1. Miután létrehozta a lemezt, hárítsa el a virtuális gép rendszerindítási környezetét.

   1. A következő parancs használatával érheti el a virtuális gépet root felhasználóként:

      `#sudo su -`

   1. Keresse meg a lemezt a használatával `dmesg` (az új lemez felderítéséhez használt módszer változhat). Az alábbi példa a **dmesg-et** használja az **SCSI** -lemezek szűrésére:

      `dmesg | grep SCSI`

      A kimenet a következő példához hasonló lesz. Ebben a példában a **SDC** lemezt szeretnénk használni:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. A logikai kötet csoport aktiválásához használja a következő parancsot:

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. `lsblk`Az LVM-nevek beolvasásához használja a parancsot:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. A kromát-környezet eléréséhez használja az alábbi parancsokat:

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. A kromát-környezet hibáinak megoldása.

   1. A kromát-környezetből való kilépéshez használja az alábbi parancsokat:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue
      ```

      > [!NOTE]
      > Ha ezt a hibaüzenetet kapja `unable to unmount /rescue` , adja hozzá az-l kapcsolót a umount parancshoz.
      >
      > Például: `umount -l /rescue`

1. Válassza le a lemezt a mentési virtuális gépről, és hajtson végre egy lapozófájlt az eredeti virtuális géppel.
1. Indítsa el az eredeti virtuális gépet, és vizsgálja meg a kapcsolatát.

## <a name="oracle-7x"></a>Oracle 7. x

1. Az érintett virtuális gép leállítása vagy lefoglalása.
1. Hozzon létre egy azonos operációsrendszer-verzióval rendelkező mentési virtuálisgép-lemezképet ugyanazon az erőforráscsoport-(RSG-) és egy felügyelt lemezt használó helyen.
1. A Azure Portal segítségével készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről.
1. Hozzon létre egy lemezt a pillanatképből az operációsrendszer-lemezről, és csatolja a mentési virtuális géphez.
1. Miután létrehozta a lemezt, hárítsa el a virtuális gép rendszerindítási környezetét.

   1. A következő parancs használatával érheti el a virtuális gépet root felhasználóként:

      `#sudo su -`

   1. Keresse meg a lemezt a használatával `dmesg` (az új lemez felderítéséhez használt módszer változhat). Az alábbi példa a **dmesg-et** használja az **SCSI** -lemezek szűrésére:

      `dmesg | grep SCSI`

      A kimenet a következő példához hasonló lesz. Ebben a példában a **SDC** lemezt szeretnénk használni:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. A kromát-környezet eléréséhez használja az alábbi parancsokat:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      ##chroot /rescue
      ```

   1. A kromát-környezet hibáinak megoldása.

   1. A kromát-környezetből való kilépéshez használja az alábbi parancsokat:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > Ha ezt a hibaüzenetet kapja `unable to unmount /rescue` , adja hozzá az-l kapcsolót a umount parancshoz.
      >
      > Például: `umount -l /rescue`

1. Válassza le a lemezt a mentési virtuális gépről, és hajtson végre egy lapozófájlt az eredeti virtuális géppel.
1. Indítsa el az eredeti virtuális gépet, és vizsgálja meg a kapcsolatát.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4, SUSE-SLES 12 SP4 SAP &&  # # SUSE-SLES 15 SP1, SUSE-SLES 15 SP1 for SAP

1. Az érintett virtuális gép leállítása vagy lefoglalása.
1. Hozzon létre egy azonos operációsrendszer-verzióval rendelkező mentési virtuálisgép-lemezképet ugyanazon az erőforráscsoport-(RSG-) és egy felügyelt lemezt használó helyen.
1. A Azure Portal segítségével készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről.
1. Hozzon létre egy lemezt a pillanatképből az operációsrendszer-lemezről, és csatolja a mentési virtuális géphez.
1. Miután létrehozta a lemezt, hárítsa el a virtuális gép rendszerindítási környezetét.

   1. A következő parancs használatával érheti el a virtuális gépet root felhasználóként:

      `#sudo su -`

   1. Keresse meg a lemezt a használatával `dmesg` (az új lemez felderítéséhez használt módszer változhat). Az alábbi példa a **dmesg-et** használja az **SCSI** -lemezek szűrésére:

      `dmesg | grep SCSI`

      A kimenet a következő példához hasonló lesz. Ebben a példában a **SDC** lemezt szeretnénk használni:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. A kromát-környezet eléréséhez használja az alábbi parancsokat:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc4 /rescue
      #mount -o nouuid /dev/sdc3 /rescue/boot/
      #mount /dev/sdc2 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. A kromát-környezet hibáinak megoldása.

   1. A kromát-környezetből való kilépéshez használja az alábbi parancsokat:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > Ha ezt a hibaüzenetet kapja `unable to unmount /rescue` , adja hozzá az-l kapcsolót a umount parancshoz.
      >
      > Például: `umount -l /rescue`

1. Válassza le a lemezt a mentési virtuális gépről, és hajtson végre egy lapozófájlt az eredeti virtuális géppel.
1. Indítsa el az eredeti virtuális gépet, és vizsgálja meg a kapcsolatát.

## <a name="next-steps"></a>Következő lépések

- [SSH-kapcsolatok hibáinak megoldása](troubleshoot-ssh-connection.md)