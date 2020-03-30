---
title: Azure Service Fabric üzemeltetési modell
description: A telepített Service Fabric-szolgáltatás replikái (vagy példányai) és a service-host folyamat közötti kapcsolatot ismerteti.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 69c7edb08693937aad5a658e0b22b00cd2a81647
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282392"
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric üzemeltetési modell
Ez a cikk áttekintést nyújt az Azure Service Fabric által biztosított alkalmazásüzemeltetési modellekről, és ismerteti a **megosztott folyamat** és az **exkluzív folyamat** modellek közötti különbségeket. Ismerteti, hogyan néz ki egy üzembe helyezett alkalmazás egy Service Fabric-csomóponton, és a szolgáltatás replikák (vagy példányok) és a service-host folyamat közötti kapcsolat.

Mielőtt továbblépne, győződjön meg arról, hogy megértette a különböző fogalmakat és kapcsolatokat a [Model an application in Service Fabric][a1]című modellben. 

> [!NOTE]
> Ebben a cikkben, kivéve, ha kifejezetten említik, mint valami mást jelent:
>
> - *A replika* egy állapotalapú szolgáltatás replikáját és egy állapotmentes szolgáltatás egy példányát is nevezi.
> - *A codepackage* a *ServiceType*típust regisztráló *ServiceHost* folyamattal egyenértékűnek minősül, és az adott *ServiceType*szolgáltatásreplikáit üzemelteti.
>

Ahhoz, hogy megértsük a hosting modell, menjünk át egy példát. Tegyük fel, hogy van egy "MyAppType" *ApplicationType típusunk,* amelynek *ServiceType* "MyServiceType" típusa van. A "MyServiceType" a "MyServicePackage" *ServicePackage csomagot* biztosítja, amely "MyCodePackage" *codepackage csomaggal* rendelkezik. A "MyCodePackage" futáskor regisztrálja *a ServiceType* "MyServiceType" típust.

Tegyük fel, hogy van egy három csomópontos fürtünk, és létrehozunk egy "MyAppType" típusú *application* **alkalmazáshálót:/App1.** Az **alkalmazáshálón belül:/App1**, hozzunk létre egy szolgáltatás **háló:/App1/ServiceA** típusú "MyServiceType". Ez a szolgáltatás két partícióval rendelkezik (például **P1** és **P2**), és partíciónként három replikával rendelkezik. Az alábbi ábrán az alkalmazás nézete látható, mivel a csomóponton üzembe helyezéskor kerül telepítésre.


![Az üzembe helyezett alkalmazás csomópontnézetének diagramja][node-view-one]


A Service Fabric aktiválta a "MyServicePackage" szolgáltatást, amely elindította a "MyCodePackage" szolgáltatást, amely mindkét partíció replikáit üzemelteti. A fürt összes csomópontja azonos nézetben van, mert úgy választottuk, hogy a partíciónkénti replikák száma megegyezik a fürtcsomópontjainak számával. Hozzunk létre egy másik szolgáltatást, **fabric:/App1/ServiceB**, az alkalmazás **háló:/App1**. Ez a szolgáltatás egy partícióval (például **P3**) és partíciónként három replikával rendelkezik. A következő ábra a csomópont új nézetét mutatja be:


![Az üzembe helyezett alkalmazás csomópontnézetének diagramja][node-view-two]


A Service Fabric a "MyServicePackage" meglévő aktiválása során elhelyezte a Service Fabric **P3** **partíciójának új replikáját:/App1/ServiceB.** Nwo. hozzunk létre egy másik alkalmazás **szövet:/App2** típusú "MyAppType". Belső **háló:/App2**, hozzon létre egy **szolgáltatáshálót:/App2/ServiceA.** Ez a szolgáltatás két partícióval (**P4** és **P5**) és partíciónként három replikával rendelkezik. Az alábbi ábra az új csomópontnézetet mutatja:


![Az üzembe helyezett alkalmazás csomópontnézetének diagramja][node-view-three]


A Service Fabric aktiválja a "MyServicePackage" új példányát, amely elindítja a "MyCodePackage" új példányát. A **szolgáltatásháló mindkét partíciójáról:/App2/ServiceA** (**P4** és **P5**) replikák kerülnek ebbe az új "MyCodePackage" példányba.

## <a name="shared-process-model"></a>Megosztott folyamat modell
Az előző szakasz ismerteti az alapértelmezett üzemeltetési modell által biztosított Service Fabric, a továbbiakban a megosztott folyamat modell. Ebben a modellben egy adott alkalmazás esetében egy adott *ServicePackage-csomagnak* csak egy példánya van aktiválva egy csomóponton (amely elindítja a benne található *összes CodePackagecsomagot).* Egy adott *ServiceType* összes szolgáltatásának összes replikája a *ServiceType*-ot regisztráló *CodePackage* csomagba kerül. Más szóval egy adott *ServiceType* csomópontösszes szolgáltatásának összes replikája ugyanazt a folyamatot osztja meg.

## <a name="exclusive-process-model"></a>Kizárólagos folyamatmodell
A service fabric által biztosított másik üzemeltetési modell az exkluzív folyamat modell. Ebben a modellben egy adott csomóponton minden replika a saját dedikált folyamat él. A Service Fabric aktiválja a *ServicePackage* új példányát (amely elindítja a benne található *összes CodePackage-t).* A replikák a *CodePackage-be* kerülnek, amely regisztrálta annak a szolgáltatásnak a *ServiceType-ját,* amelyhez a replika tartozik. 

Ha a Service Fabric 5.6-os vagy újabb verzióját használja, kiválaszthatja az Exkluzív folyamat modellt a szolgáltatás létrehozásakor (a [PowerShell,][p1] [a REST][r1]vagy a [FabricClient][c1]használatával). Adja meg **a ServicePackageActivationMode** értéket "ExclusiveProcess" (ExclusiveProcess' (Kizárólagos folyamat)

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

Ha van egy alapértelmezett szolgáltatás az alkalmazás jegyzékfájljában, a **ServicePackageActivationMode** attribútum megadásával kiválaszthatja az Exkluzív folyamat modellt:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Most hozzunk létre egy másik szolgáltatást, **fabric:/App1/ServiceC**, az alkalmazás **háló:/App1**. Ez a szolgáltatás két partícióval rendelkezik (például **P6** és **P7**), és partíciónként három replikával rendelkezik. A **ServicePackageActivationMode** beállítását "ExclusiveProcess" beállításra állította. A következő ábra új nézetet mutat a csomóponton:


![Az üzembe helyezett alkalmazás csomópontnézetének diagramja][node-view-four]


Mint látható, a Service Fabric aktiválta a "MyServicePackage" két új példányát (egyet-egyet a **P6** és **P7**partíció minden replika esetében). A Service Fabric minden replikát a *CodePackage*külön példányába helyezett el. Az Exkluzív folyamat modell használatakor egy adott alkalmazáshoz egy adott *ServicePackage* több példánya lehet aktív egy csomóponton. Az előző példában a "MyServicePackage" három példánya aktív a **fabric:/App1**számára. A "MyServicePackage" minden egyes aktív példányához tartozik egy **ServicePackageActivationId.** Ez az azonosító azonosítja a másolatot az **alkalmazáshálón belül:/App1**.

Ha csak a megosztott folyamat modellt használja egy alkalmazáshoz, a *ServicePackage-nek* csak egy aktív példánya van egy csomóponton. A ServicePackage aktiválásához a *ServicePackage* **servicePackage szolgáltatásaktiváláshoz szükséges ServicePackageActivationId** üres karakterlánc. Ez a helyzet például a **fabric:/App2**.

> [!NOTE]
>- A megosztott folyamat üzemeltetési modellje a **ServicePackageActivationMode** egyenlő **SharedProcess**értékűnek felel meg. Ez az alapértelmezett üzemeltetési modell, és a **ServicePackageActivationMode-ot** nem kell megadni a szolgáltatás létrehozásakor.
>
>- Az Exkluzív folyamat üzemeltetési modell megfelel **ServicePackageActivationMode** egyenlő **ExclusiveProcess**. A beállítás használatához a szolgáltatás létrehozásakor explicit módon kell megadnia. 
>
>- Egy szolgáltatás üzemeltetési modelljének megtekintéséhez kérdezze le a [szolgáltatás leírását,][p2]és nézze meg a **ServicePackageActivationMode**értékét.
>
>

## <a name="work-with-a-deployed-service-package"></a>Telepített szolgáltatáscsomag kal üzemeltetése
A *szolgáltatáscsomag* aktív példányát egy csomóponton [telepített szolgáltatáscsomagnak nevezzük.][p3] Ha az Exkluzív folyamat modellt használja szolgáltatások létrehozásához, egy adott alkalmazáshoz előfordulhat, hogy több telepített szolgáltatáscsomag is létezik ugyanahhoz a *ServicePackage-hez.* Ha egy telepített szolgáltatáscsomagra vonatkozó műveleteket hajt végre, meg kell **adnia a ServicePackageActivationId azonosítót** egy adott telepített szolgáltatáscsomag azonosításához. Adja meg például az azonosítót, ha [egy telepített szolgáltatáscsomag állapotát jelenti,][p4] vagy [újraindítja egy telepített szolgáltatáscsomag kódcsomagját.][p5]

Az üzembe helyezett szolgáltatáscsomagok **ServicePackageActivationId** azonosítóját a csomóponton [telepített szolgáltatáscsomagok][p3] listájának lekérdezésével találhatja meg. Amikor az [üzembe helyezett szolgáltatástípusok,][p6] [telepített replikák][p7]és [telepített kódcsomagok][p8] lekérdezése egy csomóponton, a lekérdezés eredménye is tartalmazza a szülő telepített szolgáltatáscsomag **ServicePackageActivationId.**

> [!NOTE]
>- A megosztott folyamat üzemeltetési modell, egy adott csomóponton, egy adott alkalmazás, csak egy példányát *servicepackage* aktiválva van. A **ServicePackageActivationId** egy *üres karakterlánccal*egyenlő, és nem kell megadni a telepített szolgáltatáscsomaghoz kapcsolódó műveletek végrehajtása közben. 
>
> - Az Exkluzív folyamat üzemeltetési modell, egy adott csomóponton, egy adott alkalmazás, egy vagy több példányban egy *ServicePackage* lehet aktív. Minden aktiváláshoz *nem üres* **ServicePackageActivationId**azonosító van megadva, amely a telepített szolgáltatáscsomaggal kapcsolatos műveletek végrehajtása közben van megadva. 
>
> - Ha **a ServicePackageActivationId nincs** megadva, akkor az alapértelmezett karakterlánc *kiürítése*. Ha a megosztott folyamat modell alatt aktivált telepített szolgáltatáscsomag jelen van, a művelet rajta lesz végrehajtva. Ellenkező esetben a művelet sikertelen lesz.
>
> - Ne kérdezze le egyszer, és ne gyorsítótárazza a **ServicePackageActivationId**. Az azonosító dinamikusan jön létre, és különböző okok miatt változhat. A **ServicePackageActivationId szükséges**művelet végrehajtása előtt először le kell kérdeznie az [üzembe helyezett szolgáltatáscsomagok][p3] listáját egy csomóponton. Ezután használja a **ServicePackageActivationId** a lekérdezés eredménye, hogy végre az eredeti műveletet.
>
>

## <a name="guest-executable-and-container-applications"></a>Vendég végrehajtható és tárolóalkalmazások
A Service Fabric [a vendég végrehajtható][a2] és [tárolóalkalmazásokat][a3] állapotnélküli szolgáltatásként kezeli, amelyek önállóak. Nincs Service Fabric futásidejű *ServiceHost* (folyamat vagy tároló). Mivel ezek a szolgáltatások önállóak, a *ServiceHost* ServiceHost-onkénti replikák száma nem alkalmazható ezekre a szolgáltatásokra. A szolgáltatásokkal használt leggyakoribb konfiguráció az egypartíciós, [a InstanceCount][c2] egyenlő -1-nek (a fürt minden csomópontján futó szolgáltatáskód egy példánya). 

Ezekhez a szolgáltatásokhoz az alapértelmezett **ServicePackageActivationMode** a **SharedProcess**, amely esetben a Service Fabric csak a *ServicePackage* egy példányát aktiválja egy adott alkalmazás csomópontján.  Ez azt jelenti, hogy a szolgáltatáskódnak csak egy példánya fog csomópontot futtatni. Ha azt szeretné, hogy a szolgáltatáskód több példánya is egy csomóponton fusson, a service létrehozásakor adja meg a **ServicePackageActivationMode** értéket **ExclusiveProcess** néven. Ezt például akkor teheti meg, ha a *ServiceType (ServiceManifest* fájlban *ServiceManifest*megadott) több szolgáltatást (*Service1* *a ServiceN-hez*) hoz létre, vagy ha a szolgáltatás többparticionált. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Meglévő szolgáltatás üzemeltetési modelljének módosítása
Jelenleg nem módosíthatja egy meglévő szolgáltatás üzemeltetési modelljét a megosztott folyamatról a kizárólagos folyamatra (vagy fordítva).

## <a name="choose-between-the-hosting-models"></a>Válasszon a tárhelymodellek közül
Meg kell értékelnie, hogy melyik üzemeltetési modell felel meg leginkább az Ön igényeinek. A Megosztott folyamat modell jobban használja az operációs rendszer erőforrásait, mivel kevesebb folyamat születik, és ugyanazon a folyamatban több kópia is megoszthatja a portokat. Azonban ha az egyik replikák egy hiba, ahol le kell állítania a szolgáltatás gazdagép, hatással van az összes többi replikák ugyanabban a folyamatban.

 Az Exkluzív folyamat modell jobb elkülönítést biztosít, a saját folyamat minden kópiájával. Ha az egyik kópia hibát észlel, az nem érinti a többi replikát. Ez a modell olyan esetekben hasznos, amikor a kommunikációs protokoll nem támogatja a portmegosztást. Megkönnyíti az erőforrás-szabályozás replikák szintjén történő alkalmazását. Az Exkluzív folyamat azonban több operációsrendszer-erőforrást használ fel, mivel a csomópont minden egyes replikájához egy folyamatot hoz létre.

## <a name="exclusive-process-model-and-application-model-considerations"></a>A folyamatmodellre és az alkalmazásmodellre vonatkozó kizárólagos szempontok
A legtöbb alkalmazás esetében modellezheti az alkalmazást a Service Fabric ben, *ha servicepackage-enként*egy *ServiceType-ot* tart meg. 

Bizonyos esetekben a Service Fabric *szolgáltatáscsomagonként* egynél több *ServiceType típust* is engedélyez (és egy *CodePackage* egynél több *ServiceType-ot*regisztrálhat). Az alábbiakban néhány olyan forgatókönyvet, ahol ezek a konfigurációk hasznoslehet:

- Az erőforrás-kihasználtságot úgy szeretné optimalizálni, hogy kevesebb folyamatot hoz létre, és folyamatonként nagyobb replikasűrűséggel rendelkezik.
- A különböző *ServiceTypes-replikáknak* meg kell osztaniuk néhány olyan közös adatot, amely magas inicializálással vagy memóriaköltséggel rendelkezik.
- Ingyenes szolgáltatásajánlattal rendelkezik, és szeretné korlátozni az erőforrás-kihasználtságot azáltal, hogy a szolgáltatás összes replikáját ugyanabba a folyamatba helyezi.

Az exkluzív folyamat üzemeltetési modellje nem koherens egy olyan alkalmazásmodellel, amelynek servicepackage-enként több *ServiceTypes-e* *van.* Ennek az az oka, hogy *a ServicePackage-enként* több *ServiceTypes* úgy van kialakítva, hogy nagyobb erőforrás-megosztást érjen el a replikák között, és folyamatonként nagyobb replikasűrűséget tesz lehetővé. Az Exclusive Process modell takarásban van.

Fontolja meg több *ServiceTypes* *egy ServicePackage*, egy másik *CodePackage* regisztráló minden *ServiceType.* Tegyük fel, hogy van egy *ServicePackage* "MultiTypeServicePackage", amely két *CodePackages:*

- "MyCodePackageA", amely regisztrálja *a ServiceType* "MyServiceTypeA" típust.
- "MyCodePackageB", amely regisztrálja *a ServiceType* "MyServiceTypeB" típust.

Most tegyük fel, hogy létrehozunk egy alkalmazást, **szövet:/SpecialApp**. Belső **szövet:/SpecialApp,** a következő két szolgáltatást hozunk létre az Exkluzív folyamat modellel:

- **Szolgáltatásfabric:/SpecialApp/ServiceA** típusú "MyServiceTypeA", két partícióval (például **P1** és **P2**), és partíciónként három replikával.
- **Szolgáltatásháló:/SpecialApp/ServiceB** típusú "MyServiceTypeB", két partícióval (**P3** és **P4**), és partíciónként három replikával.

Egy adott csomóponton mindkét szolgáltatás rendelkezik két replikák mindegyik. Mivel az Exkluzív folyamat modellt használtuk a szolgáltatások létrehozásához, a Service Fabric minden egyes replika "MyServicePackage" új példányát aktiválja. A "MultiTypeServicePackage" minden egyes aktiválása elindítja a "MyCodePackageA" és a "MyCodePackageB" egy példányát. Azonban csak egy "MyCodePackageA" vagy "MyCodePackageB" található a replika, amelyhez a "MultiTypeServicePackage" aktiválva volt. Az alábbi ábra a csomópontnézetet mutatja:


![Az üzembe helyezett alkalmazás csomópontnézetének diagramja][node-view-five]


A "MultiTypeServicePackage" aktiválása a **szolgáltatásfabric:/SpecialApp/ServiceA**partíció replikájára a "MyCodePackageA" a replikát üzemelteti. **P1** A "MyCodePackageB" fut. Hasonlóképpen a "MultiTypeServicePackage" aktiválása a **szolgáltatásfabric:/SpecialApp/ServiceB**partíció replikájának aktiválásakor a "MyCodePackageB" a replikát üzemelteti. **P3** A "MyCodePackageA" fut. Ezért minél nagyobb a *CodePackages* (regisztráció különböző *ServiceTypes)* *szolgáltatáscsomagonként,* annál nagyobb a redundáns erőforrás-használat. 
 
 Ha azonban a szolgáltatások **hálóját:/SpecialApp/ServiceA** és **fabric:/SpecialApp/ServiceB** a megosztott folyamat modellel hozzuk létre, a Service Fabric csak a "MultiTypeServicePackage" egy példányát aktiválja az **alkalmazáshálóhoz:/SpecialApp.** A "MyCodePackageA" a szolgáltatásháló összes replikáját **üzemelteti:/SpecialApp/ServiceA**. A "MyCodePackageB" a szolgáltatásháló összes replikáját **üzemelteti:/SpecialApp/ServiceB**. Az alábbi ábra a csomópontnézetet mutatja be ebben a beállításban: 


![Az üzembe helyezett alkalmazás csomópontnézetének diagramja][node-view-six]


Az előző példában azt gondolhatja, hogy ha a "MyCodePackageA" regisztrálja a "MyServiceTypeA" és a "MyServiceTypeB" egyaránt, és nincs "MyCodePackageB", akkor nincs redundáns *CodePackage* fut. Bár ez helyes, ez az alkalmazásmodell nem igazodik az Exclusive Process hosting modellhez. Ha a cél az, hogy minden replika a saját dedikált folyamat, nem kell regisztrálni a *két ServiceTypes* ugyanabból *CodePackage*. Ehelyett egyszerűen helyezze az egyes *ServiceType-ot* a saját *ServicePackage csomagba.*

## <a name="next-steps"></a>További lépések
[Csomagoljon be egy alkalmazást,][a4] és készítse elő az üzembe helyezésre.

[Alkalmazások telepítése és eltávolítása][a5]. Ez a cikk azt ismerteti, hogyan használhatja a PowerShell alkalmazáspéldányok kezelésére.

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
