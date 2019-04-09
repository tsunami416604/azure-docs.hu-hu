---
title: A Service Fabric az Azure-ban – áttekintés | Microsoft Docs
description: A Service Fabric áttekintése, ahol az alkalmazások számos mikroszolgáltatásból állnak a skálázhatóság és a rugalmasság érdekében. A Service Fabric egy elosztott rendszerplatform, amellyel skálázható, megbízható és könnyen felügyelhető alkalmazásokat készíthet a felhőben való használatra.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: cd2895378f713749b3a1d0ac01986b728ceafd2b
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009003"
---
# <a name="overview-of-azure-service-fabric"></a>Az Azure Service Fabric áttekintése
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások és tárolók csomagolását, üzembe helyezését és kezelését. A Service Fabric emellett választ ad a natív felhőalapú alkalmazások fejlesztésének és felügyeletének jelentős kihívásaira. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak. A Service Fabric egy következő generációs platform az ezen tárolókban futó, nagyvállalati szintű, első rétegbeli, felhőléptékű alkalmazások felépítésére és felügyeletére.

Ez a rövid videó bemutatja a Service Fabric platformot és a mikroszolgáltatásokat: 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

## <a name="compliance"></a>Megfelelőség
Az Azure Service Fabric erőforrás-szolgáltató az összes Azure-régióban érhető el, és kompatibilis az összes megfelelőségi tanúsítványok, amelyek az Azure rendelkezik; Ez az alábbiakat tartalmazza: SOC, ISO, PCI DSS, HIPAA, and GDRP. Tekintse át a megfelelőségi tanúsítványok teljes listáját a következő: [Megfelelőségi ajánlatok](https://www.microsoft.com/trustcenter/compliance/complianceofferings)

## <a name="applications-composed-of-microservices"></a>Mikroszolgáltatásokból álló alkalmazások 
A Service Fabric lehetővé teszi mikroszolgáltatásokból álló, skálázható és megbízható alkalmazásokat készítését és felügyeletét, amelyek nagy sűrűség mellett futnak számítógépek egy megosztott készletén, vagyis fürtökön. Kifinomult, könnyen használható futtatókörnyezetet biztosít, amelyben elosztott, skálázható, állapot nélküli és állapotalapú, tárolókban futó mikroszolgáltatásokat állíthat össze. Ezenkívül átfogó alkalmazásfelügyeleti képességeket kínál a telepített alkalmazások kiépítéséhez, üzembe helyezéséhez, figyeléséhez, frissítéséhez/javításához és törléséhez, beleértve a tárolóalapú szolgáltatásokat is.

A Service Fabric képezi számos jelenleg elérhető Microsoft-szolgáltatás alapját, ilyen például az Azure SQL Database, az Azure Cosmos DB, a Cortana, a Microsoft Power BI, a Microsoft Intune, az Azure Event Hubs, az Azure IoT Hub, a Dynamics 365, a Skype Vállalati verzió, valamint több alapvető Azure-szolgáltatás.

A Service Fabric segítségével olyan natív felhőalapú szolgáltatások készíthetőek, amelyek kis méretben indíthatóak el, és igény szerint nagy léptékűre skálázhatók akár többszáz vagy többezer géppel.

A modern webes szolgáltatások mikroszolgáltatásokból állnak. Mikroszolgáltatások például a protokollátjárók, a felhasználói profilok, a kosarak, a készletfeldolgozók, az üzenetsorok és a gyorsítótárak. A Service Fabric egy mikroszolgáltatás-platform, amely minden mikroszolgáltatásnak (vagy tárolónak) egyedi nevet ad, amely lehet állapot nélküli vagy állapotalapú.

A Service Fabric átfogó futásidejű és életciklus-felügyeleti képességeket biztosít az ilyen mikroszolgáltatásokból álló alkalmazások számára. Mikroszolgáltatásokat üzemeltet a Service Fabric-fürtben üzembe helyezett és aktivált tárolókban. A virtuális gépekről a tárolókra való áttérés lehetővé teszi, hogy nagyságrendekkel megnövelje a sűrűséget. Ehhez hasonlóan a sűrűséget jelentősen megnövelheti, ha a tárolókról áttér a tárolókban üzemeltetett mikroszolgáltatásokra. Például az Azure SQL Database egyetlen fürtje több száz számítógépből áll, amelyek több tízezer tárolót futtatnak, és ezek több százezer adatbázist üzemeltetnek. Mindegyik adatbázis egy állapotalapú Service Fabric-mikroszolgáltatás. 

A mikroszolgáltatás-alapú megközelítéssel kapcsolatos további részletekért olvassa el a [Miért érdemes a mikroszolgáltatás-alapú megközelítést választani alkalmazások létrehozásához?](service-fabric-overview-microservices.md) című cikket.

## <a name="container-deployment-and-orchestration"></a>Tároló üzembe helyezése és előkészítése
A Service Fabric a Microsoft [tárolóvezénylője](service-fabric-cluster-resource-manager-introduction.md), amely mikroszolgáltatásokat helyez üzembe számítógépfürtökön. A mikroszolgáltatások számos különféle módon fejleszthetőek a [Service Fabric programozási modellektől](service-fabric-choose-framework.md) és az [ASP.NET Core-tól](service-fabric-reliable-services-communication-aspnetcore.md) [tetszőleges kód üzembe helyezéséig](service-fabric-guest-executables-introduction.md). Ami pedig még ennél is fontosabb, hogy egy adott alkalmazáson belül vegyesen használhat folyamatokon belüli és tárolókon belüli szolgáltatásokat. Ha csak [tárolókat kíván üzembe helyezni és felügyelni](service-fabric-containers-overview.md), a Service Fabric tökéletes választás tárolóvezénylőként.

## <a name="any-os-any-cloud"></a>Bármely operációs rendszeren vagy felhőben
A Service Fabric bárhol futtatható. Számos környezetben létrehozhat fürtöket a Service Fabrichez, például az Azure-ban vagy a helyszínen, Windows Serveren vagy Linuxon. Akár más nyilvános felhőkben is létrehozhat fürtöket. Ráadásul az SDK fejlesztési környezete **azonos** az éles környezettel, és nem használ emulátorokat. Ez azt jelenti, hogy a helyi fejlesztési fürtön futó alkalmazások üzembe helyezhetők más környezetek fürtjein.

![Service Fabric platform][Image1]

Windows-fejlesztéshez a Service Fabric .NET SDK integrálva van a Visual Studióval és a PowerShell-lel. Lásd [a fejlesztési környezet Windowson való előkészítését](service-fabric-get-started.md) ismertető cikket. Linux-fejlesztéshez a Service Fabric Java SDK integrálva van az Eclipse-szel, és a Java-, .NET Core- és tárolóalkalmazások sablonjainak előállítása a Yeoman használatával történik. Lásd [a fejlesztési környezet Linuxon való előkészítését](service-fabric-get-started-linux.md) ismertető cikket.

A fürtök létrehozásával kapcsolatos további információkért olvassa el a [fürtök Windows Serveren vagy Linuxon való létrehozásával](service-fabric-deploy-anywhere.md) kapcsolatos témakört vagy Azure-ral való létrehozás esetén a [fürtök az Azure Portalon való létrehozásával](service-fabric-cluster-creation-via-portal.md) kapcsolatos témakört.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Állapot nélküli és állapotalapú mikroszolgáltatások a Service Fabrichez
A Service Fabric segítségével mikroszolgáltatásokból vagy tárolókból álló alkalmazásokat építhet. Az állapot nélküli mikroszolgáltatások (például a protokollátjárók és webproxyk) nem tartanak fenn változtatható állapotot a kéréseken és a szolgáltatástól ezekre kapott válaszokon kívül. Az Azure Cloud Services feldolgozói szerepkörei például állapotmentes szolgáltatások. Az állapotalapú mikroszolgáltatások (például felhasználói fiókok, adatbázisok, eszközök, kosarak és üzenetsorok) változtatható, mérvadó állapotot tartanak fenn a kéréseken és a válaszokon kívül is. A modern webes alkalmazások állapot nélküli és állapotalapú mikroszolgáltatások kombinációjából állnak. 

A Service Fabric legfontosabb különbséget az állapotalapú szolgáltatások, vagy erős koncentrálhat az [beépített programozási modellekkel](service-fabric-choose-framework.md) vagy a tárolóalapú állapotalapú szolgáltatásokkal. Az [alkalmazás-forgatókönyvek](service-fabric-application-scenarios.md) az állapotalapú szolgáltatások használatát mutatják be.


## <a name="application-lifecycle-management"></a>Alkalmazások életciklus-felügyelete
A Service Fabric támogatást nyújt a teljes alkalmazás-életciklushoz és a felhőalapú alkalmazások CI/CD folyamataihoz, beleértve a tárolókat. Az életciklus részét képezi minden a fejlesztéstől kezdve az üzembe helyezésen, valamint a napi felügyeleten és karbantartáson át a leszerelésig.

A Service Fabric alkalmazáséletciklus-felügyeleti képességei lehetővé teszik az alkalmazás-rendszergazdák és az informatikai dolgozók számára egyszerű, kevés beavatkozást igénylő munkafolyamatok használatát az alkalmazások kiépítéséhez, üzembe helyezéséhez, javításához és figyeléséhez. Ezek a beépített munkafolyamatok nagy mértékben csökkentik az informatikai dolgozók az alkalmazások folyamatos elérhetőségének biztosításából eredő terheit.

Az alkalmazások többsége együtt üzembe helyezett állapot nélküli és állapotalapú mikroszolgáltatások, tárolók és egyéb végrehajtható fájlok kombinációjából áll. Az erős típusmegadású alkalmazások révén a Service Fabric lehetővé teszi több alkalmazáspéldány üzembe helyezését. Az egyes példányok felügyelete és frissítése egymástól függetlenül történik. Ami azonban még ennél is fontosabb, a Service Fabric képes tárolókat vagy végrehajtható fájlokat üzembe helyezni és megbízhatóvá tenni. Például a Service Fabric segítségével telepíthet .NET-et, ASP.NET Core-t, node.js-t, Windows-tárolókat, Linux-tárolókat, Java virtuális gépeket, szkripteket, Angulart vagy szó szerint bármit, amelyre az alkalmazásnak szüksége van.

A Service Fabric olyan CI-/CD-eszközökkel van integrálva, mint az [Azure Pipelines](https://www.visualstudio.com/team-services/), a [Jenkins](https://jenkins.io/index.html) és az [Octopus Deploy](https://octopus.com/), és bármely más közismert CI-/CD-eszközzel is használható.

Az alkalmazások életciklus-felügyeletével kapcsolatos további tudnivalókért tekintse meg az [alkalmazások életciklusával](service-fabric-application-lifecycle.md) kapcsolatos témakört. A kódok üzembe helyezésével kapcsolatos további tudnivalókért tekintse meg a [futtatható vendégalkalmazás üzembe helyezéséről](service-fabric-deploy-existing-app.md) szóló témakört.

## <a name="key-capabilities"></a>Főbb képességek
A Service Fabric használatával a következőket teheti:

* Kódváltoztatás nélkül végezhet üzembe helyezést az Azure-ban, illetve Windowst vagy Linuxot futtató helyszíni adatközpontokban. Egyszeri írás után üzembe helyezést végezhet bárhol, bármely Service Fabric-fürtben.
* Mikroszolgáltatásokból álló skálázható alkalmazásokat fejleszthet a Service Fabric programozási modelljei, tárolók vagy tetszőleges kód felhasználásával.
* Rendkívül megbízható állapot nélküli és állapotalapú mikroszolgáltatásokat fejleszthet. Leegyszerűsítheti az alkalmazást állapotalapú mikroszolgáltatások használatával. 
* Az új Reliable Actors programozási modellel létrehozhat beépített kóddal és állapottal rendelkező felhőbeli objektumokat.
* Üzembe helyezhet és vezényelhet Windows-tárolókat és Linux-tárolókat tartalmazó tárolókat. A Service Fabric egy adatalapú, állapotalapú tárolóvezénylő.
* Másodpercek alatt üzembe helyezhet alkalmazásokat nagy sűrűség mellett számítógépenként több száz vagy több ezer alkalmazással vagy tárolóval.
* Egy adott alkalmazás különböző verzióit telepítheti egymást mellett, és mindegyiket egymástól függetlenül frissítheti.
* Alkalmazásai életciklusát állásidő nélkül felügyelheti, beleértve a jelentős és a kevésbé jelentős frissítéseket.
* Horizontálisan felskálázhatja vagy leskálázhatja a fürtök csomópontjainak számát. A csomópontok skálázásakor az alkalmazások skálázása automatikusan megtörténik.
* Figyelheti és diagnosztizálhatja az alkalmazásai állapotát, és szabályzatokat állíthat be az automatikus javítások végrehajtásához.
* Megtekintheti, ahogy az erőforrás-elosztó az alkalmazások újraelosztását vezényli a fürtben. A Service Fabric helyreáll a hibák után, és optimalizálja a terhelés elosztását az elérhető erőforrások alapján.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* További információk:
  * [Miért érdemes a mikroszolgáltatás-alapú megközelítést választani alkalmazások?](service-fabric-overview-microservices.md)
  * [A terminológia áttekintése](service-fabric-technical-overview.md)
* A [Windows fejlesztési környezet](service-fabric-get-started.md) beállítása  
* A [Linux fejlesztési környezet](service-fabric-get-started-linux.md) beállítása
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
