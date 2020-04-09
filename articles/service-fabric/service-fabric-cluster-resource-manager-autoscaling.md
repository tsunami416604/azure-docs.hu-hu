---
title: Az Azure Service Fabric automatikus méretezési szolgáltatásai és tárolói
description: Az Azure Service Fabric lehetővé teszi, hogy automatikus skálázási szabályzatok szolgáltatások és tárolók.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: edcf2774873cc23a74a47cc1c9a12e2daa2ed419
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984537"
---
# <a name="introduction-to-auto-scaling"></a>Bevezetés az automatikus méretezésbe
Automatikus skálázás egy további képesség a Service Fabric dinamikusan skálázhatja a szolgáltatások a terhelés, amely a szolgáltatások jelentési, vagy az erőforrások használata alapján. Az automatikus skálázás nagy rugalmasságot biztosít, és lehetővé teszi a szolgáltatás további példányainak vagy partícióinak igény szerinti kiépítését. A teljes automatikus skálázási folyamat automatikus és átlátható, és miután beállította a szabályzatokat egy szolgáltatáson nincs szükség manuális skálázási műveletekre a szolgáltatás szintjén. Az automatikus skálázás a szolgáltatás létrehozásakor vagy bármikor bekapcsolható a szolgáltatás frissítésével.

Egy gyakori forgatókönyv, ahol az automatikus skálázás hasznos, ha a terhelés egy adott szolgáltatás idővel változik. Például egy szolgáltatás, például egy átjáró skálázható a bejövő kérelmek kezeléséhez szükséges erőforrások mennyisége alapján. Vessünk egy pillantást egy példa, hogy ezek a méretezési szabályok is néz ki:
* Ha az átjáró összes példánya átlagosan kettőnél több magot használ, akkor az átjárószolgáltatást egy példány hozzáadásával méretezheti. Ezt minden órában, de soha nem több, mint hét példány összesen.
* Ha az átjáró összes példánya átlagosan kevesebb mint 0,5 magot használ, akkor egy példány eltávolításával méretezheti a szolgáltatást. Ezt minden órában, de soha nem kevesebb, mint három példány összesen.

Automatikus skálázás támogatja a tárolók és a rendszeres Service Fabric-szolgáltatások. Az automatikus skálázás használatához a Service Fabric futtatótime-futásidejű 6.2-es vagy újabb verzióján kell futnia. 

A cikk további ismerteti a méretezési szabályzatok, az automatikus skálázás engedélyezésének vagy letiltásának módjait, és példákat ad a szolgáltatás használatára.

## <a name="describing-auto-scaling"></a>Automatikus méretezés leírása
Automatikus skálázási házirendek definiálhatók a Service Fabric-fürt minden egyes szolgáltatásához. Minden skálázási szabályzat két részből áll:
* **Méretezési eseményindító** ismerteti, ha a szolgáltatás méretezése kerül sor. Az eseményindítóban meghatározott feltételeket a rendszer rendszeresen ellenőrzi annak megállapítására, hogy egy szolgáltatást méretezni kell-e vagy sem.

* **Méretezési mechanizmus** leírja, hogyan lesz a méretezés, amikor aktiválódik. A mechanizmus csak akkor alkalmazható, ha az eseményindító feltételei teljesülnek.

A jelenleg támogatott eseményindítók [logikai terhelési metrikákkal](service-fabric-cluster-resource-manager-metrics.md)vagy fizikai metrikákkal, például a PROCESSZOR- vagy memóriahasználattal működnek. Akárhogy is, a Service Fabric figyeli a jelentett terhelésa a metrika, és rendszeresen kiértékeli az eseményindítót, hogy meghatározza, szükség van-e méretezésre.

Két mechanizmus, amely jelenleg támogatott automatikus skálázás. Az első állapotmentes szolgáltatásokhoz vagy olyan tárolókhoz van szánva, ahol az automatikus méretezés [példányok](service-fabric-concepts-replica-lifecycle.md)hozzáadásával vagy eltávolításával történik. Mind az állapotalapú, mind az állapotmentes szolgáltatások esetében az automatikus skálázás a szolgáltatás névvel ellátott [partícióinak](service-fabric-concepts-partitioning.md) hozzáadásával vagy eltávolításával is elvégezhető.

> [!NOTE]
> Jelenleg szolgáltatásonként csak egy skálázási szabályzat támogatott, és csak egy skálázási eseményindító skálázási szabályzatonként.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Átlagos partícióterhelés-eseményindító példányalapú méretezéssel
Az első típusú eseményindító egy állapotmentes szolgáltatáspartíció példányainak terhelésén alapul. Metrika terhelések először simított a terhelést a partíció minden példányát, és majd ezeket az értékeket átlagolt a partíció összes példánya között. Három tényező határozza meg, hogy mikor lesz a szolgáltatás méretezve:

* _Az alacsonyabb terhelési küszöbérték_ határozza meg, hogy a szolgáltatás mikor lesz **méretezve**a ban. Ha a partíciók összes példányának átlagos terhelése alacsonyabb, mint ez az érték, akkor a szolgáltatás lesz méretezve.
* _A felső terhelési küszöbérték_ egy olyan érték, amely meghatározza, hogy a szolgáltatás mikor lesz **kiskálázva.** Ha a partíció összes példányának átlagos terhelése magasabb, mint ez az érték, akkor a szolgáltatás horizontálisan ki lesz osztva.
* _A méretezési időköz_ határozza meg, hogy milyen gyakran lesz ellenőrizve az eseményindító. Miután az eseményindító be van jelölve, ha méretezésre van szükség, a rendszer alkalmazza a mechanizmust. Ha nincs szükség méretezésre, akkor a program nem történik művelet. Mindkét esetben az eseményindító nem lesz újra ellenőrizve, mielőtt a méretezési időköz újra lejár.

Ez az eseményindító csak állapotmentes szolgáltatásokkal (állapotnélküli tárolók vagy Service Fabric-szolgáltatások) használható. Abban az esetben, ha egy szolgáltatás több partíciót, az eseményindító minden partíció külön-külön értékeli ki, és minden partíció lesz a megadott mechanizmus tanaként alkalmazza. Ezért ebben az esetben lehetséges, hogy a szolgáltatás partícióinak egy része horizontálisan ki lesz méretezve, néhány at, és néhány at egyáltalán nem lesz méretezve egy időben, a terhelés alapján.

Az egyetlen mechanizmus, amely használható ezzel az eseményindítóval PartitionInstanceCountScaleMechanism. Három tényező határozza meg, hogyan alkalmazzák ezt a mechanizmust:
* _A Méretezési növekmény_ határozza meg, hogy hány példány kerül hozzáadásra vagy eltávolításra a mechanizmus aktiválásakor.
* _A maximális példányszám_ a méretezés felső határát határozza meg. Ha a partíció példányainak száma eléri ezt a korlátot, akkor a szolgáltatás nem lesz horizontálisfelskálázva, függetlenül a terheléstől. Ezt a korlátot a -1 érték megadásával is ki lehet hagyni, és ebben az esetben a szolgáltatás a lehető legnagyobb mértékben ki lesz skálázva (a korlát a fürtben elérhető csomópontok száma).
* _A minimális példányszám_ határozza meg a méretezés alsó határát. Ha a partíció példányainak száma eléri ezt a korlátot, majd a szolgáltatás nem lesz méretezve, függetlenül a terhelés.

## <a name="setting-auto-scaling-policy"></a>Automatikus skálázási házirend beállítása

### <a name="using-application-manifest"></a>Alkalmazásjegyzék használata
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API-k használata
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>A PowerShell használata
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Átlagos szolgáltatásterhelés-eseményindító partícióalapú méretezéssel
A második eseményindító egy szolgáltatás összes partíciójának terhelésén alapul. Metrika terhelések először simított a terhelést a partíció minden replika vagy példánya. Állapotalapú szolgáltatások esetén a partíció terhelése az elsődleges replika terhelésének minősül, míg az állapotmentes szolgáltatások esetében a partíció terhelése a partíció összes példányának átlagos terhelése. Ezek az értékek átlaga a szolgáltatás összes partícióján, és ez az érték az automatikus skálázás aktiválásához. Ugyanaz, mint az előző mechanizmusban, három tényező határozza meg, hogy mikor lesz a szolgáltatás méretezve:

* _Az alacsonyabb terhelési küszöbérték_ határozza meg, hogy a szolgáltatás mikor lesz **méretezve**a ban. Ha a szolgáltatás összes partíciójának átlagos terhelése alacsonyabb, mint ez az érték, akkor a szolgáltatás lesz méretezve.
* _A felső terhelési küszöbérték_ egy olyan érték, amely meghatározza, hogy a szolgáltatás mikor lesz **kiskálázva.** Ha a szolgáltatás összes partíciójának átlagos terhelése magasabb, mint ez az érték, majd a szolgáltatás horizontálisan kilesz osztva.
* _A méretezési időköz_ határozza meg, hogy milyen gyakran lesz ellenőrizve az eseményindító. Miután az eseményindító be van jelölve, ha méretezésre van szükség, a rendszer alkalmazza a mechanizmust. Ha nincs szükség méretezésre, akkor a program nem történik művelet. Mindkét esetben az eseményindító nem lesz újra ellenőrizve, mielőtt a méretezési időköz újra lejár.

Ez az eseményindító állapotalapú és állapotmentes szolgáltatásokkal is használható. Az egyetlen mechanizmus, amely használható ezzel az eseményindítóaddRemoveIncrementalNamedPartitionScalingMechanism. A szolgáltatás horizontális felskálázása, majd egy új partíció t, és amikor a szolgáltatás skálázva van az egyik meglévő partíciók törlődik. Vannak korlátozások, amelyek a szolgáltatás létrehozásakor vagy frissítésekor ellenőrizhetők, és a szolgáltatás létrehozása/frissítése sikertelen lesz, ha ezek a feltételek nem teljesülnek:
* A szolgáltatáshoz elnevezett partíciósémát kell használni.
* A partícióneveknek egymást követő egész számoknak kell lenniük, mint például "0", "1", ...
* Az első partíció nevének "0" -nak kell lennie.

Ha például egy szolgáltatás kezdetben három partícióval jön létre, a partíciónevek egyetlen érvényes lehetősége a "0", "1" és "2".

A tényleges automatikus skálázási művelet, amely et hajt végre, tiszteletben tartja ezt az elnevezési sémát is:
* Ha a szolgáltatás aktuális partícióinak neve "0", "1" és "2", akkor a horizontális felskálázáshoz hozzáadott partíció neve "3".
* Ha a szolgáltatás aktuális partícióinak neve "0", "1" és "2", akkor a skálázáshoz eltávolítandó partíció "2" nevű partíció.

A példányok hozzáadásával vagy eltávolításával méretezést használó mechanizmushoz hasonló három paraméter határozza meg a mechanizmus alkalmazásának módját:
* _A Méretezési növekmény_ határozza meg, hogy hány partíciót ad hozzá vagy távolít el a mechanizmus aktiválásakor.
* _A maximális partíciószám_ a skálázás felső határát határozza meg. Ha a szolgáltatás partícióinak száma eléri ezt a korlátot, majd a szolgáltatás nem lesz horizontálisfelskálázva, függetlenül a terheléstől. Ezt a korlátot a -1 érték megadásával is ki lehet hagyni, és ebben az esetben a szolgáltatás a lehető legnagyobb mértékben ki lesz skálázva (a korlát a fürt tényleges kapacitása).
* _A minimális példányszám_ határozza meg a méretezés alsó határát. Ha a szolgáltatás partícióinak száma eléri ezt a korlátot, majd a szolgáltatás nem lesz méretezve, függetlenül a terhelés.

> [!WARNING] 
> Ha az AddRemoveIncrementalNamedPartitionScalingMechanism állapotalapú szolgáltatásokkal van használva, a Service Fabric **értesítés vagy figyelmeztetés nélkül**adja hozzá vagy távolítja el a partíciókat. Az adatok újraparticionálása nem történik meg a méretezési mechanizmus aktiválásakor. Felskálázási művelet esetén az új partíciók üresek lesznek, és horizontális felskálázási művelet esetén **a partíció törlődik a benne lévő összes adattal együtt.**

## <a name="setting-auto-scaling-policy"></a>Automatikus skálázási házirend beállítása

### <a name="using-application-manifest"></a>Alkalmazásjegyzék használata
``` xml
<NamedPartition>
    <Partition Name="0" />
</NamedPartition>
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API-k használata
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>A PowerShell használata
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Erőforrásokon alapuló automatikus méretezés

Annak érdekében, hogy az erőforrás-figyelő szolgáltatás a tényleges erőforrások alapján méretezhető

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Két mérőszám, amely a tényleges fizikai erőforrásokat jelöli. Ezek közül az egyik a servicefabric:/_CpuCores, amely a tényleges cpu-használatot jelöli (így a 0.5 egy fél mag) és a másik a servicefabric:/_MemoryInMB, amely az MB-k memóriahasználatát jelöli.
A ResourceMonitorService felelős a felhasználói szolgáltatások processzor- és memóriahasználatának nyomon követéséért. Ez a szolgáltatás súlyozott mozgóátlagot alkalmaz a lehetséges rövid élettartamú csúcsok figyelembevétele érdekében. Az erőforrás-figyelés a Windows és a Linuxon lévő tárolók számára egyaránt támogatott a tárolóba helyezett és a nem konténeres alkalmazások esetében. Az erőforrások automatikus méretezése csak az [exkluzív folyamatmodellben](service-fabric-hosting-model.md#exclusive-process-model)aktivált szolgáltatásokesetében engedélyezett.

## <a name="next-steps"></a>További lépések
További információ az [alkalmazások méretezhetőségéről.](service-fabric-concepts-scalability.md)
