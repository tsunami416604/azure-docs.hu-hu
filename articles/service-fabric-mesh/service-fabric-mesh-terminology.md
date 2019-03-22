---
title: Az Azure Service Fabric háló terminológiája |} A Microsoft Docs
description: További információk a gyakori kifejezések az Azure Service Fabric-háló.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 2d2661593ba3d9be2755d81803c8e248a2f7d0e1
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335810"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric-háló terminológiája

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. Ez a cikk részletesen a feltételeket, a dokumentációban felhasznált tudni az Azure Service Fabric-háló által használt terminológiával.

## <a name="service-fabric"></a>Service Fabric

[A Service Fabric](/azure/service-fabric/) egy nyílt forráskódú elosztott rendszerplatform, amely megkönnyíti a csomagolását, üzembe helyezése és kezelése a skálázható és megbízható mikroszolgáltatások. Service Fabric, amely az orchestrator-megoldás a Service Fabric-háló. A Service Fabric hogyan hozhat létre, és futtassa a mikroszolgáltatás-alapú alkalmazások lehetőséget biztosít. Bármely keretrendszer használatával a szolgáltatások írását, és válassza ki, az alkalmazás futtatásához több környezetben lehetőségek közül.

## <a name="application-and-service-concepts"></a>Alkalmazás és a szolgáltatással kapcsolatos fogalmak

**Service Fabric-alkalmazását rácsvonal**: Service Fabric-háló alkalmazások által leírt a [Erőforrásmodell](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (YAML, JSON, és erőforrás-fájlok), és bármilyen környezetben, ahol a Service Fabric fut, üzembe helyezhetők.

**Service Fabric natív alkalmazás**: Service Fabric natív alkalmazások által leírt a [natív alkalmazásmodell](/azure/service-fabric/service-fabric-application-model) (XML-alapú alkalmazásra és szolgáltatásjegyzékre).  Service Fabric natív alkalmazások a Service Fabric-háló nem futtatható.

**Alkalmazás**: A Service Fabric-háló alkalmazás-e az üzembe helyezés, a verziókezelés és a egy háló alkalmazás teljes élettartama egysége. Mindegyik alkalmazáspéldány életciklusának egymástól függetlenül is felügyelhetők.  Alkalmazások egy vagy több szolgáltatás kódcsomagok és beállítások állnak. Egy alkalmazás használatával az Azure Resource modell (RM) séma van megadva.  Szolgáltatások, az alkalmazás-erőforrás az erőforrás-kezelő sablon tulajdonságait ismerteti.  Az alkalmazás által hivatkozott hálózatok és az alkalmazás által használt köteteket.  Egy alkalmazás létrehozásakor az alkalmazás, szolgáltatás(ok), hálózati és kötet(ek) modellezése eltér a Service Fabric erőforrás modell használatával.

**Szolgáltatás**: Egy szolgáltatás, az alkalmazások mikroszolgáltatások jelöli, és a egy teljes és a különálló funkciót hajt végre. Mindegyik szolgáltatás egy vagy több, minden a tároló rendszerképét a kódcsomag társított futtatásához szükséges leíró kódcsomagok tevődik össze.  Az alkalmazások service replikák száma és skálázhatók.

**Kódcsomag**: Minden a tároló rendszerképét a kódcsomag, többek között az alábbi társított futtatásához szükséges kódot csomagok írják le:

* Tároló nevét, verzióját és a beállításjegyzék
* Az egyes tárolók szükséges CPU és memória-erőforrások
* Hálózati végpont
* Kötetek a tárolóban, egy önálló kötetre erőforrásra hivatkozik.

## <a name="deployment-and-application-models"></a>Üzembe helyezés és az alkalmazás modellek 

A szolgáltatások üzembe helyezéséhez meg kell ismertetik, hogyan kell futtatni. Service Fabric támogatja a három különböző üzemi modellek:

### <a name="resource-model"></a>Erőforrásmodell
Service Fabric-erőforrások, amelyeket a Service Fabric; külön-külön telepíthető beleértve az alkalmazások, szolgáltatások, hálózatok és köteteket. Erőforrások JSON-fájlok, amelyen keresztül a fürt azon végpontján használatával vannak meghatározva.  A Service Fabric tervezhetők az Azure erőforrás-modellje séma használnak. Egy YAML-fájl séma is segítségével könnyebben hozhat létre a definíciós fájlokat. Erőforrások helyezhetők, bárhol a Service Fabric futtatja. Az erőforrás-modellje ismertetik a Service Fabric-alkalmazások legegyszerűbb módja. Egyszerű üzembe helyezését és felügyeletét a tárolóalapú szolgáltatás a fő célja van. További tudnivalókért olvassa el a [Bevezetés a Service Fabric Erőforrásmodell](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Natív modell
A natív alkalmazás modellt biztosít az alkalmazások teljes alacsony szintű hozzáféréssel rendelkező Service Fabric. A regisztrált típusok jegyzékfájlok XML az alkalmazások és szolgáltatások vannak meghatározva.

A natív modell támogatja a Reliable Services keretrendszer, amely a Service Fabric-futtatókörnyezet API-k hozzáférést és a fürt kezelése a C# és Java API-kat biztosít. A natív modell támogatja a tetszőleges tárolók és a végrehajtható fájlokat is.

A natív modell nem támogatott a Service Fabric-háló környezetben.  További információkért lásd: [programozási modell áttekintése](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Compose 
[A docker Compose](https://docs.docker.com/compose/) része a Docker-projekt. Service Fabric korlátozott támogatást biztosít a Docker Compose modellt használó alkalmazások üzembe helyezéséhez.

## <a name="environments"></a>Környezetek

Service Fabric, amely egy nyílt forráskódú platform technológia, amely számos különböző szolgáltatások és termékek alapján. A Microsoft az alábbi lehetőségeket kínálja:

 - **Service Fabric-háló**: Egy teljes körűen felügyelt szolgáltatás, a Microsoft Azure Service Fabric-alkalmazások futtatásához.
 - **Az Azure Service Fabric**: Az Azure-ban üzemeltetett Service Fabric-fürt ajánlat. A Service Fabric és az Azure-infrastruktúrát, és a Service Fabric-fürtök frissítése és a konfigurációkezelés integrációjával biztosítja.
 - **Önálló Service Fabric**: A telepítés és konfigurálás eszközöket [bárhol a Service Fabric-fürtök üzembe helyezése](/azure/service-fabric/service-fabric-deploy-anywhere) (helyszíni vagy bármely más szolgáltatónál). Az Azure nem kezeli.
 - **Service Fabric fejlesztési fürtöt**: Helyi fejlesztési élményt nyújt a Windows, Linux vagy Mac a Service Fabric-alkalmazások fejlesztését.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Környezet, keretrendszer és üzembe helyezési modell támogatási mátrix
Különböző környezetek eltérő szintű támogatást keretrendszerek és üzembe helyezési modellel rendelkezik. A következő táblázat ismerteti a támogatott keretrendszer és az üzembe helyezési modellt kombinációját.

| Az alkalmazás | Leírtak szerint | Az Azure Service Fabric háló | Az Azure Service Fabric-fürtök (bármely operációs rendszeren)| Helyi fürt | Önálló fürt |
|---|---|---|---|---|---|
| Service Fabric-háló alkalmazások | Resource Model (YAML & JSON) | Támogatott |Nem támogatott | Windows – támogatott, Linux és Mac-nem támogatott | Windows – nem támogatott |
|Service Fabric natív alkalmazások | Native Application Model (XML) | Nem támogatott| Támogatott|Támogatott|Windows – támogatott|

A következő táblázat ismerteti a különböző alkalmazás-modellek és azokat az eszközöket, hogy a Service Fabric elleni őket létezik.

| Az alkalmazás | Leírtak szerint | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric-háló alkalmazások | Resource Model (YAML & JSON) | VS 2017 |Nem támogatott |Nem támogatott | Támogatott – csak a háló környezet | Nem támogatott|
|Service Fabric natív alkalmazások | Native Application Model (XML) | VS 2017 és a VS 2015| Támogatott|Támogatott|Támogatott|Támogatott|

## <a name="next-steps"></a>További lépések

A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.

Válaszok a [gyakori kérdésekre](service-fabric-mesh-faq.md).
