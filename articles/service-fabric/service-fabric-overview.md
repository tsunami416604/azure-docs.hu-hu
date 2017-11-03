---
title: "Az Azure Service Fabric áttekintése |} Microsoft Docs"
description: "A Service Fabric, ahol alkalmazások méretezés és rugalmasság biztosításához számos mikroszolgáltatások álló áttekintése. A Service Fabric egy elosztottrendszer-platform segítségével hozhatók létre, méretezhető, megbízható, és könnyen felügyelt alkalmazások a felhőben."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: msfussell
ms.custom: mvc
ms.openlocfilehash: 8ff0d38a679b673b148dd808050eda82060cfe80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-service-fabric"></a>Az Azure Service Fabric áttekintése
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások és tárolók csomagolását, üzembe helyezését és kezelését. A Service Fabric fejlesztésének és felhőalapú natív alkalmazások kezelésének jelentős kihívásaira is javítja. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak. A Service Fabric kialakításához, és a vállalati szintű, 1. rétegbeli, tárolók felhőméretű az alkalmazások kezelése a következő generációs platform jelöli.

Ez a rövid videó bemutatja a Service Fabric és mikroszolgáltatások létrehozására:<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="applications-composed-of-microservices"></a>Alkalmazások mikroszolgáltatások összetevői 
A Service Fabric és mikroszolgáltatások létrehozására, amelyek egy megosztott készletéhez gép található, amely egy fürt hivatkozunk, nagy sűrűségű futnak álló méretezhető és megbízható alkalmazások kezelése teszi lehetővé. A kifinomult, egyszerűsített futásidejű hozhat létre elosztott, méretezhető, az állapot nélküli és állapotalapú mikroszolgáltatások futó tárolók biztosít. Azt is kiépítéséhez, központi telepítése, figyeléséhez, frissítés/javítás, és törli a központilag telepített alkalmazások, beleértve a tárolóalapú szolgáltatások átfogó alkalmazás funkciókat biztosít.

A Service Fabric számos Microsoft szolgáltatás ma, beleértve az Azure SQL Database megoldásaira épül, Azure Cosmos DB, Cortana, a Microsoft Power bi-ban, a Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, a Skype vállalati verzió és számos Azure-szolgáltatásokhoz központi.

A Service Fabric szabva, amely kezdje kis lépésekkel, igény szerint, és több száz vagy ezer gépek jelentős mértékű növekedjen natív szolgáltatások létrehozására.

A mai Internet-skálázási szolgáltatások mikroszolgáltatások létrehozására a beépített. Mikroszolgáltatások például protokoll átjárók, a felhasználói profilok, vásárlás kocsik, készlet feldolgozására, üzenetsorok, és gyorsítótárba helyezi azt. A Service Fabric egy mikroszolgáltatások platform, amely minden mikroszolgáltatási (vagy a tároló) egy egyedi nevet ad, hogy állapot nélküli vagy állapot-nyilvántartó lehet.

A Service Fabric átfogó futásidejű és életciklus funkciókat biztosít azon alkalmazásoknak, amelyek épülnek, ezek mikroszolgáltatások létrehozására. Telepített és aktivált a Service Fabric-fürt között tárolókba mikroszolgáltatások rajta. A virtuális gépekről az áthelyezési tárolók lehetővé teszi sorrendje a nagyságrendet növelését sűrűség. Hasonlóképpen egy másik nagyságrendű a sűrűségének tárolókból mikroszolgáltatások ezekben a tárolókban való áthelyezésekor lesz lehetséges. Az Azure SQL Database egy fürtön például több száz futó tárolók tízezreit üzemeltető akár több ezer adatbázis több száz összesen gépek foglalja magában. Az egyes adatbázisok a Service Fabric állapot-nyilvántartó mikroszolgáltatási. 

További információ a mikroszolgáltatások módszer, olvassa el a [ezért mikroszolgáltatások megközelítése alkalmazások?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Tároló üzembe helyezési és vezénylési
A Service Fabric a Microsoft [tároló orchestrator](service-fabric-cluster-resource-manager-introduction.md) mikroszolgáltatások telepítése gépet fürtön belül. Mikroszolgáltatások használatával számos módon fejleszthetők a [programozási modellek Service Fabric](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)található telepítése [az Ön által választott bármely kód](service-fabric-deploy-existing-app.md). Kombinálhatja is fontosabb, mind a folyamatok szolgáltatások és szolgáltatások tárolókban ugyanabban az alkalmazásban. Ha szeretné [tárolók telepítése és felügyelete](service-fabric-containers-overview.md), a Service Fabric, a tároló orchestrator tökéletes választás.

## <a name="any-os-any-cloud"></a>Minden operációs rendszer, a felhő
A Service Fabric everywhere futtatja. A Service Fabric tárolófürtöket hozhat létre, sok környezetben, beleértve az Azure vagy a helyszínen, a Windows Server vagy Linux rendszeren. Fürtök a más nyilvános felhőket is létrehozhat. Ezenkívül a fejlesztési környezetet az SDK-ban is **azonos** az éles környezetbe, az érintett nem emulátor. Ez azt jelenti Mi az a helyi fejlesztési fürtök fut telepíti a fürtök, más környezetekben.

![Service Fabric-platformról][Image1]

A Windows fejlesztői a Service Fabric .NET SDK integrálva van a Visual Studio és a Powershell. Lásd: [Windows a fejlesztőkörnyezet előkészítése](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started.md). Linux-fejlesztési a Service Fabric Java SDK Eclipse integrálva van, és Yeoman a Java, a .NET Core és a tároló alkalmazások sablonok létrehozására szolgál. Lásd: [Linux rendszeren a fejlesztőkörnyezet előkészítése](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started.md)

További információk a fürtök létrehozására, [fürt létrehozása a Windows Server vagy Linux](service-fabric-deploy-anywhere.md) vagy az Azure-fürt létrehozása [az Azure-portálon](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Az állapotmentes és állapotalapú mikroszolgáltatások a Service Fabric
A Service Fabric mikroszolgáltatások vagy a tárolókat áll használó alkalmazások létrehozását teszi lehetővé. Állapot nélküli mikroszolgáltatások (például a protokoll-átjáró és a webes proxykat) nem tartanak egy változtatható állapotot kívül egy kérelem és a válasz a szolgáltatástól. Azure Cloud Services feldolgozói szerepkörök olyan állapotmentes szolgáltatások példát. Állapot-nyilvántartó mikroszolgáltatások (például a felhasználói fiókok, adatbázisok, eszközök, bevásárlási kártyák és a várólisták) túl a kérelmet, és válaszában változtatható, mérvadó állapotának karbantartása. Napjaink Internet-méretű alkalmazások állapotmentes és állapotalapú mikroszolgáltatások kombinációjából áll. 

A Service Fabric legfontosabb különbséget a állapotalapú szolgáltatások, vagy erős összpontosít a [beépített programozási modellek ](service-fabric-choose-framework.md) vagy indexelése állapotalapú szolgáltatással. A [alkalmazás-forgatókönyvekre](service-fabric-application-scenarios.md) a forgatókönyvek, ahol állapotalapú szolgáltatások használják mutatják be.


## <a name="application-lifecycle-management"></a>Alkalmazás-életciklus kezelésének
A Service Fabric támogatást nyújt a teljes alkalmazás életciklusa és a felhőalapú alkalmazások, beleértve a tárolók CI/CD. Ez élettartama fejlesztését a telepítés, a napi felügyeleti és a karbantartás végleges leszerelése tartalmazza.

Service Fabric alkalmazás életciklus-kezelési képességek engedélyezése az alkalmazás-rendszergazdák és informatikai operátort egyszerű, alacsony érintéssel munkafolyamatok használatával konfigurálta, telepítheti, javítás, és alkalmazások figyeléséhez. Ezek a munkafolyamatok beépített nagy mértékben informatikai operátorok folyamatosan rendelkezésre álló alkalmazások tartása terhelésének csökkentése érdekében.

A legtöbb alkalmazás állapotmentes és állapotalapú mikroszolgáltatások létrehozására, a tárolók és a más végrehajtható üzembe helyezett együttesen alkotják. Azzal, hogy erős típusú alkalmazásokat, a Service Fabric lehetővé teszi, hogy több alkalmazáspéldányt központi telepítését. Minden példány kezeli, és egymástól függetlenül frissíteni. A Service Fabric fontosabb, telepítheti a tárolók vagy bármilyen végrehajtható fájlok és teszi azokat a megbízható. A Service Fabric telepíthet például .NET, az ASP.NET Core, node.js, a Windows tárolók, Linux tárolók, Java virtuális gépek, parancsfájlok, Angular vagy szó semmit, amely az alkalmazás.

A Service Fabric integrálva van a CI/CD eszközök például [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html), és [Polip telepítése](https://octopus.com/) és bármely egyéb népszerű CI/CD eszközzel együtt is használható.

Alkalmazás-életciklus kezelésével kapcsolatos további információkért olvassa el a [alkalmazás életciklusa](service-fabric-application-lifecycle.md). További információ a kód telepítésére, lásd: [központi telepítése egy Vendég végrehajtható](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>Főbb képességek
A Service Fabric használatával a következő műveletek végezhetők el:

* Telepítse a Azure-bA vagy nulla kódmódosításokat Windows vagy Linux rendszerű futtató helyszíni adatközpontokkal. Egyszeri megírás, és ezután telepíti a Service Fabric-fürt bármely részén.
* Méretezhető alkalmazások fejlesztéséhez, amely épülnek mikroszolgáltatások létrehozására az a Service Fabric programozási modellek, tárolók vagy bármely kód használatával.
* Az állapotmentes és állapotalapú nagymértékben megbízható mikroszolgáltatások fejlesztéséhez. Állapot-nyilvántartó mikroszolgáltatások használatával egyszerűbbé teszik az alkalmazás tervét. 
* Az új Reliable Actors programozási modell segítségével hozzon létre felhőobjektumok önkiszolgáló található kód és állapotát.
* Telepítse és levezényelni a Windows-tárolók és a Linux-tárolók tartalmazó tárolók. A Service Fabric data-rendszereket, állapotalapú, tároló orchestrator.
* Alkalmazások telepítése a több száz vagy ezer alkalmazások vagy a tárolókat gépenként nagy sűrűségű másodpercben.
* Különböző verzióinak egymás melletti ugyanazt az alkalmazást telepíteni, és egymástól függetlenül minden alkalmazás frissítése.
* Az alkalmazások leállása, megszakítása és nem törhető frissítések beleértve nélkül életciklusának kezelését.
* Horizontális felskálázás vagy méretezni a fürtben található csomópontok számát. Csomópontok méretezni, mivel az alkalmazások automatikus méretezése.
* Figyelése és diagnosztizálása a futó alkalmazások állapotát, és az automatikus javítás végrehajtása vonatkozó házirendek beállítása.
* Tekintse meg az erőforrás-elosztó alkalmazások újraelosztása levezényelni a fürtön. A Service Fabric helyreállít a hibák, és optimalizálja a terhelés elérhető erőforrások alapján terjesztési.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Következő lépések
* További információ:
  * [Miért egy mikroszolgáltatások közelítik meg az alkalmazások?](service-fabric-overview-microservices.md)
  * [Terminológia áttekintése](service-fabric-technical-overview.md)
* Beállítása a [Windows fejlesztési környezet](service-fabric-get-started.md)  
* Beállítása a [Linux fejlesztési környezet](service-fabric-get-started-linux.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
