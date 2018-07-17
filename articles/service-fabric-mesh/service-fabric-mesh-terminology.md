---
title: Az Azure Service Fabric háló terminológiája |} A Microsoft Docs
description: További információk a gyakori kifejezések az Azure Service Fabric-háló.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: db7a89ffa4ff1a2eb271869c93b7beb088fd13b3
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076157"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric-háló terminológiája

Az Azure Service Fabric háló egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a mikroszolgáltatás-alapú alkalmazások üzembe helyezése nélkül kezelése virtuális gépek, tároló, vagy a hálózat. Ez a cikk részletesen a feltételeket, a dokumentációban felhasznált tudni az Azure Service Fabric-háló által használt terminológiával.

## <a name="service-fabric"></a>Service Fabric

Service Fabric egy nyílt forráskódú elosztott rendszerplatform, amely megkönnyíti a csomagolását, üzembe helyezése és kezelése a skálázható és megbízható mikroszolgáltatások a. Service Fabric, amely az orchestrator-megoldás a Service Fabric-háló. A Service Fabric hogyan hozhat létre, és futtassa a mikroszolgáltatás-alapú alkalmazások lehetőséget biztosít. Bármely keretrendszer használatával a szolgáltatások írását, és válassza ki, az alkalmazás futtatásához több környezetben lehetőségek közül.

## <a name="deployment-and-application-models"></a>Üzembe helyezés és az alkalmazás modellek 

A szolgáltatások üzembe helyezéséhez meg kell ismertetik, hogyan kell futtatni. Service Fabric támogatja a három különböző üzemi modellek:

### <a name="resource-model"></a>Erőforrásmodell
Erőforrások, amelyeket a Service Fabric, beleértve az alkalmazások, szolgáltatások, hálózatok és kötetek külön-külön telepíthető. Erőforrások vannak meghatározva egy YAML-fájlt vagy az Azure erőforrás-modellje séma JSON-fájlt.

Az erőforrás-modellje ismertetik a Service Fabric-alkalmazások legegyszerűbb módja. Egyszerű üzembe helyezését és felügyeletét a tárolóalapú szolgáltatás a fő célja van.

Erőforrások helyezhetők, bárhol a Service Fabric futtatja.

### <a name="native-model"></a>Natív modell
A natív alkalmazás modellt biztosít az alkalmazások teljes alacsony szintű hozzáféréssel rendelkező Service Fabric. A regisztrált típusok jegyzékfájlok XML az alkalmazások és szolgáltatások vannak meghatározva.

A natív modell támogatja a Reliable Services keretrendszer, amely a Service Fabric-futtatókörnyezet API-k hozzáférést és a fürt kezelése a C# és Java API-kat biztosít. A natív modell támogatja a tetszőleges tárolók és a végrehajtható fájlokat is.

A natív modell nem támogatott a Service Fabric-háló környezetben.

### <a name="docker-compose"></a>Docker Compose 
[A docker Compose](https://docs.docker.com/compose/) része a Docker-projekt. Service Fabric korlátozott támogatást biztosít a Docker Compose modellt használó alkalmazások üzembe helyezéséhez.

## <a name="environments"></a>Környezetek

Service Fabric, amely egy nyílt forráskódú platform technológia, amely számos különböző szolgáltatások és termékek alapján. A Microsoft az alábbi lehetőségeket kínálja:

 - **Service Fabric-háló**: egy teljes körűen felügyelt szolgáltatás, amellyel a Service Fabric-alkalmazások Microsoft Azure-ban.
 - **Az Azure Service Fabric**: az Azure-ban üzemeltetett Service Fabric-fürt ajánlat. A Service Fabric és az Azure-infrastruktúrát, és a Service Fabric-fürtök frissítése és a konfigurációkezelés integrációjával biztosítja.
 - **Önálló Service Fabric**: telepítés és konfigurálás eszközöket a [bárhol a Service Fabric-fürtök üzembe helyezése](/azure/service-fabric/service-fabric-deploy-anywhere) (helyszíni vagy bármely más szolgáltatónál). Az Azure nem kezeli.
 - **Service Fabric fejlesztési fürtöt**: Windows, Linux vagy Mac rendszeren helyi fejlesztői élményt nyújt a Service Fabric-alkalmazások fejlesztését.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Környezet, keretrendszer és üzembe helyezési modell támogatási mátrix
Különböző környezetek eltérő szintű támogatást keretrendszerek és üzembe helyezési modellel rendelkezik. A következő táblázat ismerteti a támogatott keretrendszer és az üzembe helyezési modellt kombinációját.

|Frameworks\Deployment modell |Erőforrásmodell |Modell manifest | Összeállítás|
|---|---|---|---|
|A Reliable Actors és a Reliable Services |Nem támogatott |Támogatott |Nem támogatott |
|Bármely más keretrendszer és nyelv |Támogatja a tárolók |Támogatott a folyamatok és tárolók |Támogatja a tárolók |

A következő táblázat ismerteti a támogatott környezet és az üzembe helyezési modellt kombinációját.

|Environment\Deployment modell |Erőforrásmodell |Modell manifest |Összeállítás |
|---|---|---|---|
|Az Azure Service Fabric háló |Támogatott |Nem támogatott|Nem támogatott |
|Minden más környezetre |Támogatott (néhány erőforrás rendelkezik egy környezetben működni előfeltételei) |Támogatott |Korlátozott támogatás |

## <a name="next-steps"></a>További lépések

Service Fabric-háló kapcsolatos további információkért olvassa el a áttekintése:
- [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md)
