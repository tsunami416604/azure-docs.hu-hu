---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6e7294f10ba094a1adaae399187fb9973397a561
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83868023"
---
Az Azure Shared Disks (előzetes verzió) az Azure Managed Disks új funkciója, amely lehetővé teszi a felügyelt lemezek egyidejű csatolását több virtuális géphez (VM). A felügyelt lemezek több virtuális géphez való csatolásával új vagy meglévő fürtözött alkalmazásokat telepíthet át az Azure-ba.

## <a name="how-it-works"></a>Működés

A fürtben lévő virtuális gépek a fürtözött alkalmazás által az [állandó SCSI-foglalások](https://www.t10.org/members/w_spc3.htm) (SCSI PR) használatával kiválasztott foglalás alapján olvashatják vagy írhatják a csatlakoztatott lemezeket. Az SCSI PR egy iparági szabvány, amely a helyi hálózaton (SAN) futó alkalmazások által használható. A felügyelt lemezeken az SCSI PR engedélyezése lehetővé teszi, hogy áttelepítse ezeket az alkalmazásokat az Azure-ba.

A felügyelt lemezek megosztása olyan megosztott blokkos tárhelyet kínál, amely több virtuális gépről is elérhető, ezek logikai egységként (LUN) vannak kitéve. Ezután a logikai egységeket a rendszer egy kezdeményező (virtuális gép) számára mutatja be egy cél (lemez) alapján. Ezek a logikai egységek úgy néznek ki, mint a Direct-Attached-Storage (DAS) vagy egy helyi meghajtó a virtuális géphez.

A megosztott felügyelt lemezek nem natív módon biztosítanak egy teljes körűen felügyelt fájlrendszert, amely SMB/NFS használatával érhető el. A fürt csomópontjainak kommunikációját, valamint az írási zárolást kezelő fürtszolgáltatást kell használnia, például a Windows Server feladatátvevő fürtöt (WSFC) vagy a pacemakert.

## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Lemezek mérete

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Minta számítási feladatok

### <a name="windows"></a>Windows

A legtöbb Windows-alapú fürtözés a WSFC-re épít, amely a fürtcsomópontok kommunikációjának alapvető infrastruktúráját kezeli, lehetővé téve az alkalmazások számára a párhuzamos hozzáférési minták kihasználását. A WSFC a Windows Server verziójától függően a CSV- és nem CSV-alapú beállításokat is elérhetővé teszi. A részleteket lásd: [Feladatátvevő fürt létrehozásához](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Néhány a WSFC-n futó népszerű alkalmazások közül:

- Az SQL Server fürtözött feladatátvételi példánya (FCI)
- Kibővíthető fájlkiszolgáló (SoFS)
- Általános célú fájlkiszolgáló (IW számítási feladat)
- Távoli asztali kiszolgáló felhasználói profillemez (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

A Linux-fürtök képesek kihasználni a fürtszolgáltatásokat, például a [pacemakert](https://wiki.clusterlabs.org/wiki/Pacemaker). A pacemaker a [Corosync](http://corosync.github.io/corosync/)-ra épül, és lehetővé teszi a fürtök kommunikációját a magasan elérhető környezetekben üzembe helyezett alkalmazásokhoz. Egyes gyakori fürtözött fájlrendszerek közé tartozik a [OCFS2](https://oss.oracle.com/projects/ocfs2/) és a [GFS2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). A foglalásokat és a regisztrációkat a [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) és [sg_persist](https://linux.die.net/man/8/sg_persist)segédprogramokkal is kezelheti.

#### <a name="ubuntu"></a>Ubuntu

További információ az Ubuntu magas rendelkezésre állásának beállításáról a Corosync és a pacemaker használatával az Azure Shared Disks szolgáltatásban: [Ubuntu Community diskurzus](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874).

## <a name="persistent-reservation-flow"></a>Állandó foglalási folyamat

A következő ábra egy minta 2 csomópontos fürtözött adatbázis-alkalmazást mutat be, amely az SCSI PR-t használja az egyik csomópontról a másikra történő feladatátvétel engedélyezéséhez.

![Két csomópontos fürt. A fürtön futó alkalmazások kezelik a lemezhez való hozzáférést.](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

A folyamat a következő:

1. Az Azure VM1 és VM2-on futó fürtözött alkalmazás a lemezre való írásra vagy írásra is regisztrálja a szándékát.
1. A VM1 lévő alkalmazás példánya kizárólagos foglalást tart a lemezre való íráshoz.
1. Ezt a foglalást az Azure-lemezen kell kikényszeríteni, és az adatbázis már kizárólag a lemezre tud írni. A VM2 alkalmazás példányában lévő összes írás sikertelen lesz.
1. Ha a VM1 futó alkalmazás példánya leáll, a VM2-példány mostantól kezdeményezheti az adatbázis feladatátvételét és a lemez átvételét.
1. Ez a foglalás már érvényben van az Azure-lemezen, és a lemez már nem fogadja el az írásokat a VM1. A művelet csak a VM2 származó írásokat fogadja el.
1. A fürtözött alkalmazás elvégezheti az adatbázis feladatátvételét, és kézbesítheti a VM2 érkező kéréseket.

Az alábbi ábrán egy olyan gyakori fürtözött munkaterhelés látható, amely több csomópontból származó adatok olvasását mutatja be a lemezről a párhuzamos folyamatok futtatásához, például a gépi tanulási modellek betanításához.

![Négy csomópontos virtuálisgép-fürt, az egyes csomópontok az írási szándékot regisztrálják, az alkalmazás kizárólagos foglalást tart az írási eredmények megfelelő kezelésére.](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

A folyamat a következő:

1. Az összes virtuális gépen futó fürtözött alkalmazás a lemezre való íráshoz vagy íráshoz regisztrálja a szándékot.
1. A VM1 alkalmazás-példánya kizárólagos foglalást használ a lemezre való íráshoz, miközben a más virtuális gépekről érkező olvasási adatokat nyit meg a lemezre.
1. Ezt a foglalást az Azure-lemezen kell kikényszeríteni.
1. A fürt összes csomópontja most már képes olvasni a lemezről. A fürt összes csomópontja nevében csak egy csomópont írja vissza az eredményeket a lemezre.

### <a name="ultra-disks-reservation-flow"></a>Ultra lemezek foglalási folyamata

Az ultra-lemezek további szabályozást kínálnak, összesen két szabályozásra. Ennek köszönhetően az ultra-lemezek foglalási folyamata a korábbi szakaszban leírtak szerint működhet, vagy részletesebben szabályozhatja és terjesztheti a teljesítményt.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Egy tábla képe, amely az írásvédett vagy olvasási/írási hozzáférést a foglalás tulajdonosa, a regisztrált és mások számára ábrázolja.":::

## <a name="ultra-disk-performance-throttles"></a>Ultravékony lemezek teljesítményének szabályozása

Az ultra-lemezek egyedi képességgel rendelkeznek, amely lehetővé teszi a teljesítmény beállítását a módosítható attribútumok kiszűrésével, és lehetővé teszi a módosítását. Alapértelmezés szerint csak két módosítható attribútum létezik, de a megosztott Ultra-lemezek két további attribútummal rendelkeznek.


|Attribútum  |Leírás  |
|---------|---------|
|DiskIOPSReadWrite     |Az összes olyan virtuális gépen engedélyezett IOPS száma, amely a megosztási lemezt írási hozzáféréssel csatlakoztatja.         |
|DiskMBpsReadWrite     |A teljes átviteli sebesség (MB/s) engedélyezett az összes virtuális gépen, amely a megosztott lemezt írási hozzáféréssel csatlakoztatja.         |
|DiskIOPSReadOnly*     |Az összes olyan virtuális gépen engedélyezett IOPS teljes száma, amely ReadOnly módon csatlakoztatja a megosztott lemezt.         |
|DiskMBpsReadOnly*     |A teljes átviteli sebesség (MB/s) engedélyezett az összes olyan virtuális gépen, amely a megosztott lemezt ReadOnly módon csatlakoztatja.         |

\*Csak a megosztott Ultra-lemezekre vonatkozik

A következő képletek azt mutatják be, hogyan lehet beállítani a teljesítmény attribútumokat, mivel azok felhasználó által módosítható:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - 300 IOPS/GiB IOPS korlátja, legfeljebb 160K IOPS
    - Legalább 100 IOPS
    - A DiskIOPSReadWrite + DiskIOPSReadOnly legalább 2 IOPS/GiB
- DiskMBpsRead írási/DiskMBpsReadOnly:
    - Egy lemez átviteli korlátja minden kiépített IOPS esetében 256 KiB/s, lemezenként legfeljebb 2000 MB/s-onként
    - Minden egyes kiépített IOPS esetében a minimális garantált átviteli sebesség 4KiB/s, a teljes alapértéknek legalább 1 MBps-nek kell lennie

### <a name="examples"></a>Példák

Az alábbi példák néhány forgatókönyvet mutatnak be, amelyek bemutatják, hogyan működhet a szabályozás a megosztott Ultra-lemezekkel, pontosabban.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Két csomópontos fürt fürt megosztott kötetei használatával

A következő példa egy, a fürtözött megosztott köteteket használó 2 csomópontos WSFC. Ezzel a konfigurációval mindkét virtuális gépnek egyidejű írási hozzáférése van a lemezhez, ami azt eredményezi, hogy a ReadWrite-szabályozás a két virtuális gép között oszlik el, és a nem használt ReadOnly szabályozás.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Két csomópontos CSV-példa":::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Két csomópontos fürt fürt megosztási kötetei nélkül

A következő példa egy olyan 2 csomópontos WSFC mutat be, amely nem használ fürtözött megosztott köteteket. Ezzel a konfigurációval csak egy virtuális gép rendelkezik írási hozzáféréssel a lemezhez. Ez azt eredményezi, hogy a ReadWrite-szabályozás kizárólag az elsődleges virtuális gép esetében használatos, és az írásvédett szabályozás csak a másodlagos használatban van.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV-fájl két csomópontja nem rendelkezik a CSV-vel – példa":::

#### <a name="four-node-linux-cluster"></a>Négy csomópontos Linux-fürt

A következő példa egy 4 csomópontos linuxos fürtöt mutat be egyetlen író és három kibővíthető olvasóval. Ezzel a konfigurációval csak egy virtuális gép rendelkezik írási hozzáféréssel a lemezhez. Ez azt eredményezi, hogy a ReadWrite-szabályozás kizárólag az elsődleges virtuális gép esetében használatos, és a másodlagos virtuális gépek által feldarabolt ReadOnly szabályozás.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Négy csomópontos Ultra-szabályozási példa":::
