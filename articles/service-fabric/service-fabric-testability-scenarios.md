---
title: "Hozzon létre chaos és a feladatátvételi teszt Azure mikroszolgáltatások |} Microsoft Docs"
description: "A Service Fabric használatával chaos teszt feladatátvételi teszt- és forgatókönyvek idéz elő a hibákat, és ellenőrizze a szolgáltatások."
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: d06026c750e01ad5825338a78d9af331265f434a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="testability-scenarios"></a>Tesztelhetőségi forgatókönyvek
Nagy méretű elosztott rendszerek hasonlóan a felhőalapú infrastruktúrák nem eredendően megbízhatóak. Azure Service Fabric lehetővé teszi a fejlesztők szolgáltatások futtatásához fölött megbízhatatlan infrastruktúrák írni. Írási magas színvonalú szolgáltatásokat, a fejlesztők kell tudni idéz elő az ilyen megbízhatatlan infrastruktúra a szolgáltatások stabilitásának teszteléséhez.

A tartalék elemzési szolgáltatás segítségével a fejlesztők idéz elő hiba műveletek tesztelése szolgáltatások hibák esetén. Azonban célzott szimulált hibákat kap, csak eddig. A tesztelés további végrehajtania, használja a tesztelési forgatókönyvek a Service Fabric: egy chaos teszt- és a feladatátvételi tesztet. Ezek a forgatókönyvek folyamatos kihagyásos hibák, a szabályos és a hosszú időn keresztül a fürt teljes ungraceful szimulálásához. Miután egy tesztet a sebesség és a hibák típusú van beállítva, vagy C# API-k vagy a Powershellen keresztül, hibák létrehozni a fürtöt és a szolgáltatás elindítható.

> [!WARNING]
> ChaosTestScenario rugalmasabb, szolgáltatás-alapú Chaos lesz cserélve. Tekintse meg az új cikkek [szabályozott Chaos](service-fabric-controlled-chaos.md) további részleteket.
> 
> 

## <a name="chaos-test"></a>Chaos tesztelése
A chaos forgatókönyv hibák között a teljes Service Fabric-fürtöt hoz létre. A forgatókönyv tömöríti a hibák általában látható a hónap vagy év néhány óra. Kihagyásos hibák a magas gyakorisága a kombinációja, amelyek egyébként nem talált esetekben keresi. Ez a szolgáltatás kód minőségének jelentős fejlesztéseket vezet.

### <a name="faults-simulated-in-the-chaos-test"></a>Az chaos teszt szimulált hibák
* A csomópont újraindítása
* Indítsa újra a központilag telepített kódcsomag
* Replika eltávolítása
* Indítsa újra a replikát
* (Opcionális) elsődleges replika áthelyezése
* Helyezze át egy másodlagos másodpéldány (nem kötelező)

A chaos tesztre többszöri hibák és a fürt érvényesítést a megadott ideig. A fürt stabilizálásához és érvényesítése sikeres fordított idő is lehet konfigurálni. A forgatókönyv sikertelen lesz, amikor egy egyetlen hiba a fürtellenőrzés kattint.

Vegye figyelembe például három egyidejű hibák legfeljebb egy órára futtatásához hozzon egy tesztet. A vizsgálat idéz elő a három hibák, és a fürt állapotának ellenőrzéséhez. A teszt az előző lépésben algoritmusa keretein belül a fürt akkor kerül sérült állapotba, vagy egy órán keresztül továbbítja. Ha a fürt bármely munkamenetben nem kifogástalan, vagyis azt nem stabilizálását a beállított időn belül, a teszt sikertelen lesz, és kivételt. Ez a kivétel azt jelzi, hogy valami nem megfelelő állapotba került további vizsgálat szükséges.

A jelenlegi formában a tartalék generációs motor a chaos tesztben kapott csak biztonságos hibák. Ez azt jelenti, hogy külső hibák hiányában egy kvórum vagy adatvesztés soha nem történik.

### <a name="important-configuration-options"></a>Fontos konfigurációs beállítások
* **TimeToRun**: teljes idő, a teszt lefuttatásával sikerrel befejezése előtt. A vizsgálat be tudja fejezni korábbi helyett egy érvényesítési hiba.
* **MaxClusterStabilizationTimeout**: legfeljebb ennyi ideig várjon a megfelelő legyen, a teszt végrehajtása előtt a fürt. Az elvégzett e a fürt állapota rendben, szolgáltatásának állapota rendben, a replikakészlet célméretének érhető el, a szolgáltatás partíció, és nem található InBuild replikák létezik.
* **MaxConcurrentFaults**: minden egyes ismétlés előidézett egyidejű hibák maximális száma. Minél nagyobb a szám, a szigorúbb a törik, így összetettebb feladatátvétel és az átmenet kombinációját. A vizsgálat biztosítja, hogy külső hibák hiányában nem fogja a kvórum vagy adatvesztés, függetlenül attól, milyen magas van ebben a konfigurációban.
* **EnableMoveReplicaFaults**: engedélyezheti vagy letilthatja a hibákat, amely az áthelyezés elsődleges vagy másodlagos replikán. Ezek a hibák alapesetben le vannak tiltva.
* **WaitTimeBetweenIterations**: ismétlési, azaz egy ciklikus hibák és a megfelelő érvényesítése után közti várakozási idő.

### <a name="how-to-run-the-chaos-test"></a>A chaos teszt futtatása
C#-minta

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Feladatátvételi teszt
A feladatátvételi teszt forgatókönyv, amelynek célja egy adott szolgáltatás partíció chaos tesztkörnyezet egy verziója. Ezt megvizsgálja egy adott szolgáltatás partíción feladatátvételi hatásának úgy, hogy a más szolgáltatások nem érinti. A cél a partíciónak az adatait és egyéb paraméterek beállítása után fut, amely C# API-k vagy a PowerShell hibák szolgáltatás partíció létrehozásához használja az ügyféloldali eszközként. A forgatókönyv telepítéseket szimulált hibák és a szolgáltatás érvényesítése közben az üzleti logikát a oldalon egy munkaterhelés számára. Szolgáltatás érvényesítési hiba azt jelzi, hogy a hibát, amely további vizsgálat szükséges.

### <a name="faults-simulated-in-the-failover-test"></a>A feladatátvételi teszt szimulált hibák
* Indítsa újra a telepített kódcsomag, ahol a partíció tárolása
* Egy elsődleges és másodlagos replika- vagy állapotmentes eltávolítása
* Indítsa újra a másodlagos replikáról (Ha egy megőrzött szolgáltatás)
* Helyezze át az elsődleges replika
* Helyezze át egy másodlagos másodpéldány
* Indítsa újra a partíciót

A feladatátvételi teszt választott hibát kapott, és majd érvényesítési fut a szolgáltatás stabilitásának biztosításához. A feladatátvételi teszt kapott csak egy hiba lehetséges figyelésekor egyszerre több hibák az chaos teszt. A teszt sikertelen, ha a szolgáltatás partíció nem stabilizálásához a beállított időkorláton belül minden egyes hiba után. A vizsgálat csak biztonságos hibák kapott. Ez azt jelenti, hogy külső hibák hiányában egy kvórum vagy adatvesztés történik.

### <a name="important-configuration-options"></a>Fontos konfigurációs beállítások
* **Partitionselector osztályt**: választó objektum, amely meghatározza a partíció, amelyet a telepíthető.
* **TimeToRun**: teljes idő, amely a vizsgálat befejezése előtt.
* **MaxServiceStabilizationTimeout**: legfeljebb ennyi ideig várjon a megfelelő legyen, a teszt végrehajtása előtt a fürt. Az elvégzett e szolgáltatásának állapota rendben, a replikakészlet célméretének teljesíteni az összes partíció, és nem található InBuild replikák létezik.
* **WaitTimeBetweenFaults**: minden hiba és érvényesítési ciklus közti várakozási idő.

### <a name="how-to-run-the-failover-test"></a>A feladatátvételi teszt futtatása
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
