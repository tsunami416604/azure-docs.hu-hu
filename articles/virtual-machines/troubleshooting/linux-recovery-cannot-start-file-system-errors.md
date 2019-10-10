---
title: Linux rendszerű virtuális gép hibáinak elhárítása fájlrendszerbeli hibák miatt | Microsoft Docs
description: Ismerteti, hogy a Linux rendszerű virtuális gépek miért nem indíthatók el, és hogyan oldható meg a probléma.
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
ms.openlocfilehash: 298fd336e87d07f9e65221d5e5f539e255c94993
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245330"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Linux rendszerű virtuális gép hibáinak elhárítása fájlrendszerbeli hibák miatt

A Secure Shell (SSH) használatával nem lehet csatlakozni az Azure Linux rendszerű virtuális gépekhez (VM). Ha [Azure Portal](https://portal.azure.com/)futtatja a rendszerindítási diagnosztika szolgáltatást, az alábbi példához hasonló bejegyzéseket láthat.

## <a name="examples"></a>Példák

A következő példák a lehetséges hibákra mutatnak.

### <a name="example-1"></a>1\. példa 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>2\. példa

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>3\. példa

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>4\. példa 

Ezt a példát egy tiszta fsck okozza. Ebben az esetben a virtuális géphez (/dev/sdc1 és/dev/sde1) is vannak csatolva további adatlemezek.

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Ez a probléma akkor fordulhat elő, ha a fájlrendszer nem állt le tisztán vagy tárolással kapcsolatos problémákkal. A problémák közé tartoznak a hardveres vagy szoftveres hibák, az illesztőprogramok vagy programok hibái, az írási hibák stb. Mindig fontos, hogy biztonsági másolatot készítsen a kritikus fontosságú adatmennyiségekről. A cikkben ismertetett eszközök segíthetnek a fájlrendszerek helyreállításában, de az adatvesztés továbbra is előfordulhat.

A Linux rendszerben számos fájlrendszer-ellenőr érhető el. Az Azure-beli disztribúciók leggyakoribb része a következő: [FSCK](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)és [Xfs_repair](https://access.redhat.com/documentation/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Felbontás

A probléma megoldásához indítsa el a virtuális gépet vészhelyzeti módba a [soros konzol](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) használatával, és használja az eszközt a fájlrendszer kijavításához. Ha a soros konzol nincs engedélyezve a virtuális gépen, vagy nem működik, tekintse meg a jelen cikk [virtuális gép kijavítása offline](#repair-the-vm-offline) című szakaszát.

## <a name="use-the-serial-console"></a>A soros konzol használata

1. Kapcsolódjon a soros konzolhoz.

   > [!Note]
   > A soros konzol Linux rendszeren történő használatáról további információt a következő témakörben talál:
   > * [A soros konzol használata a GRUB és az single user mód eléréséhez](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Soros konzol használata SysRq-és NMI-hívásokhoz](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Válassza a főkapcsoló ikon gombot, majd válassza a virtuális gép újraindítása lehetőséget. (Ha a soros konzol nincs engedélyezve vagy nem csatlakozik sikeresen, a gomb nem jelenik meg.)

   ![LEMEZKÉP](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Indítsa el a virtuális gépet vészhelyzeti módba.

4. Adja meg a rendszergazdai fiók jelszavát a vészhelyzeti módba való bejelentkezéshez.

5. A xfs_repair használata a-n kapcsolóval a fájlrendszer hibáinak észleléséhez. A következő példában feltételezzük, hogy a rendszerpartíció/dev/sda1. van. Cserélje le a virtuális gép megfelelő értékére:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Futtassa a következő parancsot a fájlrendszer kijavításához:

   ```
   xfs_repair /dev/sda1
   ```

7. Ha a következő hibaüzenet jelenik meg: "hiba: a fájlrendszer értékes metaadatokat módosít egy naplóban, amelyet újra kell játszani", létre kell hoznia egy ideiglenes könyvtárat, és csatlakoztatnia kell a fájlrendszert:

   ```
   mkdir /temp
   mount /dev/sda2 /temp
   ```

8. Ha a lemez csatlakoztatása sikertelen, futtassa a xfs_repair parancsot az-L kapcsolóval (kényszerített log Zero):

   ```
   xfs_repair /dev/sda2 -L
   ```

9. Ezután próbálja meg csatlakoztatni a fájlrendszert. Ha a lemez sikeresen csatlakoztatva van, a következő kimenet jelenik meg:
 
   ```
   XFS (sda2): Mounting V1 Filesystem
   XFS (sda2): Ending clean mount
   ```

10. Indítsa újra a virtuális gépet, és ellenőrizze, hogy megoldódott-e a probléma.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>A virtuális gép kijavítása kapcsolat nélküli üzemmódban

1. Csatlakoztassa a virtuális gép rendszerlemezét adatlemezként egy helyreállítási virtuális géphez (bármilyen működő Linux rendszerű virtuális géphez). Ehhez használhatja a [CLI-parancsokat](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) , vagy automatizálhatja a helyreállítási virtuális gép beállítását a [virtuális gép javítási parancsainak](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)használatával.

2. Keresse meg a csatlakoztatott rendszerlemez meghajtójának címkéjét. Ebben az esetben feltételezzük, hogy a csatlakoztatott rendszerlemez címkéje/dev/sdc1. Cserélje le a virtuális gép megfelelő értékére.

3. A xfs_repair használata a-n kapcsolóval a fájlrendszer hibáinak észleléséhez.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Futtassa a következő parancsot a fájlrendszer kijavításához:

   ```
   xfs_repair /dev/sdc1
   ```

5. Ha a következő hibaüzenet jelenik meg: "hiba: a fájlrendszer értékes metaadatokat módosít egy naplóban, amelyet újra kell játszani", létre kell hoznia egy ideiglenes könyvtárat, és csatlakoztatnia kell a fájlrendszert:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Ha a lemez csatlakoztatása sikertelen, futtassa a xfs_repair parancsot az-L kapcsolóval (kényszerített log Zero):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Ezután próbálja meg csatlakoztatni a fájlrendszert. Ha a lemez sikeresen csatlakoztatva van, a következő kimenet jelenik meg:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Válassza le és válassza le az eredeti virtuális merevlemezt, majd hozzon létre egy virtuális gépet az eredeti rendszerlemezről. Ehhez használhatja a [CLI-parancsokat](troubleshoot-recovery-disks-linux.md) vagy a [virtuális gép javítási parancsait](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) , ha a helyreállítási virtuális gép létrehozásához használta őket.

8. Ellenőrizze, hogy a probléma megoldódott-e.

## <a name="next-steps"></a>Következő lépések

* [Linux rendszerű virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez az Azure CLI 2,0-vel való csatlakoztatásával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Adatlemez csatlakoztatása Linux rendszerű virtuális géphez a portál használatával](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

