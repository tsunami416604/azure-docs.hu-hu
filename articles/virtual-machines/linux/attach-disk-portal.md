---
title: Adatlemez csatolása Linux rendszerű virtuális géphez
description: A portál használatával új vagy meglévő adatlemezt csatolhat egy linuxos virtuális géphez.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: c0129ba35841a457e73ef1061d156d3246cb65f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84660212"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Adatlemez csatlakoztatása Linux rendszerű virtuális géphez a portál használatával 
Ez a cikk bemutatja, hogyan csatolhat új és meglévő lemezeket egy linuxos virtuális géphez a Azure Portal keresztül. [Adatlemezt a Azure Portal egy Windows rendszerű virtuális géphez is csatolhat](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Mielőtt lemezeket csatol a virtuális géphez, tekintse át a következő tippeket:

* A virtuális gép mérete határozza meg, hogy hány adatlemezt tud csatlakoztatni. Részletekért lásd: [virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* A virtuális gépekhez csatolt lemezek valójában az Azure-ban tárolt VHD-fájlok. Részletekért tekintse [meg a Managed Disks bemutatása](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakört.
* A lemez csatolása után [csatlakoznia kell a Linux rendszerű virtuális géphez az új lemez csatlakoztatásához](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>A virtuális gép megkeresése
1. A virtuális gép megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com/) . Keresse meg és válassza ki a **virtuális gépeket**.
2. Válassza ki a virtuális gépet a listából.
3. A **virtuális gépek** lap oldalsávján, a **Beállítások**területen válassza a **lemezek**elemet.
   
    ![Lemez beállításainak megnyitása](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Új lemez csatolása

1. A **lemezek** ablaktáblán kattintson az **+ adatlemez hozzáadása**elemre.
2. Kattintson a **Name (név** ) legördülő menüre, és válassza a **lemez létrehozása**elemet:

    ![Azure Managed Disk létrehozása](./media/attach-disk-portal/create-new-md.png)

3. Adja meg a felügyelt lemez nevét. Tekintse át az alapértelmezett beállításokat, szükség szerint frissítsen, majd kattintson a **Létrehozás**gombra.
   
   ![Lemez beállításainak áttekintése](./media/attach-disk-portal/create-new-md-settings.png)

4. A felügyelt lemez létrehozásához és a virtuális gép konfigurációjának frissítéséhez kattintson a **Mentés** gombra:

   ![Új Azure Managed Disk mentése](./media/attach-disk-portal/confirm-create-new-md.png)

5. Miután az Azure létrehozta a lemezt, és csatolja a virtuális géphez, az új lemez megjelenik a virtuális gép lemez-beállításaiban az **adatlemezek**területen. Mivel a felügyelt lemezek egy legfelső szintű erőforrás, a lemez az erőforráscsoport gyökerénél jelenik meg:

   ![Azure felügyelt lemez az erőforráscsoport-ban](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása
1. A **lemezek** ablaktáblán kattintson az **+ adatlemez hozzáadása**elemre.
2. Az Azure-előfizetéshez elérhető meglévő felügyelt lemezek listájának megtekintéséhez kattintson a **név** legördülő menüjére. Válassza ki a csatolni kívánt felügyelt lemezt:

   ![Meglévő Azure Managed Disk csatolása](./media/attach-disk-portal/select-existing-md.png)

3. Kattintson a **Save (Mentés** ) gombra a meglévő felügyelt lemez csatolásához és a virtuális gép konfigurációjának frissítéséhez:
   
   ![Azure Managed Disk-frissítések mentése](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Miután az Azure csatlakoztatta a lemezt a virtuális géphez, az a virtuális gép lemezének beállításaiban szerepel az **adatlemezek**területen.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Csatlakozás a Linux rendszerű virtuális géphez az új lemez csatlakoztatásához
Az új lemez particionálásához, formázásához és csatlakoztatásához, így a linuxos virtuális gép használhatja az SSH-t a virtuális géphez. További információ: [SSH használata Linuxon az Azure-on](mac-create-ssh-keys.md). A következő példa egy olyan virtuális géphez csatlakozik, amelyen a *mypublicdns.westus.cloudapp.Azure.com* nyilvános DNS-bejegyzése szerepel a Felhasználónév *azureuser*használatával: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

A virtuális géphez való csatlakozás után készen áll a lemez csatlakoztatására. Először keresse meg a lemezt a használatával `dmesg` (az új lemez felderítéséhez használt módszer változhat). Az alábbi példa a dmesg-et használja az *SCSI* -lemezek szűrésére:

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

Itt a *SDC* a kívánt lemez. 

### <a name="partition-a-new-disk"></a>Új lemez particionálása
Ha olyan meglévő lemezt használ, amely tartalmaz egy adatkészletet, ugorjon a lemez csatlakoztatása elemre. Ha új lemezt csatol, particionálnia kell a lemezt.

> [!NOTE]
> Azt javasoljuk, hogy használja az fdisk legújabb verzióit, illetve azokat, amelyek elérhetők a disztribúcióhoz.

Particionálja a lemezt az `fdisk` használatával. Ha a lemez mérete 2 tebibájt (TiB) vagy nagyobb, akkor GPT particionálást kell használnia `parted` . Ha a lemez mérete 2TiB alatt van, akkor MBR-vagy GPT-particionálást is használhat. Legyen az 1. partíció elsődleges lemeze, és fogadja el a többi alapértelmezett értéket. A következő példa elindítja a `fdisk` folyamatot a */dev/SDC*:

```bash
sudo fdisk /dev/sdc
```

Az `n` paranccsal adhat hozzá egy új partíciót. Ebben a példában az elsődleges partíciót is választjuk, `p` és elfogadjuk a többi alapértelmezett értéket. A kimenet az alábbi példához hasonló lesz:

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

A partíciós tábla kinyomtatásához írja be a parancsot, `p` majd `w` írja be a táblát a lemezre, és lépjen ki. A kimenetnek az alábbi példához hasonlóan kell kinéznie:

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

Most írjon egy fájlrendszert a partícióra a `mkfs` paranccsal. Adja meg a fájlrendszer típusát és az eszköz nevét. Az alábbi példa egy *ext4* fájlrendszert hoz létre az előző lépésekben létrehozott */dev/sdc1* -partíción:

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

#### <a name="alternate-method-using-parted"></a>Alternatív metódus, amely a szakaszos
Az fdisk segédprogramnak interaktív bemenetre van szüksége, ezért nem ideális az Automation-parancsfájlokban való használathoz. Az elválasztott [segédprogram azonban](https://www.gnu.org/software/parted/) parancsfájlokkal is rendelkezhet, így az automatizálási forgatókönyvek esetében jobban kihasználható. Az elválasztott segédprogram egy adatlemez particionálására és formázására használható. Az alábbi forgatókönyvhöz egy új adatlemez-/dev/SDC használunk, és formázza a [XFS](https://xfs.wiki.kernel.org/) fájlrendszer használatával.
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
partprobe /dev/sdc1
```
A fentiekben leírtak szerint a [partprobe](https://linux.die.net/man/8/partprobe) segédprogram használatával gondoskodhat arról, hogy a kernel azonnal tisztában legyen az új partícióval és a fájlrendszerrel. A partprobe használatának sikertelensége esetén a blkid vagy a lslbk parancsok azonnal nem adhatják vissza az új fájlrendszer UUID azonosítóját.

### <a name="mount-the-disk"></a>A lemez csatlakoztatása
Hozzon létre egy könyvtárat a fájlrendszer csatlakoztatásához a használatával `mkdir` . A következő példa egy könyvtárat hoz létre a */datadrive*:

```bash
sudo mkdir /datadrive
```

Ezzel a paranccsal `mount` csatlakoztathatja a fájlrendszert. Az alábbi példa a */dev/sdc1* partíciót csatlakoztatja a */datadrive* csatlakoztatási ponthoz:

```bash
sudo mount /dev/sdc1 /datadrive
```

Annak biztosítása érdekében, hogy a meghajtó újracsatlakoztatása újraindítás után automatikusan megtörténjen, hozzá kell adni az */etc/fstab* fájlhoz. Emellett erősen ajánlott az UUID (univerzálisan egyedi azonosító) használata az */etc/fstab* -ben a meghajtóra, nem csak az eszköz neve (például */dev/sdc1*). Ha indítás közben az operációs rendszer lemezhibát észlel, az UUID használatával elkerülhető egy nem megfelelő lemez a megadott helyre történő csatolása. A többi adatlemez ettől még ugyanazokat az eszközazonosítókat kapja. Az új meghajtó UUID-jének megkereséséhez használja a `blkid` segédprogramot:

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
> Az **/etc/fstab** fájl nem megfelelő szerkesztése nem indítható rendszert eredményezhet. Ha nem biztos a dolgában, a fájl megfelelő szerkesztésével kapcsolatos információkért olvassa el a disztribúció dokumentációját. Azt is javasoljuk, hogy a Szerkesztés előtt hozza létre az/etc/fstab fájl biztonsági másolatát.

Ezután nyissa meg az */etc/fstab* fájlt egy szövegszerkesztőben a következőképpen:

```bash
sudo vi /etc/fstab
```

Ebben a példában használja az előző lépésekben létrehozott */dev/sdc1* -eszköz UUID értékét, valamint a */datadrive*csatlakoztatási pont. Adja hozzá a következő sort az */etc/fstab* fájl végéhez:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```
Ha elkészült, mentse az */etc/fstab* fájlt, és indítsa újra a rendszerét.
> [!NOTE]
> Ha később eltávolít egy adatlemezt az fstab szerkesztése nélkül, a virtuális gép nem tud elindulni. A legtöbb disztribúció a *sikertelen* és/vagy *nobootwait* fstab-beállításokat tartalmazza. Ezek a beállítások lehetővé teszik a rendszer rendszerindítását akkor is, ha a lemez nem csatlakoztatható a rendszerindítási időpontban. A paraméterekkel kapcsolatos további információkért olvassa el a terjesztés dokumentációját.
> 
> A *nem sikertelen* beállítás biztosítja, hogy a virtuális gép akkor is elindul, ha a fájlrendszer sérült, vagy ha a lemez nem létezik a rendszerindítási időpontban. Ha ezt a lehetőséget választja, a következő témakörben leírtak szerint [nem lehet SSH-t Linux virtuális gépre az FSTAB-hibák miatt](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) .

### <a name="trimunmap-support-for-linux-in-azure"></a>A Linux az Azure-ban történő LEKÉPEZÉSÉNEK megszüntetése-támogatása
Egyes linuxos kernelek támogatják a TRIM/LEKÉPEZÉSÉNEK megszüntetése műveleteket a lemezen lévő nem használt blokkok elvetéséhez. Ez a funkció elsősorban a standard szintű tárolásban hasznos, hogy tájékoztassa az Azure-t arról, hogy a törölt lapok már nem érvényesek, és el lehet dobni, és pénzt takaríthat meg, ha nagyméretű fájlokat hoz létre, majd törli őket.

A Linux rendszerű virtuális gépen kétféleképpen engedélyezhető a TRIM-támogatás. A szokásos módon tekintse meg az ajánlott módszert az eloszlásban:

* Használja a `discard` csatlakoztatási lehetőséget az */etc/fstab*-ben, például:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Bizonyos esetekben a `discard` beállítás teljesítménybeli következményekkel járhat. Azt is megteheti, hogy manuálisan futtatja a `fstrim` parancsot a parancssorból, vagy hozzáadja azt a crontabhoz, hogy rendszeresen fusson:
  
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
[Adatlemezt](add-disk.md) az Azure CLI használatával is csatolhat.
