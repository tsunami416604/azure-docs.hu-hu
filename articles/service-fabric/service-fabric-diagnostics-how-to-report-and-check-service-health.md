---
title: Jelentés és állapotellenőrzése az Azure Service Fabric segítségével
description: Ismerje meg, hogyan küldhet állapotjelentéseket a szolgáltatáskódból, és hogyan ellenőrizheti a szolgáltatás állapotát az Azure Service Fabric által biztosított állapotfigyelő eszközök használatával.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2b7a9c44a84e3ce15eaec22c8f57bb48f79dae05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464638"
---
# <a name="report-and-check-service-health"></a>Szolgáltatásállapot jelentése és ellenőrzése
Ha a szolgáltatások problémákba ütköznek, az incidensekre és kimaradásokra való reagálás idotartama attól függ, hogy képes-e gyorsan észlelni a problémákat. Ha problémákat és hibákat jelent az Azure Service Fabric állapotkezelőa a szolgáltatáskódból, használhatja a Service Fabric által biztosított szabványos állapotfigyelő eszközöket az állapot ellenőrzéséhez.

A szolgáltatás állapotának jelentésére három féleképpen lehet jelentést tenni:

* Partition [Partition](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) vagy [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) objektumok használata.  
  A és `CodePackageActivationContext` `Partition` az objektumok segítségével jelentheti az aktuális környezet részét beálló elemek állapotát. Például a replika részeként futó kód csak az adott replika, a partíció, amelyhez tartozik, és az alkalmazás, amelynek része.
* A `FabricClient` címet használja.   
  Az állapot `FabricClient` jelentésére a szolgáltatáskód, ha a fürt nem [biztonságos,](service-fabric-cluster-security.md) vagy ha a szolgáltatás fut rendszergazdai jogosultságokkal. A legtöbb valós forgatókönyv nem használ nem biztonságos fürtöket, és nem biztosít rendszergazdai jogosultságokat. A `FabricClient`programmal a fürt részét vevő bármely entitás állapotát jelentheti. Ideális esetben azonban a szolgáltatáskód csak a saját állapotához kapcsolódó jelentéseket küldjön.
* Használja a REST API-kat a fürt, az alkalmazás, az üzembe helyezett alkalmazás, szolgáltatás, szolgáltatáscsomag, partíció, replika vagy csomópont szintjén. Ezzel egy tárolón belüli állapotjelentésre használható.

Ez a cikk végigvezeti egy példát, amely a szolgáltatáskódból származó állapotjelentést jelent. A példa azt is bemutatja, hogy a Service Fabric által biztosított eszközök hogyan használhatók az állapot ellenőrzésére. Ez a cikk célja, hogy egy gyors bevezetés a Service Fabric állapotfigyelési képességeit. További részletes információkért olvassa el az állapotról szóló részletes cikkek sorozatát, amelyek a cikk végén található hivatkozással kezdődnek.

## <a name="prerequisites"></a>Előfeltételek
A következő telepítésnek kell lennie:

* Visual Studio 2015 vagy Visual Studio 2019
* Szolgáltatás fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Helyi biztonságos fejlesztői fürt létrehozása
* Nyissa meg a Rendszergazdai jogosultságokkal rendelkező PowerShellt, és futtassa a következő parancsokat:

![Biztonságos fejlesztői fürt létrehozásának parancsai](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Alkalmazás üzembe helyezése és állapotának ellenőrzése
1. Nyissa meg a Visual Studio alkalmazást rendszergazdaként.
1. Hozzon létre egy projektet az **Állapotalapú szolgáltatás** sablon használatával.
   
    ![Service Fabric-alkalmazás létrehozása állapotalapú szolgáltatással](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Nyomja **le az F5** billentyűt az alkalmazás hibakeresési módban történő futtatásához. Az alkalmazás a helyi fürtre van telepítve.
1. Az alkalmazás futása után kattintson a jobb gombbal a Helyi fürtkezelő ikonra az értesítési területen, és válassza a **Helyi fürt kezelése parancsot** a helyi menüből a Service Fabric Explorer megnyitásához.
   
    ![A Szolgáltatásháló-kezelő megnyitása az értesítési területről](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Az alkalmazás állapotát a lemezképhez ugyanúgy kell megjeleníteni. Ebben az időben az alkalmazás kifogástalannak kell lennie, hibák nélkül.
   
    ![Kifogástalan alkalmazás a Service Fabric Intézőben](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Az állapot a PowerShell használatával is ellenőrizheti. Az ```Get-ServiceFabricApplicationHealth``` alkalmazás állapotának ellenőrzésére, és a ```Get-ServiceFabricServiceHealth``` szolgáltatás állapotának ellenőrzésére is használható. A PowerShell ben az azonos alkalmazás állapotjelentése ezen a lemezképen található.
   
    ![Kifogástalan alkalmazás a PowerShellben](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Egyéni állapotesemények hozzáadása a szolgáltatáskódhoz
A Service Fabric projektsablonok a Visual Studio tartalmaznak mintakódot. A következő lépések bemutatják, hogyan jelentheti az egyéni állapoteseményeket a szolgáltatáskódból. Ezek a jelentések automatikusan megjelennek a Service Fabric által biztosított állapotfigyelés szabványos eszközeiben, például a Service Fabric Explorer, az Azure Portal állapotnézetében és a PowerShellben.

1. Nyissa meg újra a korábban létrehozott alkalmazást a Visual Studióban, vagy hozzon létre egy új alkalmazást az **Állapotalapú Szolgáltatás** Visual Studio sablon használatával.
1. Nyissa meg a Stateful1.cs `myDictionary.TryGetValueAsync` fájlt, `RunAsync` és keresse meg a hívást a metódusban. Láthatja, hogy ez `result` a metódus egy olyan értéket ad vissza, amely tartalmazza a számláló aktuális értékét, mert az alkalmazás kulcslogikája a számláló futásának megtartása. Ha ez az alkalmazás egy valódi alkalmazás, és ha az eredmény hiánya jelentett hibát, akkor szeretné, hogy a zászló az esemény.
1. Ha egy állapotesemény jelentéséhez az eredmény hiánya hibát jelent, adja hozzá a következő lépéseket.
   
    a. Adja `System.Fabric.Health` hozzá a névteret a Stateful1.cs fájlhoz.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. A következő kód `myDictionary.TryGetValueAsync` hozzáadása a hívás után
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    A replika állapotát jelentjük, mert egy állapotalapú szolgáltatásból jelentik. A `HealthInformation` paraméter a jelentett állapotproblémával kapcsolatos információkat tárolja.
   
    Ha állapotmentes szolgáltatást hozott létre, használja a következő kódot
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Ha a szolgáltatás rendszergazdai jogosultságokkal fut, vagy ha a `FabricClient` fürt nem [biztonságos,](service-fabric-cluster-security.md)az állapot jelentésére is használhatja az alábbi lépésekben látható módon.  
   
    a. Hozza `FabricClient` létre a `var myDictionary` példányt a deklaráció után.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. A hívás után `myDictionary.TryGetValueAsync` adja hozzá a következő kódot.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Szimuláljuk ezt a hibát, és lássuk, hogy megjelenik-e az állapotfigyelő eszközökben. A hiba szimulálása érdekében fűzze ki a korábban hozzáadott állapotjelentési kód első sorát. Miután megjegyzést az első sorban, a kód fog kinézni a következő példa.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Ez a kód minden `RunAsync` alkalommal kiírja az állapotjelentést. A módosítás után nyomja le az **F5 billentyűt** az alkalmazás futtatásához.
1. Az alkalmazás futása után nyissa meg a Service Fabric Explorer alkalmazást az alkalmazás állapotának ellenőrzéséhez. Ezúttal a Service Fabric Explorer azt mutatja, hogy az alkalmazás nem megfelelő állapotú. Az alkalmazás nem megfelelő állapotúként jelenik meg, mert a korábban hozzáadott kódból jelentett hiba.
   
    ![Nem megfelelő állapotú alkalmazás a Service Fabric-kezelőben](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Ha kiválasztja az elsődleges replika a Service Fabric Explorer fanézetében, látni fogja, hogy **az állapot** hibát jelez. A Service Fabric Explorer a kódban szereplő `HealthInformation` paraméterhez hozzáadott állapotjelentés részleteit is megjeleníti. Ugyanazokat az állapotjelentéseket láthatja a PowerShellben és az Azure Portalon.
   
    ![Replika állapota a Service Fabric Intézőben](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ez a jelentés mindaddig az állapotkezelőben marad, amíg egy másik jelentés nem váltja fel, vagy amíg ez a replika törlésre nem kerül. Mivel az objektumban nem állítottuk be `TimeToLive` ezt az `HealthInformation` állapotjelentést, a jelentés soha nem jár le.

Azt javasoljuk, hogy az állapot kell jelenteni a legrészletesebb szinten, amely ebben az esetben a replika. Az állapotát is `Partition`jelentheti a rendszeren.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Az állapot `Application`jelentéséhez a `DeployedApplication`és a `DeployedServicePackage`, a használatát használja. `CodePackageActivationContext`

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>További lépések
* [Mély merülés a Service Fabric egészségén](service-fabric-health-introduction.md)
* [REST API a szolgáltatás állapotának jelentéséhez](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API az alkalmazások állapotának jelentéséhez](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

