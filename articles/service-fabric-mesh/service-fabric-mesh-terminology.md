---
title: Az Azure Service Fabric hálójának terminológiája
description: Ez a cikk részletezi az Azure Service Fabric Mesh által használt terminológiát, hogy jobban megértse a dokumentációban használt kifejezéseket.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 6df7a6b708bca81f1390ac879f24ea4c22c38bee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351973"
---
# <a name="service-fabric-mesh-terminology"></a>Szolgáltatás hálóhálójának terminológiája

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. Ez a cikk részletezi az Azure Service Fabric Mesh által használt terminológiát, hogy jobban megértse a dokumentációban használt kifejezéseket.

## <a name="service-fabric"></a>Service Fabric

[A Service Fabric](/azure/service-fabric/) egy nyílt forráskódú elosztott rendszerplatform, amely megkönnyíti a méretezhető és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését. A Service Fabric az orchestrator, amely a Service Fabric Mesh. A Service Fabric lehetőséget biztosít a mikroszolgáltatások alkalmazásai létrehozásához és futtatásához. Bármely keretrendszer segítségével a szolgáltatások írása, és válassza ki, hogy hol futtassa az alkalmazást több környezeti lehetőségek.

## <a name="application-and-service-concepts"></a>Alkalmazási és szolgáltatási koncepciók

**Service Fabric Mesh Application**: A Service Fabric mesh alkalmazások leírása az [erőforrásmodell](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (YAML és JSON erőforrásfájlok) és telepíthető bármely környezetben, ahol a Service Fabric fut.

**Service Fabric natív alkalmazás:** Service Fabric natív alkalmazások a [natív alkalmazásmodell](/azure/service-fabric/service-fabric-application-model) (XML-alapú alkalmazás és szolgáltatásjegyzékek) írja le.  A Service Fabric natív alkalmazásai nem futtathatók a Service Fabric Mesh alkalmazásban.

**Alkalmazás**: A Service Fabric Mesh alkalmazás egy hálóalkalmazás központi telepítésének, verziószámozásának és élettartamának egysége. Az egyes alkalmazáspéldányok életciklusa egymástól függetlenül kezelhető.  Az alkalmazások egy vagy több szolgáltatáskódcsomagból és -beállításból állnak. Egy alkalmazás az Azure Resource Model (RM) séma használatával van definiálva.  A szolgáltatások az erőforrás-védelmi sablonban az alkalmazáserőforrás tulajdonságaiként vannak leírva.  Az alkalmazás által használt hálózatokra és kötetekre az alkalmazás hivatkozik.  Egy alkalmazás létrehozásakor az alkalmazás, a szolgáltatás(ok), a hálózat és a kötet(ek) a Service Fabric erőforrásmodell használatával modelleződnek.

**Szolgáltatás**: Egy alkalmazás szolgáltatása mikroszolgáltatást jelöl, és teljes és önálló függvényt hajt végre. Minden szolgáltatás egy vagy több kódcsomagból áll, amelyek leírják a kódcsomaghoz társított tárolórendszerkép futtatásához szükséges mindent.  Az alkalmazás szolgáltatásreplikáinak száma skálázható be és ki.

**Kódcsomag**: A kódcsomagok leírnak mindent, ami a kódcsomaghoz társított tárolórendszerkép futtatásához szükséges, beleértve a következőket:

* Tároló neve, verziója és rendszerleíró adatbázisa
* Az egyes tárolókhoz szükséges PROCESSZOR- és memóriaerőforrások
* Hálózati végpontok
* A tárolóba csatlakoztatandó kötetek, amelyek egy külön köteterőforrásra hivatkoznak.

## <a name="deployment-and-application-models"></a>Központi telepítési és alkalmazásmodellek 

A szolgáltatások üzembe helyezéséhez le kell írnia, hogyan kell futtatni. A Service Fabric három különböző üzembe helyezési modellt támogat:

### <a name="resource-model"></a>Erőforrásmodell
A Service Fabric-erőforrások olyan ok, amely külön-külön telepíthető a Service Fabric számára; beleértve az alkalmazásokat, szolgáltatásokat, hálózatokat és köteteket. Az erőforrások meghatározása JSON-fájl lal van definiálva, amely fürtvégpontra telepíthető.  A Service Fabric Mesh, az Azure Resource Model séma használatos. A YAML-fájlséma a definíciós fájlok könnyebb megszerzőségére is használható. Az erőforrások bárhol üzembe helyezhetők, ahol a Service Fabric fut. Az erőforrás-modell a legegyszerűbb módja a Service Fabric-alkalmazások leírásának. Fő hangsúly az egyszerű üzembe helyezés és a konténeres szolgáltatások kezelése. További információ: [Bevezetés a Service Fabric erőforrásmodellbe](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)című olvasni.

### <a name="native-model"></a>Natív modell
A natív alkalmazásmodell teljes alacsony szintű hozzáférést biztosít az alkalmazások számára a Service Fabric-hez. Az alkalmazások és szolgáltatások az XML-jegyzékfájlokban regisztrált típusokként vannak definiálva.

A natív modell támogatja a Reliable Services keretrendszer, amely hozzáférést biztosít a Service Fabric futásidejű API-k és a fürtfelügyeleti API-k C# és Java. A natív modell is támogatja az tetszőleges tárolók és végrehajtható fájlokat.

A natív modell nem támogatott a Service Fabric Mesh környezetben.  További információt a [programozási modell áttekintése című témakörben talál.](/azure/service-fabric/service-fabric-choose-framework)

### <a name="docker-compose"></a>Docker-írás 
[A Docker Compose](https://docs.docker.com/compose/) a Docker-projekt része. A Service Fabric korlátozott támogatást nyújt a Docker Compose modellt használó alkalmazások üzembe helyezéséhez.

## <a name="environments"></a>Környezetek

A Service Fabric egy nyílt forráskódú platformtechnológia, amelyen számos különböző szolgáltatás és termék alapul. A Microsoft a következő lehetőségeket biztosítja:

 - **Service Fabric Mesh**: A Microsoft Azure-beli Service Fabric-alkalmazások futtatásához teljes körűen felügyelt szolgáltatás.
 - **Azure Service Fabric:** Az Azure üzemeltetett Service Fabric-fürtajánlat. Integrációt biztosít a Service Fabric és az Azure-infrastruktúra között, valamint a Service Fabric-fürtök frissítése és konfigurációs kezelése között.
 - **Service Fabric önálló:** A telepítési és konfigurációs eszközök telepítése [Service Fabric-fürtök bárhol](/azure/service-fabric/service-fabric-deploy-anywhere) (a helyszínen vagy bármely felhőszolgáltató) üzembe helyezéséhez. Nem az Azure kezeli.
 - **Service Fabric fejlesztői fürt:** Helyi fejlesztési élményt nyújt Windows, Linux vagy Mac a Service Fabric-alkalmazások fejlesztéséhez.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Környezet, keretrendszer és telepítési modell támogatási mátrixa
A különböző környezetek különböző szintű támogatást nyújtanak a keretrendszerekhez és a telepítési modellekhez. Az alábbi táblázat ismerteti a támogatott keretrendszer és üzembe helyezési modell kombinációit.

| Az alkalmazás típusa | Leírta: | Azure service fabric háló | Azure Service Fabric-fürtök (bármely operációs rendszer)| Helyi fürt | Önálló fürt |
|---|---|---|---|---|---|
| Szolgáltatásháló-alkalmazások | Erőforrásmodell (YAML & JSON) | Támogatott |Nem támogatott | Windows- támogatott, Linux és Mac- nem támogatott | Windows- nem támogatott |
|Szolgáltatásháló natív alkalmazásai | Natív alkalmazásmodell (XML) | Nem támogatott| Támogatott|Támogatott|Windows- támogatott|

Az alábbi táblázat ismerteti a különböző alkalmazásmodellek és a Service Fabric számára létező eszközök.

| Az alkalmazás típusa | Leírta: | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Szolgáltatásháló-alkalmazások | Erőforrásmodell (YAML & JSON) | VS 2017 |Nem támogatott |Nem támogatott | Támogatott - csak hálós környezet | Nem támogatott|
|Szolgáltatásháló natív alkalmazásai | Natív alkalmazásmodell (XML) | VS 2017 és VS 2015| Támogatott|Támogatott|Támogatott|Támogatott|

## <a name="next-steps"></a>További lépések

A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.

Válaszok a [gyakori kérdésekre](service-fabric-mesh-faq.md).
