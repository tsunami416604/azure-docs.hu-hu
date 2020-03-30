---
title: Káosz és feladatátvételi tesztek létrehozása az Azure Service Fabric számára
description: A Service Fabric chaos teszt és feladatátvételi teszt forgatókönyvek használatával hibákat okozhat, és ellenőrizze a szolgáltatások megbízhatóságát.
author: motanv
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: motanv
ms.openlocfilehash: 206b02024ad052a12e87cfdf1773815027e8aec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465531"
---
# <a name="testability-scenarios"></a>Tesztelhetőségi forgatókönyvek
A nagy elosztott rendszerek, például a felhőalapú infrastruktúrák eredendően megbízhatatlanok. Az Azure Service Fabric lehetővé teszi a fejlesztők számára, hogy a megbízhatatlan infrastruktúrákon futó szolgáltatásokat írhassák. Annak érdekében, hogy magas színvonalú szolgáltatásokat írjanak, a fejlesztőknek képesnek kell lenniük arra, hogy ilyen megbízhatatlan infrastruktúrát indukáljanak szolgáltatásaik stabilitásának tesztelésére.

A hibaelemzési szolgáltatás lehetővé teszi a fejlesztők számára, hogy hibaműveleteket idézzenek elő a szolgáltatások tesztelésére hibák jelenlétében. Azonban a célzott szimulált hibák csak eddig. A tesztelés további, használhatja a tesztforgatókönyvek a Service Fabric: a chaos teszt és a feladatátvételi teszt. Ezek a forgatókönyvek szimulálják a folyamatos interleaved hibák, mind a kecses és ungraceful, a fürt hosszabb ideig. Miután egy teszt van konfigurálva a sebesség és a hibák, c# API-k vagy powershell keresztül indítható, hibák létrehozása a fürtben és a szolgáltatásban.

> [!WARNING]
> ChaosTestScenario váltja fel egy rugalmasabb, szolgáltatásalapú Chaos. További részletekért olvassa el az [ellenőrzött káosz](service-fabric-controlled-chaos.md) című új cikket.
> 
> 

## <a name="chaos-test"></a>Káosz teszt
A káosz forgatókönyv hibákat hoz létre a teljes Service Fabric-fürtön. A forgatókönyv tömöríti hibák általában látható hónapokban vagy években néhány órára. Az áthatárolt hibák és a magas hibaarány kombinációja olyan sarokeseteket keres, amelyek egyébként kimaradtak. Ez a szolgáltatás kódminőségének jelentős javulásához vezet.

### <a name="faults-simulated-in-the-chaos-test"></a>Hibák szimulált a káosz teszt
* Csomópont újraindítása
* Telepített kódcsomag újraindítása
* Kópia eltávolítása
* Kópia újraindítása
* Elsődleges kópia áthelyezése (nem kötelező)
* Másodlagos replika áthelyezése (nem kötelező)

A chaos teszt több hibaismétlést és fürtérvényesítést futtat a megadott ideig. A fürt stabilizálására és sikeres érvényesítésére fordított idő szintén konfigurálható. A forgatókönyv sikertelen, ha egyetlen hibát talál be a fürt érvényesítése során.

Vegyünk például egy tesztkészletet, amely egy órán keresztül fut, legfeljebb három egyidejű hibával. A teszt három hibát idéz elő, majd ellenőrzi a fürt állapotát. A teszt végighalad az előző lépésen, amíg a fürt nem lesz kifogástalan, vagy egy óra telik el. Ha a fürt nem megfelelő állapotúvá válik bármely iterációban, azaz nem stabilizálódik egy beállított időn belül, a teszt kivétellel sikertelen lesz. Ez a kivétel azt jelzi, hogy valami elromlott, és további vizsgálatot igényel.

Jelenlegi formájában a hibageneráló motor a káosztesztben csak biztonságos hibákat idéz elő. Ez azt jelenti, hogy külső hibák hiányában a kvórum vagy az adatvesztés soha nem fog bekövetkezni.

### <a name="important-configuration-options"></a>Fontos konfigurációs lehetőségek
* **TimeToRun**: A teszt teljes ideje a sikeres befejezés előtt. A teszt korábban is befejeződhet egy érvényesítési hiba helyett.
* **MaxClusterStabilizationTimeout**: Maximális idő, hogy várjon a fürt kifogástalan állapotba hoz, mielőtt a teszt sikertelen. Az elvégzett ellenőrzések azt jelzik, hogy a fürt állapota rendben van-e, a szolgáltatás állapota rendben van-e, a célreplika-készlet mérete a szolgáltatáspartícióhoz, és nincs inbuild-replikák léteznek.
* **MaxConcurrentFaults**: Az egyes ismétlésekben okozott egyidejű hibák maximális száma. Minél nagyobb a szám, annál agresszívebb a teszt, ami összetettebb feladatátvételeket és átmeneti kombinációkat eredményez. A teszt garantálja, hogy külső hibák hiányában nem lesz kvórum vagy adatvesztés, függetlenül attól, hogy milyen magas ez a konfiguráció.
* **EnableMoveReplicaFaults**: Engedélyezi vagy letiltja azokat a hibákat, amelyek az elsődleges vagy másodlagos replikák áthelyezését okozzák. Ezek a hibák alapértelmezés szerint le vannak tiltva.
* **WaitTimeBetweenIterations**: Az ismétlések közötti várakozási idő, azaz egy hibakör és a megfelelő érvényesítés után.

### <a name="how-to-run-the-chaos-test"></a>Hogyan kell futtatni a káosz teszt
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

A Service Fabric Powershell modul két módot tartalmaz egy káosz forgatókönyv megkezdéséhez. `Invoke-ServiceFabricChaosTestScenario`ügyfélalapú, és ha az ügyfélgép a teszt felénél leáll, nem kerül elő további hibák. Alternatív megoldásként van egy sor parancsok célja, hogy a teszt fut a gép leállítása esetén. `Start-ServiceFabricChaos`egy állapotalapú és megbízható rendszerszolgáltatást használ, amelyet FaultAnalysisService-nek hívnak, biztosítva, hogy a hibák a TimeToRun felfutásáig maradjanak. `Stop-ServiceFabricChaos`manuálisan leállíthatja a forgatókönyvet, és `Get-ServiceFabricChaosReport` beszerezheti a jelentést. További információkért lásd: az [Azure Service Fabric Powershell referencia](https://docs.microsoft.com/powershell/module/servicefabric/?view=azureservicefabricps) és [indukáló ellenőrzött káosz service Fabric-fürtök.](service-fabric-controlled-chaos.md)

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
A feladatátvételi teszt forgatókönyv egy változata a chaos teszt forgatókönyv, amely egy adott szolgáltatáspartíciót céloz meg. Teszteli a feladatátvétel hatását egy adott szolgáltatáspartíción, miközben a többi szolgáltatás változatlanmarad. Miután konfigurálta a célpartíció adatait és egyéb paramétereket, ügyféloldali eszközként fut, amely c# API-kat vagy PowerShellt használ egy szolgáltatáspartíció hibáinak létrehozásához. A forgatókönyv szimulált hibák és szolgáltatás-ellenőrzés sorozatán keresztül halad, miközben az üzleti logika az oldalon fut, hogy számítási feladatot biztosítson. A szolgáltatás-érvényesítés sikertelenség olyan problémát jelez, amely további vizsgálatot igényel.

### <a name="faults-simulated-in-the-failover-test"></a>A feladatátvételi tesztben szimulált hibák
* A partíciót üzemeltető telepített kódcsomag újraindítása
* Elsődleges/másodlagos replika vagy állapotmentes példány eltávolítása
* Elsődleges másodlagos replika újraindítása (megőrzött szolgáltatás esetén)
* Elsődleges kópia áthelyezése
* Másodlagos replika áthelyezése
* A partíció újraindítása

A feladatátvételi teszt egy kiválasztott hibát idéz elő, majd futtatja az ellenőrzést a szolgáltatáson a stabilitás biztosítása érdekében. A feladatátvételi teszt egyszerre csak egy hibát idéz elő, szemben a káoszteszt lehetséges többszörös hibáival. Ha a szolgáltatáspartíció nem stabilizálódik a beállított időkorláton belül minden hiba után, a teszt sikertelen lesz. A vizsgálat csak biztonságos hibákat idéz elő. Ez azt jelenti, hogy külső hibák hiányában nem következik be kvórum vagy adatvesztés.

### <a name="important-configuration-options"></a>Fontos konfigurációs lehetőségek
* **PartitionSelector**: Kijelölőobjektum, amely megadja a megcélozni kívánt partíciót.
* **TimeToRun**: A teszt befejezéséhez futó teljes idő.
* **MaxServiceStabilizationTimeout**: Maximális idő, hogy várjon a fürt kifogástalan állapotba hoz, mielőtt a teszt sikertelen. Az elvégzett ellenőrzések azt jelzik, hogy a szolgáltatás állapota rendben van-e, a célreplikakészlet mérete az összes partícióra teljesül, és nincs inbuild-replikák léteznek.
* **WaitTimeBetweenFaults**: Minden hiba és ellenőrzési ciklus között várakozni való várakozási idő.

### <a name="how-to-run-the-failover-test"></a>A feladatátvételi teszt futtatása
**C #**

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


**Powershell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
