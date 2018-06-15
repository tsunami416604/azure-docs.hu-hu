---
title: Az Azure Service Fabric üzemeltetési modell |} Microsoft Docs
description: Replikák (vagy példányok) egy telepített Service Fabric-szolgáltatás és folyamata közötti kapcsolatot ismerteti.
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: d56bb10041e3baffddf6fd4121a6e1f7ba8e0632
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206320"
---
# <a name="azure-service-fabric-hosting-model"></a>Az Azure Service Fabric üzemeltetési modell
Ez a cikk egy áttekintést nyújt az Azure Service Fabric által biztosított modellek futtató alkalmazás, és közötti különbségeket mutatja a **megosztott folyamat** és **kizárólagos folyamat** modellek. Leírja, hogyan egy telepített alkalmazás néz ki egy Service Fabric-csomópont, és a replikák (vagy példányok) szolgáltatás és folyamata közötti kapcsolat.

A folytatás előtt, lehet, hogy megismerte a különféle fogalmakat és a kapcsolatokat, tekintse meg a [egy alkalmazás a Service Fabric modell][a1]. 

> [!NOTE]
> A cikkben kivéve, ha explicit módon azt jelenti, egy másik:
>
> - *A replika* mindkét egy replikát készít egy állapotalapú szolgáltatás és állapot nélküli szolgáltatás egy példánya hivatkozik.
> - *CodePackage* kezelnek egyenértékűként egy *ServiceHost* folyamat, amely regisztrálja a *ServiceType*, és az adott szolgáltatások állomások replikák *ServiceType*.
>

Ha szeretné megtudni a üzemeltetési modell, bemutatjuk, egy példán keresztül. Tegyük fel, van egy *ApplicationType* "MyAppType", amely rendelkezik egy *ServiceType* "MyServiceType". "MyServiceType" biztosítja a *ServicePackage* "MyServicePackage", amely rendelkezik egy *CodePackage* "MyCodePackage". "MyCodePackage" regisztrálja *ServiceType* "MyServiceType" futtatásakor.

Tegyük fel, egy három csomópontos fürtre van, és létrehozhatunk egy *alkalmazás* **fabric: / App1** "MyAppType" típusú. Ebben az alkalmazásban **fabric: / App1**, azt a szolgáltatás létrehozása **fabric: / App1/ServiceA** "MyServiceType" típusú. Ez a szolgáltatás két partíciókkal rendelkezik (például **P1** és **P2**), és három replikák partíciónként. Az alábbi ábrán látható az alkalmazás, mert a nézet említi telepített egy csomóponton.


![Diagram nézet csomópont a központilag telepített alkalmazás][node-view-one]


A Service Fabric "MyServicePackage", amelynek hatására elindult "MyCodePackage", amely a replikák mindkét a partíciók az üzemeltető aktiválva. A fürt összes csomópontjának rendelkeznie ugyanazt a nézetet, mert a fürtben található csomópontok számának egyenlőnek kell lennie partíciónként replikák száma választottuk. Hozzon létre egy másik szolgáltatás **fabric: / App1/ServiceB**, az alkalmazás **fabric: / App1**. A szolgáltatás egy partíció rendelkezik (például **P3**), és három replikák partíciónként. Az alábbi ábrán a nézet a csomóponton:


![Diagram nézet csomópont a központilag telepített alkalmazás][node-view-two]


A Service Fabric helyezni az új replikára partíció **P3** szolgáltatás **fabric: / App1/ServiceB** a meglévő "MyServicePackage"-aktiválásnál. Most. Hozzon létre egy másik alkalmazás **fabric: / App2** "MyAppType" típusú. Belül **fabric: / App2**, hozzon létre szolgáltatást **fabric: / App2/ServiceA**. Ez a szolgáltatás két partíciói (**P4** és **P5**), és három replikák partíciónként. Az alábbi ábrán látható, az új csomópont nézet:


![Diagram nézet csomópont a központilag telepített alkalmazás][node-view-three]


A Service Fabric aktiválja "MyServicePackage", amely elindítja a "MyCodePackage" másolatát egy új példányát. Replikák szolgáltatás mindkét partíciókból **fabric: / App2/ServiceA** (**P4** és **P5**) kerülnek, az új példány "MyCodePackage".

## <a name="shared-process-model"></a>Megosztott folyamatmodell
Az előző szakaszban az alapértelmezett futtatási modell Service Fabric, a megosztott folyamatmodell néven által biztosított ismerteti. Ebben a modellben egy adott alkalmazáshoz, csak az egyik másolata egy adott *ServicePackage* aktiválva van a csomópont (amely elindítja az összes a *CodePackages* benne tárolt). Az összes olyan szolgáltatás, a replikákat egy adott *ServiceType* kerülnek a *CodePackage* , amely regisztrálja, amelyek *ServiceType*. Ez azt jelenti, minden replika az összes olyan szolgáltatás egy olyan csomópontján egy adott *ServiceType* osztani ugyanazt a folyamatot.

## <a name="exclusive-process-model"></a>Kizárólagos folyamatmodell
A Service Fabric által biztosított egyéb üzemeltetési modell kizárólagos folyamatmodell. Ebben a modellben adott csomóponton minden egyes replikának él, a saját dedikált folyamat. A Service Fabric aktiválja az új példányt a *ServicePackage* (amely elindítja az összes a *CodePackages* benne tárolt). Replikák helyezi a *CodePackage* regisztrált a *ServiceType* a szolgáltatás, amelyhez a replika tartozik. 

Ha a Service Fabric 5.6 verzióját használja, vagy később, válassza a kizárólagos folyamatmodell időpontjában szolgáltatás létrehozása (használatával [PowerShell][p1], [REST] [ r1], vagy [FabricClient][c1]). Adja meg **ServicePackageActivationMode** mint "ExclusiveProcess".

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Ha egy alapértelmezett szolgáltatás a alkalmazásjegyzékben, dönthet úgy, kizárólagos folyamatmodell megadásával a **ServicePackageActivationMode** attribútum:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Most hozzon létre egy másik szolgáltatás **fabric: / App1/ServiceC**, az alkalmazás **fabric: / App1**. Ez a szolgáltatás két partíciókkal rendelkezik (például **P6** és **S7**), és három replikák partíciónként. Beállíthatja **ServicePackageActivationMode** "ExclusiveProcess" számára. Az alábbi ábrán látható a csomóponton új nézet:


![Diagram nézet csomópont a központilag telepített alkalmazás][node-view-four]


Ahogy látja, a Service Fabric aktiválása "MyServicePackage" két új példányát (egy partícióról minden egyes replikához **P6** és **S7**). A Service Fabric minden egyes replikának helyezett dedikált példányát *CodePackage*. Egy adott alkalmazás használatakor a kizárólagos folyamatmodell, először az egy adott *ServicePackage* azon a csomóponton aktív lehet. Az előző példában három másolatot "MyServicePackage" aktívak **fabric: / App1**. "MyServicePackage" aktív másolatait mindegyike rendelkezik egy **ServicePackageActivationId** társítva. Ezt az Azonosítót azonosítja az alkalmazáson belül példányt **fabric: / App1**.

Az alkalmazás csak a megosztott folyamatmodell használata esetén csak egy aktív példányát van *ServicePackage* csomóponton. A **ServicePackageActivationId** a aktiválásához *ServicePackage* egy üres karakterlánc. Ez a helyzet, például a **fabric: / App2**.

> [!NOTE]
>- A megosztott üzemeltetési modell megfelel-e folyamat **ServicePackageActivationMode** egyenlő **SharedProcess**. Ez az az alapértelmezett futtatási modell, és **ServicePackageActivationMode** nem kell megadni a szolgáltatás létrehozása idején.
>
>- A kizárólagos folyamat üzemeltetési modell megfelel-e **ServicePackageActivationMode** egyenlő **ExclusiveProcess**. Ez a beállítás használatához a szolgáltatás létrehozása idején explicit módon kell megadnia. 
>
>- A szolgáltatás üzemeltetési modell megtekintéséhez kérdezze a [szolgáltatás leírását][p2], és keresse meg az értéket **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Egy telepített service-csomag használata
Egy aktív másolatot készít egy *ServicePackage* csomóponton nevezzük egy [telepített service-csomag][p3]. Egy adott alkalmazáshoz, a szolgáltatások létrehozásának kizárólagos folyamatmodell használatakor előfordulhat ugyanazon több telepített szervizcsomagok *ServicePackage*. Egy telepített szervizcsomag jellemző műveleteket hajt végre, ha meg kell adnia **ServicePackageActivationId** egy adott központilag telepített service-csomag azonosításához. Például adja meg az Azonosítót, ha Ön [telepített szervizcsomag állapotának reporting] [ p4] vagy [újraindítása a kódcsomag egy telepített service-csomag] [p5].

Azt is megtudhatja, a **ServicePackageActivationId** egy telepített service-csomag listájának lekérdezésével [telepített szervizcsomagok] [ p3] csomóponton. Ha kérdez le a a [szolgáltatástípusok telepített][p6], [replikák telepített][p7], és [kód csomagok telepítése ] [ p8] a csomóponton a lekérdezés eredménye is tartalmaz a **ServicePackageActivationId** a szülő telepített service-csomag.

> [!NOTE]
>- A megosztott üzemeltetési folyamatmodell, adott csomóponton, egy adott alkalmazáshoz, csak egy példányát a *ServicePackage* aktiválva van. Rendelkezik egy **ServicePackageActivationId** egyenlő *üres karakterlánc*, és a telepített service-csomag kapcsolódó műveletek végrehajtása közben nem kell megadni. 
>
> - A kizárólagos folyamat üzemeltető alapján egy adott csomópont, egy adott alkalmazáshoz, a modell egy vagy több példányát egy *ServicePackage* lehet aktív. Az egyes aktiválások rendelkezik egy *nem üres* **ServicePackageActivationId**megadva a telepített service-csomag kapcsolódó műveletek végrehajtása közben. 
>
> - Ha **ServicePackageActivationId** van megadva, alapértelmezés szerint a *üres karakterlánc*. Ha egy telepített szolgáltatást csomagot, amely alatt a megosztott folyamatmodell aktiválása megtörtént, a rendszer a művelet a végezhető. Ellenkező esetben a művelet sikertelen lesz.
>
> - Egyszer, és a gyorsítótár nem lekérdezni a **ServicePackageActivationId**. Az azonosító dinamikusan generált, és számos okból módosíthatja. Igénylő művelet végrehajtása előtt **ServicePackageActivationId**, érdemes először kérdezze le a listája [telepített szervizcsomagok] [ p3] csomóponton. Ezután a **ServicePackageActivationId** az eredeti művelet végrehajtásához a lekérdezés eredménye.
>
>

## <a name="guest-executable-and-container-applications"></a>Vendég végrehajtható parancs és a tároló alkalmazások
Kezeli a Service Fabric [Vendég végrehajtható] [ a2] és [tároló] [ a3] állapotmentes szolgáltatásokhoz, amelyek önálló kérelmeket. Nincs a nem a Service Fabric-futtatókörnyezet *ServiceHost* (a folyamatot vagy tároló). Ezek a szolgáltatások önálló, mert egyes replikák száma *ServiceHost* esetén nem alkalmazható ezeket a szolgáltatásokat. A leggyakoribb konfigurációja ezekkel a szolgáltatásokkal használt, egypartíciós, a [InstanceCount] [ c2] – 1 (egy példányát a szolgáltatáskód hibáit, a fürt mindegyik csomópontján fut). 

Az alapértelmezett **ServicePackageActivationMode** ezeket a szolgáltatásokat a **SharedProcess**, ebben az esetben a Service Fabric csak akkor aktiválódik, egy példányát *ServicePackage* csomóponton egy adott alkalmazáshoz.  Ez azt jelenti, hogy szolgáltatás kódot csak egy példányát futtatja egy csomópont. Ha azt szeretné, hogy több példányát egy csomópontján futtatni a szolgáltatáskód hibáit, adja meg **ServicePackageActivationMode** , **ExclusiveProcess** a szolgáltatás létrehozása idején. Például ehhez több szolgáltatások létrehozásakor (*Service1* való *ServiceN*) a *ServiceType* (a megadott *ServiceManifest*), vagy ha a szolgáltatás több particionált. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Módosítsa a meglévő szolgáltatási üzemeltetési modell
Jelenleg nem módosíthatja a meglévő szolgáltatási üzemeltetési modell megosztott folyamat kizárólagos folyamat (vagy fordítva).

## <a name="choose-between-the-hosting-models"></a>Válassza ki az üzemeltetési modellek között
Azt ki kell értékelnie legjobb üzemeltetési modellt a követelményeinek megfelelő. A megosztott folyamatmodell operációs rendszer erőforrást használ, mert kevesebb folyamatok indított vannak, és ugyanabban a folyamatban több replika is ugyanazt a portot. Azonban ha a replikák egyike hibát amikor kell a szolgáltatásgazda leállíthatja, az hatással van ugyanazt a folyamatot a többi replikához.

 A kizárólagos folyamatmodell el vannak különítve jobb, és minden replika-folyamatban. A replikák egyike hiba, ha más replikák nincs hatással. Ez a modell akkor hasznos olyan esetekben, ahol port megosztása nem támogatja a kommunikációs protokollhoz. Elősegíti a replika szintű erőforrás-irányítás alkalmazni lehessen. Azonban a kizárólagos folyamat több operációs rendszer-erőforrásokat használ fel, mivel azt a csomópont minden egyes replikához egy folyamatot indít.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Kizárólagos folyamatmodell és alkalmazás modell kapcsolatos szempontok
A legtöbb alkalmazás esetén egyik tartja az alkalmazás a Service Fabric is modell *ServiceType* / *ServicePackage*. 

Bizonyos esetekben a Service Fabric is lehetővé teszi, hogy egynél több *ServiceType* / *ServicePackage* (és egy *CodePackage* regisztrálhatja az egynél több  *ServiceType*). Az alábbiakban néhány a forgatókönyv, ahol ezek a beállítások akkor lehet hasznos:

- Erőforrás-használat származtatását kevesebb folyamatokat, és amelyek magasabb replika sűrűsége folyamatonként optimalizálni szeretné.
- Replikák különböző *ServiceTypes* kell néhány gyakori adatmegosztásra magas inicializálási vagy memória költsége.
- Egy szabad szolgáltatásajánlat rendelkezik, és el szeretné helyezni a korlátozása az erőforrás-használat a szolgáltatás összes replika tegyen ugyanazon folyamatban.

A kizárólagos folyamat futtatási modell nincs összefüggő több alkalmazás-modellel *ServiceTypes* / *ServicePackage*. Ez azért, mert több *ServiceTypes* / *ServicePackage* replikák közötti magasabb erőforrás eléréséhez, és a következő lehetővé teszi, hogy magasabb replika sűrűség folyamatonként. A kizárólagos folyamatmodell célja, hogy különböző eredmények elérése érdekében.

Gondoljuk végig azt az esetet több *ServiceTypes* / *ServicePackage*, egy másik *CodePackage* regisztrálása egyes *ServiceType*. Tegyük fel, van egy *ServicePackage* "MultiTypeServicePackge", amely két *CodePackages*:

- "MyCodePackageA", amely *ServiceType* "MyServiceTypeA".
- "MyCodePackageB", amely *ServiceType* "MyServiceTypeB".

Most tegyük fel, hogy hozzon létre egy alkalmazást, **fabric: / SpecialApp**. Belül **fabric: / SpecialApp**, a következő két szolgáltatás kizárólagos folyamatmodellt létrehozhatunk:

- Szolgáltatás **fabric: / SpecialApp/ServiceA** "MyServiceTypeA" típusú, és két partíció (például **P1** és **P2**), és három replikák partíciónként.
- Szolgáltatás **fabric: / SpecialApp/ServiceB** "MyServiceTypeB" típusú, és két partíció (**P3** és **P4**), és három replikák partíciónként.

Adott csomóponton a szolgáltatások is két replika. Kizárólagos folyamatmodell használatával hozzon létre a szolgáltatást, mert a Service Fabric aktiválja másolatát "MyServicePackage" minden egyes replikához. Az egyes aktiválások "MultiTypeServicePackge" a "MyCodePackageA" és "MyCodePackageB" másolatát elindul. Azonban csak az egyik "MyCodePackageA" vagy "MyCodePackageB" az replikát, amelynek "MultiTypeServicePackge" lett aktiválva. Az alábbi ábrán látható, a csomópont megtekintése:


![A telepített alkalmazás csomópont nézet diagramja][node-view-five]


A partíció replikája számára "MultiTypeServicePackge" aktiválásának **P1** szolgáltatás **fabric: / SpecialApp/ServiceA**, "MyCodePackageA" a replika üzemelteti. "MyCodePackageB" fut. Hasonlóképpen, a "MultiTypeServicePackge" a replika partíció aktiválásának **P3** szolgáltatás **fabric: / SpecialApp/ServiceB**, "MyCodePackageB" a replika üzemelteti. "MyCodePackageA" fut. Ezért minél nagyobb a száma *CodePackages* (regisztrálása különböző *ServiceTypes*) / *ServicePackage*, annál a redundáns Erőforrás kihasználtsága. 
 
 Azonban ha a szolgáltatások létrehozhatunk **fabric: / SpecialApp/ServiceA** és **fabric: / SpecialApp/ServiceB** megosztott folyamatmodellt, a Service Fabric csak egy példányban aktiválja " MultiTypeServicePackge "az alkalmazás **fabric: / SpecialApp**. "MyCodePackageA" a szolgáltatás összes replikát üzemeltető **fabric: / SpecialApp/ServiceA**. "MyCodePackageB" a szolgáltatás összes replikát üzemeltető **fabric: / SpecialApp/ServiceB**. Az alábbi ábrán a csomópont nézet ebben a beállításban: 


![A telepített alkalmazás csomópont nézet diagramja][node-view-six]


Az előző példában azt hiszi, hogy ha "MyCodePackageA" regisztrálása "MyServiceTypeA" és a "MyServiceTypeB", és nem MyCodePackageB van, nem redundáns van *CodePackage* futtatása. Bár ez helyes, a alkalmazásmodell topológia nem igazodik a futtatási modell kizárólagos folyamat. Ha az a célja, hogy minden replika be a saját dedikált folyamat, nem kell regisztrálnia mindkét *ServiceTypes* azonos *CodePackage*. Ehelyett egyszerűen helyezünk egyes *ServiceType* a saját *ServicePackage*.

## <a name="next-steps"></a>További lépések
[Egy alkalmazás becsomagolása] [ a4] és üzembe helyezésére.

[Központi telepítése és távolíthat el alkalmazásokat][a5]. Ez a cikk ismerteti, hogyan lehet alkalmazáspéldányok kezelése a PowerShell használatával.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage
