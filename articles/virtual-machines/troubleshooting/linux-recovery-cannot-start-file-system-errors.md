---
title: A Linux virtuális gép indítási problémáinak elhárítása a fájlrendszer hibái miatt | Microsoft dokumentumok
description: A linuxos virtuális gép indításának okát és a probléma megoldását ismerteti.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842401"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>A Linux virtuális gépek indítási problémáinak elhárítása a fájlrendszer hibái miatt

Nem tud csatlakozni egy Azure Linux virtuális gép (VM) biztonságos rendszerhéj (SSH) használatával. Amikor futtatja a rendszerindítási diagnosztika funkciót az [Azure Portalon,](https://portal.azure.com/)az alábbi példákhoz hasonló naplóbejegyzéseket láthat.

## <a name="examples"></a>Példák

Az alábbi példák példák a lehetséges hibákra.

### <a name="example-1"></a>1. példa 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>2. példa

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>3. példa

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>4. példa 

Ezt a példát egy tiszta fsck okozza. Ebben az esetben a virtuális géphez (/dev/sdc1 és /dev/sde1) további adatlemezek is találhatók.

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Ez a probléma akkor fordulhat elő, ha a fájlrendszer nem állt le tisztán, vagy a tárolással kapcsolatos problémák. A problémák közé tartozik a hardver vagy szoftver hibák, problémák illesztőprogramok vagy programok, írási hibák, stb Mindig fontos, hogy készítsen biztonsági másolatot a kritikus fontosságú adatokról. A cikkben ismertető eszközök segíthetnek a fájlrendszerek helyreállításában, de az adatvesztés továbbra is előfordulhat.

Linux több fájlrendszer ellenőrzők állnak rendelkezésre. Az Azure-beli disztribúciók leggyakoribbak: [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)és [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Megoldás:

A probléma megoldásához indítsa el a virtuális gép vészüzemmódba a [soros konzol](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) használatával, és használja ezt az eszközt a fájlrendszer javításához. Ha a soros konzol nincs engedélyezve a virtuális gép, vagy nem működik, tekintse meg [a javítás a virtuális gép offline](#repair-the-vm-offline) szakaszában ebben a cikkben.

## <a name="use-the-serial-console"></a>A soros konzol használata

1. Csatlakozzon a soros konzolhoz.

   > [!Note]
   > A soros konzol Linuxon való használatáról a következő témakörben talál további információt:
   > * [A GRUB és az egyfelhasználós mód eléréséhez használjon soros konzolt](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Soros konzol használata SysRq és NMI hívásokhoz](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Kattintson a Bekapcsoló ikon gombra, majd a Virtuális gép újraindítása parancsra. (Ha a soros konzol nincs engedélyezve vagy nincs sikeresen csatlakoztatva, a gomb nem jelenik meg.)

   ![KÉP](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Indítsa el a virtuális gép vészhelyzeti üzemmódba.

4. Adja meg a gyökérfiók jelszavát a vészhelyzeti módba való bejelentkezéshez.

5. A fájlrendszer hibáinak észleléséhez használja a xfs_repair a -n kapcsolóval. A következő mintában feltételezzük, hogy a rendszerpartíció /dev/sda1. Cserélje le a virtuális gép megfelelő értékére:

   ```
   xfs_repair -n /dev/sda1
   ```

6. A fájlrendszer javításához futtassa a következő parancsot:

   ```
   xfs_repair /dev/sda1
   ```

7. Ha a következő hibaüzenet jelenik meg: "HIBA: A fájlrendszer értékes metaadat-változásokkal rendelkezik egy naplóban, amelyet újra le kell játszani", hozzon létre egy ideiglenes könyvtárat, és csatlakoztassa a fájlrendszert:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Ha a lemez csatlakoztatása nem sikerül, futtassa a xfs_repair parancsot a -L kapcsolóval (a force log nullázása):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Ezután próbálja meg csatlakoztatni a fájlrendszert. Ha a lemez csatlakoztatása sikeres, a következő kimenetet kapja:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Indítsa újra a virtuális gép, majd ellenőrizze, hogy a probléma megoldódott.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>A virtuális gép kapcsolat nélküli javítása

1. Csatlakoztassa a virtuális gép rendszerlemezét adatlemezként egy helyreállítási virtuális géphez (bármely működő Linux virtuális géphez). Ehhez használhatja a [CLI parancsokat,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) vagy automatizálhatja a helyreállítási virtuális gép beállítását a [virtuális gép javítási parancsaival.](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

2. Keresse meg a csatlakoztatott rendszerlemez meghajtócímkéjét. Ebben az esetben feltételezzük, hogy a csatlakoztatott rendszerlemez címkéje /dev/sdc1. Cserélje le a virtuális gép megfelelő értékére.

3. A fájlrendszer hibáinak észleléséhez használja a xfs_repair a -n kapcsolóval.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. A fájlrendszer javításához futtassa a következő parancsot:

   ```
   xfs_repair /dev/sdc1
   ```

5. Ha a következő hibaüzenet jelenik meg: "HIBA: A fájlrendszer értékes metaadat-változásokkal rendelkezik egy naplóban, amelyet újra le kell játszani", hozzon létre egy ideiglenes könyvtárat, és csatlakoztassa a fájlrendszert:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Ha a lemez csatlakoztatása nem sikerül, futtassa a xfs_repair parancsot a -L kapcsolóval (a force log nullázása):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Ezután próbálja meg csatlakoztatni a fájlrendszert. Ha a lemez csatlakoztatása sikeres, a következő kimenetet kapja:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Válassza le és válassza le az eredeti virtuális merevlemezt, majd hozzon létre egy virtuális gépet az eredeti rendszerlemezről. Ehhez [használhatja a CLI-parancsokat](troubleshoot-recovery-disks-linux.md) vagy a [virtuális gép javítási parancsait,](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) ha a helyreállítási virtuális gép létrehozásához használta őket.

8. Ellenőrizze, hogy megoldódott-e a probléma.

## <a name="next-steps"></a>További lépések

* [Linuxos virtuális gép hibaelhárítása az operációs rendszer lemezének az Azure CLI 2.0-s környezetével egy helyreállítási virtuális géphez való csatlakoztatásával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Adatlemez csatolása a portálon Linux virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

