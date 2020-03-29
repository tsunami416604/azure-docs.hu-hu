---
title: A felhőszolgáltatások és a Szolgáltatásháló közötti különbségek
description: Az alkalmazások felhőszolgáltatásokból a Service Fabricbe való áttelepítésének fogalmi áttekintése.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 283ad2c63bb59771dab7881522e737f773ab1705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463366"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Ismerje meg a cloud services és a Service Fabric közötti különbségeket az alkalmazások áttelepítése előtt.
A Microsoft Azure Service Fabric a következő generációs felhőalkalmazás-platform a nagy mértékben méretezhető, rendkívül megbízható elosztott alkalmazásokhoz. Számos új funkciót vezet be a csomagoláshoz, a telepítéshez, a frissítéshez és az elosztott felhőalkalmazások kezeléséhez. 

Ez egy bevezető útmutató az alkalmazások áttelepítéséhez a Felhőszolgáltatásokból a Service Fabricbe. Elsősorban a Felhőszolgáltatások és a Service Fabric közötti architekturális és tervezési különbségekre összpontosít.

## <a name="applications-and-infrastructure"></a>Alkalmazások és infrastruktúra
A Felhőszolgáltatások és a Service Fabric közötti alapvető különbség a virtuális gépek, a munkaterhelések és az alkalmazások közötti kapcsolat. A számítási feladatok itt a kód, amelyet egy adott feladat végrehajtásához vagy egy szolgáltatás biztosításához ír.

* **A Cloud Services az alkalmazások virtuális gépként történő üzembe helyezéséről szól.** A kódot írsz szorosan kapcsolódik egy virtuális gép példány, például egy webes vagy feldolgozói szerepkör. Számítási feladatok üzembe helyezése a Cloud Services-ben egy vagy több virtuálisgép-példányok, amelyek a számítási feladatok futtatásához. Az alkalmazások és a virtuális gépek nem különítik el, így nincs hivatalos definíciója az alkalmazásnak. Egy alkalmazás a felhőszolgáltatások központi telepítésén belüli webes vagy feldolgozói szerepkör-példányok készletének tekinthető, vagy egy teljes felhőszolgáltatások központi telepítésének. Ebben a példában egy alkalmazás szerepkörpéldányok készleteként jelenik meg.

![Felhőszolgáltatási alkalmazások és topológia][1]

* **A Service Fabric az alkalmazások üzembe helyezéséről szól a meglévő virtuális gépeken vagy a Service Fabric szolgáltatást futtató gépeken Windows vagy Linux rendszeren.** A szolgáltatások írása teljesen levannak választva az alapul szolgáló infrastruktúrától, amelya Service Fabric alkalmazásplatform absztrakt, így egy alkalmazás több környezetben is telepíthető. A Service Fabric számítási feladatok az úgynevezett "szolgáltatás", és egy vagy több szolgáltatás van csoportosítva egy hivatalosan meghatározott alkalmazás, amely a Service Fabric-alkalmazás platformon fut. Több alkalmazás is telepíthető egyetlen Service Fabric-fürtre.

![Service Fabric-alkalmazások és topológia][2]

A Service Fabric maga egy alkalmazásplatform-réteg, amely Windows vagy Linux rendszeren fut, míg a Cloud Services egy olyan rendszer, amely az Azure által felügyelt virtuális gépek üzembe helyezéséhez a kapcsolódó számítási feladatok.
A Service Fabric alkalmazásmodell számos előnnyel rendelkezik:

* Gyors telepítési idő. A virtuálisgép-példányok létrehozása időigényes lehet. A Service Fabric virtuális gépek csak egyszer üzembe helyezhetők egy fürt, amely a Service Fabric alkalmazás platformot. Ettől a ponttól kezdve az alkalmazáscsomagok nagyon gyorsan telepíthetők a fürtre.
* Nagy sűrűségű tárhely. A Cloud Services-ben a feldolgozói szerepkör virtuális gép e számítási feladatok at üzemeltet. A Service Fabric alkalmazások elkülönülnek a virtuális gépek, amelyek futtatják őket, ami azt jelenti, hogy telepíthet nagyszámú alkalmazást kis számú virtuális gépek, amely csökkentheti a nagyobb telepítések teljes költségét.
* A Service Fabric platform futtatható bárhol, amely Windows Server vagy Linux gépek, függetlenül attól, hogy az Azure-ban vagy a helyszínen. A platform egy absztrakciós réteget biztosít az alapul szolgáló infrastruktúrán, így az alkalmazás különböző környezetekben futtatható. 
* Elosztott alkalmazáskezelés. A Service Fabric egy olyan platform, amely nem csak az elosztott alkalmazásokat üzemelteti, hanem a virtuális gép vagy a gép életciklusátől függetlenül is segít az életciklusuk kezelésében.

## <a name="application-architecture"></a>Alkalmazásarchitektúra
A Cloud Services-alkalmazások architektúrája általában számos külső szolgáltatásfüggőséget tartalmaz, például a Service Bus, az Azure Table és a Blob Storage, az SQL, a Redis és mások az alkalmazások állapotának és adatainak, valamint a web és a web és a web és a web és a web és a web és a web közötti kommunikáció kezelésének kezeléséhez. Feldolgozói szerepkörök egy felhőszolgáltatások központi telepítésében. Egy teljes Cloud Services-alkalmazás a következő höz hasonló lehet:  

![Felhőszolgáltatások architektúrája][9]

A Service Fabric-alkalmazások is választhatnak, hogy ugyanazokat a külső szolgáltatásokat egy teljes alkalmazásban. Ebben a példában a Cloud Services architektúra, a legegyszerűbb áttelepítési útvonala a Cloud Services service Fabric csak a Cloud Services üzembe helyezését egy Service Fabric-alkalmazás, megtartva a teljes architektúra ugyanaz. A webes és a feldolgozói szerepkörök a Service Fabric állapotmentes szolgáltatások minimális kódmódosítással portolhatók.

![Service Fabric architektúra egyszerű áttelepítés után][10]

Ebben a szakaszban a rendszernek továbbra is ugyanúgy kell működnie, mint korábban. A Service Fabric állapotalapú funkcióinak kihasználva a külső állapottárolók adott esetben állapotalapú szolgáltatásokként internalizálhatók. Ez nagyobb szerepet játszik, mint a webes és feldolgozói szerepkörök egyszerű áttelepítése a Service Fabric állapotmentes szolgáltatásokba, mivel olyan egyéni szolgáltatások írását igényli, amelyek egyenértékű funkciókat biztosítanak az alkalmazásnak, mint a külső szolgáltatások korábban. Ennek előnyei a következők: 

* Külső függőségek eltávolítása 
* A telepítési, felügyeleti és frissítési modellek egyesítése. 

A szolgáltatások internalizálásának egy architektúrája a következőkre mutathatja be a következőket:

![Service Fabric architektúra teljes áttelepítés után][11]

## <a name="communication-and-workflow"></a>Kommunikáció és munkafolyamat
A legtöbb Felhőszolgáltatás-alkalmazás egynél több rétegből áll. Hasonlóképpen a Service Fabric-alkalmazás több szolgáltatásból (általában sok szolgáltatásból) áll. Két közös kommunikációs modell a közvetlen kommunikáció és a külső tartós tároló.

### <a name="direct-communication"></a>Közvetlen kommunikáció
A közvetlen kommunikáció, a szintek közvetlenül kommunikálhatnak az egyes rétegek által elérhetővé tett végpontokon keresztül. Állapotnélküli környezetekben, például a Cloud Services, ez azt jelenti, hogy véletlenszerűen vagy ciklikus multiplexelésként kiválasztja a virtuálisgép-szerepkör egy példányát a terhelés elosztása, és közvetlenül csatlakozik a végponthoz.

![A felhőszolgáltatások közvetlen kommunikációja][5]

 A közvetlen kommunikáció a Service Fabric közös kommunikációs modellje. A legfontosabb különbség a Service Fabric és a Cloud Services, hogy a Felhőszolgáltatások virtuális géphez csatlakozik, míg a Service Fabric egy szolgáltatáshoz csatlakozik. Ez egy fontos különbség, több okból is:

* A Service Fabric szolgáltatásai nem kötődnek az őket tároló virtuális gépekhez; a szolgáltatások a fürtben mozoghatnak, és valójában különböző okok miatt várhatóan mozogni fognak: erőforrás-kiegyenlítés, feladatátvétel, alkalmazás- és infrastruktúra-frissítések, valamint elhelyezési vagy terhelési korlátozások. Ez azt jelenti, hogy a szolgáltatáspéldány címe bármikor megváltozhat. 
* A Service Fabric virtuális gép e virtuális gép üzemeltethet több szolgáltatást, mindegyik egyedi végpontok.

A Service Fabric egy szolgáltatásfelderítési mechanizmust biztosít, amelyet elnevezési szolgáltatásnak neveznek, amely a szolgáltatások végpontcímeinek feloldására használható. 

![Service Fabric közvetlen kommunikáció][6]

### <a name="queues"></a>Üzenetsorok
Az állapotmentes környezetekben, például a Cloud Services-környezetekben lévő rétegek közötti közös kommunikációs mechanizmus egy külső tárolási várólista használata a munkafeladatok egyik rétegről a másikra történő tartós tárolására. Egy gyakori forgatókönyv egy webes réteg, amely feladatokat küld egy Azure-várólistába vagy szolgáltatásbuszba, ahol a feldolgozói szerepkör példányai dequeue és a feladatok feldolgozása.

![Felhőszolgáltatások várólista-kommunikációja][7]

Ugyanaz tasza that a Service Fabric is használható a Service Fabric. Ez akkor lehet hasznos, ha egy meglévő Cloud Services-alkalmazást áttelepít a Service Fabric. 

![Service Fabric közvetlen kommunikáció][8]

## <a name="parity"></a>Parity
[A Felhőszolgáltatások hasonló a Service Fabric-hez a felügyelet és a könnyű használat foka között, de most már egy örökölt szolgáltatás, és a Service Fabric új fejlesztéshez ajánlott;](https://docs.microsoft.com/azure/app-service/overview-compare) a következő egy API-összehasonlítás:


| **Felhőalapú szolgáltatás API-ja** | **Service Fabric API** | **Megjegyzések** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId vagy . NodeName (Csomópontneve) | Az azonosító a NodeName tulajdonsága |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | Szűrés a NodeName-re és az FD tulajdonság használata |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Szűrés a NodeName névre, és a Frissítés tulajdonság használata |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext vagy elnevezés (ResolveService) | CodePackageActivationContext, amelyet a FabricRuntime.GetActivationContext és a serviceInitializationParameters.CodePackageActivationContext alatt biztosított replikákon keresztül biztosít. Inicializálni |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Ha azt szeretnénk, hogy ugyanazt a fajta szűrés típus szerint lejuthat a csomóponttípusok listáját a fürtjegyzékkeresztül FabricClient.ClusterManager.GetClusterManifest és megragad a szerepkör/csomópont típusok onnan. |
| RoleEnvironment.GetIsAvailable | Csatlakozás-WindowsFabricCluster vagy hozzon létre egy FabricRuntime mutatott egy adott csomópont | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Szerepkör.GetInstances | FabricClient.QueryManager.GetNodeList vagy ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext vagy elnevezés (ResolveService) | * |

## <a name="next-steps"></a>Következő lépések
A legegyszerűbb áttelepítési útvonal a Cloud Services service Fabric csak a Cloud Services központi telepítését egy Service Fabric-alkalmazás, megtartva az alkalmazás általános architektúráját nagyjából azonos. A következő cikk egy útmutatót, amely segít átalakítani a webes vagy feldolgozói szerepkör egy Service Fabric állapotmentes szolgáltatás.

* [Egyszerű áttelepítés: webes vagy feldolgozói szerepkör átalakítása service fabric állapotmentes szolgáltatássá](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

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
