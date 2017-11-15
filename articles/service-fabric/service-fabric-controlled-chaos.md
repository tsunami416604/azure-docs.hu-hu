---
title: "A Service Fabric-fürtök idéz elő Chaos |} Microsoft Docs"
description: "Van, és a fürt Analysis Service API-k kezelése segítségével Chaos a fürtben."
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/10/2017
ms.author: motanv
ms.openlocfilehash: c78d9e77d807f3ccf8c1f56d856abad8135989c2
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>A Service Fabric-fürtök ellenőrzött Chaos idéz elő
Nagy méretű elosztott rendszerek, például a felhőalapú infrastruktúrák nem eredendően megbízhatóak. Az Azure Service Fabric lehetővé teszi a fejlesztők egy nem megbízható infrastruktúrán megbízható elosztott szolgáltatások írni. Egy nem megbízható infrastruktúrán robusztus elosztott szolgáltatások írni fejlesztők kell tesztelni szolgáltatásaik stabilitását, míg az alapul szolgáló megbízhatatlan infrastruktúra megy keresztül bonyolult Állapotváltások hibák miatt.

A [van, és a fürt Analysis Service](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (más néven a tartalék Analysis Services) segítségével a fejlesztők a hibák ellenőrzéséhez a szolgáltatások idéz elő. Ezek a megcélzott szimulált hibák, például [partíció újraindítása](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), segíthet a leggyakrabban használt Állapotváltások megadásával. Azonban célzott szimulált hibák definition vannak optimalizálva, és így nem teljesíti az hibák elhárítása, hogy másolatot csak a Állapotváltások rögzített előrejelzése, hosszúak és bonyolultak sorrendjét. Egy torzítatlan tesztelési Chaos is használhatja.

Chaos időszakos, kihagyásos hibák (szabályos és ungraceful) a fürt teljes szimulálja a hosszú idő alatt. A Service Fabric API-hívások áll egy sikeres-e hiba, a például újraindítás replika tartalék szabályos hibát mivel ez egy zárja be a megnyitott követ replikán. Távolítsa el a replika, helyezze át az elsődleges, és áthelyezése másodlagos másodpéldány nem az Chaos látja el a más sikeres-e hibák. Ungraceful hibák folyamat kilép, például indítsa újra a csomópont- és restrat kód csomagjának. 

Miután konfigurálta a Chaos sebessége és milyen típusú hibákat, megkezdheti a C#, Powershell vagy REST API létrehozása hibák, a fürt és a szolgáltatások indítására Chaos. Konfigurálhatja Chaos futtatásához egy megadott időszak (például egy órát), mely Chaos leállítása után automatikusan, vagy (C#, Powershell vagy REST) bármikor leállítására StopChaos API hívása.

> [!NOTE]
> A jelenlegi formában az Chaos kapott csak biztonságos hibák, ami azt jelenti, hogy külső hibák hiányában a kvórum elvesztése vagy adatvesztés soha nem történik.
>

Chaos futtatása közben különböző események pillanatnyilag a Futtatás állapotát rögzítő hoz létre. Például egy ExecutingFaultsEvent tartalmaz, amelyek Chaos úgy döntött, hogy a munkamenetben végrehajtani minden hibájával. Egy ValidationFailedEvent érvényesítési hibája (állapotfigyelő és stabilitását hibát) a fürt ellenőrzése során talált részleteit tartalmazza. A GetChaosReport API (C#, Powershell vagy REST) Chaos futtatásakor a jelentés megtekintése hívhat meg. Beolvasása őrzi meg ezeket az eseményeket egy [megbízható szótár](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections), amelynek van két konfigurációban meghatározni csonkolása házirend: **MaxStoredChaosEventCount** (alapértelmezett értéke 25000) és **StoredActionCleanupIntervalInSeconds** (alapértelmezett értéke 3600). Minden *StoredActionCleanupIntervalInSeconds* Chaos ellenőrzések és az összes, de a legutóbbi *MaxStoredChaosEventCount* események, a megbízható szótárból kiürítésekor.

## <a name="faults-induced-in-chaos"></a>A Chaos előidézett hibák
Chaos állít elő hibák teljes Service Fabric-fürt között, és tömöríti olyan néhány órát a hónap vagy év láthatók. Kihagyásos hibák a magas gyakorisága a kombinációja, amelyek egyébként kimaradhatnak esetekben keresi. Ebben a gyakorlatban az Chaos a szolgáltatást kód minőségének jelentős fejlesztéseket vezet.

Chaos kapott a következő kategóriákból hibák:

* A csomópont újraindítása
* Indítsa újra a központilag telepített kódcsomag
* Replika eltávolítása
* Indítsa újra a replikát
* Helyezze át egy elsődleges replika (konfigurálható)
* Helyezze át egy másodlagos másodpéldány (konfigurálható)

Chaos többszöri futtatja. Mindegyik iteráció hibák és a fürt ellenőrzése a megadott ideig áll. Beállíthatja, hogy a fürt stabilizálásához és érvényesítése sikeres töltött időt. Ha a fürt ellenőrzése hibát tartalmaz, a Chaos hoz létre, és továbbra is fennáll a ValidationFailedEvent az UTC-időbélyeg és a hiba részletei. Vegye figyelembe például három egyidejű hibák legfeljebb egy óráig futtatásához beállított Chaos példánya. Chaos három hibák kapott, és majd a fürt állapotát ellenőrzi. Az telepítéseket az előző lépést, amíg explicit módon a StopChaosAsync API-n keresztül leállt vagy egy óra továbbítja. Ha a fürt akkor kerül sérült állapotba, az ismétlések (Ez azt jelenti, hogy nem stabilizálását vagy nem lesz kifogástalan, az átadott MaxClusterStabilizationTimeout belül), Chaos egy ValidationFailedEvent állít elő. Az esemény azt jelzi, hogy valami nem megfelelő állapotba került, és előfordulhat, hogy további vizsgálat szükséges.

Ahhoz, hogy mely hibák Chaos előidézett, használhatja GetChaosReport API-t (powershell, a C# vagy REST). Az API-t a következő szegmensnél az átadott-a folytatási kód vagy átadott-időtartománya alapján Chaos jelentés lekérése. Megadhatja a következő szegmens Chaos jelentés beolvasandó ContinuationToken StartTimeUtc és EndTimeUtc időtartományt adhat meg, de nem adható meg mind a ContinuationToken, és az időtartományt az adott hívásban. Ha több mint 100 Chaos események, a Chaos jelentés eredmény abban az esetben szegmensek Ha a szegmens ne legyen nagyobb 100 Chaos események tartalmaz.

## <a name="important-configuration-options"></a>Fontos konfigurációs beállítások
* **TimeToRun**: teljes idő, hogy Chaos fut, mielőtt befejezné sikerrel. Le is Chaos előtt a StopChaos API-n keresztül futás TimeToRun időszakra.

* **MaxClusterStabilizationTimeout**: a fürt egy ValidationFailedEvent létrehozása előtt lett megfelelő várakozási idő maximális mennyisége. A Várakozás, hogy a fürt a terhelés csökkentése, amíg azt helyreállítja. Az elvégzett a következők:
  * Ha a fürt állapota rendben
  * Ha a szolgáltatás állapota rendben
  * Ha a cél replika méretének beállítása érhető el, a szolgáltatás partíció
  * Hogy létezik-e nem található InBuild replikák
* **MaxConcurrentFaults**: minden egyes ismétlés előidézett egyidejű hibák maximális száma. A nagyobb az érték, annál agresszív Chaos és a feladatátvétel és a a állapot átmenet kombinációja, amely a fürt végighalad összetettebbek is. 

> [!NOTE]
> Attól függetlenül történik hogyan nagy érték *MaxConcurrentFaults* rendelkezik, Chaos biztosítja, hogy - külső hibák - hiányában nincs kvórum elvesztése vagy adatvesztés.
>

* **EnableMoveReplicaFaults**: engedélyezheti vagy letilthatja az elsődleges vagy másodlagos replikák áthelyezése okozó hibák. Ezek a hibák alapesetben le vannak tiltva.
* **WaitTimeBetweenIterations**: ismétlések közti várakozási idő. Ez azt jelenti, hogy mennyi ideig Chaos felfüggeszti a ciklikus hibák, és hogy a megfelelő a fürt állapotának ellenőrzése befejeződött végrehajtása után. Minél nagyobb, az alsó érték az átlagos injektálási gyakorisága.
* **WaitTimeBetweenFaults**: egy egyetlen munkamenetben két egymást követő hibák közti várakozási idő. Minél nagyobb az értéke, annál kisebb a egyidejűségi beállítása pedig (vagy az átfedett) hibákat.
* **ClusterHealthPolicy**: fürt állapotházirend Between Chaos az ismétlés a fürt állapotának ellenőrzésére szolgál. Ha a fürt állapota hibás, vagy ha egy váratlan kivétel során hiba történik, Chaos a következő-állapotellenőrzése - arra, hogy ellássa a fürt egy ideig recuperate 30 percet várakozik.
* **A környezetben**: (karakterlánc, karakterlánc) gyűjteménye, írja be kulcs-érték párokat. A térkép Chaos futtatás adatainak rögzítésére is használható. Nem lehet 100-nál több ilyen párok, és minden karakterlánc (kulcs vagy érték), legfeljebb 4095 karakter hosszú lehet. Ez a térkép a Chaos tárolását a környezetben a meghatározott Futtatás futtassa az alapszintű állítja be.
* **ChaosTargetFilter**: Ez a szűrő cél Chaos hibákra csak egyes csomóponttípusok vagy csak egyes alkalmazáspéldányok használható. Ha nem használja a ChaosTargetFilter, Chaos hibákat entitásokhoz fürt. ChaosTargetFilter használata esetén a Chaos hibákat csak az entitásokat, amelyek megfelelnek a ChaosTargetFilter megadását. NodeTypeInclusionList és ApplicationInclusionList engedélyezése csak a "union" szemantikáját. Ez azt jelenti nincs lehetőség a NodeTypeInclusionList és ApplicationInclusionList metszetét adja meg. Például nincs lehetőség a adja meg a "hiba az alkalmazás csak akkor, ha be van kapcsolva, hogy csomóponttípus." Entitás NodeTypeInclusionList vagy ApplicationInclusionList szerepel, ha az entitás nem zárható ChaosTargetFilter használatával. Akkor is, ha applicationX ApplicationInclusionList nem jelenik meg, néhány Chaos munkamenetben applicationX is hibát jelez, mert akkor fordul elő, egy olyan csomópontján, amely megtalálható a NodeTypeInclusionList nodeTypeY lennie. Ha NodeTypeInclusionList és ApplicationInclusionList egyaránt null értékű vagy üres, egy ArgumentException vált ki.
    * **NodeTypeInclusionList**: csomóponttípusok foglalandó Chaos hibák listája. Hibák összes típusú (csomópont újraindítása, indítsa újra a codepackage, távolítsa el a replika, indítsa újra a replikát, áthelyezése elsődleges és másodlagos áthelyezése) a következő csomópont típusú csomópontok engedélyezve vannak. Ha a nodetype (azaz NodeTypeX) nem szerepel a NodeTypeInclusionList, akkor soha nem lesz engedélyezve NodeTypeX csomópontjait csomópont szintű hibák (például NodeRestart), de kód csomag és a replika hibák továbbra is engedélyezhető az NodeTypeX Ha az alkalmazás a ApplicationInclusionList NodeTypeX csomópontja elhelyezése történik. Legfeljebb 100 csomópont típusnevek tartalmazhat erre a listára, ezt a számot növelheti, a konfiguráció frissítése a MaxNumberOfNodeTypesInChaosTargetFilter konfigurációhoz szükség.
    * **ApplicationInclusionList**: az alkalmazás URI-azonosítók Chaos hibák foglalandó listáját. Az alkalmazások szolgáltatásokhoz tartozó összes replika replika hibák (újraindítás replika, távolítsa el a replika, áthelyezés elsődleges és áthelyezése másodlagos) Chaos által kezelhető. Chaos előfordulhat, hogy indítsa újra a kódcsomag, csak akkor, ha a kódcsomag üzemeltető replikái csak ezeket az alkalmazásokat. Ha egy alkalmazás nem jelenik meg ebben a listában, azt is továbbra is hibát jelez néhány Chaos munkamenetben, ha az alkalmazás fejeződik be, amely a NodeTypeInclusionList incuded csomópont típusú csomóponton. Azonban ha applicationX van kötve egy elhelyezési korlátozás és applicationX nodeTypeY hiányzik a ApplicationInclusionList és nodeTypeY hiányzik a NodeTypeInclusionList, majd applicationX soha nem hibát jelez majd. Legfeljebb 1000 alkalmazásnevek tartalmazhat erre a listára, ezt a számot növelheti, a konfiguráció frissítése a MaxNumberOfApplicationsInChaosTargetFilter konfigurációhoz szükség.

## <a name="how-to-run-chaos"></a>Chaos futtatása

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
            // During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
            // the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration. 
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
            // for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList, 
            // restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
            // even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy. 
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration. 
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
# the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
# for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
# even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
git 