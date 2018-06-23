---
title: Adatlemez hozzáadása a Linux virtuális gép az Azure parancssori felülettel |} Microsoft Docs
description: Ismerje meg, állandó adatlemezt hozzáadása a Linux virtuális Gépet az Azure-ral
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c41090943e4053ddf0ea46e9da1b3b5c7dbbf132
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331223"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)
Ez a cikk bemutatja, hogyan csatolni egy állandó lemezt a virtuális Gépet, hogy az adatok - megőrizhető akkor is, ha a virtuális gép van újra kiépíteni karbantartás vagy átméretezése miatt. 


## <a name="attach-a-new-disk-to-a-vm"></a>Új lemez csatolása a virtuális gépek

Ha szeretne egy új, üres adatlemez hozzáadása a virtuális gépen, használja a [az méretű lemez csatolása](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) parancsot a `--new` paraméter. Ha a virtuális Gépet egy rendelkezésre állási zónában, a lemez automatikusan létrejön a virtuális gép ugyanazon a területen. További információkért lásd: [rendelkezésre állási zónák áttekintése](../../availability-zones/az-overview.md). Az alábbi példakód létrehozza nevű lemez *myDataDisk* , amely 50 Gb-nál:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --disk myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása 

Meglévő lemez csatolása a lemez-azonosító található, és adja át az Azonosítót a [az méretű lemez csatolása](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) parancsot. A következő példa lekérdezések nevű lemez *myDataDisk* a *myResourceGroup*, majd csatolja a nevű virtuális gép *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```


## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Csatlakoztassa a Linux virtuális Gépet az új lemez csatlakoztatása
A partíció formázása és csatlakoztassa az új lemezt, a Linux virtuális gép használhassa az SSH-ból a virtuális Gépet. További információ: [SSH használata Linuxon az Azure-on](mac-create-ssh-keys.md). Az alábbi példa kapcsolódik a virtuális gép és a nyilvános DNS-bejegyzés a *mypublicdns.westus.cloudapp.azure.com* a felhasználónévvel *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Miután csatlakozott a virtuális Gépet, készen áll a lemezt csatolni. Először keresse meg a lemezt használó `dmesg` (az új lemez felderítéséhez használt módszer változhat). Az alábbi példában dmesg szűréshez *SCSI* lemezek:

```bash
dmesg | grep SCSI
```

A kimenet a következő példához hasonló:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Itt *sdc* azt szeretnénk, ha a lemez. A lemez particionálása `fdisk`, legyen egy elsődleges szabad a %1 partíció, és fogadja el az alapértelmezett beállításokat. A következő példában elindul a `fdisk` folyamat */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Használja a `n` parancs használatával adja hozzá az új partíciót. Ebben a példában azt is választhatja, `p` az elsődleges partíció, és fogadja el a többi alapértelmezett értékkel. A kimenet az alábbihoz hasonló lesz:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Írja be a partíciós táblán nyomtatása `p` majd `w` írása a lemezre, és lépjen ki a tábla. A kimenet az alábbihoz hasonlóan kell kinéznie:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Most, a fájlrendszer írni a partíció a `mkfs` parancsot. Adja meg a fájlrendszer típusát és az eszköz nevét. Az alábbi példa létrehoz egy *ext4* a fájlrendszer a */dev/sdc1* az előző lépésben létrehozott partíciót:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

A kimenet a következő példához hasonló:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Most, hozzon létre egy könyvtárat, csatlakoztassa a fájlkiszolgáló rendszer az `mkdir`. Az alábbi példa létrehoz egy könyvtárat, */datadrive*:

```bash
sudo mkdir /datadrive
```

Használjon `mount` a fájlrendszer majd csatlakoztatni. Az alábbi példa csatlakoztatja a */dev/sdc1* a partícióazonosító a */datadrive* csatlakoztatási pontot:

```bash
sudo mount /dev/sdc1 /datadrive
```

Annak érdekében, hogy a meghajtó a rendszer újraindítása után automatikusan csatlakoztatni, azt kell felvenni a */etc/fstab* fájlt. Is ajánlott szolgál, hogy a UUID (univerzálisan egyedi azonosítója) */etc/fstab* csak az eszköz neve helyett hivatkozik (például a */dev/sdc1*). Az operációs rendszer lemezhiba rendszerindítás során észleli, ha a UUID használatával elkerülhető a hibás lemez, folyamatban van egy adott helyre. Adatlemezek fennmaradó volna lehet hozzárendelni ezeket azonos eszközazonosítókat. Az új meghajtó UUID megkereséséhez használja a `blkid` segédprogram:

```bash
sudo -i blkid
```

A kimeneti az alábbihoz hasonlít:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Nem megfelelő módosítása a **/etc/fstab** fájl meghiúsulását eredményezheti. Ha nem ismeri, tekintse meg a telepítési dokumentációban talál információkat arról a fájl megfelelően szerkesztése. Ajánlott továbbá, hogy létrejött-e a fájl biztonsági másolata /etc/fstab szerkesztése előtt.

Ezután nyissa meg a */etc/fstab* fájlt egy szövegszerkesztőben az alábbiak szerint:

```bash
sudo vi /etc/fstab
```

Ebben a példában az UUID érték legyen használva a */dev/sdc1* jött létre az előző lépést, és a csatlakoztatási pont az eszközök */datadrive*. Adja hozzá az alábbi végének a */etc/fstab* fájlt:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Később eltávolítása adatlemezt fstab szerkesztése nélkül, hogy a virtuális gépek a rendszerindítás. A legtöbb terjesztéseket adja meg, vagy a *nofail* és/vagy *nobootwait* fstab beállítások. Ezek a beállítások lehetővé teszik a rendszert a akkor is, ha a lemez meghibásodik csatlakoztatni a rendszerindítás. A terjesztési dokumentációjában talál további információt ezekről a paraméterekről.
> 
> A *nofail* lehetőség biztosítja, hogy a virtuális gép elindul, még akkor is, ha a fájlrendszer sérült, vagy a lemez nem létezik a következő rendszerindításkor. Ez a beállítás nélkül felmerülhet viselkedés leírtak [nem SSH Linux virtuális gép között az FSTAB hibák miatt](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Az Azure Linux vágást/UNMAP támogatása
Egyes Linux kernelek támogatja a vágás/UNMAP műveleteket elveti a nem használt blokkok a lemezen. Ez a funkció olyan elsősorban hasznos, Standard szintű tárolást tájékoztatja Azure törölt lapok már nem érvényesek és is elvesznek, és pénzt is mentheti, ha nagy fájlok létrehozása, majd törli őket.

Két módon vágást engedélyezése támogatja a Linux virtuális gép van. A szokásos módon olvassa el a terjesztési az ajánlott módszer:

* Használja a `discard` lehetőség a csatlakoztatási */etc/fstab*, például:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Bizonyos esetekben a `discard` lehetőség is van a teljesítményre. Alternatív megoldásként futtathatja a `fstrim` manuálisan parancsot a parancssorból, vagy adja hozzá a crontab rendszeresen futtatásához:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL vagy CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>További lépések
* Annak érdekében, hogy megfelelően van-e konfigurálva a Linux virtuális Gépre, tekintse át a [a Linux-gépek teljesítményének optimalizálásához](optimization.md) javaslatok.
* Bontsa ki a tárolási kapacitás további lemezek felvételével és [RAID konfigurálása](configure-raid.md) további teljesítmény.

