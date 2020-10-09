---
title: Felkészülés Azure Stack Edge Pro-eszköz meghibásodására
description: Ismerteti, hogyan kérhető le egy Azure Stack Edge Pro rendszerű eszköz cseréje.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 0d36a09b67e110551470f3908800e77c35b1e3be
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843881"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Felkészülés Azure Stack Edge Pro GPU-eszköz meghibásodására

Ebből a cikkből megtudhatja, hogyan készítheti elő az eszköz meghibásodását, ha részletesen leírja, hogyan menti és biztonsági másolatot készít az eszköz konfigurációjának és adatairól az Azure Stack Edge Pro GPU-eszközön. 

A cikk nem tartalmazza a Azure Stack Edge Pro GPU-eszközön üzembe helyezett Kubernetes és IoT-tárolók biztonsági mentésének lépéseit. 

## <a name="understand-device-failures"></a>Az eszköz meghibásodásának ismertetése

Az Azure Stack Edge Pro GPU-eszköz kétféle hardveres hibát képes tapasztalni.

- A hardver-összetevők lecserélését igénylő tolerálható hibák. Ezek a hibák lehetővé teszik az eszköz csökkentett teljesítményű állapotú üzemeltetését. Ilyen hibák például egyetlen sikertelen tápegység (PSU) vagy egyetlen hibás lemez az eszközön. Ezekben az esetekben az eszköz továbbra is működhet. A sikertelen összetevők lecseréléséhez ajánlott a legkorábban felvenni a kapcsolatot a Microsoft ügyfélszolgálata.

- Nem tolerálható hibák, amelyek a teljes eszköz cseréjét igénylik. Ilyen hiba lehet például, ha két lemez meghibásodik az eszközön. Ezekben a példányokban kapcsolatba lép Microsoft ügyfélszolgálata és azt követően, hogy az eszköz cseréjére van szükség, a Azure Stack Edge-eszköz cseréjének megkönnyítése érdekében.

A nem tolerálható hibák előkészítéséhez biztonsági mentést kell készítenie az eszközön a következőkről:

- Az eszköz konfigurációjával kapcsolatos információk.
- Az Edge helyi megosztások és az Edge Cloud shares szolgáltatásban található adat.
- Az eszközön futó virtuális gépekhez társított fájlok és mappák.


## <a name="back-up-device-configuration"></a>Eszköz konfigurációjának biztonsági mentése

Az eszköz kezdeti konfigurálásakor fontos, hogy az eszköz konfigurációs információinak másolatát az [üzembe helyezési ellenőrzőlistán](azure-stack-edge-gpu-deploy-checklist.md)vázolt módon őrizze meg. A helyreállítás során a rendszer ezt a konfigurációs információt fogja használni az új helyettesítő eszközre való alkalmazásához. 

## <a name="protect-device-data"></a>Eszközadatok védelme

Az eszköz adattípusa a következő típusok egyike lehet:

- Az Edge Felhőbeli megosztások
- Helyi megosztásokban lévő adatforgalom
- Virtuális gépek fájljai és mappái

A következő részekben ismertetjük azokat a lépéseket és ajánlásokat, amelyek az eszközön az ilyen típusú adattípusok védelme érdekében szükségesek.

## <a name="protect-data-in-edge-cloud-shares"></a>Az Edge Cloud shares adatainak védelme

Az eszközről az Azure-ba létrehozott Edge Cloud shares-megosztásokat hozhat létre. A rendelkezésre álló hálózati sávszélességtől függően állítsa be a sávszélesség-sablonokat az eszközön, hogy az adatvesztést csökkentse a nem tolerálható meghibásodás esetén.

> [!IMPORTANT] 
> Ha az eszköz nem tolerálható hibával rendelkezik, előfordulhat, hogy az eszközről az Azure-ra nem kivont helyi adatok elvesznek. 

## <a name="protect-data-in-edge-local-shares"></a>Az adatvédelmet a peremhálózat helyi megosztásai között

Ha a Kubernetes vagy a IoT Edge telepítését konfigurálja úgy, hogy helyileg mentse az alkalmazásadatok az eszközön, és ne szinkronizáljon az Azure Storage szolgáltatással. Az adattárolást az eszköz egyik megosztásán tárolja a rendszer. Előfordulhat, hogy fontos, hogy biztonsági másolatot készítsen az ezekben a megosztásokban található adatforgalomról.

A következő harmadik féltől származó adatvédelmi megoldások biztonsági mentési megoldást nyújthatnak a helyi SMB-vagy NFS-megosztások adatvédelmére. 

| Külső gyártótól származó szoftverek           | Hivatkozás a megoldásra                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | https://www.cohesity.com/solution/cloud/azure/ <br> Részletekért vegye fel a kapcsolatot az adatkapcsolattal.          |
| Veritas                        | Részletekért forduljon a Veritashez.   |


## <a name="protect-files-and-folders-on-vms"></a>A virtuális gépek fájljainak és mappáinak a védelemmel való ellátása

Az Azure Stack Edge együttműködik a Azure Backup és más, harmadik féltől származó adatvédelmi megoldásokkal, amelyek biztonsági mentési megoldást biztosítanak az eszközön üzembe helyezett virtuális gépeken tárolt adatvédelemhez. Az alábbi táblázat a választható megoldásokra mutató hivatkozásokat tartalmazza.


| Biztonsági mentési megoldások        | Támogatott operációs rendszer   | Referencia                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Microsoft Azure Recovery Services (MARS) ügynök a Azure Backup | Windows        | [A MARS-ügynök ismertetése](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows, Linux | [Rövid Microsoft Azure integrációs, biztonsági mentési és helyreállítási megoldás](https://www.cohesity.com/solution/cloud/azure) <br>Részletekért vegye fel a kapcsolatot az adatkapcsolattal.                          |
| Veritas                 | Windows, Linux | Részletekért forduljon a Veritashez.                    |



## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan lehet [helyreállítani egy sikertelen Azure stack Edge Pro GPU-eszközről](azure-stack-edge-gpu-recover-device-failure.md).
