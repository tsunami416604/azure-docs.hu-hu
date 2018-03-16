---
title: "Futtatási modell Azure Service Fabric |} Microsoft Docs"
description: "Replikák (vagy példányok) egy telepített szolgáltatás Fabric-szolgáltatás és folyamata közötti kapcsolatot ismerteti."
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 0206a9a486e3511834a23b3cc3f20f236a1cc261
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="service-fabric-hosting-model"></a>A Service Fabric üzemeltetési modell
Ez a cikk a Service Fabric által biztosított modellek futtató alkalmazás áttekintést nyújt, és közötti különbségeket mutatja a **megosztott folyamat** és **kizárólagos folyamat** modellek. Leírja, hogyan egy telepített alkalmazás néz ki egy Service Fabric-csomópont és a replikák (vagy példányok) szolgáltatás és folyamata közötti kapcsolat.

A folytatás előtt, győződjön meg arról, hogy jártas [Service Fabric alkalmazásmodell] [ a1] és megismerheti a különféle fogalmakat és a közöttük lévő kapcsolat. 

> [!NOTE]
> Ebben a cikkben az egyszerűség kivéve, ha explicit módon említett:
>
> - A word összes használatát *replika* mindkét egy replikát készít egy állapotalapú szolgáltatásból vagy állapotmentes szolgáltatások példányának hivatkozik.
> - *CodePackage* kezelt megfelelője *ServiceHost* folyamat, amely regisztrálja a *ServiceType* és az adott szolgáltatások állomások replikák *ServiceType*.
>

Ha szeretné megtudni a üzemeltetési modell, ossza meg velünk ismerteti egy példán keresztül. Ossza meg velünk tegyük fel például, van egy *ApplicationType* "MyAppType", amely rendelkezik egy *ServiceType* "MyServiceType".  "MyServiceType" biztosítja a *ServicePackage* "MyServicePackage", amely rendelkezik egy *CodePackage* "MyCodePackage". "MyCodePackage" regisztrálja *ServiceType* "MyServiceType" futtatásakor.

Tegyük fel, egy három csomópontos fürtre van, és létrehozhatunk egy *alkalmazás* **fabric: / App1** "MyAppType" típusú. Belül a *alkalmazás* **fabric: / App1** létrehozhatunk egy szolgáltatás **fabric: / App1/ServiceA** típusa "MyServiceType", amely két partíciókkal rendelkezik (tegyük fel például **P1**   &  **P2**) és partíciónként három replikáit. Az alábbi ábrán látható az alkalmazás, mert a nézet említi telepített egy csomóponton.

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-one]
</center>

A Service Fabric "MyServicePackage", amelynek hatására elindult "MyCodePackage", amely a replikák mindkét a partíciók az üzemeltető aktiválva.  Például **P1** & **P2**. A fürt összes csomópontjának fog lehet ugyanazt a nézetet, mert a fürtben található csomópontok számának egyenlőnek kell lennie partíciónként replikák száma választottuk. Hozzon létre egy másik szolgáltatás **fabric: / App1/ServiceB** az alkalmazásban **fabric: / App1**, egy partíciót tartalmaz (tegyük fel például **P3**) és három replikák partíciónként. Az alábbi ábrán a nézet a csomóponton:

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-two]
</center>

Ahogyan azt láthatja, a Service Fabric helyezni az új replikára partíció **P3** szolgáltatás **fabric: / App1/ServiceB** a meglévő "MyServicePackage"-aktiválásnál. Most már megadható, hogy hozzon létre egy másik *alkalmazás* **fabric: / App2** "MyAppType" típusú. Belül **fabric: / App2**, hozzon létre egy szolgáltatást, **fabric: / App2/ServiceA** két partíciókkal rendelkezik, amely (tegyük fel például **P4** & **P5**) és három replikák partíciónként. A következő ábrákon látható az új csomópont nézet:

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-three]
</center>

A Service Fabric aktiválja "MyServicePackage", amely elindítja a "MyCodePackage" másolatát egy új példányát. Replikák szolgáltatás mindkét partíciókból **fabric: / App2/ServiceA** (például **P4** & **P5**) kerülnek, az új példány "MyCodePackage".

## <a name="shared-process-model"></a>Megosztott folyamatmodell
Mi látott felett az alapértelmezett futtatási modell Service Fabric által biztosított és nevezzük **megosztott folyamat** modell. Ebben a modellben az egy adott *alkalmazás*, csak egy másolata egy adott *ServicePackage* aktiválódik egy *csomópont* (amely elindítja az összes a *CodePackages* benne tárolt) és az összes olyan szolgáltatás, a replikák egy adott *ServiceType* kerülnek a *CodePackage* , amely regisztrálja, amelyek *ServiceType*. Ez azt jelenti, minden replika az összes olyan szolgáltatás egy olyan csomópontján egy adott *ServiceType* osztani ugyanazt a folyamatot.

## <a name="exclusive-process-model"></a>Kizárólagos folyamatmodell
A Service Fabric által biztosított egyéb üzemeltetési modell **kizárólagos folyamat** modell. Ebben a modellben az egy adott *csomópont*, minden egyes replikának elhelyezéséhez, a Service Fabric egy új példányát aktiválja *ServicePackage* (amely elindítja az összes a *CodePackages* benne tárolt), és a replika az a *CodePackage* regisztrált a *ServiceType* a szolgáltatás, mely a replika tartozik. Ez azt jelenti minden egyes replikának él, a saját dedikált folyamat. 

Ez a modell verzióját a Service Fabric 5.6 verziójától kezdve alkalmazható. **Kizárólagos folyamat** modell választható ki a szolgáltatás létrehozása idején (használatával [PowerShell][p1], [REST][r1], vagy [FabricClient][c1]) megadásával **ServicePackageActivationMode** mint "ExclusiveProcess".

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

Ha egy alapértelmezett szolgáltatás a alkalmazásjegyzékben, választhat **kizárólagos folyamat** modellre megadásával **ServicePackageActivationMode** attribútumot a lent látható módon:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Az előző példát folytatva, lehetővé teszi, hogy hozzon létre egy másik szolgáltatást **fabric: / App1/ServiceC** alkalmazásban **fabric: / App1** két partíciókkal rendelkezik, amely (tegyük fel például **P6**  &  **S7**) és a partíciónként három replikák **ServicePackageActivationMode** "ExclusiveProcess" értékre. Alábbi ábrán a csomóponton új nézet:

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-four]
</center>

Ahogy látja, a Service Fabric aktiválása "MyServicePackage" két új példányát (egy partícióról minden egyes replikához **P6** & **S7**) és minden egyes replikának helyezett dedikált példányát *CodePackage*. Itt található, ami arról értesít egy másik művelet során **kizárólagos folyamat** modellt használja, az egy adott *alkalmazás*, először az egy adott *ServicePackage* lehet aktív egy *csomópont*. A fenti példában látható, hogy a "MyServicePackage" három másolatot aktívak a **fabric: / App1**. "MyServicePackage" aktív másolatait mindegyike rendelkezik egy **ServicePackageActivationId** tartozó, amely azonosítja a másolatot belül *alkalmazás* **fabric: / App1**.

Ha csak **megosztott folyamat** modellje egy *alkalmazás*, például **fabric: / App2** a fenti példában esetében csak egy aktív példányát *ServicePackage*  a egy *csomópont* és **ServicePackageActivationId** a aktiválásához *ServicePackage* "üres karakterlánc".

> [!NOTE]
>- **Megosztott folyamat** futtatási modell megfelel-e **ServicePackageActivationMode** egyenlő **SharedProcess**. Ez az az alapértelmezett futtatási modell és **ServicePackageActivationMode** nem kell megadni a szolgáltatás létrehozása idején.
>
>- **Kizárólagos folyamat** futtatási modell megfelel-e **ServicePackageActivationMode** beállítása **ExclusiveProcess** és explicit módon kell adni a szolgáltatás létrehozása idején. 
>
>- A szolgáltatás üzemeltetési modell lekérdezésével is ismert a [szolgáltatás leírása] [ p2] és megnézi a értékének **ServicePackageActivationMode**.
>
>

## <a name="working-with-deployed-service-package"></a>A telepített service csomag használata
Egy aktív másolatot készít egy *ServicePackage* csomóponton kezeli [telepített service-csomag][p3]. Korábban már említettük, amikor **kizárólagos folyamat** modellt használja a szolgáltatások létrehozásához egy adott *alkalmazás*, előfordulhat, hogy az azonos több telepített szervizcsomagok  *ServicePackage*. Egy telepített szervizcsomag vonatkozó műveletek során (például [telepített szervizcsomag állapotának reporting] [ p4] vagy [kódcsomag a telepített szolgáltatások újraindítása csomag][p5]), **ServicePackageActivationId** azonosításához egy adott központilag telepített service csomag kell megadni.

**ServicePackageActivationId** a telepített szolgáltatások listája lekérdezésével csomag szerezhetők [telepített szervizcsomagok] [ p3] csomóponton. Lekérdezésekor [szolgáltatástípusok telepített][p6], [replikák telepített][p7], és [kód csomagoktelepített] [ p8] a csomóponton a lekérdezés eredménye is tartalmaz a **ServicePackageActivationId** a szülő telepített service-csomag.

> [!NOTE]
>- A **megosztott folyamat** futtatási modell, a egy adott *csomópont*, az egy adott *alkalmazás*, csak egy példányát egy *ServicePackage* aktiválva van. Rendelkezik **ServicePackageActivationId** egyenlő *üres karakterlánc* és a telepített service-csomag kapcsolatos műveletek végrehajtása közben nem kell megadni. 
>
> - A **kizárólagos folyamat** futtatási modell, az egy adott *csomópont*, az egy adott *alkalmazás*, egy vagy több példányát egy *ServicePackage* lehet aktív. Az egyes aktiválások rendelkezik egy *nem üres* **ServicePackageActivationId**, megadott telepített szolgáltatások csomag kapcsolatos műveletek végrehajtása közben. 
>
> - Ha **ServicePackageActivationId** értéke nincs megadva az alapértelmezett érték a "üres karakterlánc". Ha a telepített szolgáltatások csomag, amely alatt aktiválása **megosztott folyamat** modell jelen, akkor a művelet végrehajtására kerül sor azt, egyébként pedig a művelet sikertelen lesz.
>
> - Egyszer, és a gyorsítótár nem lekérdezési **ServicePackageActivationId**, mivel a dinamikusan generált, és a különböző okokból módosíthatja. Igénylő művelet végrehajtása előtt **ServicePackageActivationId**, érdemes először kérdezze le a listája [telepített szervizcsomagok] [ p3] egy csomópontot, majd használja a **ServicePackageActivationId** a lekérdezés eredménye az eredeti művelet végrehajtásához.
>
>

## <a name="guest-executable-and-container-applications"></a>Vendég végrehajtható parancs és a tároló alkalmazások
Kezeli a Service Fabric [Vendég végrehajtható] [ a2] és [tároló] [ a3] állapotmentes szolgáltatásokhoz, amelyek önálló kérelmeket: nincs a nem a Service Fabric-futtatókörnyezet *ServiceHost* (a folyamatot vagy tároló). Mivel ezek a szolgáltatások önálló, a replikák másodpercenkénti száma *ServiceHost* esetén nem alkalmazható ezeket a szolgáltatásokat. A leggyakoribb konfigurációja ezekkel a szolgáltatásokkal használt, egypartíciós rendelkező [InstanceCount] [ c2] – 1 (azaz egy másolatot a fürt mindegyik csomópontján fut kódjának). 

Az alapértelmezett **ServicePackageActivationMode** ezeket a szolgáltatásokat a **SharedProcess**, ebben az esetben a Service Fabric csak akkor aktiválódik, egy példányát *ServicePackage* egy a *Csomópont* az egy adott *alkalmazás*.  Ez azt jelenti, hogy szolgáltatás kódot csak egy példányát futtatja egy *csomópont*. Ha azt szeretné, a szolgáltatás kódot futtathatnak több példányát egy *csomópont* több szolgáltatás létrehozásakor (*Service1* való *ServiceN*) a *ServiceType* (a megadott *ServiceManifest*), vagy ha a szolgáltatás több particionált, meg kell adnia **ServicePackageActivationMode** , **ExclusiveProcess** a szolgáltatás létrehozása idején.

## <a name="changing-hosting-model-of-an-existing-service"></a>Meglévő szolgáltatás üzemeltetési modelljének megváltoztatása
A meglévő szolgáltatás üzemeltetési modelljének megváltoztatása **megosztott folyamat** való **kizárólagos folyamat** és viszont keresztül frissítése vagy mechanizmus frissítése (vagy az alapértelmezett szolgáltatás az alkalmazásjegyzékben szereplő specifikáció) jelenleg nem támogatott. Ez a szolgáltatás támogatása későbbi verzióiban határozza meg.

## <a name="choosing-between-shared-process-and-exclusive-process-model"></a>Választás az megosztott folyamat és kizárólagos folyamatmodell
Mindkét e üzemeltetési modellek az informatikai szakemberek és hátrányának és értékelje ki, melyik az a követelményeinek legjobban megfelelő felhasználónak rendelkeznie kell rendelkeznie. **Megosztott folyamat** modell lehetővé teszi, hogy az operációs rendszer erőforrások jobb használatát, mert kevesebb folyamatok vannak indított, ugyanabban a folyamatban több replika megoszthatja a portokat, stb. Azonban a replikák egyik találatok, ahol kell állítsa le a szolgáltatásgazda hiba, ha azt egyéb ugyanabban a folyamatban lévő összes replika hatással lesz.

 **Kizárólagos folyamat** modell és a saját folyamat minden replika jobb elkülönítést is biztosít, és egy átirányítóban replika nem befolyásolja a többi. Az ismét hasznos olyan esetekben, ahol port megosztása nem támogatja a kommunikációs protokollhoz. Elősegíti a replika szintű erőforrás-irányítás alkalmazni lehessen. Azonban **kizárólagos folyamat** fog használni, azt a csomópont minden egyes replikához egy folyamatot indít több operációs rendszer erőforrást.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Kizárólagos folyamatmodell és alkalmazás modell kapcsolatos szempontok
Az ajánlott módszer a következő modellre: az alkalmazás a Service Fabric az adatok megőrzése egy *ServiceType* / *ServicePackage* és a legtöbb alkalmazás esetén ez a modell működik. 

Bizonyos esetekben használható szánt, a Service Fabric is lehetővé teszi, hogy egynél több *ServiceType* / *ServicePackage* (és egy *CodePackage* regisztrálhatja az egynél több  *ServiceType*). Az alábbiakban néhány a forgatókönyv, ahol ezek a beállítások akkor lehet hasznos:

- Az operációs rendszer erőforrás-használat származtatását kevesebb folyamatokat, és amelyek magasabb replika sűrűsége folyamatonként optimalizálni szeretné.
- A különböző ServiceTypes replikák kell néhány gyakori adatmegosztásra magas inicializálási vagy memória költsége.
- Szabad szolgáltatásajánlat rendelkezik, és el szeretné helyezni a korlátozása az erőforrás-használat a szolgáltatás összes replika tegyen ugyanazon folyamatban.

**Kizárólagos folyamat** futtatási modell nincs összefüggő több alkalmazás modellel *ServiceTypes* / *ServicePackage*. Ez azért, mert több *ServiceTypes* / *ServicePackage* készültek, magasabb szintű erőforrás-megosztás között replikákat, és lehetővé teszi, hogy magasabb replika sűrűség folyamatonként eléréséhez. Ez az ellentétesen mi **kizárólagos folyamat** modell elérésére szolgál.

Gondoljuk végig azt az esetet több *ServiceTypes* / *ServicePackage* másik *CodePackage* regisztrálása egyes *ServiceType*. Tegyük fel, van egy *ServicePackage* "MultiTypeServicePackge", amely két *CodePackages*:

- "MyCodePackageA", amely *ServiceType* "MyServiceTypeA".
- "MyCodePackageB", amely *ServiceType* "MyServiceTypeB".

Mostantól lehetővé teszi, hogy tegyük fel például, létrehozhatunk egy *alkalmazás* **fabric: / SpecialApp** és a belső **fabric: / SpecialApp** tudjuk létrehozni a következő két szolgáltatást együtt **kizárólagos folyamat** modell:

- Szolgáltatás **fabric: / SpecialApp/ServiceA** "MyServiceTypeA" típusú, és két partíció (tegyük fel például **P1** és **P2**) és partíciónként három replikáit.
- Szolgáltatás **fabric: / SpecialApp/ServiceB** "MyServiceTypeB" típusú, és két partíció (tegyük fel például **P3** és **P4**) és partíciónként három replikáit.

Egy adott csomópont mindkét a szolgáltatás két replika lesz. Mivel használtuk **kizárólagos folyamat** modell a szolgáltatások létrehozására a Service Fabric aktiválódik egy új példányt a "MyServicePackge" minden egyes replikához. Az egyes aktiválások "MultiTypeServicePackge" a "MyCodePackageA" és "MyCodePackageB" másolatát indul el. Azonban csak az egyik "MyCodePackageA" vagy "MyCodePackageB" fogja futtatni a replikát, amelynek "MultiTypeServicePackge" lett aktiválva. Alábbi ábrán látható, a csomópont megtekintése:

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-five]
</center>

A partíció replikája számára "MultiTypeServicePackge" aktiválásának **P1** szolgáltatás **fabric: / SpecialApp/ServiceA**, a replikát üzemeltető "MyCodePackageA" és "MyCodePackageB" megfelelően működik, és . Hasonlóképpen, a partíció replikája számára "MultiTypeServicePackge" aktiválásának **P3** szolgáltatás **fabric: / SpecialApp/ServiceB**, "MyCodePackageB" üzemelteti a replikát, és "MyCodePackageA" csak felfelé és fut, és így tovább. Emiatt több száma *CodePackages* (regisztrálása különböző *ServiceTypes*) / *ServicePackage*, magasabb lesz redundáns Erőforrás kihasználtsága. 
 
 Azonban ha szolgáltatások létrehozhatunk **fabric: / SpecialApp/ServiceA** és **fabric: / SpecialApp/ServiceB** a **megosztott folyamat** modellre, a Service Fabric csak az egyik aktiválódik a "MultiTypeServicePackge" példányát *alkalmazás* **fabric: / SpecialApp** (a korábban látott). "MyCodePackageA" fogja futtatni a szolgáltatást az összes replika **fabric: / SpecialApp/ServiceA** (vagy bármely pontosabban "MyServiceTypeA" típusú szolgáltatás). "MyCodePackageB" fogja futtatni a szolgáltatást az összes replika **fabric: / SpecialApp/ServiceB** (vagy bármely pontosabban "MyServiceTypeB" típusú szolgáltatás). Az alábbi ábrán a csomópont nézet ebben a beállításban: 

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-six]
</center>

Az előző példában azt hiszi, ha "MyCodePackageA" is "MyServiceTypeA" és "MyServiceTypeB" regisztrálja, és nem MyCodePackageB van, akkor nem lesznek nem redundáns *CodePackage* futtatása. Így megfelelő, azonban említett mint korábban. Az alkalmazás modell nem igazodik **kizárólagos folyamat** futtatási modell. A célja, hogy minden replika be a saját dedikált folyamat, ha regisztrálja, mindkettő *ServiceTypes* azonos *CodePackage* nincs rá szükség. Minden egyes üzembe *ServiceType* a saját *ServicePacakge* több természetes döntés.

## <a name="next-steps"></a>További lépések
[Egy alkalmazás becsomagolása] [ a4] és üzembe helyezésére.

[Központi telepítése és távolíthat el alkalmazásokat] [ a5] ismerteti, hogyan lehet alkalmazáspéldányok kezelése a PowerShell használatával.

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
