---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09e5a6c5eee21e5432c4cf96a63fd2337307954a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211395"
---
Az Azure Shared Disks (előzetes verzió) az Azure Managed Disks új funkciója, amely lehetővé teszi egy Azure felügyelt lemez egyidejű csatlakoztatását több virtuális géphez (VM). A felügyelt lemezek több virtuális géphez való csatolásával új vagy meglévő fürtözött alkalmazásokat telepíthet át az Azure-ba.

## <a name="how-it-works"></a>Működés

A fürtben lévő virtuális gépek a fürtözött alkalmazás által az [állandó SCSI-foglalások](https://www.t10.org/members/w_spc3.htm) (SCSI PR) használatával kiválasztott foglalás alapján olvashatják vagy írhatják a csatlakoztatott lemezeket. Az SCSI PR egy jól ismert iparági szabvány, amelyet a helyi hálózaton (SAN) futó alkalmazások is használhatnak. A felügyelt lemezeken az SCSI PR engedélyezése lehetővé teszi, hogy áttelepítse ezeket az alkalmazásokat az Azure-ba.

A megosztott lemezekkel rendelkező felügyelt lemezek olyan megosztott blokkolási tárolót kínálnak, amelyet több virtuális gép is elérhet, ez a logikai egység (LUN) számként van kitéve. Ezután a logikai egységeket a rendszer egy kezdeményező (virtuális gép) számára mutatja be egy cél (lemez) alapján. Ezek a logikai egységek úgy néznek ki, mint a Direct-Attached-Storage (DAS) vagy egy helyi meghajtó a virtuális géphez.

Az engedélyezett megosztott lemezekkel rendelkező felügyelt lemezek nem rendelkeznek natív módon olyan teljes körűen felügyelt fájlrendszerrel, amely SMB/NFS használatával érhető el. A fürt csomópontjainak kommunikációját, valamint az írási zárolást kezelő fürtszolgáltatást kell használnia, például a Windows Server feladatátvevő fürtöt (WSFC) vagy a pacemakert.

## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Lemezek mérete

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Minta számítási feladatok

### <a name="windows"></a>Windows

A legtöbb Windows-alapú fürtözés a WSFC-ra épül, amely az összes alapvető infrastruktúrát kezeli a fürt csomópontjainak kommunikációjában, így az alkalmazások kihasználhatják a párhuzamos hozzáférési minták előnyeit. A WSFC a Windows Server verziójától függően CSV-és nem CSV-alapú beállításokat is lehetővé tesz. További részletekért tekintse meg a [feladatátvevő fürt létrehozása](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)című témakört.

A WSFC-on futó népszerű alkalmazások többek között a következők:

- SQL Server feladatátvevő fürt példányai (%-ban)
- Kibővíthető fájlkiszolgáló (SoFS)
- Általános célú fájlkiszolgáló (IW munkaterhelés)
- Távoli asztal Server felhasználói profil lemeze (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

A Linux-fürtök képesek kihasználni a fürtszolgáltatásokat, például a [pacemakert](https://wiki.clusterlabs.org/wiki/Pacemaker). A pacemaker a [Corosync](http://corosync.github.io/corosync/)-ra épül, és lehetővé teszi a fürtök kommunikációját a magasan elérhető környezetekben üzembe helyezett alkalmazásokhoz. Egyes gyakori fürtözött fájlrendszerek közé tartozik a [OCFS2](https://oss.oracle.com/projects/ocfs2/) és a [GFS2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). A foglalásokat és a regisztrációkat a [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) és [sg_persist](https://linux.die.net/man/8/sg_persist)segédprogramokkal is kezelheti.

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