---
title: Bevezetés az Azure Service Fabric erőforrásmodelljébe
description: Ismerje meg a Service Fabric erőforrásmodell, egyszerűsített megközelítés a Service Fabric Mesh-alkalmazások meghatározásához.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 0ae2ed163560aee4c0c3525ab31910e37afaa5b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259135"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Bevezetés a Service Fabric erőforrás-modell

A Service Fabric erőforrásmodell egy egyszerű megközelítést ír le a Service Fabric Mesh alkalmazást alkotó erőforrások meghatározásához. Az egyes erőforrások bármely Service Fabric-környezetben telepíthetők.  A Service Fabric erőforrásmodell is kompatibilis az Azure Resource Manager modell. Ez a modell jelenleg a következő típusú erőforrásokat támogatja:

- Alkalmazások és szolgáltatások
- Hálózatok
- Átjárók
- Titkok és titkok/értékek
- Kötetek

Minden erőforrás deklaratív módon van leírva egy erőforrásfájlban, amely egy egyszerű YAML- vagy JSON-dokumentum, amely leírja a hálóalkalmazást, és amelyet a Service Fabric platform létesít.

## <a name="applications-and-services"></a>Alkalmazások és szolgáltatások

Az alkalmazás-erőforrás a hálóalkalmazások központi telepítésének, verziószámozásának és élettartamának egysége. Egy vagy több szolgáltatás-erőforrásokból áll, amelyek egy mikroszolgáltatást képviselnek. Minden egyes Szolgáltatás-erőforrás viszont egy vagy több kódcsomagból áll, amelyek leírják a kódcsomaghoz társított tárolórendszerkép futtatásához szükséges mindent.

![Alkalmazások és szolgáltatások][Image1]

A Service erőforrás deklarálja a következőket:

- Tároló neve, verziója és rendszerleíró adatbázisa
- Az egyes tárolókhoz szükséges PROCESSZOR- és memóriaerőforrások
- Hálózati végpontok
- Más erőforrásokra, például hálózatokra, kötetekre és titkokra mutató hivatkozások 

A Szolgáltatás-erőforrás részeként definiált összes kódcsomag telepítve van, és csoportosan együtt van aktiválva. A Szolgáltatás erőforrás azt is leírja, hogy a szolgáltatás futtatásához szükséges példányok száma, és más erőforrásokra (például hálózati erőforrásra) is hivatkozik, amelytől függ.

Ha egy hálóalkalmazás egynél több szolgáltatásból áll, nem garantált, hogy ugyanazon a csomóponton futnak együtt. Emellett az alkalmazás frissítése során egyetlen szolgáltatás frissítésének sikertelensítése azt eredményezi, hogy az összes szolgáltatás visszaáll a korábbi verzióra.

Ahogy korábban utalt, az egyes alkalmazáspéldányok életciklusa egymástól függetlenül kezelhető. Például egy alkalmazáspéldány a többi alkalmazáspéldánytól függetlenül frissíthető. Általában megkell tartani a szolgáltatások száma egy alkalmazásban meglehetősen kicsi, mivel minél több szolgáltatást helyez el egy alkalmazás, annál nehezebb lesz az egyes szolgáltatások egymástól függetlenül kezelése.

## <a name="networks"></a>Hálózatok

A hálózati erőforrás egyénileg telepíthető erőforrás, függetlenül attól, hogy egy alkalmazás vagy szolgáltatás erőforrás, amely hivatkozhat rá, mint a függőség. Arra használják, hogy hozzon létre egy hálózatot az alkalmazások. A különböző alkalmazásokból származó több szolgáltatás ugyanannak a hálózatnak a része lehet.  További információt a [Service Fabric Mesh alkalmazások hálózatba való csatlakoztatásáról](service-fabric-mesh-networks-and-gateways.md)talál.

> [!NOTE]
> Az aktuális előnézet csak egy az egyhez hozzárendelést támogat az alkalmazások és a hálózatok között

![Hálózat és átjáró][Image2]

## <a name="gateways"></a>Átjárók
Az átjáró erőforrás két hálózatot köt össze, és útvonalak forgalmat.  Az átjáró lehetővé teszi a szolgáltatások számára, hogy kommunikáljanak a külső ügyfelekkel, és be- és beléptetést biztosít a szolgáltatás(oka)ba.  Az átjáró is használható a Mesh alkalmazás csatlakoztatásához a saját, meglévő virtuális hálózathoz. További információt a [Service Fabric Mesh alkalmazások hálózatba való csatlakoztatásáról](service-fabric-mesh-networks-and-gateways.md)talál.

![Hálózat és átjáró][Image2]

## <a name="secrets"></a>Titkos kulcsok

A titkos kulcsok erőforrások egy alkalmazástól vagy szolgáltatás-erőforrástól függetlenül telepíthetők, amelyek függőségként hivatkozhatnak rá. Arra használják, hogy biztonságosan szállít titkokat az alkalmazások. Különböző alkalmazásokból származó több szolgáltatás is hivatkozhat ugyanannak a titkos titoknak az értékeire.

## <a name="volumes"></a>Kötetek

A tárolók gyakran ideiglenes lemezeket tesznek elérhetővé. Az ideiglenes lemezek azonban rövid élettartamúak, így új ideiglenes lemezt kap, és elveszíti az adatokat, amikor egy tároló összeomlik. Az ideiglenes lemezeken lévő információkat is nehéz megosztani más tárolókkal. Kötetek olyan könyvtárak, amelyek a tárolópéldányokon belül vannak csatlakoztatva, amelyek segítségével megőrizheti az állapotot. A kötetek általános célú fájltárolást biztosítanak, és lehetővé teszik a fájlok normál lemezI/O-fájl API-k használatával történő olvasását/írását. A kötet erőforrás egy deklaratív módon, hogyan van csatlakoztatva egy könyvtár és a biztonsági tároló hozzá (vagy Az Azure Files kötet vagy a Service Fabric megbízható kötet).  További információért olvassa el [az állapot tárolása című szöveget.](service-fabric-mesh-storing-state.md#volumes)

![Kötetek][Image3]

## <a name="programming-models"></a>Programozási modellek
A szolgáltatáserőforrás csak egy tárolórendszerkép futtatását igényli, amelyre az erőforráshoz társított kódcsomag(ok) hivatkoznak. Bármilyen, bármilyen nyelven írt kódot futtathat a tárolón belüli bármely keretrendszer használatával anélkül, hogy ismernie kellene vagy használnia kellene a Service Fabric Mesh-specifikus API-kat. 

Az alkalmazáskód továbbra is hordozható, még a Service Fabric Mesh-en kívül is, és az alkalmazás-telepítések konzisztensek maradnak, függetlenül attól, hogy milyen nyelvet vagy keretrendszert használnak a szolgáltatások megvalósításához. Függetlenül attól, hogy az alkalmazás ASP.NET Core, Go, vagy csak egy sor folyamatok és parancsfájlok, a Service Fabric Mesh erőforrás telepítési modell ugyanaz marad. 

## <a name="packaging-and-deployment"></a>Csomagolás és üzembe helyezés

Az erőforrásmodellen alapuló Service Fabric Mesh-alkalmazások Docker-tárolókként vannak csomagolva.  A Service Fabric Mesh egy megosztott, több-bérlős környezet, és a tárolók magas szintű elkülönítést biztosítanak.  Ezeket az alkalmazásokat JSON vagy YAML formátumban írják le (amelyet aztán JSON-ra konvertálnak). Mesh alkalmazás üzembe helyezésekor az Azure Service Fabric Mesh, az alkalmazás leírásához használt JSON egy Azure Resource Manager sablon. Az erőforrások azure-erőforrásokhoz vannak rendelve.  Mesh-alkalmazás üzembe helyezésekor egy Service Fabric-fürtre (önálló vagy Az Azure által üzemeltetett), az alkalmazás leírásához használt JSON egy azure Resource Manager-sablonhoz hasonló formátum.  Üzembe helyezés után a Mesh-alkalmazások HTTP-felületeken vagy az Azure CLI-n keresztül kezelhetők. 


## <a name="next-steps"></a>További lépések 
Ha többet szeretne megtudni a Service Fabric Mesh-ről, olvassa el az áttekintést:
- [A szolgáltatásháló hálója – áttekintés](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
