---
title: A Service Fabric-fürtök idéz elő a Chaos |} A Microsoft Docs
description: Tartalék injektálással vagy a fürt Analysis Service API-k használatával káosz kezelésére a fürtben.
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: a1b334b34e8e234d9ce5cc5ad5cd77bf5ba7118c
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555523"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Szabályozott káosz a Service Fabric-fürtök idéz elő
Nagy méretű elosztott rendszerek, a felhőalapú infrastruktúrák rendszer természetüknél fogva nem megbízható. Az Azure Service Fabric lehetővé teszi a fejlesztők számára, hogy írja felett egy nem megbízható infrastruktúra megbízható elosztott szolgáltatásokat. Egy nem megbízható infrastruktúra épülő hatékony elosztott szolgáltatások írását, fejlesztőknek kell tesztelheti a szolgáltatásaik stabilitását, míg az alapul szolgáló megbízhatatlan infrastruktúra korszakát bonyolult állapotváltásra hibák miatt.

A [hibabeszúrás és elemzési szolgáltatás](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (más néven a Fault Analysis Service) lehetővé teszi a fejlesztők az idéz elő hibákat szolgáltatásaik teszteléséhez. Megcélzott ezeket a hibákat, például szimulált [partíció újraindítása](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), gyakorolja a leggyakoribb állapotváltásra segítségével. Azonban célzott szimulált hibák definíció szerint vannak torzítatlan, és így előfordulhat, hogy hagyja ki hibák elhárítása a show mentése csak az állapotváltozási adat áramlik nehéz megjósolni, hosszúak és bonyolultak sorrendjét. Egy torzítatlan populáció tesztelési káosz is használhatja.

A Chaos időszakos, kihagyásos hibák (szabályos és végbemenjen) terjesszen a fürtön keresztül hosszabb ideig szimulálja. Egy szabályos tartalék Service Fabric API-hívások készlete áll, például újraindítás replika tartalék-e egy szabályos tartalék mivel ez egy nyitott követ egy replikát a Bezárás gombra. Távolítsa el a replika, helyezze át az elsődleges replika, és áthelyezése másodlagos replika a más, a Chaos gyakorolja sikeres-e hibák. A hibák végbemenjen folyamat kilép, indítsa újra a csomópontot, majd indítsa újra a kódcsomag. 

Miután konfigurálta a Chaos sebessége és milyen típusú hibákat, elindíthatja a Chaos – C#, Powershell vagy a REST API létrehozása a hibák, a fürt és a szolgáltatások elindításához. Konfigurálható káosz szeretné futtatni a megadott időtartam során (például óránként), mely káosz leállítása után automatikusan, vagy (C#, Powershell vagy REST) bármikor leállíthatja a StopChaos API hívása.

> [!NOTE]
> A jelenlegi formájában az káosz kapott csak biztonságos hibák, ami azt jelenti, hogy külső hibák hiányában a kvórum elvesztése vagy adatvesztés soha nem történik.
>

A Chaos futása közben a különböző események abban a pillanatban a Futtatás állapotát rögzítő hoz létre. Például egy ExecutingFaultsEvent tartalmaz minden hibájával együtt, amely a Chaos úgy döntött, hogy hajtsa végre a, hogy az iteráció. Egy ValidationFailedEvent egy érvényesítési hiba (állapot vagy a stabilitás problémák), amely a fürt érvényesítése során található részleteit tartalmazza. A getchaosreport hívásban API (C#, a Powershell vagy REST) a jelentés megtekintése a Chaos futtatások hívhat meg. Ezek az események beolvasása megmaradjon az egy [megbízható szótárban](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), amely rendelkezik egy csonkolása házirend által rögzített két konfigurációt: **MaxStoredChaosEventCount** (alapértelmezett érték a 25000) és **StoredActionCleanupIntervalInSeconds** (alapértelmezett értéke 3600). Minden *StoredActionCleanupIntervalInSeconds* káosz ellenőrzések és az összes, de a legutóbbi *MaxStoredChaosEventCount* események, a megbízható szótárban a rendszer üríti ki.

## <a name="faults-induced-in-chaos"></a>A Chaos okozta hibák
A Chaos állít elő, a hibák a teljes Service Fabric-fürtön, és tömöríti, órákat, hónap vagy év láthatók a hibák. Összefűzéses hibák a magas laphibák aránya együttes használata, amelyek egyébként nem észlelnének esetekben keresi meg. Ebben a gyakorlatban a Chaos vezet a kód minőségének a jelentős fejlesztéseket tartalmaz.

A Chaos kapott hibákat a következő kategóriákban:

* Csomópont újraindítása
* Indítsa újra a központilag telepített kódcsomag
* Replika eltávolítása
* Indítsa újra a replika
* Helyezze át egy elsődleges replika (konfigurálható)
* Helyezze át egy másodlagos másodpéldány (konfigurálható)

A Chaos több iterációban futtatja. Minden egyes ismétléskor hibák és a megadott időszakon belül a fürtellenőrzés áll. Beállíthatja, hogy az idő stabilizálódhatnak a fürt és az érvényesítés sikeres legyen. Ha hiba található a fürt ellenőrzését, a Chaos állít elő, és továbbra is fennáll egy ValidationFailedEvent az UTC-időbélyeg és a hiba részletei. Vegyük példaként, amely három egyidejű hibák, mely legfeljebb egy órát futtatása Chaos egy példánya. A Chaos három hibákat kapott, és fürt állapotát ellenőrzi. Halad az előző lépést addig, amíg explicit módon leállt a StopChaosAsync API-n keresztül vagy óránként továbbítja. Ha a fürt akkor kerül sérült, az ismétlések (, nem stabilizálódhatnak vagy nem lesz kifogástalan állapotú, az átadott MaxClusterStabilizationTimeout belül), a Chaos egy ValidationFailedEvent állít elő. Az esemény azt jelzi, hogy probléma merült fel, és előfordulhat, hogy további vizsgálat szükséges.

Melyik káosz okozta hibák lekéréséhez használhatja a getchaosreport hívásban API (powershell, a C# vagy REST). Az API lekérdezi a következő szegmenst a Chaos jelentés, az átadott a folytatási token vagy az átadott-időtartomány alapján. Megadhatja a continuationtoken argumentumot használja, a következő szegmenst a Chaos jelentés beolvasni vagy megadhatja az időtartományt StartTimeUtc és EndTimeUtc, de nem adható meg mind a continuationtoken argumentumot használja, és az időtartományt az adott hívásban. Ha több mint 100 káosz események, a Chaos jelentés adja vissza a szegmensek Ha egy szegmens tartalmazza: 100-nál több káosz események.

## <a name="important-configuration-options"></a>Fontos konfigurációs beállításai
* **Timetorun érték**: Teljes futásakor a káosz, sikeres befejezése előtt. A Chaos előtt futott a timetorun érték időszakra vonatkozóan a StopChaos API-n keresztül is leállíthatja.

* **MaxClusterStabilizationTimeout**: A maximális mennyisége, és várja meg a fürt előtt egy ValidationFailedEvent előállító állapotúak lesznek. Ezt várja meg, hogy a fürt terhelésének csökkentése közben helyreállítása folyamatban van. Az elvégzett a következők:
  * Ha a fürt állapota OK
  * Ha a szolgáltatás állapota OK
  * Ha a cél replika méretének beállítása érhető el a szolgáltatás partíció
  * Hogy létezik-e nem található InBuild replikák
* **MaxConcurrentFaults**: Minden egyes ismétléskor előidézett egyidejű hibák maximális száma. A magasabb az érték, annál több agresszív káosz és a feladatátvételeket, és az állapot átmenet kombinációit, amely végighalad a fürt összetettebbek is. 

> [!NOTE]
> Függetlenül attól a nagy értékű *MaxConcurrentFaults* rendelkezik, a Chaos garantálja a – külső hibákat – hiányában nem kvórum elvesztése vagy adatvesztés.
>

* **EnableMoveReplicaFaults**: Engedélyezheti vagy letilthatja a hibákat, amelyek az elsődleges vagy másodlagos replikák áthelyezése. Ezek a hibák alapértelmezés szerint engedélyezve vannak.
* **WaitTimeBetweenIterations**: Az ismétlések közötti várakozási idő mennyisége. Azt jelenti mennyi ideig káosz szüneteltetni fogja a hibák és kellene a fürt állapota megfelelő érvényesítése befejeződött egy ciklikus végrehajtása után. A magasabb, az alacsonyabb érték átlagos tartalék injektálási sebessége.
* **WaitTimeBetweenFaults**: Az egyetlen ismétléskor két egymást követő hibák közötti várakozás időtartama. A magasabb az érték, az alacsonyabb az egyidejűségi (vagy az átfedett) hibákat.
* **ClusterHealthPolicy**: Fürt állapotházirend segítségével káosz ismétléseinek között a fürt állapotának ellenőrzése. Ha a fürt állapota hibás, vagy ha váratlan kivétel történt a tartalék végrehajtás során történik, a Chaos Várjon 30 percet, mielőtt a következő állapot-ellenőrzés – recuperate hosszabb időt biztosít a fürt.
* **Környezet**: Gyűjteménye (karakterlánc, karakterlánc), írja be a kulcs-érték párokat. A térkép segítségével rögzíti a Chaos Futtatás kapcsolatos információt. Nem lehet több mint 100 ilyen párok, és mindegyik sztring (kulcs vagy érték), legfeljebb 4095 karakter hosszú lehet. Ezen a térképen az alapszintű, a káosz, futtassa a környezetben a meghatározott Futtatás tárolását állítja be.
* **– Egyidejűleg**: Ez a szűrő segítségével cél káosz hibák csak egyes csomóponttípusok vagy csak egyes alkalmazáspéldányok. – Egyidejűleg nem használatos, ha a Chaos hibákat a fürt összes entitást. – Egyidejűleg használata esetén a Chaos hibákat csak azokat az entitásokat, amelyek megfelelnek a – egyidejűleg specifikációnak. A NodeTypeInclusionList is és az ApplicationInclusionList engedélyezése csak a union szemantikáját. Más szóval nincs lehetőség a NodeTypeInclusionList is és az ApplicationInclusionList metszetét adja meg. Ha például nincs lehetőség adja meg a "hiba az alkalmazás csak akkor, ha be van kapcsolva a csomópont típusa." Miután egy entitás tartalmazza a NodeTypeInclusionList vagy ApplicationInclusionList, entitás nem zárható ki – egyidejűleg használatával. Akkor is, ha az ApplicationInclusionList applicationX nem jelenik meg, néhány káosz ismétléskor applicationX is hibás történik a kell egy olyan csomópontján, amely megtalálható a NodeTypeInclusionList nodeTypeY, mert. Ha a NodeTypeInclusionList is és az ApplicationInclusionList is null értékű vagy üres, akkor egy ArgumentException lépett fel.
    * **A NodeTypeInclusionList**: Hibák a Chaos foglalandó típusok listája. Minden típusú hibák (csomópont újraindítása, indítsa újra a codepackage, távolítsa el a replika, indítsa újra a replikát, helyezze át az elsődleges és másodlagos áthelyezése) a következő csomópont típusú csomópontok engedélyezve vannak. Ha egy NodeType csomóponttípus (például NodeTypeX) nem jelenik meg a NodeTypeInclusionList majd soha nem lesznek engedélyezve NodeTypeX csomópontjait csomópont szolgáltatói hibák (például NodeRestart), de a kód csomag és a replika hibákat továbbra is engedélyezhető a NodeTypeX, ha az alkalmazás a ApplicationInclusionList történik a NodeTypeX csomópontján találhatók. Legfeljebb 100 csomópont típusnevek felvehetők a listában, ezt a számot növelheti, a konfiguráció frissítése a MaxNumberOfNodeTypesInChaosTargetFilter konfigurációhoz szükség.
    * **ApplicationInclusionList**: A kérelem URI-k hibák Chaos foglalandó listája. Ezekhez az alkalmazásokhoz, szolgáltatásokhoz tartozó összes replika replika hibák (újraindítás replika, távolítsa el a replika, áthelyezés elsődleges és áthelyezése másodlagos) káosz által kezelhető. A Chaos előfordulhat, hogy indítsa újra a egy kódcsomaghoz csak akkor, ha a kódcsomag replikák csak ezeket az alkalmazásokat futtatja. Ha egy alkalmazás nem jelenik meg ebben a listában, azt is továbbra is hibás néhány káosz ismétléskor, ha az alkalmazás egy olyan csomópontján, a csomópont típusa, amely megtalálható a NodeTypeInclusionList is említi. Azonban ha applicationX kötődik elhelyezési korlátozások és applicationX nodeTypeY hiányzik az ApplicationInclusionList és nodeTypeY hiányzik a NodeTypeInclusionList, majd applicationX soha nem hibás lesz. Legfeljebb 1000 alkalmazásnevek felvehetők a listában, ezt a számot növelheti, a konfiguráció frissítése a MaxNumberOfApplicationsInChaosTargetFilter konfigurációhoz szükség.

## <a name="how-to-run-chaos"></a>A Chaos futtatása

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
