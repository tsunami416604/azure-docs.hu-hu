---
title: Állapottárolási lehetőségek az Azure Service Fabric Mesh szolgáltatásban
description: Ismerje meg az Azure Service Fabric Mesh szolgáltatáson futó Service Fabric Mesh-alkalmazások megbízható tárolását.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259096"
---
# <a name="state-management-with-service-fabric"></a>Állapotkezelés a Service Fabric segítségével

A Service Fabric számos különböző lehetőséget támogat az állapottároláshoz. Az állapotkezelési minták és a Service Fabric fogalmi áttekintését a [Service Fabric fogalmai: Állapot](/azure/service-fabric/service-fabric-concepts-state)című témakörben találja. Ugyanezek a fogalmak érvényesek, hogy a szolgáltatások a Service Fabric Mesh-en belül vagy kívül futnak. 

A Service Fabric Mesh segítségével egyszerűen üzembe helyezhet egy új alkalmazást, és csatlakoztathatja egy azure-ban üzemeltetett meglévő adattárhoz. A távoli adatbázisok használata mellett számos lehetőség van az adatok tárolására, attól függően, hogy a szolgáltatás helyi vagy távtárolóra vágyik-e. 

## <a name="volumes"></a>Kötetek

A tárolók gyakran használnak ideiglenes lemezeket. Az ideiglenes lemezek azonban rövid élettartamúak, így új ideiglenes lemezt kap, és elveszíti az adatokat, amikor egy tároló összeomlik. Az ideiglenes lemezeken lévő információkat is nehéz megosztani más tárolókkal. Kötetek olyan könyvtárak, amelyek a tárolópéldányokon belül vannak csatlakoztatva, amelyek segítségével megőrizheti az állapotot. A kötetek általános célú fájltárolást biztosítanak, és lehetővé teszik a fájlok normál lemezI/O-fájl API-k használatával történő olvasását/írását. A Kötet erőforrás ismerteti, hogyan kell csatlakoztatni egy könyvtárat, és melyik háttértárolót kell használni. Az adatok tárolásához választhat az Azure File storage vagy a Service Fabric Volume lemez között.

![Kötetek][image3]

### <a name="service-fabric-reliable-volume"></a>Szolgáltatásháló megbízható kötete

A Service Fabric megbízható kötet egy Docker-kötet-illesztőprogram, amely helyi kötetet csatlakoztat egy tárolóhoz. Az olvasások és írások helyi műveletek és gyors. Az adatok replikálása másodlagos csomópontokra történik, így magas rendelkezésre állású. Feladatátvétel is gyors. Amikor egy tároló összeomlik, átadja a rendszer a következő nek egy csomópontot, amely már rendelkezik az adatok másolatával. Például: [Hogyan telepíthet egy alkalmazást a Service Fabric megbízható kötet.](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md)

### <a name="azure-files-volume"></a>Azure-fájlok kötete

Az Azure Files Volume egy Docker-kötet-illesztőprogram, amely az Azure Files-megosztást egy tárolóhoz csatlakoztatja. Az Azure Files storage hálózati tárolót használ, így az olvasások és írások a hálózaton keresztül történnek. A Service Fabric megbízható kötetéhez képest az Azure Files storage kevésbé hatékony, de olcsóbb és teljesen megbízható adatlehetőséget biztosít. Például: Alkalmazás üzembe helyezése az Azure Files volume használatával című [témakörben.](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

## <a name="next-steps"></a>További lépések

Az alkalmazásmodellről a [Service Fabric-erőforrások című](service-fabric-mesh-service-fabric-resources.md) témakörben talál további információt.

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
