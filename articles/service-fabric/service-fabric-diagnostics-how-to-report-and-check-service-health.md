---
title: Jelentés, és ellenőrizze az állapotát az Azure Service Fabric |} Microsoft Docs
description: 'Útmutató: a rendszerállapot-jelentések küldése a szolgáltatás kódból és a szolgáltatás állapotának ellenőrzéséhez hajtsa végre az Azure Service Fabric biztosító állapota Hálózatfigyelő eszközök.'
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: ''
ms.assetid: 7c712c22-d333-44bc-b837-d0b3603d9da8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/2/2017
ms.author: dekapur
ms.openlocfilehash: 82ee3cbca40713d527f64ae4698cb9ce64a10215
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208420"
---
# <a name="report-and-check-service-health"></a>Szolgáltatásállapot jelentése és ellenőrzése
Ha a szolgáltatások problémája, válaszol, és oldja meg az incidensek és a leállások arra a képességére, a problémák gyorsan észlelése arra a képességére, függ. Ha készít jelentést az problémák és hibák az Azure Service Fabric-kezelő szolgáltatás programkódból, szabványos állapotfigyelési, amely a rendszerállapot állapotának biztosít a Service Fabric eszközök is használhatja.

Három módon, hogy az a szolgáltatás állapotát jelentheti:

* Használjon [partíció](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) vagy [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) objektumok.  
  Használhatja a `Partition` és `CodePackageActivationContext` objektumok jelentésének elemeit tartalmazza az aktuális környezet állapotáról. Például az kódot, amely futtatja a replikák is tud jelentéseket az egészségügyi csak az adott replika, a partíció tartozik, és az alkalmazás, amely egy része.
* Használjon `FabricClient`.   
  Használhat `FabricClient` jelentés állapotába. a szolgáltatás kódból, ha a fürt nem [biztonságos](service-fabric-cluster-security.md) , vagy ha a szolgáltatás fut-e rendszergazda jogosultságokkal. A legtöbb olyan valós forgatókönyv a nem biztonságos fürtök használata, vagy adjon meg rendszergazdai jogosultságokkal. A `FabricClient`, egészségügyi jelentheti a minden entitás, amely a fürt része. Ideális esetben azonban szolgáltatáskódot küldött e-csak a saját egészségügyi kapcsolatos jelentések.
* Használnia a REST API-k a fürt, alkalmazás, központilag telepített alkalmazás, szolgáltatás, szolgáltatáscsomagot, partíció, a replika vagy csomópont szintek. Ez az állapotát a tárolóban lévő jelentésének is használható.

Ez a cikk végigvezeti egy példa, amely a szolgáltatáskód állapotát jelzi. A példa azt is bemutatja, hogyan a Service Fabric által biztosított eszközök segítségével ellenőrizze az állapot ellenőrzése. Ez a cikk célja egy gyors Bevezetés a Service Fabric képességeit állapotfigyelési lehet. Részletesebb információkért olvassa el az adatsorozat részletes ismertető cikk gyűjtemény állapotát a hivatkozás, ez a cikk végén kezdődő.

## <a name="prerequisites"></a>Előfeltételek
Rendelkeznie kell a következőkkel:

* Visual Studio 2015 vagy Visual Studio 2017
* A Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>A helyi biztonságos fejlesztési fürtök létrehozásához
* Nyissa meg a Powershellt rendszergazdai jogosultságokkal, és futtassa a következő parancsokat:

![Parancsok, amelyek bemutatják, hogyan hozhat létre egy biztonságos fejlesztési fürtöt](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Az alkalmazás központi telepítését, és ellenőrizze annak állapotát
1. Nyissa meg a Visual Studio rendszergazdaként.
2. A projekt létrehozása a **állapotalapú alkalmazások és szolgáltatások szolgáltatás** sablont.
   
    ![Állapotalapú szolgáltatással a Service Fabric-alkalmazás létrehozása](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
3. Nyomja le az **F5** az alkalmazás hibakeresési módban fusson. Az alkalmazás központi telepítése a helyi fürthöz.
4. Miután az alkalmazás fut, kattintson a jobb gombbal a Local Cluster Manager ikonra az értesítési területen, és válassza ki **helyi fürt kezelése** a helyi menü megnyitásához a Service Fabric Explorerben talál.
   
    ![Nyissa meg a Service Fabric Explorer az értesítési területről](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
5. Az alkalmazás állapotának üzenetnek kell megjelennie, mint a kép. Ilyenkor az alkalmazás kifogástalan, és hiba nélkül kell lennie.
   
    ![A Service Fabric Explorerben megfelelő alkalmazás](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
6. A PowerShell használatával is ellenőrizheti az állapotát. Használhat ```Get-ServiceFabricApplicationHealth``` ellenőrzéséhez az alkalmazás állapotát, és használja ```Get-ServiceFabricServiceHealth``` ellenőrizni egy szolgáltatás állapotát. A jelentés a PowerShellben ugyanahhoz az alkalmazáshoz a képen van.
   
    ![A PowerShell megfelelő alkalmazás](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Egyéni állapotával kapcsolatos események hozzáadása a szolgáltatáskód hibáit
A Service Fabric a Visual Studio projektsablonjai példakódot tartalmaz. A következő lépések bemutatják, hogyan lehet jelentést egyéni állapotával kapcsolatos események a szolgáltatás kódból. Ezek a jelentések jelennek meg automatikusan az eszközt az állapotfigyelés, hogy a Service Fabric biztosítanak, például a Service Fabric Explorer, az Azure portál állapotának megtekintése és a PowerShell.

1. Nyissa meg újra az alkalmazást a Visual Studio korábban létrehozott, vagy hozzon létre egy új alkalmazás segítségével a **állapotalapú alkalmazások és szolgáltatások szolgáltatás** Visual Studio-sablont.
2. Nyissa meg a Stateful1.cs fájlt, és keresse a `myDictionary.TryGetValueAsync` -hívás a `RunAsync` metódust. Láthatja, hogy ez a metódus visszaadja a `result` , amely aktuális értéke a számláló, mert az alkalmazás fő programot az adatok megőrzése futtató számát. Ha ez egy valós alkalmazás, és az eredmény hiánya képviselt hibát, Ön szeretné jelző esemény.
3. Olyan állapotesemény, amikor eredmény hiánya jelenti. a hiba jelentését, adja hozzá az alábbi lépéseket.
   
    a. Adja hozzá a `System.Fabric.Health` névtér a Stateful1.cs fájlba.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Az alábbi kód után adja hozzá a `myDictionary.TryGetValueAsync` hívása
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    A replika állapota azt jelenti, mivel azt egy állapotalapú szolgáltatásból jelentve. A `HealthInformation` paraméter, amely ügynökökről állapottal kapcsolatos probléma kapcsolatos információkat tárolja.
   
    Ha létrehozta az állapotmentes szolgáltatások, a következő kódot használja.
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
4. Ha a szolgáltatás rendszergazda jogosultságokkal fut, vagy ha a fürt nem [biztonságos](service-fabric-cluster-security.md), is `FabricClient` jelentés állapotába, ahogy az az alábbi lépéseket.  
   
    a. Hozzon létre a `FabricClient` példány után a `var myDictionary` deklarációjában.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Az alábbi kód után adja hozzá a `myDictionary.TryGetValueAsync` hívható meg.
   
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
5. Most szimulálhatja ezt a hibát, és látja, az egészségügyi Hálózatfigyelő eszközök megjelennek. A hiba, a korábban hozzáadott állapotfigyelő jelentéskészítési kód első sorában megjegyzésbe szimulálásához. Miután az első sor megjegyzéssé, a kód az alábbihoz hasonlóan fog kinézni.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Ezzel a kóddal akkor következik be, a jelentés minden alkalommal, amikor `RunAsync` hajt végre. A módosítás elvégzése után nyomja le az **F5** az alkalmazás futtatásához.
6. Miután az alkalmazás fut, nyissa meg a Service Fabric Explorerben talál, az alkalmazás állapotának ellenőrzéséhez. Most, a Service Fabric Explorer jeleníti meg, hogy az alkalmazás állapota nem megfelelő. Ez a jelentett kód, hogy azt korábban hozzáadott a következő hiba miatt.
   
    ![A Service Fabric Explorerben a nem megfelelő alkalmazás](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
7. Ha az elsődleges másodpéldány a Service Fabric Explorer fanézetben, láthatja, hogy **állapota** jelzi a hiba, túl. Service Fabric Explorer is hozzáadott állapotfigyelő jelentés részleteit jeleníti meg a `HealthInformation` paraméter a kódban. Az azonos állapotjelentéseiben PowerShell és az Azure-portálon tekintheti meg.
   
    ![A Service Fabric Explorerben a replika állapota](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ez a jelentés marad, az állapotkezelő a azt egy másik jelentés, vagy amíg a replika nem váltja fel. Mivel jelenleg nincs beállítva `TimeToLive` esetében ez a jelentés a `HealthInformation` objektum, a jelentés soha nem jár le.

Azt javasoljuk, hogy a legtöbb részletes szinten, amely a jelen esetben ez a replika állapota jelenteni. A health is jelentheti `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

A jelentés-állapot `Application`, `DeployedApplication`, és `DeployedServicePackage`, használjon `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>További lépések
* [Részletes bemutatója a Service Fabric állapota](service-fabric-health-introduction.md)
* [REST API-t a jelentéskészítési szolgáltatás állapota](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API-alkalmazás állapotának jelentéskészítéshez](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

