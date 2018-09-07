---
title: Feladatátvételi és káosztesztek létrehozása az Azure Service Fabrichez |} A Microsoft Docs
description: A Service Fabric használatával káosz teszt- és feladatátvételi Tesztelési forgatókönyvek idéz elő hibákat, és ellenőrizze a szolgáltatás megbízhatóságát.
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: d12c5097d4ba5e0ccfe0e2b2cbc8ccd758c32d98
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44051289"
---
# <a name="testability-scenarios"></a>Testability alkalmazási helyzetek
Nagy méretű elosztott rendszerek, a felhőalapú infrastruktúrák rendszer természetüknél fogva nem megbízható. Az Azure Service Fabric-ra épülve a nem megbízható hálózathatáron infrastruktúrák szolgáltatások írását lehetővé teszi a fejlesztők számára. Írás a magas színvonalú szolgáltatásokat, a fejlesztők kell tudni idéz elő az ilyen megbízhatatlan infrastruktúra szolgáltatásaik stabilitását teszteléséhez.

A Fault Analysis Service lehetővé teszi a fejlesztők a tartalék műveletek tesztelése szolgáltatások folytonosságát hibák esetén idéz elő. Azonban célzott szimulált hibák elsajátíthatja az első csak eddig. Hogy a tesztelés további, használhatja a tesztcélú forgatókönyveket a Service Fabricben: egy káosz teszt- és a egy feladatátvételi tesztet. Ezek a forgatókönyvek folyamatos kihagyásos hibák, biztonságos és a hosszabb időn keresztül a fürt teljes végbemenjen szimulálása. Miután egy tesztet a sebesség és a hibák típusú van beállítva, azt vagy C# API-k vagy a Powershellen keresztül, a hibák létrehozni a fürtöt és a szolgáltatás elindítható.

> [!WARNING]
> ChaosTestScenario váltja fel egy rugalmasabb, szolgáltatás-alapú káosz. Tekintse meg az új cikk [szabályozott káosz](service-fabric-controlled-chaos.md) további részletekért.
> 
> 

## <a name="chaos-test"></a>A Chaos teszt
A chaos forgatókönyv hibák között a teljes Service Fabric-fürtöt hoz létre. A forgatókönyv tömöríti a hibák általában látható a hónap vagy év néhány óra. Összefűzéses hibák a magas laphibák aránya kombinációja, amely a rendszer ellenkező esetben nem talált esetekben keresi meg. Ez a kód minőségének a jelentős fejlesztéseket tartalmaz vezet.

### <a name="faults-simulated-in-the-chaos-test"></a>A chaos teszt szimulált hibák
* Csomópont újraindítása
* Indítsa újra a központilag telepített kódcsomag
* Replika eltávolítása
* Indítsa újra a replika
* Helyezze át egy elsődleges replika (nem kötelező)
* Helyezze át egy másodlagos másodpéldány (nem kötelező)

A chaos tesztfuttatásai több ismétlésének hibák és a fürt érvényesítést a megadott időszakban. Az idő stabilizálódhatnak a fürt és az érvényesítés sikeres, akkor is konfigurálható. A forgatókönyvben sikertelen lesz, ha eléri a fürtellenőrzés egyetlen hiba.

Vegyük példaként egy teszt futtatásához egy órán keresztül, amely legfeljebb három egyidejű hibák beállítása. A vizsgálat három hibák idéz elő, és a fürt állapotának ellenőrzéséhez. A teszt keresztül az előző lépésben iterálni fogja, amíg a fürt akkor kerül sérült, vagy egy órán keresztül továbbítja. Ha a fürt akkor kerül sérült, az ismétlések, vagyis azt nem stabilizálódhatnak a beállított időn belül, a teszt sikertelen lesz, és a egy kivételt. Ez a kivétel azt jelzi, hogy probléma merült fel, és további vizsgálat szükséges.

A jelenlegi formájában a tartalék generációs motor a chaos tesztben kapott csak biztonságos hibák. Ez azt jelenti, hogy külső hibák hiányában egy kvórum vagy adatvesztés soha nem kerül.

### <a name="important-configuration-options"></a>Fontos konfigurációs beállításai
* **Timetorun érték**: teljes idő, hogy a teszt sikeres befejezése előtt fog futni. A teszt korábban be tudja helyett egy érvényesítési hiba.
* **MaxClusterStabilizationTimeout**: legfeljebb ennyi idő múlva a fürt előtt a teszt sikertelen állapotúak lesznek. Az elvégzett fürtállapot-e OK, a service health rendben, a cél másodpéldánykészletének méretét a szolgáltatás partíció érhető el, és nem található InBuild replikák létezik.
* **MaxConcurrentFaults**: minden egyes ismétléskor okozta egyidejű hibák maximális száma. Minél nagyobb a száma, a agresszívabb a teszt, így összetettebb feladatátvételt és a Váltás kombinációk eredményez. A teszt garantálja, hogy a külső hibák hiányában nem fogja a kvórum vagy adatvesztés, attól függetlenül, a nagy az ebben a konfigurációban.
* **EnableMoveReplicaFaults**: engedélyezheti vagy letilthatja a hibákat okozó az áthelyezés elsődleges vagy másodlagos replikára. Ezek a hibák alapértelmezés szerint le vannak tiltva.
* **WaitTimeBetweenIterations**: ismétlések, azaz egy ciklikus hibák és a megfelelő érvényesítése után közötti várakozási idő mennyiségét.

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
A feladatátvételi teszt forgatókönyv, amely egy adott szolgáltatás partíció célozza káosz vizsgálati eset verziója is. Eközben a többi szolgáltatás nincs hatással a feladatátvétel egy adott szolgáltatás partíció gyakorolt hatása azt teszteli. A cél a partíciónak az adatait és más paramétereket konfigurálása után fut, amely a C# API-k vagy a PowerShell segítségével hozzon létre egy service partíció hibák ügyféloldali eszközként. A forgatókönyv végighalad szimulált hibák és a szolgáltatás-ellenőrzés sorozata, az üzleti logikát az oldalon adja meg a számítási feladatok futtatása közben. A szolgáltatás-ellenőrzési hiba azt jelzi, hogy a hibát, amely további vizsgálatot igényel.

### <a name="faults-simulated-in-the-failover-test"></a>A feladatátvételi teszt szimulált hibák
* Indítsa újra a partíció üzemeltető üzembe helyezett kód csomag
* Elsődleges és másodlagos replika vagy állapotmentes példány eltávolítása
* Indítsa újra a másodlagos replikáról (Ha egy megőrzött szolgáltatás)
* Helyezze át az elsődleges replika
* Egy másodlagos másodpéldány áthelyezése
* Indítsa újra a partíció

A feladatátvételi teszt a kiválasztott hibát kapott, és érvényesítési majd fut a szolgáltatás stabilitásának biztosításához. A feladatátvételi teszt kapott csak egyetlen tartalék figyelésekor lehetséges egyszerre több hibák a chaos teszt. Ha a szolgáltatás partíció nem stabilizálódhatnak a beállított időkorláton belül minden hiba után, a teszt sikertelen lesz. A vizsgálat csak biztonságos hibákat kapott. Ez azt jelenti, hogy a külső hibák hiányában egy kvórum vagy adatvesztés nem történik.

### <a name="important-configuration-options"></a>Fontos konfigurációs beállításai
* **PartitionSelector**: választó objektum, amely meghatározza a partíció, amely kell kapjon.
* **Timetorun érték**: teljes idő, hogy a teszt befejezése előtt fog futni.
* **MaxServiceStabilizationTimeout**: legfeljebb ennyi idő múlva a fürt előtt a teszt sikertelen állapotúak lesznek. Az elvégzett a service health e OK, a cél másodpéldánykészletének méretét a érhető el az összes partíciót, és nem található InBuild replikák létezik.
* **WaitTimeBetweenFaults**: minden tartalék és érvényesítési ciklus közötti várakozási idő.

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
