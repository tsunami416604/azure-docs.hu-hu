---
title: "Cloud Services és a Service Fabric közötti különbségek |} Microsoft Docs"
description: "Fogalmi áttekintése áttelepítéséhez a Service Fabric Felhőszolgáltatások alkalmazások."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4bb5d92cd46533b46b388d178990f230424b09dc
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Cloud Services és a Service Fabric közötti különbségekről áttelepítése előtt ismerje meg alkalmazások.
A Microsoft Azure Service Fabric a következő generációs alkalmazás felhőplatform jól skálázható, nagymértékben megbízható elosztott alkalmazásokhoz. Számos új szolgáltatást csomagolása, telepítése, frissítése és elosztott felhőalapú alkalmazások kezelése okozna. 

Ez egy bevezető útmutatóban áttelepítése a Service Fabric Felhőszolgáltatások alkalmazások. Architekturális elsősorban a összpontosít, és Felhőszolgáltatások és a Service Fabric közötti különbségek tervezése.

## <a name="applications-and-infrastructure"></a>Alkalmazások és infrastruktúra
A felhőalapú szolgáltatások és a Service Fabric alapvető különbség, a virtuális gépek, munkaterhelések és alkalmazások közötti kapcsolat. A munkaterhelés itt van definiálva, az adott feladat végrehajtására, vagy adjon meg egy szolgáltatás írt kódot.

* **Cloud Services csomag van, mint a virtuális gépek alkalmazások központi telepítésével kapcsolatos.** A Virtuálisgép-példány, például egy webes vagy feldolgozói szerepkör szorosan csatlakoztatott írt kódot. A munkaterhelés, a Cloud Services telepítéséhez van a munkaterhelés futtató egy vagy több Virtuálisgép-példányok telepítése. Az alkalmazások és a virtuális gépek nem elválasztási van, és ezért nem formális definíciója van egy alkalmazás. Az alkalmazás-re webes vagy feldolgozói szerepkör példányok belül a Felhőszolgáltatások központi telepítés akár egy teljes felhőalapú szolgáltatások telepítése. Ebben a példában az alkalmazás szerepkör példányok jelenik meg.

![A felhőalapú szolgáltatások alkalmazásokhoz és topológia][1]

* **A Service Fabric tárgya meglévő virtuális gépek vagy a Service Fabric futó Windows vagy Linux rendszerű gépek-alkalmazások telepítése.** A szolgáltatások írt teljesen leválasztott a az alkalmazás mögötti infrastruktúra, amelyeket el a Service Fabric-alkalmazás platform, az alkalmazás több környezetekben telepíthető. A munkaterhelés, a Service Fabric "szolgáltatás" nevezik, és egy vagy több szolgáltatás hivatalosan által meghatározott alkalmazást, a Service Fabric-alkalmazás platformon futó vannak csoportosítva. Service Fabric-fürt egyetlen több alkalmazást is telepíthető.

![Service Fabric-alkalmazások és a topológia][2]

Maga a Service Fabric egy platform alkalmazásréteg futó Windows vagy Linux,, mivel a Cloud Services rendszer csatolt munkaterhelések az Azure által kezelt virtuális gépek telepítéséhez.
A Service Fabric-alkalmazás modell számos előnnyel rendelkezik:

* Gyors üzembe helyezési idők. Virtuálisgép-példányok létrehozása időigényes lehet. A Service Fabric virtuális gépek csak telepítése után a fürtöt, amely üzemelteti a Service Fabric-alkalmazás platform. Ettől kezdve alkalmazáscsomagok telepíthetők a fürt nagyon gyorsan.
* Nagy sűrűségű üzemeltetéséhez. A felhőalapú szolgáltatások a feldolgozói szerepkör virtuális gépek egy munkaterhelés üzemelteti. A Service Fabric alkalmazások függetlenek a futtató virtuális gépeken, azaz nagyszámú virtuális gépek, amelyek csökkenthető a teljes költség szempontjából nagyobb üzemelő példányok esetében kevés alkalmazások telepítése.
* A Service Fabric platform futtatható bárhol, amely rendelkezik a Windows Server vagy Linux rendszerű gépek, Azure vagy a helyszíni. A platform absztrakciós réteget biztosít az alapul szolgáló infrastruktúra feletti, az alkalmazás futtatható a különböző környezetekben. 
* Elosztott alkalmazások kezelése. A Service Fabric a platform nem csak állomások elosztott alkalmazásokat, de az életciklus, függetlenül a üzemeltető virtuális gép vagy gépek életciklusának kezelésére segítségével is.

## <a name="application-architecture"></a>Alkalmazásarchitektúra
Cloud Services alkalmazás architektúra általában tartalmaz számos külső függőségei, például a Service Bus Azure Table és a Blob-tároló, SQL, Redis vagy mások állapotának és adatainak kérelem és a webkiszolgáló közötti kommunikáció kezelése és Cloud Services telepítések feldolgozói szerepköröket. A teljes Felhőszolgáltatások alkalmazás például nézhet ki:  

![A felhőalapú szolgáltatások architektúrája][9]

Service Fabric-alkalmazások is beállíthatja a teljes alkalmazás ugyanazon külső szolgáltatások használatával. Ebben a példában Felhőszolgáltatások architektúrát használ, a legegyszerűbb áttelepítési út Felhőszolgáltatások a Service Fabric-hoz csak a felhőalapú szolgáltatások telepítése cserélje le a Service Fabric-alkalmazás, megőrzi az általános architektúrája azonos. A webes és feldolgozói szerepkörök a Service Fabric állapotmentes szolgáltatások révén minimális kódmódosítással is használatát.

![Egyszerű áttelepítés után a Service Fabric-architektúra][10]

Ebben a szakaszban a rendszer továbbra is működni azonos az előzővel. A Service Fabric állapotalapú szolgáltatások, külső állapot tárolók kihasználni is internalized, állapotalapú alkalmazások és szolgáltatások részlegeinek. Ez az bonyolultabb mint egy egyszerű webes és feldolgozói szerepkörök történő áttelepítésének Service Fabric állapotmentes szolgáltatásokhoz, mivel az egyéni szolgáltatások, amelyek az alkalmazás funkciókat biztosítanak, ahogy a külső szolgáltatások előtt írása. Az ezzel a következő előnyöket nyújtja: 

* Külső függőségek eltávolítása 
* A központi telepítési, kezelési és frissítési modell egységesíti. 

Egy példa eredményül kapott architektúrájának ezeket a szolgáltatásokat internalizing nézhet ki:

![A teljes áttelepítés után a Service Fabric-architektúra][11]

## <a name="communication-and-workflow"></a>Kommunikációs és a munkafolyamat
A legtöbb Cloud Service-alkalmazások egynél több réteg áll. Hasonlóképpen a Service Fabric-alkalmazás több szolgáltatás (általában számos szolgáltatás) áll. Két közös kommunikációs modellt közvetlen kommunikációra és egy külső tartós tárolási keresztül.

### <a name="direct-communication"></a>Közvetlen kommunikációt
Az közvetlen kommunikációt rétegek közvetlenül a végpont által az egyes rétegek kommunikációt. Állapot nélküli környezetekben, például a Cloud Services, ez azt jelenti, hogy kiválasztása egy Virtuálisgép-szerepkör példánya vagy véletlenszerűen vagy ciklikus multiplexelés egyenleg betölteni, és közvetlenül csatlakozik a végpont.

![A közvetlen kommunikációt cloud Services csomag][5]

 Közvetlen kommunikációra a Service Fabric egy általános kommunikációs modellt. A kulcs közötti Service Fabric és a Cloud Services különbség, hogy a Cloud Services csatlakoztat egy virtuális Gépet, mivel a Service Fabric csatlakozzon a szolgáltatáshoz. Ez fontos különbség néhány lehetnek az okai:

* A Service Fabric szolgáltatások nincs kötve a virtuális gépeket üzemeltető szolgáltatások előfordulhat, hogy Navigálás a fürthöz, és tulajdonképpen várhatóan különböző okokból Navigálás: erőforrás terheléselosztás, feladatátvevő, alkalmazások és az infrastruktúra frissítések és elhelyezési vagy terheléselosztási vannak. Ez azt jelenti, hogy egy szolgáltatáspéldány cím bármikor módosíthatja. 
* Egy virtuális Gépet, a Service Fabric több szolgáltatásra, az egyedi végpontok száma tárolhatja.

Service Fabric szolgáltatás felderítési mechanizmust, a elnevezési szolgáltatást, amely segítségével hárítsa el a végponti címeket szolgáltatások nevű biztosít. 

![A Service Fabric közvetlen kommunikációt][6]

### <a name="queues"></a>Üzenetsorok
Egy általános kommunikációs mechanizmus állapot nélküli környezetekben, például Felhőszolgáltatások rétegek közötti, hogy egy külső tároló várólista tartósan tárolja a másik egy rétegtől munkahelyi feladatokhoz. Egy általános forgatókönyv egy webes réteghez, amely a feladatok küldése az Azure Queue vagy a Service Bus ahol feldolgozói szerepkör példányok created és a feladatok feldolgozásához.

![Cloud Services várólista kommunikáció][7]

Az azonos kommunikációs modellt a Service Fabric használható. Ez akkor lehet hasznos, az áttelepítés alatt a Service Fabric Felhőszolgáltatások egy meglévő alkalmazást. 

![A Service Fabric közvetlen kommunikációt][8]

## <a name="next-steps"></a>Következő lépések
A legegyszerűbb áttelepítési út Felhőszolgáltatások a Service Fabric-hoz csak a felhőalapú szolgáltatások telepítése cserélje le a Service Fabric-alkalmazás, megőrzi az alkalmazás általános architektúrája többé-kevésbé megegyezik. A következő cikk nyújt egy útmutató egy webes vagy feldolgozói szerepkör átalakítása a Service Fabric állapotmentes szolgáltatások segítségével.

* [Egyszerű áttelepítési: webes vagy feldolgozói szerepkör konvertálása a Service Fabric állapotmentes szolgáltatások](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
