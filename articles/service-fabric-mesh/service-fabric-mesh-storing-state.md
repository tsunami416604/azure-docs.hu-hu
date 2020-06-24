---
title: Az Azure Service Fabric Mesh állapotának tárolási lehetőségei
description: Ismerje meg az Azure Service Fabric Mesh szolgáltatásban futó Service Fabric Mesh-alkalmazásokban lévő állapotok megbízható tárolását.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84692682"
---
# <a name="state-management-with-service-fabric"></a>Állapot-kezelés Service Fabric

Service Fabric számos különböző lehetőséget támogat az állapot-tároláshoz. Az állapot-felügyeleti minták és Service Fabric fogalmi áttekintését lásd [: Service Fabric fogalmak: State](/azure/service-fabric/service-fabric-concepts-state). Mindezen fogalmak érvényesek, függetlenül attól, hogy a szolgáltatásai Service Fabric hálón belül vagy kívül futnak-e. 

A Service Fabric Mesh használatával egyszerűen üzembe helyezhető egy új alkalmazás, és az Azure-ban üzemeltetett meglévő adattárhoz csatlakoztatható. A távoli adatbázisok mellett számos lehetőség áll rendelkezésre az adattárolásra attól függően, hogy a szolgáltatás helyi vagy távoli tárterületet kíván-e használni. 

## <a name="volumes"></a>Kötetek

A tárolók gyakran használják az ideiglenes lemezeket. Az ideiglenes lemezek elmúló jellegűek, ezért új ideiglenes lemezt kap, és elveszíti az adatokat, amikor a tároló összeomlik. A többi tárolóval rendelkező ideiglenes lemezekkel kapcsolatos információkat is nehéz megosztani. A kötetek olyan könyvtárak, amelyek az állapot megőrzéséhez használható tároló-példányokon belül vannak csatlakoztatva. A kötetek általános célú fájlmegosztást biztosítanak, és lehetővé teszik a fájlok olvasását/írását a normál lemez I/O-fájl API-jai használatával. A mennyiségi erőforrás leírja, hogyan csatlakoztathat egy könyvtárat, és hogy mely biztonsági mentést kell használni a tárolóhoz. Az adattároláshoz az Azure file Storage vagy az Service Fabric kötet lemezét is választhatja.

![Kötetek][image3]

### <a name="service-fabric-reliable-volume"></a>Megbízható kötet Service Fabric

Service Fabric megbízható kötet a helyi kötetek tárolóba csatlakoztatására szolgáló Docker-kötet-illesztőprogram. Az olvasások és írások helyi műveletek és gyorsak. A rendszer az adatreplikációt a másodlagos csomópontokra replikálja, így azok elérhetővé válnak. A feladatátvétel is gyorsan elvégezhető. Amikor egy tároló összeomlik, a feladatátvétel olyan csomópontra történik, amely már rendelkezik az adatai másolatával. Példaként tekintse meg, [Hogyan helyezhet üzembe egy alkalmazást Service Fabric megbízható kötettel](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Azure Files kötet

Azure Files kötet egy Azure Files-megosztás tárolóba csatlakoztatására szolgáló Docker-kötet-illesztőprogram. Azure Files a tárterület hálózati tárterületet használ, ezért az olvasás és az írás a hálózaton keresztül történik. A Service Fabric megbízható kötethez képest Azure Files tárterület kevésbé nagy teljesítményű, de olcsóbb és teljesen megbízható adatbeállítást biztosít. Példaként tekintse meg, [Hogyan helyezhet üzembe egy alkalmazást Azure Files kötettel](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>További lépések

Az alkalmazás modelljével kapcsolatos információkért lásd: [Service Fabric erőforrások](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
