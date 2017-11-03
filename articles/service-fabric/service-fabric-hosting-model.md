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
ms.openlocfilehash: ecc9038cf895ddaeb06dd0e4e9852d5ef4a4513a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="service-fabric-hosting-model"></a>A Service Fabric üzemeltetési modell
Ez a cikk a Service Fabric által biztosított modellek futtató alkalmazás áttekintést nyújt, és közötti különbségeket mutatja a **megosztott folyamat** és **kizárólagos folyamat** modellek. Leírja, hogyan egy telepített alkalmazás néz ki egy Service Fabric-csomópont és a replikák (vagy példányok) szolgáltatás és folyamata közötti kapcsolat.

A folytatás előtt, győződjön meg arról, hogy jártas [Service Fabric alkalmazásmodell] [ a1] és megismerheti a különféle fogalmakat és a közöttük lévő kapcsolat. 

> [!NOTE]
> Ebben a cikkben az egyszerűség kivéve, ha explicit módon említett:
>
> - Minden használ, a word *replika* mindkét egy replikát készít egy állapotalapú szolgáltatásból vagy állapotmentes szolgáltatások példányának hivatkozik.
> - *CodePackage* kezelt megfelelője *ServiceHost* folyamat, amely regisztrálja a *ServiceType* és az adott szolgáltatások állomások replikák *ServiceType*.
>

Ha szeretné megtudni a üzemeltetési modell, ossza meg velünk ismerteti egy példán keresztül. Ossza meg velünk tegyük fel például, van egy *ApplicationType* "MyAppType", amely rendelkezik egy *ServiceType* "MyServiceType" által biztosított *ServicePackage* "MyServicePackage", amely rendelkezik egy *CodePackage* "MyCodePackage", amely *ServiceType* "MyServiceType" futtatásakor.

Tegyük fel, 3 csomópontból álló fürt tudunk és létrehozhatunk egy *alkalmazás* **fabric: / App1** "MyAppType" típusú. Ez belül *alkalmazás* **fabric: / App1** létrehozhatunk egy szolgáltatás **háló: / App1/ServiceA** "MyServiceType", amelynek 2 partíció típusú (tegyük fel például **P1** & **P2**) és partíciónként 3 replikák. Az alábbi ábrán látható az alkalmazás, mert a nézet említi telepített egy csomóponton.

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-one]
</center>

A Service Fabric aktiválása "MyServicePackage", "MyCodePackage", amely mindkét a partíciókból replikák azaz üzemeltető indítottak **P1** & **P2**. Vegye figyelembe, hogy az a fürt összes csomópontjának azonos nézet lehet, mert a fürtben található csomópontok száma egyenlő partíciónként replikák száma választottuk. Hozzon létre egy másik szolgáltatás **fabric: / App1/ServiceB** alkalmazásban **fabric: / App1** 1 partíciót tartalmaz (tegyük fel például **P3**) és 3 replikák partíciónként. Alábbi ábrán a nézet a csomóponton:

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-two]
</center>

Ahogyan azt láthatja, a Service Fabric helyezni az új replikára partíció **P3** szolgáltatás **fabric: / App1/ServiceB** a meglévő "MyServicePackage"-aktiválásnál. Most segítségével létrehozhat egy másik *alkalmazás* **fabric: / App2** "MyAppType" típusú, és belül **fabric: / App2** szolgáltatás létrehozása **háló: / App2/ServiceA** 2-partíciókkal rendelkezik, amely (tegyük fel például **P4** & **P5**) és partíciónként 3 replikák. Az új csomópont nézet a következő ábrákon látható:

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-three]
</center>

Jelenleg a Service Fabric aktiválva van a "MyServicePackage", "MyCodePackage" és a replikák új szolgáltatás mindkét partíciókból elinduló másolatát **fabric: / App2/ServiceA** (azaz **P4** & **P5**) kerülnek, az új példány "MyCodePackage".

## <a name="shared-process-model"></a>Megosztott folyamatmodell
Mi látott felett az alapértelmezett futtatási modell Service Fabric által biztosított és nevezzük **megosztott folyamat** modell. Ebben a modellben az egy adott *alkalmazás*, csak egy másolata egy adott *ServicePackage* aktiválódik egy *csomópont* (amely elindítja az összes a *CodePackages* benne tárolt) és az összes olyan szolgáltatás, a replikák egy adott *ServiceType* kerülnek a *CodePackage* , amely regisztrálja, amelyek *ServiceType*. Ez azt jelenti, minden replika az összes olyan szolgáltatás egy olyan csomópontján egy adott *ServiceType* osztani ugyanazt a folyamatot.

## <a name="exclusive-process-model"></a>Kizárólagos folyamatmodell
A Service Fabric által biztosított egyéb üzemeltetési modell **kizárólagos folyamat** modell. Ebben a modellben az egy adott *csomópont*, minden egyes replikának elhelyezéséhez, a Service Fabric egy új példányát aktiválja *ServicePackage* (amely elindítja az összes a *CodePackages* benne tárolt), és a replika az a *CodePackage* regisztrált a *ServiceType* a szolgáltatás, mely a replika tartozik. Ez azt jelenti minden egyes replikának él, a saját dedikált folyamat. 

Ez a modell verzióját a Service Fabric 5.6 verziójától kezdve alkalmazható. **Kizárólagos folyamat** modell választható ki a szolgáltatás létrehozása idején (használatával [PowerShell][p1], [REST] [ r1] vagy [FabricClient][c1]) megadásával **ServicePackageActivationMode** mint "ExclusiveProcess".

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
Folytatása a fenti példában, lehetővé teszi, hogy hozzon létre egy másik szolgáltatást **háló: / App1/ServiceC** alkalmazásban **fabric: / App1** 2 partíció tartalmaz (tegyük fel például **P6** & **S7**) és a partíciónként 3 replikák **ServicePackageActivationMode** "ExclusiveProcess" értékre. Alábbi ábrán a csomóponton új nézet:

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-four]
</center>

Ahogy látja, a Service Fabric aktiválása "MyServicePackage" két új példányát (egy partícióról minden egyes replikához **P6** & **S7**) és minden egyes replikának helyezett dedikált példányát *CodePackage*. Itt található, ami arról értesít egy másik művelet során **kizárólagos folyamat** modellt használja, az egy adott *alkalmazás*, először az egy adott *ServicePackage* lehet aktív egy *csomópont*. A fenti példában látható, hogy a "MyServicePackage" három másolatot aktívak a **fabric: / App1**. "MyServicePackage" aktív másolatait mindegyike rendelkezik egy **ServicePackageActivationId** társítva belül példányt azonosítja *alkalmazás* **fabric: / App1**.

Ha csak **megosztott folyamat** modellje egy *alkalmazás*, például **fabric: / App2** a fenti példában esetében csak egy aktív példányát *ServicePackage*  a egy *csomópont* és **ServicePackageActivationId** a aktiválásához *ServicePackage* "üres karakterlánc".

> [!NOTE]
>- **Megosztott folyamat** futtatási modell megfelel-e **ServicePackageActivationMode** egyenlő **SharedProcess**. Ez az az alapértelmezett futtatási modell és **ServicePackageActivationMode** nem kell megadni a szolgáltatás létrehozása idején.
>
>- **Kizárólagos folyamat** futtatási modell megfelel-e **ServicePackageActivationMode** egyenlő **ExclusiveProcess** és explicit módon kell megadni a szolgáltatás létrehozása idején. 
>
>- A szolgáltatás üzemeltetési modell lekérdezésével is ismert a [szolgáltatás leírása] [ p2] és megnézi a értékének **ServicePackageActivationMode**.
>
>

## <a name="working-with-deployed-service-package"></a>A telepített service csomag használata
Egy aktív másolatot készít egy *ServicePackage* csomóponton kezeli [telepített service-csomag][p3]. Korábban már említettük, amikor **kizárólagos folyamat** modellt használja a szolgáltatások létrehozásához egy adott *alkalmazás*, előfordulhat, hogy az azonos több telepített szervizcsomagok *ServicePackage*. Például a telepített szervizcsomag vonatkozó műveletek során [telepített szervizcsomag állapotának reporting] [ p4] vagy [kódcsomag egy telepített service-csomag újraindítása] [ p5] stb., **ServicePackageActivationId** azonosításához egy adott központilag telepített service csomag kell megadni.

 **ServicePackageActivationId** a telepített szolgáltatások listája lekérdezésével csomag szerezhetők [telepített szervizcsomagok] [ p3] csomóponton. Lekérdezésekor [szolgáltatástípusok telepített][p6], [replikák telepített] [ p7] és [kód csomagok telepített] [ p8] a csomóponton a lekérdezés eredménye is tartalmaz a **ServicePackageActivationId** szülő telepített service-csomag.

> [!NOTE]
>- A **megosztott folyamat** futtatási modell, a egy adott *csomópont*, az egy adott *alkalmazás*, csak egy példányát egy *ServicePackage* aktiválva van. Rendelkezik **ServicePackageActivationId** egyenlő *üres karakterlánc* és nem szükséges megadni teljesítő telepített szervizcsomag kapcsolatos műveletek közben. 
>
> - A **kizárólagos folyamat** futtatási modell, az egy adott *csomópont*, az egy adott *alkalmazás*, egy vagy több példányát egy *ServicePackage* lehet aktív. Az egyes aktiválások rendelkezik egy *nem üres* **ServicePackageActivationId** és meg kell adni a telepített végrehajtása közben szolgáltatás csomag kapcsolódó műveletek. 
>
> - Ha **ServicePackageActivationId** értéke nincs megadva az alapértelmezett érték a "üres karakterlánc". Ha a telepített szolgáltatások csomag, amely alatt aktiválása **megosztott folyamat** modell jelen, akkor a művelet végrehajtására kerül sor azt, egyébként pedig a művelet sikertelen lesz.
>
> - Nem ajánlott lekérdezése egyszer, és a gyorsítótár **ServicePackageActivationId** dinamikusan generált, és számos okból módosíthatja. Igénylő művelet végrehajtása előtt **ServicePackageActivationId**, érdemes először kérdezze le a listája [telepített szervizcsomagok] [ p3] egy csomópontot, majd használja a **ServicePackageActivationId** a lekérdezés eredménye az eredeti művelet végrehajtásához.
>
>

## <a name="guest-executable-and-container-applications"></a>Vendég végrehajtható parancs és a tároló alkalmazások
Kezeli a Service Fabric [Vendég végrehajtható] [ a2] és [tároló] [ a3] alkalmazások vannak statless szolgáltatásként nincs azaz nem a Service Fabric-futtatókörnyezet *ServiceHost* (a folyamatot vagy tároló). Mivel ezek a szolgáltatások önálló, a replikák másodpercenkénti száma *ServiceHost* esetén nem alkalmazható ezeket a szolgáltatásokat. A leggyakoribb konfigurációja ezekkel a szolgáltatásokkal használt, egypartíciós rendelkező [InstanceCount] [ c2] – 1 (azaz egy másolatot a fürt mindegyik csomópontján fut kódjának). 

Az alapértelmezett **ServicePackageActivationMode** ezeket a szolgáltatásokat a **SharedProcess** ebben az esetben a Service Fabric csak akkor aktiválódik, egy példányát *ServicePackage* a egy *csomópont* az egy adott *alkalmazás* ami azt jelenti, hogy a szolgáltatás kódot csak egy példányban fog futni egy *csomópont*. Ha azt szeretné, a szolgáltatás kódot futtathatnak több példányát egy *csomópont* több szolgáltatás létrehozásakor (*Service1* való *ServiceN*) a *ServiceType* (a megadott *ServiceManifest*), vagy ha a szolgáltatás több particionált, meg kell adnia **ServicePackageActivationMode** , **ExclusiveProcess** a szolgáltatás létrehozása idején.

## <a name="changing-hosting-model-of-an-existing-service"></a>Meglévő szolgáltatás üzemeltetési modelljének megváltoztatása
A meglévő szolgáltatás üzemeltetési modelljének megváltoztatása **megosztott folyamat** való **kizárólagos folyamat** és viszont keresztül frissítése vagy mechanizmus frissítése (vagy az alapértelmezett szolgáltatás az alkalmazásjegyzékben szereplő specifikáció) jelenleg nem támogatott. Ez a szolgáltatás támogatása későbbi verzióiban határozza meg.

## <a name="choosing-between-shared-process-and-exclusive-process-model"></a>Választás az megosztott folyamat és kizárólagos folyamatmodell
Mindkét e üzemeltetési modellek az informatikai szakemberek és hátrányának és értékelje ki, melyik az a követelményeinek legjobban megfelelő felhasználónak rendelkeznie kell rendelkeznie. **Megosztott folyamat** modell lehetővé teszi, hogy az operációs rendszer erőforrások jobb használatát, mert kevesebb folyamatok vannak indított, ugyanabban a folyamatban több replika megoszthatja a portokat, stb. Azonban a replikák egyik találatok, ahol kell állítsa le a szolgáltatásgazda hiba, ha azt egyéb ugyanabban a folyamatban lévő összes replika hatással lesz.

 **Kizárólagos folyamat** modell és a saját folyamat minden replika jobb elkülönítést is biztosít, és egy átirányítóban replika nem befolyásolja a többi. Az ismét hasznos olyan esetekben, ahol port megosztása nem támogatja a kommunikációs protokollhoz. Elősegíti a replika szintű erőforrás-irányítás alkalmazni lehessen. Másrészről **kizárólagos folyamat** fog használni, azt a csomópont minden egyes replikához egy folyamatot indít több operációs rendszer erőforrást.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Kizárólagos folyamatmodell és alkalmazás modell kapcsolatos szempontok
Az ajánlott módszer a következő modellre: az alkalmazás a Service Fabric az adatok megőrzése egy *ServiceType* / *ServicePackage* és a legtöbb alkalmazás esetén ez a modell működik. 

Bizonyos esetekben használható szánt, a Service Fabric is lehetővé teszi, hogy egynél több *ServiceType* / *ServicePackage* (és egy *CodePackage* regisztrálhatja az egynél több  *ServiceType*). Az alábbiakban néhány a forgatókönyv, ahol ezek a beállítások akkor lehet hasznos:

- Az operációs rendszer erőforrás-használat származtatását kevesebb folyamatokat, és amelyek magasabb replika sűrűsége folyamatonként optimalizálni szeretné.
- A különböző ServiceTypes replikák kell néhány gyakori adatmegosztásra magas inicializálási vagy memória költsége.
- Szabad szolgáltatásajánlat rendelkezik, és el szeretné helyezni a korlátozása az erőforrás-használat a szolgáltatás összes replika tegyen ugyanazon folyamatban.

**Kizárólagos folyamat** futtatási modell nincs összefüggő több alkalmazás modellel *ServiceTypes* / *ServicePackage*. Ez azért, mert több *ServiceTypes* / *ServicePackage* replikák közötti magasabb erőforrás elérésére szolgál, és lehetővé teszi, hogy magasabb replika sűrűség folyamatonként. Ez az ellentétesen mi **kizárólagos folyamat** modell elérésére szolgál.

Gondoljuk végig azt az esetet több *ServiceTypes* / *ServicePackage* másik *CodePackage* regisztrálása egyes *ServiceType*. Tegyük fel, van egy *ServicePackage* "MultiTypeServicePackge", amely két *CodePackages*:

- "MyCodePackageA", amely *ServiceType* "MyServiceTypeA".
- "MyCodePackageB", amely *ServiceType* "MyServiceTypeB".

Mostantól lehetővé teszi, hogy tegyük fel például, létrehozhatunk egy *alkalmazás* **fabric: / SpecialApp** és a belső **fabric: / SpecialApp** tudjuk létrehozni a következő két szolgáltatást együtt **kizárólagos folyamat** modell:

- Szolgáltatás **fabric: / SpecialApp/ServiceA** "MyServiceTypeA" típusú, és két partíció (tegyük fel például **P1** és **P2**) és 3 replikák partíciónként.
- Szolgáltatás **fabric: / SpecialApp/ServiceB** "MyServiceTypeB" típusú, és két partíció (tegyük fel például **P3** és **P4**) és 3 replikák partíciónként.

Egy adott csomópont mindkét a szolgáltatás két replika lesz. Mivel használtuk **kizárólagos folyamat** modell a szolgáltatások létrehozására a Service Fabric aktiválódik egy új példányt a "MyServicePackge" minden egyes replikához. Az egyes aktiválások "MultiTypeServicePackge" a "MyCodePackageA" és "MyCodePackageB" másolatát indul el. Azonban csak az egyik "MyCodePackageA" vagy "MyCodePackageB" fogja futtatni a replikát, amelynek "MultiTypeServicePackge" lett aktiválva. Alábbi ábrán látható, a csomópont megtekintése:

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-five]
</center>

 Ahogyan azt láthatja, a partíció replikája "MultiTypeServicePackge" aktiválásának **P1** szolgáltatás **fabric: / SpecialApp/ServiceA**, "MyCodePackageA" üzemelteti a replikát, és "MyCodePackageB" ugyanúgy működik és elérhető. Hasonlóképpen, a partíció replikája számára "MultiTypeServicePackge" aktiválásának **P3** szolgáltatás **fabric: / SpecialApp/ServiceB**, "MyCodePackageB" üzemelteti a replikát, és "MyCodePackageA" csak felfelé és fut, és így tovább. Emiatt több száma *CodePackages* (regisztrálása különböző *ServiceTypes*) / *ServicePackage*, magasabb lesz redundáns Erőforrás kihasználtsága. 
 
 Ha szolgáltatások létrehozhatunk egyrészről **fabric: / SpecialApp/ServiceA** és **háló: / SpecialApp/ServiceB** rendelkező **megosztott folyamat** modellre, a Service Fabric aktiválódik "MultiTypeServicePackge" csak egy példányát a *alkalmazás* **háló: / SpecialApp** (a korábban látott). "MyCodePackageA" összes replika szolgáltatás fogja tárolni **fabric: / SpecialApp/ServiceA** (vagy bármely pontosabban "MyServiceTypeA" típusú szolgáltatás) "MyCodePackageB" összes replika szolgáltatás fogja futtatni, és **fabric: / SpecialApp/ServiceB** (vagy bármely pontosabban "MyServiceTypeB" típusú szolgáltatás). Következő diagramon láthatók a csomópont nézetet, az ezt a beállítást: 

<center>
![Telepített alkalmazás csomópont ábrázolása][node-view-six]
</center>

A fenti példában, ha "MyCodePackageA" is "MyServiceTypeA" és "MyServiceTypeB" regisztrálja, és nem MyCodePackageB van, akkor nem lesznek nem redundáns érdemes *CodePackage* futtatása. Helyes, azonban amint azt korábban említettük, az alkalmazás modell topológia nem igazodik **kizárólagos folyamat** futtatási modell. Ha a cél az, hogy minden replika be a saját dedikált feldolgozni regisztrálja, mindkettő *ServiceTypes* azonos *CodePackage* nincs rá szükség, és minden egyes üzembe *ServiceType* a saját *ServicePacakge* több természetes döntés.

## <a name="next-steps"></a>Következő lépések
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
[a2]: service-fabric-deploy-existing-app.md
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
