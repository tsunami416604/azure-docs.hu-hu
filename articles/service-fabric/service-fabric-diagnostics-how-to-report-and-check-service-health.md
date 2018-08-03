---
title: Az Azure Service Fabric állapot jelentése és ellenőrzése |} A Microsoft Docs
description: Megismerheti a szolgáltatás kódból rendszerállapot-jelentések küldése és a szolgáltatás állapotának ellenőrzéséhez nyújt az Azure Service Fabric health figyelési eszközök segítségével.
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
ms.openlocfilehash: d374886efb708797db1dd6352aa063a56aff4f44
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427308"
---
# <a name="report-and-check-service-health"></a>Szolgáltatásállapot jelentése és ellenőrzése
A szolgáltatások problémát észlel, amikor válaszol, és javítsa ki az incidensek és üzemkimaradások utáni helyreállításon képességét a lehetővé teszi a hibák gyors észlelésében függ. Ha jelenti problémák és hibák az Azure Service Fabric health Manager a szolgáltatás kódból, standard állapotfigyelési eszközöket, amelyek a Service Fabric biztosít az állapot ellenőrzéséhez használhatja.

Három módon, hogy a szolgáltatás állapotának jelentheti:

* Használat [partíció](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) vagy [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) objektumokat.  
  Használhatja a `Partition` és `CodePackageActivationContext` objektumok elemeket az aktuális környezet részét képező állapotának jelentésére. Például egy replika részeként futó kódok jelentheti a health csak az adott replika, a partíció, amely tartozik, és az alkalmazás, amely egy része.
* Használat `FabricClient`.   
  Használhat `FabricClient` jelentés állapotába. Ha a fürt nem a szolgáltatás kódból [biztonságos](service-fabric-cluster-security.md) , vagy ha a szolgáltatás fut-e rendszergazda jogosultságokkal. A legtöbb valós életből vett példák ne használjon nem biztonságos fürtökhöz, vagy adja meg a rendszergazdai jogosultságokat. A `FabricClient`, egészségügyi jelentheti a minden entitás, amely a fürt része. Ideális esetben azonban szolgáltatás kódot csak küldjön-e a saját egészségügyi kapcsolatos jelentések.
* A REST API-k használata a fürt, alkalmazások, az üzembe helyezett alkalmazás, szolgáltatás, szolgáltatáscsomag, partíció, replika, vagy a csomópont szintek. Ez a jelentés összesíti a tárolóban lévő is használható.

Ez a cikk végigvezeti egy példa, amely a szolgáltatás kódból állapotáról jelentést. A példa azt is bemutatja, hogyan a Service Fabric által biztosított eszközök segítségével ellenőrizze az állapot ellenőrzése. Ez a cikk röviden bemutatja a Service Fabric képességeit állapotfigyelési történő van készült. További részletes információkért olvassa el a részletes ismertető cikksorozat egészségügyi kezdődik, ez a cikk végén található hivatkozásra.

## <a name="prerequisites"></a>Előfeltételek
A következőkkel kell rendelkeznie:

* Visual Studio 2015 vagy Visual Studio 2017
* A Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Biztonságos helyi fejlesztési fürt létrehozása
* Nyissa meg a Powershellt rendszergazdai jogosultságokkal, és futtassa a következő parancsokat:

![Parancsok, amelyek bemutatják a biztonságos fejlesztési fürt létrehozása](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Alkalmazás üzembe helyezése, és ellenőrizze az állapotát
1. Nyissa meg a Visual Studiót rendszergazdaként.
1. Hozzon létre egy projektet a **állapotalapú szolgáltatás** sablont.
   
    ![Állapotalapú szolgáltatás a Service Fabric-alkalmazás létrehozása](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Nyomja meg **F5** hibakeresési módban az alkalmazás futtatásához. Az alkalmazás telepítve van a helyi fürthöz.
1. Miután az alkalmazás fut, kattintson a jobb gombbal a Local Cluster Manager ikonra az értesítési területen, és válassza ki **helyi fürt kezelése** a Service Fabric Explorerben nyissa meg a helyi menüből.
   
    ![Nyissa meg a Service Fabric Explorer az értesítési területről.](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Az alkalmazás állapotának üzenetnek kell megjelennie, ahogyan a képen is látható. Jelenleg az alkalmazás hiba nélkül kifogástalan állapotban kell lennie.
   
    ![Kifogástalan állapotú alkalmazások a Service Fabric Explorerben](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Az állapotfigyelő a PowerShell használatával is ellenőrizheti. Használhat ```Get-ServiceFabricApplicationHealth``` ellenőrzéséhez az alkalmazás állapotáról, és használja ```Get-ServiceFabricServiceHealth``` ellenőrizni egy szolgáltatás állapotát. Az állapotjelentés ugyanahhoz az alkalmazáshoz a PowerShell van ezen a képen.
   
    ![A PowerShell megfelelő alkalmazás](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>A szolgáltatás kód egyéni hálózatállapot-események hozzáadása
A Visual Studio projektsablonjai a Service Fabric mintakód tartalmazza. A következő lépések bemutatják, hogyan lehet jelentést egyéni hálózatállapot-események a szolgáltatás kódból. Ezek a jelentések a az eszközt, hogy a Service Fabric biztosít, a Service Fabric Explorer, az Azure portal állapotának megtekintése és a PowerShell használatával az állapotmonitorozásra vonatkozó automatikusan megjelennek.

1. Nyissa meg újra az alkalmazást a Visual Studióban korábban létrehozott, vagy hozzon létre egy új alkalmazást használatával a **állapotalapú szolgáltatás** Visual Studio-sablont.
1. Nyissa meg a Stateful1.cs fájlt, és keresse meg a `myDictionary.TryGetValueAsync` hívja meg a `RunAsync` metódust. Láthatja, hogy a metódus visszatérése egy `result` a számláló a jelenlegi érték, amely tárolja, mivel ebben az alkalmazásban a kulcs logika, hogy a futó számát. Ha ez egy valódi alkalmazás, és az eredmény hiánya jelölt hiba, érdemes jelző esemény.
1. A health eseményt, amikor eredmény hiánya jelenti. a hiba jelentéséhez, adja hozzá az alábbi lépéseket.
   
    a. Adja hozzá a `System.Fabric.Health` névtér a Stateful1.cs fájlt.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Az alábbi kód után adja hozzá a `myDictionary.TryGetValueAsync` meghívása
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Replika állapota azt jelentést, mert van egy állapotalapú szolgáltatás által jelentett. A `HealthInformation` paraméter az állapottal kapcsolatos probléma jelentve kapcsolatos információkat tárolja.
   
    Ha egy állapotmentes szolgáltatás hozta létre, a következő kód használatával
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Ha a rendszergazda jogosultságokkal a szolgáltatás fut, vagy ha a fürt nem [biztonságos](service-fabric-cluster-security.md), is `FabricClient` jelentés állapotába, ahogyan az alábbi lépéseket.  
   
    a. Hozzon létre a `FabricClient` példány után a `var myDictionary` nyilatkozatot.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Az alábbi kód után adja hozzá a `myDictionary.TryGetValueAsync` hívja.
   
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
1. Most ezt a hibát szimulálni, és próbálja ki, hogy a rendszerállapot-figyelési eszközök megjelennek. A hibát szimulálni, tegye megjegyzésbe az első sorban a kódot, amely korábban hozzáadott reporting állapotát. Követően, tegye megjegyzésbe az első sort, a kód az alábbihoz hasonlóan fog kinézni.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Ez a kód akkor következik be, a jelentés minden alkalommal, amikor `RunAsync` hajt végre. A módosítás elvégzése után nyomja le az **F5** az alkalmazás futtatásához.
1. Miután az alkalmazás fut, nyissa meg a Service Fabric Explorer az alkalmazás állapotának ellenőrzéséhez. Ezúttal a Service Fabric Explorer mutatja, hogy az alkalmazás állapota nem megfelelő. Ez a következő jelentett a kódban, hogy korábban hozzáadott hiba miatt.
   
    ![Sérült alkalmazás a Service Fabric Explorerben](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Ha az elsődleges replikán a Service Fabric Explorert a fanézetben megtekintheti, látni fogja, hogy **állapot** azt jelzi, hogy egy hiba túl. A Service Fabric Explorer is hozzáadott egészségügyi jelentés részleteit jeleníti meg a `HealthInformation` paraméter a kódban. Láthatja, hogy ugyanazt a rendszerállapot-jelentések a PowerShell és az Azure Portalon.
   
    ![Replika állapotát a Service Fabric Explorerben](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ez a jelentés a health manager marad mindaddig, amíg váltja fel, egy másik jelentést, vagy amíg ez a másodpéldány nem törli. Mivel mi nem állította be `TimeToLive` esetében ez a jelentés a `HealthInformation` objektumot, a jelentés soha nem jár le.

Azt javasoljuk, hogy egészségügyi kell jelenteni a legrészletesebb szintjét, amely ebben az esetben a replikát. Az egészségügyi is jelentheti `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

A jelentés állapotába `Application`, `DeployedApplication`, és `DeployedServicePackage`, használjon `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>További lépések
* [A Service Fabric állapotának részletes bemutatása](service-fabric-health-introduction.md)
* [REST API a jelentéskészítési szolgáltatás állapota](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API-alkalmazás állapotának reporting](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

