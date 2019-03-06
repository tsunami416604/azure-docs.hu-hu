---
title: Adatlemez hozzáadása Linux rendszerű virtuális géphez az Azure parancssori felületével |} A Microsoft Docs
description: Ismerje meg, egy állandó adatlemezt hozzáadása a Linux rendszerű virtuális gép az Azure CLI-vel
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
ms.subservice: disks
ms.openlocfilehash: 81805188c72bce6a7ea89496c8036743b29e9075
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452759"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)
Ez a cikk bemutatja, hogyan való állandó lemez csatolása a virtuális gép így megőrizheti az adatait – még akkor is, ha a virtuális gép a karbantartás vagy átméretezése miatt van kiépíteni.


## <a name="attach-a-new-disk-to-a-vm"></a>Új lemez csatolása virtuális géphez

Ha szeretne hozzáadni egy új, üres adatlemezt a virtuális Gépen, használja a [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) parancsot a `--new` paraméter. Ha a virtuális gép egy rendelkezésre állási zónában, a lemez automatikusan létrejön ugyanabban a zónában, a virtuális géppel. További információkért lásd: [a rendelkezésre állási zónákat áttekintő](../../availability-zones/az-overview.md). A következő példában létrehozunk egy nevű lemez *myDataDisk* , amely 50 Gb méretű:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --disk myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása

Meglévő lemez csatlakoztatása, keresse meg a lemez Azonosítóját, és adja át az Azonosítót, a [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) parancsot. A következő példalekérdezés nevű lemez *myDataDisk* a *myResourceGroup*, majd csatolja azt a virtuális gép nevű *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Csatlakoztassa az új lemezt a Linux rendszerű virtuális gép kapcsolódni

A partíció formázása és csatlakoztassa az új lemezt a Linux rendszerű virtuális gép is azt, SSH-t a virtuális gép használja. További információ: [SSH használata Linuxon az Azure-on](mac-create-ssh-keys.md). Az alábbi példában a nyilvános DNS-bejegyzést a virtuális gép csatlakozik *mypublicdns.westus.cloudapp.azure.com* , amelynek *azureuser*:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Miután csatlakozott a virtuális gép, készen áll a lemezt. Először keresse meg a lemez használatával `dmesg` (a metódus segítségével fogja felderíteni az új lemez eltérőek lehetnek). Az alábbi példában dmesg szűrés *SCSI* lemezek:

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

Itt *sdc* a lemezt, amelyet meg szeretnénk. Particionálja a lemezt a `parted`, ha a lemez mérete 2 tebibytes (TiB) vagy nagyobb, akkor kell használnia a GPT particionálás, ha alatt 2TiB, használhatja az MBR vagy GPT particionálás. Ha MBR particionálás használata esetén használhatja `fdisk`. Adja meg egy elsődleges lemez 1 partíción, és fogadja el a többi alapértelmezett értéket. A következő példa elindítja a `fdisk` folyamatát */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Használja a `n` parancs használatával adja hozzá egy új partíciót. Ebben a példában is lehetőséget választjuk `p` egy elsődleges partíció, és fogadja el az alapértelmezett értékeket a többi. A kimenet a következő példához hasonló lesz:

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

Nyomtassa ki a partíciós táblán beírásával `p` majd `w` írni a lemezre, és lépjen ki a táblában. A kimenet a következő példához hasonlóan kell kinéznie:

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
Használja az alábbi parancsot a kernel frissítése:
```
partprobe 
```

Most írjon fájlrendszert a partícióra az `mkfs` parancsot. Adja meg a fájlrendszer típusát és az eszköz nevét. A következő példában létrehozunk egy *ext4* a fájlrendszer a */dev/sdc1* az előző lépésekben létrehozott partíciót:

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

Most hozzon létre egy könyvtárat, a fájl rendszer használatával csatlakoztassa `mkdir`. A következő példában létrehozunk egy könyvtárban található */datadrive*:

```bash
sudo mkdir /datadrive
```

Használat `mount` majd csatlakoztatni a fájlrendszer. Az alábbi példa csatlakoztatja a */dev/sdc1* történő particionálása a */datadrive* csatlakoztatási pont:

```bash
sudo mount /dev/sdc1 /datadrive
```

Annak érdekében, hogy a meghajtó újraindítás után automatikusan csatlakoztatni, hozzá kell adni a a */etc/fstab* fájlt. Azt is javasoljuk szolgál, hogy az UUID (univerzálisan egyedi azonosító) */etc/fstab* lehet hivatkozni az eszköznév helyett (például */dev/sdc1*). Ha az operációs rendszer észleli a lemezhiba rendszerindítás során, UUID használatával elkerülheti a helytelen lemez, folyamatban van egy adott helyre. Adatlemezek fennmaradó majd hozzájuk ezek azonos eszközazonosítókat. Az új meghajtó UUID megkereséséhez használja a `blkid` segédprogrammal:

```bash
sudo blkid
```

A kimenet a következő példához hasonlóan néz ki:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Nem megfelelően szerkesztése a **/etc/fstab** fájl meghiúsulását eredményezheti. Ha nem tudja biztosan, tekintse meg a telepítési dokumentációban talál információkat megfelelően szerkesztése ezt a fájlt. Emellett javasoljuk, hogy a /etc/fstab fájl biztonsági másolata jön létre szerkesztése előtt.

Ezután nyissa meg a */etc/fstab* módon fájlt egy szövegszerkesztőben:

```bash
sudo vi /etc/fstab
```

Ebben a példában használja az UUID azonosító értékét a */dev/sdc1* létrehozott az előző lépést, és a csatlakoztatási pont az eszköz */datadrive*. Adja hozzá a következő sor végére a */etc/fstab* fájlt:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Később fstab szerkesztése nélkül az adatlemez eltávolítása a virtuális gép rendszerindítás okozhatnak. A legtöbb disztribúciók adja meg, vagy a *nofail* és/vagy *nobootwait* fstab beállítások. Ezek a beállítások lehetővé teszik a rendszert a akkor is, ha a lemezhiba rendszerindítás csatlakoztatásához. További információ ezekről a paraméterekről a disztribúció dokumentációjában.
>
> A *nofail* beállítással biztosíthatja, hogy a virtuális gép elindul még akkor is, ha a fájlrendszer sérült, vagy a lemez nem létezik a rendszerindítás közben. Ez a beállítás nélkül felmerülhet viselkedés leírtak szerint [nem SSH a Linux rendszerű virtuális gépek FSTAB-hibák miatt](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)
>
> Az Azure virtuális gép soros konzoljához konzolhozzáférést biztosít a virtuális gép használható, ha módosítja az fstab egy rendszerindítási hibát eredményezett. További részleteket a [soros konzol dokumentáció](https://docs.microsoft.com/en-us/azure/virtual-machines/troubleshooting/serial-console-linux).

### <a name="trimunmap-support-for-linux-in-azure"></a>Az Azure-ban Linux TRIM/UNMAP támogatása
Egyes Linux-kernelek vannak a elveti a nem használt blokkolja a lemez TRIM/UNMAP műveletek támogatásához. Ez a funkció elsősorban hasznos oldalak törölt Azure már nem érvényesek, és lehet elvetni, és pénzt takaríthat meg, ha nagy méretű fájlokat hoz létre, és törölje őket tájékoztatása standard szintű tárolóban.

Nincsenek a TRIM engedélyezéséhez kétféleképpen támogatja a Linux rendszerű virtuális gépen. A szokásos módon tekintse meg a terjesztési az ajánlott módszer:

* Használja a `discard` lehetőség a csatlakoztatási */etc/fstab*, például:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Bizonyos esetekben a `discard` lehetőség is van a teljesítményre. Másik megoldásként futtathatja a `fstrim` manuálisan parancsot a parancssorból, vagy adja hozzá a crontab rendszeresen futtatásához:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Hibaelhárítás

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>További lépések

* Annak érdekében, hogy a Linux rendszerű virtuális gép megfelelően van konfigurálva, tekintse át a [a Linux-gép teljesítményének optimalizálásához](optimization.md) javaslatok.
* Bontsa ki a tárolási kapacitás további lemezek felvételével és [RAID konfigurálása](configure-raid.md) további teljesítmény érdekében.
