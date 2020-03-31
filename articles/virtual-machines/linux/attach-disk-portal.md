---
title: Adatlemez csatolása Linux os virtuális géphez
description: A portál használatával új vagy meglévő adatlemezt csatolhat egy Linux virtuális géphez.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 746cef8dfe026c731a677cbf77f729d36342f007
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969356"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Adatlemez csatolása a portálon Linux virtuális géphez 
Ez a cikk bemutatja, hogyan csatolhatja az új és a meglévő lemezeket egy Linux virtuális gép az Azure Portalon keresztül. Adatlemezt is [csatolhat egy Windows virtuális géphez az Azure Portalon.](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 

Mielőtt lemezeket csatolna a virtuális géphez, tekintse át az alábbi tippeket:

* A virtuális gép mérete határozza meg, hogy hány adatlemez csatlakoztatható. További információt a [Virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* A virtuális gépekhez csatlakoztatott lemezek valójában .vhd fájlok at tárolnak az Azure-ban. További részletek a [Felügyelt lemezek bevezetéscímű témakörben talál.](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* A lemez csatlakoztatása után csatlakoznia kell [a Linux virtuális géphez az új lemez csatlakoztatásához](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>A virtuális gép megkeresése
1. A virtuális gép megkereséséhez nyissa meg az [Azure Portalon.](https://portal.azure.com/) Keressen és válasszon **virtuális gépek**lehetőséget.
2. Válassza ki a virtuális gép a listából.
3. A **Virtuális gépek** lap oldalsávja **Beállítások**csoportban válassza a **Lemezek**lehetőséget.
   
    ![Lemezbeállítások megnyitása](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Új lemez csatolása

1. A **Lemezek** ablaktáblán kattintson a **+ Adatlemez hozzáadása**gombra .
2. Kattintson a **Név** menülegördülő menüre, és válassza **a Lemez létrehozása parancsot:**

    ![Azure felügyelt lemez létrehozása](./media/attach-disk-portal/create-new-md.png)

3. Adja meg a felügyelt lemez nevét. Tekintse át az alapértelmezett beállításokat, frissítse szükség szerint, majd kattintson a **Létrehozás gombra.**
   
   ![Lemezbeállítások áttekintése](./media/attach-disk-portal/create-new-md-settings.png)

4. A felügyelt lemez létrehozásához és a virtuális gép konfigurációjának frissítéséhez kattintson a **Mentés** gombra:

   ![Új Azure felügyelt lemez mentése](./media/attach-disk-portal/confirm-create-new-md.png)

5. Miután az Azure létrehozza a lemezt, és csatolja a virtuális géphez, az új lemez megjelenik a virtuális gép lemezbeállításai között **Adatlemezek**. Mivel a felügyelt lemezek legfelső szintű erőforrások, a lemez az erőforráscsoport gyökerében jelenik meg:

   ![Azure felügyelt lemez az erőforráscsoportban](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása
1. A **Lemezek** ablaktáblán kattintson a **+ Adatlemez hozzáadása**gombra .
2. Kattintson a **Név** legördülő menüre az Azure-előfizetése számára elérhető meglévő felügyelt lemezek listájának megtekintéséhez. Válassza ki a csatolni kívánt felügyelt lemezt:

   ![Meglévő Azure felügyelt lemez csatolása](./media/attach-disk-portal/select-existing-md.png)

3. Kattintson a **Mentés** gombra a meglévő felügyelt lemez csatolásához és a virtuális gép konfigurációjának frissítéséhez:
   
   ![Az Azure felügyelt lemez frissítéseinek mentése](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Miután az Azure csatlakoztatja a lemezt a virtuális géphez, az megjelenik a virtuális gép lemezbeállításai között **Adatlemezek**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Csatlakozás a Linux virtuális géphez az új lemez csatlakoztatásához
Particionálás, formátum, és csatlakoztassa az új lemezt, így a Linux virtuális gép használhatja, SSH a virtuális gép. További információ: [SSH használata Linuxon az Azure-on](mac-create-ssh-keys.md). A következő példa csatlakozik egy virtuális géphez a *mypublicdns.westus.cloudapp.azure.com* nyilvános DNS-bejegyzésével az *azureuser*felhasználónévvel: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Miután csatlakozott a virtuális géphez, készen áll a lemez csatlakoztatására. Először keresse meg `dmesg` a lemezt (az új lemez felderítéséhez használt módszer eltérő lehet). A következő példa a dmesg segítségével szűri az *SCSI-lemezeket:*

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

Itt, *sdc* van a korong amit mi akar. 

### <a name="partition-a-new-disk"></a>Új lemez particionálása
Ha adatokat tartalmazó meglévő lemezt használ, ugorjon a lemez csatlakoztatására. Ha új lemezt csatol, particionálnia kell a lemezt.

> [!NOTE]
> Javasoljuk, hogy az fdisk vagy a szétválás legújabb, a szétvezető készülékhez elérhető verziót használja.

Particionálja a lemezt az `fdisk` használatával. Ha a lemez mérete 2 tebibyte (TiB) vagy nagyobb, akkor GPT-particionálást kell használnia, gpt particionálást hajthat `parted` végre. Ha a lemez mérete 2TiB alatt van, akkor mbr vagy GPT particionálást is használhat. Legyen elsődleges lemez az 1-es partíción, és fogadja el a többi alapértelmezést. A következő példa `fdisk` elindítja a folyamatot a */dev/sdc oldalon:*

```bash
sudo fdisk /dev/sdc
```

Az `n` paranccsal adhat hozzá egy új partíciót. Ebben a példában `p` is választhat egy elsődleges partíciót, és elfogadja a többi alapértelmezett értékeket. A kimenet az alábbi példához hasonló lesz:

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

Nyomtassa ki `p` a partíciós táblát gépeléssel, majd `w` írja meg a táblát lemezre, majd lépjen ki. A kimenetnek a következő példához hasonlóan kell kinéznie:

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

Most írjon egy fájlrendszert a `mkfs` partícióra a paranccsal. Adja meg a fájlrendszer típusát és az eszköz nevét. A következő példa létrehoz egy *ext4* fájlrendszert a */dev/sdc1* partíción, amelyet az előző lépésekben hoztak létre:

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

#### <a name="alternate-method-using-parted"></a>Alternatív módszer a parted használatával
Az fdisk segédprogramnak interaktív bevitelre van szüksége, ezért nem ideális az automatizálási parancsfájlokon belüli használatra. Bármennyire, a [elválaszt](https://www.gnu.org/software/parted/) típus- lehet kézírás és így lendül jobb -ban automatizálás forgatókönyv. Az elpart segédprogram segítségével particionálható és formázható egy adatlemez. Az alábbi forgatókönyvhez egy új adatlemezt használunk /dev/sdc, és formázzuk az [XFS](https://xfs.wiki.kernel.org/) fájlrendszer használatával.
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
partprobe /dev/sdc1
```
Mint fent ebbi, használjuk a [partprobe](https://linux.die.net/man/8/partprobe) segédprogram, hogy megbizonyosodjon arról, a kernel azonnal tisztában van az új partíció és fájlrendszer. A partprobe használatának elmulasztása azt eredményezheti, hogy a blkid vagy lslbk parancsok nem adják vissza azonnal az új fájlrendszer UUID azonosítóját.

### <a name="mount-the-disk"></a>A lemez csatlakoztatása
Hozzon létre egy könyvtárat `mkdir`a fájlrendszer csatlakoztatására a használatával. A következő példa létrehoz egy könyvtárat a */datadrive-on:*

```bash
sudo mkdir /datadrive
```

Ezután `mount` csatlakoztatja a fájlrendszert. A következő példa a */dev/sdc1* partíciót a */datadrive* csatlakoztatási ponthoz csatlakoztatja:

```bash
sudo mount /dev/sdc1 /datadrive
```

Annak érdekében, hogy a meghajtó újraindítás után automatikusan újra fellegyen szerelve, hozzá kell adni az */etc/fstab* fájlhoz. Erősen ajánlott továbbá, hogy az UUID (Univerzálisan egyedi azonosító) az */etc/fstab* kapcsolóban a meghajtóra hivatkozzon, ne csak az eszköz nevére (például */dev/sdc1).* Ha indítás közben az operációs rendszer lemezhibát észlel, az UUID használatával elkerülhető egy nem megfelelő lemez a megadott helyre történő csatolása. A többi adatlemez ettől még ugyanazokat az eszközazonosítókat kapja. Az új meghajtó UUID-jének megkereséséhez használja a `blkid` segédprogramot:

```bash
sudo -i blkid
```

A kimenet a következő példához hasonlóan néz ki:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Az **/etc/fstab** fájl helytelen szerkesztése nem indítható rendszert eredményezhet. Ha nem biztos a dolgában, a fájl megfelelő szerkesztésével kapcsolatos információkért olvassa el a disztribúció dokumentációját. Azt is javasoljuk, hogy az /etc/fstab fájl biztonsági mentése szerkesztés előtt létrejön.

Ezután nyissa meg az */etc/fstab* fájlt egy szövegszerkesztőben az alábbiak szerint:

```bash
sudo vi /etc/fstab
```

Ebben a példában használja az UUID értéket az előző lépésekben létrehozott */dev/sdc1* eszközhöz, valamint a /datadrive csatlakoztatási *pontját.* Adja hozzá a következő sort az */etc/fstab* fájl végéhez:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```
Ha elkészült, mentse az */etc/fstab* fájlt, és indítsa újra a rendszert.
> [!NOTE]
> Később eltávolítása adatlemez szerkesztés nélkül fstab okozhat a virtuális gép nem indul el. A legtöbb disztribúció vagy a *nofail* és/vagy *nobootwait* fstab beállításokat biztosítja. Ezek a beállítások lehetővé teszik a rendszer indítását akkor is, ha a lemez nem csatlakoztatva van a rendszerindításkor. Ezekről a paraméterekről további információt a disztribúció dokumentációjában talál.
> 
> A *nofail* beállítás biztosítja, hogy a virtuális gép akkor is elindul, ha a fájlrendszer sérült, vagy a lemez nem létezik a rendszerindításkor. E lehetőség nélkül [fstab hibák miatt a Cannot SSH–Linux VM című](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) részben leírtak szerint előfordulhat viselkedés.

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP támogatás Linuxhoz az Azure-ban
Egyes Linux kernelek támogatják a TRIM/UNMAP műveleteket a nem használt blokkok elvetéséhez a lemezen. Ez a funkció elsősorban a szabványos tárolás, hogy tájékoztassa az Azure-t, hogy a törölt lapok már nem érvényesek, és eldobhatók, és pénzt takaríthat meg, ha nagy fájlokat hoz létre, majd törli őket.

A LINUX virtuális gépben kétféleképpen engedélyezheti a TRIM-támogatást. Mint mindig, konzultáljon a forgalmazás az ajánlott megközelítés:

* Használja `discard` a csatlakoztatási lehetőséget az */etc/fstab kapcsolóban,* például:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Bizonyos esetekben `discard` a lehetőség nek teljesítménybeli következményei lehetnek. Azt is megteheti, `fstrim` hogy a parancsot manuálisan futtatja a parancssorból, vagy hozzáadja a crontab-hoz, hogy rendszeresen fusson:
  
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

## <a name="next-steps"></a>További lépések
Az Azure CLI használatával [is csatolhat adatlemezt.](add-disk.md)
