---
title: Tárolási lehetőségek az Azure Service Fabric-háló állapot |} A Microsoft Docs
description: Ismerje meg az állapot megbízhatóan tárolja az Azure Service Fabric-háló futó Service Fabric-háló alkalmazások.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: ef51040d1bad74ee74d5901d1f5acbe875c02a07
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337569"
---
# <a name="state-management-with-service-fabric"></a>A Service Fabric állapotkezelés

A Service Fabric számos különböző lehetőség támogatja a állapot tárolásához. Az állapot minták és a Service Fabric fogalmi áttekintése: [Service Fabric fogalmak: Állapot](/azure/service-fabric/service-fabric-concepts-state). Az összes ezek ugyanezek a fogalmak alkalmazhatók-e a szolgáltatások futnak, belül vagy kívül a Service Fabric-háló. 

Service Fabric tervezhetők is egyszerűen üzembe helyezni egy új alkalmazást és csatlakoztathatja azt egy Azure-ban üzemeltetett meglévő adattár. Amellett, hogy minden olyan távoli adatbázis használata esetén többféle módon, attól függően, hogy a szolgáltatás kívánja-e a helyi vagy távoli tároló adatainak tárolásához. 

## <a name="volumes"></a>Kötetek

Tárolók gyakran győződjön meg arról, használja az ideiglenes lemezek. Ideiglenes lemezek olyan rövid élettartamú, azonban, hogy egy új ideiglenes lemez lekérése, és elveszíti az adatokat, ha összeomlik, egy tárolót. Emellett akkor is nehéz ideiglenes lemezek az információk megosztása más tárolóktól. Kötetek, amely csatlakozik a tárolópéldányok állapotban maradnak használó belső könyvtárak. Kötetek általános célú a file storage biztosítanak, és lehetővé teszi olvasási/írási fájlt normál lemez i/o-fájl API-k használatával. A köteterőforrás ismerteti, hogyan csatlakoztathat egy könyvtárat és a storage használata biztonsági. Kiválaszthatja az Azure File storage vagy a Service Fabric kötetet tartalmazó lemezt adatok tárolására.

![Kötetek][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric Reliable Volume

Service Fabric Reliable kötet egy tárolóba helyi kötet csatlakoztatásához használt Docker kötet illesztőprogramot. Írások és olvasások: a helyi operations és gyors. Az adatok másodlagos csomópontot, magas rendelkezésre állásúvá tétele replikációja meg. Is a gyors feladatátvétel. Ha összeomlik, egy tároló, azt átadja a feladatokat egy csomópont, amely már rendelkezik egy másolatot az adatokról. Egy vonatkozó példáért lásd: [hogyan helyezhet üzembe egy alkalmazást a Service Fabric Reliable kötet](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Az Azure Files-kötet

Az Azure Files-kötet egy Azure-fájlmegosztási tárolóba való csatlakoztatásához használt Docker kötet illesztőprogramot. Az Azure Files storage hálózati tárolást használ, ezért olvassa be és ír a hálózaton keresztül történik. Service Fabric Reliable kötet képest, az Azure Files storage kevésbé hatékony, de olcsóbb és teljesen megbízható lehetőséget kínál. Egy vonatkozó példáért lásd: [hogyan helyezhet üzembe egy alkalmazást az Azure Files kötet](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>További lépések

Az alkalmazásmodell kapcsolatos tudnivalókat lásd: [Service Fabric-erőforrások](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
