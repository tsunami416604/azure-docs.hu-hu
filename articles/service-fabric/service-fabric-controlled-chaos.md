---
title: Káosz kiváltása a Service Fabric-fürtökben
description: Hibabefecskendezés és fürtelemzési szolgáltatás API-k használatával a fürtben lévő káosz kezeléséhez.
author: motanv
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 37b451abd0a519dff17aba9b2d6c42b4762f30cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463165"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Irányított káosz kiváltása a Service Fabric-fürtökben
A nagyméretű elosztott rendszerek, például a felhőalapú infrastruktúrák eredendően megbízhatatlanok. Az Azure Service Fabric lehetővé teszi a fejlesztők számára, hogy megbízható elosztott szolgáltatásokat írjanak egy megbízhatatlan infrastruktúrán. Ahhoz, hogy megbízható infrastruktúrára megbízható infrastruktúrára lehessen megbízható szolgáltatásokat írni, a fejlesztőknek képesnek kell lenniük a szolgáltatásaik stabilitásának tesztelésére, miközben a mögöttes megbízhatatlan infrastruktúra hibák miatt bonyolult állapotátmeneteken megy keresztül.

A [hibabefecskendezési és fürtelemzési szolgáltatás](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (más néven a hibaelemzési szolgáltatás) lehetővé teszi a fejlesztők számára, hogy hibákat indukáljanak a szolgáltatásaik teszteléséhez. Ezek a célzott szimulált hibák, például [egy partíció újraindítása,](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps)segíthetnek a leggyakoribb állapotátmenetek gyakorlásában. Azonban a célzott szimulált hibák definíció szerint elfogultak, és így hiányolhatják azokat a hibákat, amelyek csak nehezen megjósolható, hosszú és bonyolult állapotátmenetek sorozatában jelennek meg. Elfogulatlan teszteléshez használhatja a Chaos-t.

Chaos szimulálja időszakos, áthatárolt hibák (kecses és ungraceful) a fürt hosszabb ideig. A kecses hiba a Service Fabric API-hívások készletéből áll, például a replika-hiba újraindítása egy kecses hiba, mert ez egy közeli, amelyet egy replika megnyitása követ. Távolítsa el a replika, helyezze át az elsődleges replika, és mozgassa a másodlagos replika a többi kecses hibák által gyakorolt Chaos. A nem kecses hibák folyamatkilépések, például az újraindítási csomópont és az újraindítási kódcsomag. 

Miután konfigurálta a Chaos-t a sebességés a hibák használatával, elindíthatja a Chaos-t a C#, a Powershell vagy a REST API-n keresztül a fürtés a szolgáltatások hibáinak létrehozásához. Beállíthatja, hogy a Chaos egy adott időszakra (például egy órára) fusson, amely után a Chaos automatikusan leáll, vagy bármikor meghívhat StopChaos API-t (C#, Powershell vagy REST) annak leállításához.

> [!NOTE]
> Jelenlegi formájában a Chaos csak biztonságos hibákat idéz elő, ami azt jelenti, hogy külső hibák hiányában a kvórum elvesztése vagy az adatvesztés soha nem fordul elő.
>

AChaos futása közben különböző eseményeket hoz létre, amelyek rögzítik a futtatás jelenlegi állapotát. Például egy ExecutingFaultsEvent tartalmazza az összes hibát, amely chaos úgy döntött, hogy végre, hogy az iteráció. A ValidationFailedEvent a fürt érvényesítése során talált érvényesítési hiba (állapot- vagy stabilitási problémák) részleteit tartalmazza. Meghívhatja a GetChaosReport API-t (C#, Powershell vagy REST) a Chaos-futtatások jelentésének lehívásához. Ezek az események egy [megbízható szótárban](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections)maradnak meg, amely két konfiguráció által diktált csonkolási házirenddel rendelkezik: **MaxStoredChaosEventCount** (alapértelmezett érték: 25000) és **StoredActionCleanupIntervalInSeconds** (az alapértelmezett érték 3600). Minden *StoredActionCleanupIntervalInSeconds* Chaos-ellenőrzés és a legutóbbi *MaxStoredChaosEventCount* események kivételével minden törlődik a megbízható szótárból.

## <a name="faults-induced-in-chaos"></a>Hibák okozta Chaos
A káosz hibákat generál a teljes Service Fabric-fürtön, és néhány órán belül tömöríti a hónapokban vagy években látható hibákat. Az áthatárolt hibák és a magas hibaarány kombinációja olyan sarokeseteket talál, amelyek egyébként kimaradhatnak. A Chaos ezen gyakorlata a szolgáltatás kódminőségének jelentős javulásához vezet.

A káosz a következő kategóriák hibáit idézi elő:

* Csomópont újraindítása
* Telepített kódcsomag újraindítása
* Kópia eltávolítása
* Kópia újraindítása
* Elsődleges kópia áthelyezése (konfigurálható)
* Másodlagos replika áthelyezése (konfigurálható)

A káosz több ismétlésben fut. Minden iteráció hibákból és fürtérvényesítésből áll a megadott időszakra vonatkozóan. Beállíthatja, hogy a fürt stabilizálása és sikeres érvényesítése szükséges idő szükséges legyen. Ha hiba található a fürt érvényesítése, Chaos létrehoz, és megőrzi a ValidationFailedEvent az UTC időbélyeg és a hiba részleteit. Fontolja meg például a Chaos egy példányát, amely egy órára van beállítva, legfeljebb három egyidejű hibával. A káosz három hibát okoz, majd ellenőrzi a fürt állapotát. Végighalad az előző lépésen, amíg kifejezetten le nem állítja a StopChaosAsync API-n vagy egy órás fázisokon keresztül. Ha a fürt nem megfelelő állapotúvá válik bármely iterációban (azaz nem stabilizálódik, vagy nem lesz kifogástalan az átadott MaxClusterStabilizationTimeout-on belül), a Chaos létrehoz egy ValidationFailedEvent.If the cluster becomes unhealthy in any iterration (azaz iteráció does iteráció or itdoes does not stabilize or it does not become healthy within the passed-in MaxClusterStabilizationTimeout), Chaos létrehoz egy ValidationFailedEvent. Ez az esemény azt jelzi, hogy valami elromlott, és további vizsgálatra lehet szükség.

A Chaos által kiváltott hibák lehívásához használhatja a GetChaosReport API-t (powershell, C#vagy REST). Az API leadja a Chaos-jelentés következő szegmensét az átadott folytatási jogkivonat vagy az átadott időtartomány alapján. Megadhatja a ContinuationToken a Chaos jelentés következő szegmensének lekéréséhez, vagy megadhatja az időtartományt a StartTimeUtc és az EndTimeUtc keresztül, de nem adhatja meg mind a ContinuationToken-t, mind az időtartományt ugyanabban a hívásban. Ha több mint 100 Chaos-esemény van, a Chaos-jelentést olyan szegmensekben adja vissza, ahol egy szegmens legfeljebb 100 Chaos-eseményt tartalmaz.

## <a name="important-configuration-options"></a>Fontos konfigurációs lehetőségek
* **TimeToRun:** A Chaos teljes ideje, mielőtt sikeresen befejeződik. A StopChaos API-n keresztül leállíthatja a Chaos-t, mielőtt a TimeToRun időszakhoz futna.

* **MaxClusterStabilizationTimeout**: A fürt kifogástalan állapotúvá válása előtti maximális várakozási idő. Ez a várakozás a fürt betöltésének csökkentése a helyreállítás során. Az elvégzett ellenőrzések a következők:
  * Ha a fürt állapota rendben van
  * Ha a szolgáltatás állapota rendben van
  * Ha a célreplika-készlet mérete elérhető a szolgáltatáspartícióhoz
  * Nincsenek InBuild-replikák
* **MaxConcurrentFaults**: Az egyes ismétlésekben indukált egyidejű hibák maximális száma. Minél nagyobb a szám, annál agresszívebb a Káosz, és a feladatátvételek és a fürt által átmenő állapotátmenet-kombinációk is összetettebbek. 

> [!NOTE]
> Függetlenül attól, hogy milyen magas érték *maxConcurrentFaults* van, Chaos garantálja - külső hibák hiányában - nincs kvórum elvesztése vagy adatvesztés.
>

* **EnableMoveReplicaFaults**: Engedélyezi vagy letiltja azokat a hibákat, amelyek az elsődleges vagy másodlagos replikák áthelyezését okozzák. Ezek a hibák alapértelmezés szerint engedélyezve vannak.
* **WaitTimeBetweenIters**: Az ismétlések közötti várakozási idő. Ez azt jelenti, hogy a Chaos a hibák köre végrehajtása és a fürt állapotának megfelelő érvényesítése befejezése után szünetel. Minél magasabb az érték, annál alacsonyabb az átlagos hibabefecskendezési arány.
* **WaitTimeBetweenFaults**: Az az idő, amíg két egymást követő hiba között egyetlen iterációban várakozik. Minél nagyobb az érték, annál alacsonyabb a hibák egyidejűségi értéke (vagy átfedése).
* **ClusterHealthPolicy**: A fürt állapotházirendje a fürt állapotának ellenőrzésére szolgál a Chaos iterációk között. Ha a fürt állapota hibás, vagy ha egy váratlan kivétel történik a hiba végrehajtása során, chaos vár 30 percet, mielőtt a következő állapot-ellenőrzés - hogy a fürt egy kis időt a regenerálódás.
* **Környezet**: (karakterlánc, karakterlánc) típusú kulcs-érték párok gyűjteménye. A térkép segítségével rögzítheti a Chaos futással kapcsolatos információkat. Nem lehet több, mint 100 ilyen pár, és minden karakterlánc (kulcs vagy érték) legfeljebb 4095 karakter hosszú lehet. Ezt a térképet a Chaos run kezdője állítja be, hogy az adott futtatás környezetét tetszés szerint tárolja.
* **ChaosTargetFilter**: Ez a szűrő használható chaos hibák célzására csak bizonyos csomóponttípusokra, vagy csak bizonyos alkalmazáspéldányokra. Ha a ChaosTargetFilter nincs használva, a Chaos az összes fürtentitást hibát okoz. ChaosTargetFilter esetén a Chaos csak azokat az entitásokat hibáztatja, amelyek megfelelnek a ChaosTargetFilter specifikációnak. A NodeTypeInclusionList és az ApplicationInclusionList csak az egyesítő szemantikát engedélyezi. Más szóval nem lehet megadni a NodeTypeInclusionList és az ApplicationInclusionList csomópontot. Például nem lehet megadni "hiba ez az alkalmazás csak akkor, ha az adott csomópont típus." Ha egy entitás szerepel a NodeTypeInclusionList vagy az ApplicationInclusionList listán, az entitás nem zárható ki a ChaosTargetFilter használatával. Még akkor is, ha az applicationX nem jelenik meg ApplicationInclusionList, néhány Chaos iterációs applicationX lehet hibás, mert történetesen egy csomópont nodeTypeY, amely szerepel NodeTypeInclusionList. Ha mind a NodeTypeInclusionList, mind az ApplicationInclusionList értéke null vagy üres, argumentexception jelenik meg.
    * **NodeTypeInclusionList**: A Chaos-hibákközé beillesztő csomóponttípusok listája. Minden típusú hiba (újraindítás csomópont, újraindítás kódcsomag, távolítsa replika, újraindítás replika, áthelyezés elsődleges, és másodlagos áthelyezése) engedélyezve vannak a csomópontok ezen csomóponttípusok. Ha egy csomóponttípus (például NodeTypeX) nem jelenik meg a NodeTypeInclusionList listában, akkor a nodeTypeX csomópontjainak csomópontszintű hibái (például a NodeRestart) soha nem lesznek engedélyezve, de a kódcsomag és a replikahibák továbbra is engedélyezhetők a NodeTypeX-hez, ha a NodeTypeX alkalmazása továbbra is engedélyezhető Az ApplicationInclusionList történetesen a NodeTypeX csomópontján található. Legfeljebb 100 csomóponttípus szerepelhet ebben a listában, a szám növeléséhez a MaxNumberOfNodeTypesInChaosTargetFilter konfigurációhoz konfigurációs frissítés szükséges.
    * **ApplicationInclusionList**: A Chaos hibák közé foglalandó alkalmazás URI-k listája. Ezeknek az alkalmazásoknak a szolgáltatásaihoz tartozó összes replikahiba (replika újraindítása, replika eltávolítása, elsődleges áthelyezése és másodlagos áthelyezése) a Chaos által. A Chaos csak akkor indíthat újra egy kódcsomagot, ha a kódcsomag csak ezen alkalmazások replikáit üzemelteti. Ha egy alkalmazás nem jelenik meg ebben a listában, akkor is hibás lehet néhány Chaos iterációban, ha az alkalmazás a NodeTypeInclusionList listában szereplő csomóponttípus csomópontjára kerül. Ha azonban az applicationX az elhelyezési megkötéseken keresztül a nodeTypeY-hoz van kötve, és az applicationX hiányzik az ApplicationInclusionList list-ből, és a nodeTypeY hiányzik a NodeTypeInclusionList list-ből, akkor az applicationX soha nem lesz hibás. Legfeljebb 1000 alkalmazásnév szerepelhet a listában, hogy növelje ezt a számot, a MaxNumberOfApplicationsInChaosTargetFilter konfigurációhoz konfigurációs frissítés szükséges.

## <a name="how-to-run-chaos"></a>Hogyan kell futtatni Chaos

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
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
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
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
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
