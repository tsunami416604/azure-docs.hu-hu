---
title: Az Azure Service Fabric üzemeltetési modell |} A Microsoft Docs
description: Üzembe helyezett Service Fabric-szolgáltatás és a szolgáltatás gazdafolyamat replikák (vagy példányok) közötti kapcsolatot ismerteti.
services: service-fabric
documentationcenter: .net
author: harahma
manager: chackdan
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: d2d958a89bff40483e1cd473538f7d1a6971d266
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663281"
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric hosting model
Ez a cikk az Azure Service Fabric által biztosított modellek üzemeltető alkalmazás nyújt áttekintést, és közötti különbségeket ismerteti a **megosztott folyamatot** és **kizárólagos folyamat** modellek. Leírja, hogyan néz ki a központilag telepített alkalmazás egy Service Fabric-csomópont és a szolgáltatás és a szolgáltatás gazdafolyamat replikák (vagy példányok) közötti kapcsolat.

Mielőtt továbblépne, győződjön meg, hogy különböző kapcsolatos fogalmak megismeréséhez és kapcsolatok ismertetett [minta Service Fabric-alkalmazásokhoz][a1]. 

> [!NOTE]
> Ebben a cikkben kivéve, ha explicit módon említett, ami azt jelenti, más:
>
> - *Replika* egy állapotalapú szolgáltatás és a egy állapotmentes szolgáltatás egy példányának mindkét replika hivatkozik.
> - *CodePackage* kezelik egyenértékűként egy *ServiceHost* folyamat, amely regisztrál egy *ServiceType*, és -szolgáltatások, amelyek gazdagépek replikáját *ServiceType*.
>

Szeretné megtudni, a üzemeltetési modell, nézzük végig egy példa. Tegyük fel, hogy egy *ApplicationType* "MyAppType", amely rendelkezik egy *ServiceType* "MyServiceType". "MyServiceType" biztosítják a *ServicePackage* "MyServicePackage", amely rendelkezik egy *CodePackage* "MyCodePackage". "MyCodePackage" regisztrálja *ServiceType* "MyServiceType" futtatásakor.

Tegyük fel, és egy három csomópontos fürt van, és létrehozunk egy *alkalmazás* **fabric: / App1** "MyAppType" típusú. Ebben az alkalmazásban **fabric: / App1**, létrehozunk egy szolgáltatás **fabric: / App1/ServiceA** "MyServiceType" típusú. Ez a szolgáltatás két partícióval rendelkezik (például **P1** és **P2**), és három replika partíciónként. Az alábbi ábrán látható, mert ez az alkalmazás nézetét is említi üzembe helyezett egy csomóponton.


![Az üzembe helyezett alkalmazás csomópont nézet diagramja][node-view-one]


A Service Fabric aktiválva "MyServicePackage", amelynek hatására elindult a "MyCodePackage", amely a egyaránt a partíciók replikáit. A fürt összes csomópontja van ugyanabban a nézetben, mert a partíciónként a fürtben található csomópontok számának egyenlőnek kell lennie a replikák száma választottuk. Hozzon létre egy másik szolgáltatás, **fabric: / App1/ServiceB**, az alkalmazás **fabric: / App1**. Ez a szolgáltatás egy partícióval rendelkezik (például **P3**), és három replika partíciónként. Az alábbi ábrán látható, a csomóponton az új nézet:


![Az üzembe helyezett alkalmazás csomópont nézet diagramja][node-view-two]


A Service Fabric helyezni az új replikára partíció **P3** szolgáltatás **fabric: / App1/ServiceB** a "MyServicePackage" meglévő aktiválását. Most. Hozzon létre egy másik alkalmazás **fabric: / App2** "MyAppType" típusú. Belül **fabric: / App2**, hozzon létre egy szolgáltatás **fabric: / App2/ServiceA**. Ez a szolgáltatás két partícióval rendelkezik (**P4** és **P5**), és három replika partíciónként. Az alábbi ábrán látható, az új csomópont nézet:


![Az üzembe helyezett alkalmazás csomópont nézet diagramja][node-view-three]


A Service Fabric aktiválja a "MyServicePackage',"MyCodePackage"új példányának indító egy új példányát. A szolgáltatás mindkét partíciójáról replikák **fabric: / App2/ServiceA** (**P4** és **P5**) kerülnek, az új példány "MyCodePackage".

## <a name="shared-process-model"></a>A megosztott folyamatmodell
Az előző szakasz futtatási modell a megosztott folyamatmodell néven, a Service Fabric által biztosított alapértelmezett. Ebben a modellben egy adott alkalmazáshoz, csak egy példányát egy adott *ServicePackage* egy csomóponton aktív (amely elindítja az összes a *CodePackages* benne tárolt). Az összes szolgáltatások minden replika egy adott *ServiceType* kerülnek a *CodePackage* , amely regisztrálja, amelyek *ServiceType*. Más szóval minden replika összes szolgáltatás egy olyan csomópontján, egy adott *ServiceType* osztani ugyanazt a folyamatot.

## <a name="exclusive-process-model"></a>Kizárólagos folyamatmodell
A Service Fabric által biztosított egyéb üzemeltetési modell a kizárólagos folyamatmodell. Ebben a modellben egy adott csomóponton minden egyes replikának helyén a saját dedikált folyamat során. A Service Fabric aktiválja az új példányának *ServicePackage* (amely elindítja az összes a *CodePackages* benne tárolt). Replikák kerülnek a *CodePackage* regisztrált a *ServiceType* a szolgáltatás, amelyhez a replika tartozik. 

Ha a Service Fabric 5.6-os verzióját használja, vagy később választhat a kizárólagos folyamatmodell időben létrehoz egy szolgáltatást (használatával [PowerShell][p1], [REST] [ r1], vagy [FabricClient][c1]). Adja meg **ServicePackageActivationMode** mint "ExclusiveProcess".

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

Ha egy alapértelmezett szolgáltatás az alkalmazásjegyzékben, a kizárólagos folyamatmodell megadásával kiválaszthatja a **ServicePackageActivationMode** attribútum:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Most hozzunk létre egy másik szolgáltatás, **fabric: / App1/ServiceC**, az alkalmazás **fabric: / App1**. Ez a szolgáltatás két partícióval rendelkezik (például **P6** és **S7**), és három replika partíciónként. Beállított **ServicePackageActivationMode** a "ExclusiveProcess". Az alábbi ábrán látható, a csomópont új nézet:


![Az üzembe helyezett alkalmazás csomópont nézet diagramja][node-view-four]


Amint láthatja, a Service Fabric aktiválva "MyServicePackage" új két példányban (egy partícióról minden egyes replikának a **P6** és **S7**). A Service Fabric minden egyes replikának helyezett saját dedikált példányát *CodePackage*. A kizárólagos folyamatmodell egy adott alkalmazás használatakor először az egy adott *ServicePackage* lehet egy csomóponton aktív. Az előző példában a "MyServicePackage" három másolata aktívak az **fabric: / App1**. Ezeket "MyServicePackage" aktív példánya mindegyike rendelkezik egy **ServicePackageActivationId** társítva. Ezzel az Azonosítóval azonosítja az alkalmazáson belül a másolatot **fabric: / App1**.

Ha egy alkalmazás csak a megosztott folyamatot modellt használ, nincs-e csak egy aktív másolata, *ServicePackage* egy csomóponton. A **ServicePackageActivationId** ez aktiválásához *ServicePackage* üres karakterlánc. Ez a helyzet, ha például a **fabric: / App2**.

> [!NOTE]
>- Üzemeltetési modell felel meg a megosztott folyamatot **ServicePackageActivationMode** egyenlő **SharedProcess**. Ez a futtatási modell, az alapértelmezett és **ServicePackageActivationMode** a szolgáltatás létrehozása alkalmával nem kell megadni.
>
>- Üzemeltetési modell felel meg a kizárólagos folyamat **ServicePackageActivationMode** egyenlő **ExclusiveProcess**. Ez a beállítás használatához a szolgáltatás létrehozásának időpontjában explicit módon kell megadnia. 
>
>- A szolgáltatás üzemeltetési modell megtekintéséhez lekérdezheti a [szolgáltatás leírása][p2], és tekintse meg az értékét **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>A telepített service-csomag használata
Egy aktív másolata, egy *ServicePackage* a csomópont a neve egy [service-csomagban üzembe helyezett][p3]. A kizárólagos folyamatmodell szolgáltatások egy adott alkalmazás létrehozásának használatakor előfordulhat, a több telepített szervizcsomagok *ServicePackage*. Egy telepített szervizcsomag jellemző műveleteket hajt végre, ha meg kell adnia **ServicePackageActivationId** egy adott központilag telepített service-csomag azonosításához. Például adja meg az Azonosítót, ha Ön [egy üzembe helyezett service-csomag állapotát reporting] [ p4] vagy [a kódcsomag üzembe helyezett szolgáltatás újraindítása] [p5].

Talál a **ServicePackageActivationId** egy üzembe helyezett service-csomag listája lekérdezésével [service-csomagok üzembe helyezett] [ p3] egy csomóponton. Amikor kérdez le a a [üzembe helyezett szolgáltatástípusok][p6], [üzembe helyezett replikák][p7], és [kódcsomagok telepített ] [ p8] egy csomóponton, a lekérdezés eredménye is tartalmaz a **ServicePackageActivationId** a szülő telepített service-csomag.

> [!NOTE]
>- Egy adott alkalmazás egy adott csomóponton a megosztott folyamatot üzemeltetési modell alatt csak egy példányát egy *ServicePackage* aktiválva van. Rendelkezik egy **ServicePackageActivationId** egyenlő *üres karakterlánc*, és nem kell megadni a telepített csomag kapcsolódó műveletek végrehajtása közben. 
>
> - Az üzemeltető kizárólagos folyamat alatt egy adott csomópont egy adott alkalmazáshoz, a modell egy vagy több példányban egy *ServicePackage* lehet aktív. Az egyes aktiválások rendelkezik egy *nem üres* **ServicePackageActivationId**, a telepített csomag kapcsolódó műveletek végrehajtása során megadott. 
>
> - Ha **ServicePackageActivationId** van megadva, a rendszer alapértelmezés szerint *üres karakterlánc*. Ha egy telepített service-csomag, amely alatt a közös folyamatmodell aktiválva volt megtalálható, a művelet rajta történik. Ellenkező esetben a művelet sikertelen lesz.
>
> - Egyszer, és a gyorsítótár nem lekérdezni a **ServicePackageActivationId**. Az azonosító dinamikusan hozzuk létre, és módosíthatja a különböző okok miatt. Szüksége van egy művelet végrehajtása előtt **ServicePackageActivationId**, először le kell kérdezni listájának [service-csomagok üzembe helyezett] [ p3] egy csomóponton. Ezután a **ServicePackageActivationId** a lekérdezési eredmény az eredeti művelet végrehajtásához.
>
>

## <a name="guest-executable-and-container-applications"></a>Vendég végrehajtható fájl és tároló alkalmazások
Kezeli a Service Fabric [futtatható vendégalkalmazás] [ a2] és [tároló] [ a3] alkalmazások, például állapotmentes szolgáltatások, amelyek önálló. Nem Service Fabric-futtatókörnyezet, a hiba nem *ServiceHost* (a folyamat vagy tároló). Ezek a szolgáltatások önálló, mert kiszolgálónként replikák száma *ServiceHost* nem alkalmazható ezen szolgáltatások. Ezekkel a szolgáltatásokkal használt a leggyakrabban használt konfigurációs single-partition- [InstanceCount] [ c2] – 1 (egy példányát a fürt mindegyik csomópontján fut a szolgáltatás-kódot). 

Az alapértelmezett **ServicePackageActivationMode** ezen szolgáltatások **SharedProcess**, ebben az esetben a Service Fabric egy példánya csak aktiválja *ServicePackage* a csomópont egy adott alkalmazáshoz.  Ez azt jelenti, hogy csak egy példányát a webszolgáltatás kódjához fog futni egy csomópontot. Ha azt szeretné, hogy a kódban, hogy a csomóponton futó szolgáltatás több példányát, adja meg **ServicePackageActivationMode** , **ExclusiveProcess** a szolgáltatás létrehozásának időpontjában. Például, ehhez több szolgáltatás létrehozásakor (*Service1* való *ServiceN*), *ServiceType* (a megadott *ServiceManifest*), vagy ha a szolgáltatás több particionált. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Egy létező szolgáltatás, a üzemeltetési modell módosítása
Jelenleg nem módosíthatja egy létező szolgáltatás, a üzemeltetési modell a megosztott folyamat kizárólagos folyamatát (vagy fordítva).

## <a name="choose-between-the-hosting-models"></a>Válassza ki, a üzemeltetési modellek között
Ki kell értékelni melyik ajánlott üzemeltetési modell a legjobban a követelményeinek. A megosztott folyamatmodell operációs rendszer erőforrásokat használ hatékonyabban, mert kevesebb folyamatokat a rendszer létrehozta, és több replika ugyanabban a folyamatban is ugyanazt a portot. Azonban ha replikára tartalmaz egy hibát, leállíthatja a szolgáltatásgazda van szüksége, a következőkre hat ugyanabban a folyamatban lévő összes többi replika.

 A kizárólagos folyamatmodell el vannak különítve jobb, az összes replikára, külön folyamatban. Ha hiba van a replikára, más replikák nem érinti. Ez a modell hasznos olyan esetekben, ahol-port megosztása nem támogatja a kommunikációs protokollt. Ez megkönnyíti a alkalmazni az erőforrás-szabályozás replika szintjén teszi. Azonban a kizárólagos folyamat több erőforrást használ fel operációs rendszer, ahogy azt a csomópont minden egyes replikának egy folyamata indít.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Kizárólagos folyamatmodell és alkalmazás modell kapcsolatos szempontok
A legtöbb alkalmazás esetén modellezheti az alkalmazás a Service Fabric egy tartja *ServiceType* kiszolgálónként *ServicePackage*. 

Bizonyos esetekben a Service Fabric lehetővé teszi több *ServiceType* kiszolgálónként *ServicePackage* (és a egy *CodePackage* regisztrálhatja egynél több  *ServiceType*). Az alábbiakban a forgatókönyvek, ahol ezek a beállítások akkor lehet hasznos:

- Erőforrás-használat optimalizálása származtatását kevesebb folyamatokat, és a magasabb replika sűrűségű folyamatonként által szeretné.
- A különböző replikák *ServiceTypes* kell ahhoz, hogy bizonyos közös adatokhoz, amely egy nagy inicializálása, vagy a memória költsége.
- Egy ingyenes szolgáltatás, amely rendelkezik, és szeretné korlátozni az erőforrás-használat ugyanahhoz a folyamathoz a szolgáltatás összes replika írja.

A kizárólagos folyamat futtatási modell még nem következetes egy alkalmazás több kellene modellel *ServiceTypes* kiszolgálónként *ServicePackage*. Ennek oka, hogy több *ServiceTypes* kiszolgálónként *ServicePackage* tervezték, hogy magasabb szintű erőforrás-replika között megosztást érhet el, és lehetővé teszi nagyobb replika sűrűsége folyamatonként. A kizárólagos folyamatmodell célja különböző eredmények elérése érdekében.

Fontolja meg a kis-és több *ServiceTypes* kiszolgálónként *ServicePackage*, egy másik *CodePackage* regisztrálása egyes *ServiceType*. Tegyük fel, hogy egy *ServicePackage* "MultiTypeServicePackage", amely két *CodePackages*:

- "MyCodePackageA", amely regisztrálja *ServiceType* "MyServiceTypeA".
- "MyCodePackageB", amely regisztrálja *ServiceType* "MyServiceTypeB".

Most tegyük fel, hogy létrehozunk egy alkalmazást, **fabric: / SpecialApp**. Belül **fabric: / SpecialApp**, hozunk létre a következő két szolgáltatás kizárólagos folyamatmodellt:

- Szolgáltatás **fabric: / SpecialApp/ServiceA** típusa "MyServiceTypeA", a két partíció (például **P1** és **P2**), és három replika partíciónként.
- Szolgáltatás **fabric: / SpecialApp/ServiceB** típusa "MyServiceTypeB", a két partíció (**P3** és **P4**), és három replika partíciónként.

Az egy adott csomópont mindkét szolgáltatás rendelkezésre áll két replika. A kizárólagos folyamatmodell használtuk a szolgáltatások létrehozását, mert a Service Fabric aktiválja minden replika "MyServicePackage" új példányát. Minden egyes aktiválási "MultiTypeServicePackage", "MyCodePackageA" és "MyCodePackageB" másolatát elindul. Azonban csak az egyik "MyCodePackageA" vagy "MyCodePackageB" az replikát, amelynek "MultiTypeServicePackage" aktiválva lett. Az alábbi ábrán látható, a csomópont nézet:


![A csomópontnézet üzembe helyezett alkalmazás ábrája][node-view-five]


A partíció replikája számára "MultiTypeServicePackage" aktiválásának **P1** szolgáltatás **fabric: / SpecialApp/ServiceA**, "MyCodePackageA" üzemelteti a replikát. "MyCodePackageB" fut. Hasonlóképpen, a "MultiTypeServicePackage" a partíció replikája aktiválását **P3** szolgáltatás **fabric: / SpecialApp/ServiceB**, "MyCodePackageB" üzemelteti a replikát. "MyCodePackageA" fut. Ezért, annál nagyobb szám *CodePackages* (regisztrálása másik *ServiceTypes*) / *ServicePackage*, annál nagyobb a redundáns erőforrás-használat. 
 
 Azonban ha hozunk létre, a szolgáltatások **fabric: / SpecialApp/ServiceA** és **fabric: / SpecialApp/ServiceB** megosztott folyamatmodellt, Service Fabric aktiválja csak egy példányát: MultiTypeServicePackage "az alkalmazás **fabric: / SpecialApp**. "MyCodePackageA" üzemelteti a szolgáltatást az összes replika **fabric: / SpecialApp/ServiceA**. "MyCodePackageB" üzemelteti a szolgáltatást az összes replika **fabric: / SpecialApp/ServiceB**. Ezt a beállítást a csomópont nézet az alábbi ábrán látható: 


![A csomópontnézet üzembe helyezett alkalmazás ábrája][node-view-six]


Az előző példában úgy tűnhet, ha "MyCodePackageA" is "MyServiceTypeA" és "MyServiceTypeB", regisztrálja, és nincs MyCodePackageB van, akkor nem redundáns van *CodePackage* futtatása. Bár ez helyes, az alkalmazásmodell nem esik egybe a kizárólagos folyamat futtatási modell. Ha a cél a saját dedikált folyamat egyes replika helyezi, nem kell regisztrálnia, mind *ServiceTypes* azonos *CodePackage*. Ehelyett egyszerűen nyilvántartani egyes *ServiceType* a saját *ServicePackage*.

## <a name="next-steps"></a>További lépések
[Alkalmazás becsomagolása] [ a4] és felkészülés üzembe helyezéséhez.

[Üzembe helyezése és távolíthat el alkalmazásokat][a5]. Ez a cikk bemutatja, hogyan alkalmazáspéldányok kezelése a PowerShell használatával.

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

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
