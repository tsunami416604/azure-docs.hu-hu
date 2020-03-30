---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472016"
---
Az Azure megosztott lemezei (előzetes verzió) egy új funkció az Azure felügyelt lemezei, amely lehetővé teszi, hogy egy Azure felügyelt lemez több virtuális gépekhez (VM) egyszerre. Egy felügyelt lemez csatlakoztatása több virtuális géphez lehetővé teszi, hogy új üzembe helyezése vagy a meglévő fürtözött alkalmazások áttelepítése az Azure-ba.

## <a name="how-it-works"></a>Működés

A fürtben lévő virtuális gépek az [SCSI-állandó foglalások (SCSI](https://www.t10.org/members/w_spc3.htm) PR) használatával a fürtözött alkalmazás által kiválasztott foglalás alapján olvashatnak vagy írhatnak a csatlakoztatott lemezre. Az SCSI PR egy jól ismert iparági szabvány, amelyet a helyszíni tárolóhálózaton (SAN) futó alkalmazások használnak. Az SCSI PR engedélyezése felügyelt lemezen lehetővé teszi, hogy ezeket az alkalmazásokat az Azure-ba telepítse át.

A megosztott lemezekkel rendelkező felügyelt lemezek olyan megosztott blokktárolót kínálnak, amelyet több virtuális gép is elérhet, ez logikai egységszámként (LU- k) jelenik meg. A logikai és azonosítók ezután egy célból (lemezről) jelennek meg a kezdeményezőnek (VM). Ezek a helyi tárolók úgy néznek ki, mint a közvetlenül csatlakoztatott tároló (DAS) vagy a virtuális gép helyi meghajtója.

A megosztott lemezekkel rendelkező felügyelt lemezek natív módon nem kínálnak olyan teljesen felügyelt fájlrendszert, amely SMB/NFS használatával érhető el. Olyan fürtkezelőt kell használnia, például a Windows Server feladatátvevő fürtjét (WSFC) vagy a Pacemakert, amely kezeli a fürtcsomópont-kommunikációt és az írási zárolást.

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