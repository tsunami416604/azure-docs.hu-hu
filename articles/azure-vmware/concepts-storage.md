---
title: Fogalmak – tárolás
description: Ismerje meg a főbb tárolási képességeket az Azure VMware Solution előzetes privát felhőkben.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 8afd58c61f2f78eec2a92354be6d88178340912e
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90024315"
---
# <a name="azure-vmware-solution-preview-storage-concepts"></a>Azure VMware-megoldás előzetes verziójú tárolási fogalmak

Az Azure VMware-megoldás privát felhők natív, fürtre kiterjedő tárolót biztosítanak a VMware vSAN. A fürt minden gazdagépéről a vSAN-adattárban lévő összes helyi tárterületet használja a rendszer, és alapértelmezés szerint elérhető az inaktív adatok titkosítása. Az Azure Storage-erőforrások használatával kiterjesztheti a privát felhők tárolási képességeit.

## <a name="vsan-clusters"></a>vSAN-fürtök

Az egyes vSAN lévő helyi tárterületet a rendszer az adattárolók részeként használja. Az összes diskgroups 1,6 TB-os NVMe-gyorsítótári szintet használ, amely a nyers, a gazdagép, az SSD-alapú kapacitás 15,4 TB. A fürt nyers kapacitási szintjeinek mérete a gazdagépek kapacitása, ahányszor a gazdagépek száma. Egy négy gazda fürt például 61,6 TB-os nyers kapacitást biztosít a vSAN kapacitási szinten.

A fürtállomások helyi tárterülete a fürtre kiterjedő vSAN-adattárban használatos. A rendszer minden adattárolót egy privát Felhőbeli üzembe helyezés részeként hoz létre, és azonnal használható. A cloudadmin-felhasználó és a CloudAdmin csoport összes felhasználója felügyelheti az adattárolókat ezekkel a vSAN jogosultságokkal:
- Adattár. AllocateSpace
- Datastore.Browse
- Datastore.Config
- Adattár. DeleteFile
- Adattár. FileManagement
- Adattár. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Inaktív adatok titkosítása

a vSAN adattárolók alapértelmezés szerint a REST titkosítást használják. A titkosítási megoldás KMS-alapú, és támogatja a vCenter műveleteket. A kulcs titkosítási kulcsainak tárolása egy Azure Key Vault történik, és ha egy gazdagépet bármilyen okból eltávolítanak a fürtből, a rendszer azonnal érvényteleníti az SSD-k adatait.

## <a name="scaling"></a>Méretezés

A natív fürt tárolókapacitása méretezhető úgy, hogy gazdagépeket ad hozzá egy fürthöz. A gazdagépeket használó fürtök esetén a teljes fürtre kiterjedő kapacitást 15,4 TB-kal növelik minden további állomással. A GP-gazdagépekkel létrehozott fürtökön az egyes további gazdagépek esetében 7,7 TB-kal nőtt a nyers kapacitása. Mindkét típusú fürtben a gazdagépek körülbelül 10 percet vesznek fel a fürtbe. A fürtök méretezésével kapcsolatos utasításokért tekintse meg a [privát felhő méretezése című oktatóanyagot][tutorial-scale-private-cloud] .

## <a name="azure-storage-integration"></a>Azure Storage-integráció

Használhatja az Azure Storage-szolgáltatásokat a saját felhőben futó munkaterheléseken. Az Azure Storage szolgáltatásai közé tartoznak a Storage-fiókok, a Table Storage és a Blob Storage. A számítási feladatok Azure Storage-szolgáltatásokhoz való kapcsolódása nem haladja meg az internetet. Ez a kapcsolat nagyobb biztonságot nyújt, és lehetővé teszi SLA-alapú Azure Storage-szolgáltatások használatát a saját Felhőbeli számítási feladatokban.

## <a name="next-steps"></a>Következő lépések

A következő lépés a [saját Felhőbeli identitással kapcsolatos fogalmak][concepts-identity]megismerése.

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
