---
title: "A Service Fabric-projekt létrehozása további lépések |} Microsoft Docs"
description: "Ez a cikk core fejlesztési feladatokhoz mutató hivatkozásokat tartalmaz a Service Fabric"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: rwike77
ms.openlocfilehash: 4523c63493bc9cb3f96713c4abbc1dd1da9130d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>A Service Fabric-alkalmazás és a következő lépések
Az Azure Service Fabric-alkalmazás létrehozása. Ez a cikk ismerteti a projekt és a potenciális lépéseket makeup.

## <a name="your-application"></a>Az alkalmazás
Minden egyes új alkalmazás egy alkalmazás projektet tartalmaz. Előfordulhat, hogy egy vagy két további projektek, attól függően, hogy a kiválasztott szolgáltatás típusának.

### <a name="the-application-project"></a>A projekt
Az alkalmazási projektet tartalmaz:

* A szolgáltatások, az alkalmazás alkotó mutató hivatkozások gyűjteménye.
* Három közzététele profilok (1-csomópont helyi 5-csomópont helyi és felhőalapú), melyekkel a különböző környezetek – például az alapértelmezés szerint a fürt végpontja, és hogy kell-e a frissítés központi telepítések kapcsolatos beállítások használata beállítások megőrzése érdekében.
* Három alkalmazás paraméter fájlok (ugyanaz, mint a fent), hogy segítségével konfigurációinak környezetfüggő alkalmazás, például a szolgáltatás létrehozásához a partíciók száma.
* A telepítési parancsfájlt, amely segítségével telepítheti az alkalmazást, a parancssorban vagy egy automatizált folyamatos integrációt és telepítést folyamat részeként.
* Az alkalmazás jegyzékében, amely ismerteti az alkalmazást. A jegyzék a ApplicationPackageRoot mappában található.

### <a name="stateless-service"></a>Állapotmentes szolgáltatások
Ha hozzáad egy új állapotmentes szolgáltatások, a Visual Studio ad hozzá a projekt a megoldás, amely tartalmazza a felvett típus `StatelessService`. A szolgáltatás növeli a számlálót egy helyi változót.

### <a name="stateful-service"></a>Az állapotalapú szolgáltatás
Ha hozzáad egy új állapotalapú szolgáltatásból, a Visual Studio ad hozzá a projekt a megoldás, amely tartalmazza a felvett típus `StatefulService`. A szolgáltatás növeli a számlálót a `RunAsync` metódust, és tárolja az eredményeket egy `ReliableDictionary`.

### <a name="actor-service"></a>Aktorszolgáltatás
Ha hozzáad egy új megbízható szereplő, a Visual Studio ad hozzá két projektet a megoldásban: szereplő projektben és egy illesztőfelület-projekthez.

Az aktor projekt beállítás metódusokat biztosít, és a számláló értékének kiolvasásakor megbízhatóan megőrzött belül a aktorállapot. A felület projekt más szolgáltatások segítségével a szereplő meghívása felületet biztosít.

### <a name="stateless-web-api"></a>Állapot nélküli webes API-k
Az állapot nélküli Web API-projektet egy alapszintű webszolgáltatás, amelyet segítségével nyissa meg az alkalmazás külső ügyfelek számára biztosít. További információ a projekt felépítése, lásd: [Service Fabric Web API szolgáltatások OWIN önálló üzemeltető](service-fabric-reliable-services-communication-webapi.md).


### <a name="aspnet-core"></a>Az ASP.NET core
A Service Fabric SDK biztosít az ASP.NET Core olyan sablonokat, amelyeket az ASP.NET Core projektek önálló: üres, [Web API][aspnet-webapi], és [webalkalmazás] [aspnet-webapp].

### <a name="guest-executables-and-guest-containers"></a>Vendég végrehajtható fájlok és a Vendég-tárolókon

A Service Fabric "Vendég" egy olyan szolgáltatás, a platform programozási modellek nem épített. Csomagot a bináris fájlok esetében a Vendég vagy [közvetlenül az alkalmazáscsomag](service-fabric-deploy-existing-app.md) vagy [keresztül a tároló lemezkép](service-fabric-deploy-container.md). Mindkét esetben a Visual Studio létrehozza a szükséges összetevők a **ApplicationPackageRoot** mappában található a projektet. A Visual Studio nem hoz létre egy új service-projekt, mert a kódot már létezik a rendszerben található. Ha azt szeretné, a Vendég-projektek a Service Fabric-alkalmazás projekt együtt kezelheti, később is hozzáadhatja ugyanahhoz a Visual Studio-megoldáshoz.

## <a name="next-steps"></a>Következő lépések
### <a name="create-an-azure-cluster"></a>Egy Azure-fürt létrehozása
A Service Fabric SDK egy helyi fürtöt biztosít a fejlesztéshez és teszteléshez. A fürt létrehozása az Azure-ban: [a Service Fabric-fürt beállítása az Azure-portálról][create-cluster-in-portal].

### <a name="publish-your-application-to-azure"></a>Tegye közzé az alkalmazást az Azure-bA
Az alkalmazás közvetlenül a Visual Studio Azure fürthöz is közzéteheti. Megtudhatja, hogyan, lásd: [az Azure-bA az alkalmazás közzétételéhez][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>A fürt megjelenítése a Service Fabric Explorer használatával
Service Fabric Explorer egyszerű módot nyújt a fürt megjelenítése, beleértve a központilag telepített alkalmazások és a fizikai elrendezését kínál. További tudnivalókért lásd: [a fürt megjelenítése a Service Fabric Explorer használatával][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Verziója és a szolgáltatások frissítésére
A Service Fabric lehetővé teszi, hogy függetlenül verziószámozásának és független az alkalmazás szolgáltatások frissítésével. További tudnivalókért lásd: [Versioning és a szolgáltatások frissítését][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>A Visual Studio Team Services folyamatos integráció konfigurálása
Ha szeretné megtudni, hogyan állíthat be a folyamatos integrációs folyamatban a Service Fabric-alkalmazás, lásd: [folyamatos integráció konfigurálása a Visual Studio Team Services][ci-with-vso].

<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
