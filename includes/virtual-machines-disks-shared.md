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
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008536"
---
Az Azure megosztott lemezei (előzetes verzió) egy új funkció az Azure felügyelt lemezei számára, amelyek lehetővé teszik a felügyelt lemez egyidejű csatlakoztatását több virtuális géphez (VM). Egy felügyelt lemez csatlakoztatása több virtuális géphez lehetővé teszi, hogy új üzembe helyezése vagy a meglévő fürtözött alkalmazások áttelepítése az Azure-ba.

## <a name="how-it-works"></a>Működés

A fürtben lévő virtuális gépek az [SCSI-állandó foglalások (SCSI](https://www.t10.org/members/w_spc3.htm) PR) használatával a fürtözött alkalmazás által kiválasztott foglalás alapján olvashatnak vagy írhatnak a csatlakoztatott lemezre. Az SCSI PR egy iparági szabvány, amelyet a helyszíni tárolóhálózaton (SAN) futó alkalmazások használnak. Az SCSI PR engedélyezése felügyelt lemezen lehetővé teszi, hogy ezeket az alkalmazásokat az Azure-ba telepítse át.

A megosztott lemezek megosztott blokktárolót kínálnak, amely több virtuális gépről érhető el, ezek logikai egységszámként (LUN) érhetők el. A logikai és azonosítók ezután egy célból (lemezről) jelennek meg a kezdeményezőnek (VM). Ezek a helyi tárolók úgy néznek ki, mint a közvetlenül csatlakoztatott tároló (DAS) vagy a virtuális gép helyi meghajtója.

A megosztott felügyelt lemezek natív módon nem kínálnak teljes körűen felügyelt fájlrendszert, amely SMB/NFS-sel érhető el. Olyan fürtkezelőt kell használnia, például a Windows Server feladatátvevő fürtjét (WSFC) vagy a Pacemakert, amely kezeli a fürtcsomópont-kommunikációt és az írási zárolást.

## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Lemezméretek

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Számítási feladatok minta

### <a name="windows"></a>Windows

A legtöbb Windows-alapú fürtözés a WSFC-re épül, amely a fürtcsomópontok kommunikációjának összes alapvető infrastruktúráját kezeli, lehetővé téve az alkalmazások számára, hogy kihasználják a párhuzamos hozzáférési minták előnyeit. A WSFC a Windows Server verziójától függően csv- és nem CSV-alapú beállításokat is lehetővé tesz. További információt a [Feladatátvevő fürt létrehozása című dokumentumban talál.](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)

Néhány wsfc-en futó népszerű alkalmazás a következő:

- SQL Server feladatátvevő fürtpéldányai (FCI)
- Kibővített fájlkiszolgáló (SoFS)
- Általános használatra szánt fájlkiszolgáló (IW-munkaterhelés)
- Távoli asztali kiszolgáló felhasználói profillemeze (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

A Linux-fürtök kihasználhatják a fürtkezelőket, például [a Pacemakert.](https://wiki.clusterlabs.org/wiki/Pacemaker) A pacemaker a [Corosync-ra](http://corosync.github.io/corosync/)épül, amely lehetővé teszi a fürtkommunikációt a magas rendelkezésre állású környezetekben telepített alkalmazások számára. Néhány gyakori fürtözött fájlrendszer közé tartozik [az ocfs2](https://oss.oracle.com/projects/ocfs2/) és [a gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). A foglalásokat és a regisztrációkat olyan segédprogramokkal kezelheti, mint [a fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) és [a sg_persist.](https://linux.die.net/man/8/sg_persist)

## <a name="persistent-reservation-flow"></a>Állandó foglalási folyamat

Az alábbi ábrán egy minta 2 csomós fürtözött adatbázis-alkalmazás, amely az SCSI PR segítségével lehetővé teszi a feladatátvételt az egyik csomópontról a másikra.

![Két csomópont-fürt. A fürtön futó alkalmazás kezeli a lemez elérését](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Az áramlás a következő:

1. Az Azure VM1 és VM2 futó fürtözött alkalmazás regisztrálja a lemezolvasási vagy -írási szándékát.
1. Az alkalmazáspéldány a VM1 majd kizárólagos foglalást igényel a lemezre íráshoz.
1. Ez a foglalás az Azure-lemezen van kényszerítve, és az adatbázis most már kizárólag írni a lemezre. A VM2-en az alkalmazáspéldányból származó írások nem lesznek sikeresek.
1. Ha az alkalmazáspéldány a VM1-en leáll, a VM2-n lévő példány most már kezdeményezheti az adatbázis feladatátvételt és a lemez átvétele.
1. Ez a foglalás most már az Azure-lemezen, és a lemez már nem fogadja el a virtuális gépről írt írásokat. Csak a VM2-ből fogad el írásokat.
1. A fürtözött alkalmazás befejezheti az adatbázis feladatátvételt, és a VM2-től érkező kérelmeket szolgálhat ki.

Az alábbi ábra egy másik gyakori fürtözött számítási feladatokat mutat be, amelyek több csomópontból állnak, amelyek adatokat olvasnak a lemezről a párhuzamos folyamatok futtatásához, például a gépi tanulási modellek betanításához.

![Négy csomópont virtuálisgép-fürt, minden csomópont regisztrálja az írási szándékot, az alkalmazás kizárólagos foglalást vesz igénybe az írási eredmények megfelelő kezeléséhez](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Az áramlás a következő:

1. Az összes virtuális gépen futó fürtözött alkalmazás regisztrálja a lemezre való olvasásvagy írás szándékát.
1. A VM1 alkalmazáspéldány a lemezre íráshoz kizárólagos foglalást készít, miközben más virtuális gépekről nyitja meg az olvasásokat a lemezre.
1. Ez a foglalás az Azure-lemezen van kényszerítve.
1. A fürt összes csomópontja most már olvashat a lemezről. Csak egy csomópont írja vissza az eredményeket a lemezre a fürt összes csomópontja nevében.

### <a name="ultra-disks-reservation-flow"></a>Ultra lemezek foglalási folyamata

Az ultralemezek további fojtószelepet kínálnak, összesen két fojtószelepet. Ennek köszönhetően az ultralemezek foglalási folyamat a korábbi szakaszban leírtak szerint működhet, vagy szabályozhatja és terjesztheti a teljesítményt részletesebben.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Ultra lemez teljesítményszabályozása

Az ultralemezek rendelkeznek azzal az egyedülálló képességgel, hogy lehetővé tegyék a teljesítmény beállítását a módosítható attribútumok kiteszik, és lehetővé teszik azok módosítását. Alapértelmezés szerint csak két módosítható attribútum létezik, de a megosztott ultralemezek két további attribútummal rendelkeznek.


|Attribútum  |Leírás  |
|---------|---------|
|DiskIOPS readwrite     |A megosztási lemezt írási hozzáféréssel rendelkező összes virtuális gépen engedélyezett IOPS-ok teljes száma.         |
|DiskMBpsReadWrite írása     |A teljes átviteli sebesség (MB/s) engedélyezett az összes virtuális gép csatlakoztatása a megosztott lemez írási hozzáféréssel.         |
|DiskIOPSReadOnly*     |A megosztott lemezt readonly-ként rögzítő összes virtuális gépen engedélyezett IOPS-ok teljes száma.         |
|DiskMBpsReadOnly*     |A megosztott lemezt readonly-ként rögzítő összes virtuális gép számára engedélyezett teljes átviteli sebesség (MB/s).         |

\*Csak megosztott ultralemezekre vonatkozik

A következő képletek bemutatják, hogyan állíthatók be a teljesítményjellemzők, mivel a felhasználók módosíthatók:

- DiskiOPSReadWrite/DiskIOPSReadOnly: 
    - 300 IOPS/GiB IOPS-korlát, lemezenként legfeljebb 160 K IOPS-érték
    - Legalább 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly legalább 2 IOPS/GiB
- DiskMBpsRead/ DiskMBpsReadOnly:
    - Egyetlen lemez átviteli korlátja 256 KiB/s minden egyes kiosztott IOPS esetén, lemezenként legfeljebb 2000 Mb/s-ig
    - A minimális garantált lemezátviteli sebesség 4KiB/s minden egyes kiosztott IOPS esetén, a teljes alapterv minimum 1 M/s

### <a name="examples"></a>Példák

Az alábbi példák néhány forgatókönyvet mutatnak be, amelyek bemutatják, hogy a szabályozás hogyan működhet a megosztott ultralemezekkel, különösen.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Fürt megosztott köteteit használó két csomópont

Az alábbi példa egy 2 csomópontos WSFC fürtözött megosztott köteteket használó. Ezzel a konfigurációval mindkét virtuális gép egyidejű írási hozzáféréssel rendelkezik a lemezhez, ami azt eredményezi, hogy a ReadWrite szabályozás a két virtuális gép között oszlik meg, és a csak a csak olvasható szabályozás nincs használatban.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV két csomópont ultra példa":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Két csomópontfürt fürtmegosztási kötetek nélkül

Az alábbi példa egy kétcsomópontos WSFC-t mutat be, amely nem fürtözött megosztott köteteket használ. Ezzel a konfigurációval csak egy virtuális gép rendelkezik írási hozzáféréssel a lemezhez. Ennek eredményeként a ReadWrite szabályozás kizárólag az elsődleges virtuális gép és a ReadOnly szabályozás csak a másodlagos által használt.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV két csomópont nincs csv ultra lemez példa":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>Négy csomópont Linux-fürt

Az alábbiakban egy 4 csomópontos Linux-fürt egyetlen íróval és három kibővített olvasókkal. Ezzel a konfigurációval csak egy virtuális gép rendelkezik írási hozzáféréssel a lemezhez. Ennek eredményeként a ReadWrite szabályozás kizárólag az elsődleges virtuális gép és a ReadOnly szabályozás a másodlagos virtuális gépek által felosztott.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Négy csomópont ultra-szabályozás példa":::

:::image-end:::