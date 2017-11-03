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
ms.date: 08/09/2017
ms.author: motanv
ms.openlocfilehash: 3b3b93bc9ec5ecdcfc289e5b62e84de6aa4172ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>A Service Fabric-fürtök ellenőrzött Chaos idéz elő
Nagy méretű elosztott rendszerek, például a felhőalapú infrastruktúrák nem eredendően megbízhatóak. Az Azure Service Fabric lehetővé teszi a fejlesztők egy nem megbízható infrastruktúrán megbízható elosztott szolgáltatások írni. Egy nem megbízható infrastruktúrán robusztus elosztott szolgáltatások írni fejlesztők kell tesztelni szolgáltatásaik stabilitását, míg az alapul szolgáló megbízhatatlan infrastruktúra megy keresztül bonyolult Állapotváltások hibák miatt.

A [van, és a fürt Analysis Service](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (más néven a tartalék Analysis Services) segítségével a fejlesztők a hibák ellenőrzéséhez a szolgáltatások idéz elő. Ezek a megcélzott szimulált hibák, például [partíció újraindítása](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), segíthet a leggyakrabban használt Állapotváltások megadásával. Azonban célzott szimulált hibák definition vannak optimalizálva, és így nem teljesíti az hibák elhárítása, hogy másolatot csak a Állapotváltások rögzített előrejelzése, hosszúak és bonyolultak sorrendjét. Egy torzítatlan tesztelési Chaos is használhatja.

Chaos időszakos, kihagyásos hibák (szabályos és ungraceful) a fürt teljes szimulálja a hosszú idő alatt. Miután konfigurálta a Chaos sebessége és milyen típusú hibákat, megkezdheti a C# vagy Powershell API hibák létrehozása a fürt és a szolgáltatások indítására Chaos. Konfigurálhatja Chaos futtatásához egy megadott időszak (például egy órát), mely Chaos leállítása után automatikusan, vagy (C# vagy Powershell) bármikor leállítására StopChaos API hívása.

> [!NOTE]
> A jelenlegi formában az Chaos kapott csak biztonságos hibák, ami azt jelenti, hogy külső hibák hiányában a kvórum elvesztése vagy adatvesztés soha nem történik.
>

Chaos futtatása közben különböző események pillanatnyilag a Futtatás állapotát rögzítő hoz létre. Például egy ExecutingFaultsEvent tartalmaz, amelyek Chaos úgy döntött, hogy a munkamenetben végrehajtani minden hibájával. Egy ValidationFailedEvent érvényesítési hibája (állapotfigyelő és stabilitását hibát) a fürt ellenőrzése során talált részleteit tartalmazza. A GetChaosReport API (C# vagy Powershell) a Chaos futtatása jelentés megtekintése hívhat meg. Beolvasása őrzi meg ezeket az eseményeket egy [megbízható szótár](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections), amelynek van két konfigurációban meghatározni csonkolása házirend: **MaxStoredChaosEventCount** (alapértelmezett értéke 25000) és **StoredActionCleanupIntervalInSeconds** (alapértelmezett értéke 3600). Minden *StoredActionCleanupIntervalInSeconds* Chaos ellenőrzések és az összes, de a legutóbbi *MaxStoredChaosEventCount* események, a megbízható szótárból kiürítésekor.

## <a name="faults-induced-in-chaos"></a>A Chaos előidézett hibák
Chaos állít elő hibák teljes Service Fabric-fürt között, és tömöríti olyan néhány órát a hónap vagy év láthatók. Kihagyásos hibák a magas gyakorisága a kombinációja, amelyek egyébként kimaradhatnak esetekben keresi. Ebben a gyakorlatban az Chaos a szolgáltatást kód minőségének jelentős fejlesztéseket vezet.

Chaos kapott a következő kategóriákból hibák:

* A csomópont újraindítása
* Indítsa újra a központilag telepített kódcsomag
* Replika eltávolítása
* Indítsa újra a replikát
* Helyezze át egy elsődleges replika (konfigurálható)
* Helyezze át egy másodlagos másodpéldány (konfigurálható)

Chaos többszöri futtatja. Mindegyik iteráció hibák és a fürt ellenőrzése a megadott ideig áll. Beállíthatja, hogy a fürt stabilizálásához és érvényesítése sikeres töltött időt. Ha a fürt ellenőrzése hibát tartalmaz, a Chaos hoz létre, és továbbra is fennáll a ValidationFailedEvent az UTC-időbélyeg és a hiba részletei. Vegye figyelembe például három egyidejű hibák legfeljebb egy óráig futtatásához beállított Chaos példánya. Chaos három hibák kapott, és majd a fürt állapotát ellenőrzi. Az telepítéseket az előző lépést, amíg explicit módon a StopChaosAsync API-n keresztül leállt vagy egy óra továbbítja. Ha a fürt akkor kerül sérült állapotba, az ismétlések (Ez azt jelenti, hogy azt nem stabilizálásához belül a átadott MaxClusterStabilizationTimeout) Chaos egy ValidationFailedEvent állít elő. Az esemény azt jelzi, hogy valami nem megfelelő állapotba került, és előfordulhat, hogy további vizsgálat szükséges.

Ahhoz, hogy mely hibák Chaos előidézett, használhatja GetChaosReport API-t (powershell vagy C#). Az API-t a következő szegmensnél az átadott-a folytatási kód vagy átadott-időtartománya alapján Chaos jelentés lekérése. Megadhatja a következő szegmens Chaos jelentés beolvasandó ContinuationToken StartTimeUtc és EndTimeUtc időtartományt adhat meg, de nem adható meg mind a ContinuationToken, és az időtartományt az adott hívásban. Ha több mint 100 Chaos események, a Chaos jelentés eredmény abban az esetben szegmensek Ha a szegmens ne legyen nagyobb 100 Chaos események tartalmaz.

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
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

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

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

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
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

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
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
