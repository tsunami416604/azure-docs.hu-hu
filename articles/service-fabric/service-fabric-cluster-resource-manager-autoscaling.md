---
title: Az Azure Service Fabric automatikus méretezési szolgáltatások és tárolók |} Microsoft Docs
description: Az Azure Service Fabric automatikus skálázás szolgáltatások és a tárolókat házirendek megadását teszi lehetővé.
services: service-fabric
documentationcenter: .net
author: radicmilos
manager: ''
editor: nipuzovi
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: cd19c0e51ca1ac7863058d7c3944400719508f9b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="introduction-to-auto-scaling"></a>Az automatikus skálázás bemutatása
Automatikus skálázás be a Service Fabric egy további lehetőség a szolgáltatások alapján be, hogy a szolgáltatások jelentik, vagy az erőforrás-használata alapján dinamikusan méretezése. Automatikus skálázással biztosít kiváló a rugalmasság, és lehetővé teszi, hogy további példányok vagy a szolgáltatást az igény szerinti partíciók kiépítését. A teljes automatikus skálázás folyamat automatizált és átlátható, és a szolgáltatás-házirendek beállítása után nincs szükség kézi skálázási műveletek a szolgáltatás szintjén. Automatikus skálázással is be kell kapcsolni vagy a szolgáltatás-létrehozás időpontjában, vagy bármikor a szolgáltatás frissítésével.

Egy gyakori forgatókönyv, ahol az automatikus skálázás akkor hasznos, akkor, ha egy adott szolgáltatás terhelése időnként eltérő. Például egy szolgáltatást, mint például egy átjáró méretezheti alapján bejövő kérelmek kezeléséhez szükséges erőforrások mennyisége. Most vessen egy pillantást mi nézhet ki ezeket a méretezési szabályokat példát:
* Ha az átjáró összes példánya átlagosan használ kettőnél több mag, majd az átjárószolgáltatás kimenő méretezhető hozzáadja egy további példányt. Ehhez minden órában, de soha nem kell több mint hét példányok összesen.
* Ha az átjáró összes példánya átlagosan használ kisebb, mint 0,5 mag, majd a szolgáltatás méretezésére egy példány eltávolításával. Ehhez minden órában, de soha nem kell a 3-nál kevesebb példányok összesen.

Automatikus skálázással tárolók és a normál Service Fabric-szolgáltatások esetén támogatott. Ez a cikk többi a méretezési házirendek, engedélyezése vagy letiltása az automatikus skálázás, módszereket ismerteti, és ezzel a szolgáltatással kapcsolatos példákat.

## <a name="describing-auto-scaling"></a>Automatikus skálázással leíró
A Service Fabric-fürt minden egyes szolgáltatás automatikus skálázás házirendek meghatározása. Minden egyes méretezési házirend két részből áll:
* **Skálázás eseményindító** írja le, ha a szolgáltatás skálázás történik. Az eseményindító meghatározott feltételek meghatározni, ha a szolgáltatás méretezhető-e, vagy nem rendszeres időközönként ellenőrzi.

* **Skálázás mechanizmus** ismerteti, hogyan skálázás történik, amikor adatbázistáblába. Mechanizmus csak akkor érvényes, ha az eseményindító a feltételek teljesülnek.

Jelenleg támogatott indítók működik vagy [logikai terhelési metrika](service-fabric-cluster-resource-manager-metrics.md), vagy a CPU és memória kihasználtsága például fizikai metrikákat. Mindkét módszer esetén a Service Fabric a jelentett terhelési metrika figyeli, és rendszeres időközönként annak megállapításához, hogy szükséges-e a skálázás eseményindító kiértékelik.

Az automatikus skálázás jelenleg támogatott kétféle módszer van. Célja, hogy az állapotmentes szolgáltatásokhoz vagy a tárolókat, amelyen az automatikus skálázás végrehajtja a rendszer hozzáadásával vagy eltávolításával [példányok](service-fabric-concepts-replica-lifecycle.md). Állapot-nyilvántartó és a állapotmentes szolgáltatások esetén az automatikus skálázás is végrehajtható hozzáadásával vagy eltávolításával nevű [partíciók](service-fabric-concepts-partitioning.md) a szolgáltatást.

> [!NOTE]
> Jelenleg nincs támogatása csak egy méretezési házirend-szolgáltatás esetében.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Átlagos partíció terheléselosztási eseményindító példányok skálázás
Az első eseményindító típusú állapotmentes szolgáltatások partíció-példány betöltése alapul. Metrika terhelések vannak először Görbített szerezze be a partíció minden példánya a terhelés, és ezután program átlagolja az ezeket az értékeket a partíció minden példányára. Nincsenek három tényezőket, amelyek meghatározzák, hogy mikor lesznek méretezve, a szolgáltatás:

* _Alacsonyabb betöltési küszöb_ érték, amely meghatározza, hogy mikor lesz a szolgáltatás **a méretezhető**. Ha az átlagos terhelés szoftverpéldányok a partíciók száma nem éri el ezt az értéket, majd a szolgáltatás lesznek méretezve, a.
* _Felső betöltési küszöb_ érték, amely meghatározza, hogy mikor lesz a szolgáltatás **horizontálisan**. Ha az átlagos terhelés szoftverpéldányok a partíció nem éri el ezt az értéket, majd a szolgáltatás lesznek méretezve ki.
* _Méretezési időköz_ határozza meg, milyen gyakran kell ellenőrizni az eseményindító. Ha az eseményindító be van jelölve, skálázás van szüksége a mechanizmust alkalmazza a rendszer. Ha már nincs szükség a méretezés, semmilyen művelet kerül végrehajtásra. Mindkét esetben eseményindító nem kerül sor újra méretezési időköz újra lejárta előtt.

Ehhez az eseményindítóhoz csak állapotmentes szolgáltatásokhoz (állapot nélküli tárolók vagy a Service Fabric-szolgáltatások) használható. Abban az esetben, ha egy szolgáltatás több partíciókkal rendelkezik, az eseményindító értékeli ki a minden partíció külön-külön, és mindegyik partíció lesz a megadott mechanizmusa egymástól függetlenül alkalmazni kell. Ezért ebben az esetben is lehet, hogy néhány, a szolgáltatás a partíciók ki lesznek méretezve, néhány lesznek méretezve, a, és néhány nem lehet méretezni minden egy időben, a terhelés alapján.

Ehhez az eseményindítóhoz használható csak mechanizmust PartitionInstanceCountScaleMechanism. A mechanizmus alkalmazásának a módját meghatározó tényezők van:
* _Méretezhető biztonsági_ határozza meg, hány példányban hozzáadni vagy eltávolítani mechanizmus kiváltásakor.
* _Példányok maximális száma_ meghatározza a felső határ méretezéshez. Ha a partíció-példányok száma eléri a korlátot, majd a szolgáltatás fogja nem terjeszthető ki, függetlenül a terhelést. Hagyja ki ezt a határt a -1 érték megadásával, és ebben az esetben a szolgáltatás lesznek méretezve, amennyire csak lehet (a határérték érhetők el a fürt a csomópontok száma) kívül.
* _Minimális példányszám_ határozza meg az alsó határ méretezést. Ha a partíció-példányok száma eléri a korlátot, majd szolgáltatás fog nem méretezhető a függetlenül a terhelés.

## <a name="setting-auto-scaling-policy"></a>Az automatikus skálázás házirend beállítása

### <a name="using-application-manifest"></a>Az alkalmazásjegyzék használatával
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
### <a name="using-c-apis"></a>C# API-k használatával
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
### <a name="using-powershell"></a>Powershell-lel
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Átlagos szolgáltatás betöltési eseményindító partíció alapú skálázás
A második eseményindító a terhelés az összes partíció egy szolgáltatás alapul. Metrika terhelések először vannak Görbített beszerzése a terhelés az összes replika és a partíció példánya. Állapotalapú szolgáltatások esetén a partíció terheléselosztási tekinthető az elsődleges másodpéldány, betöltése közben állapotmentes szolgáltatások a terhelést a partíció az átlagos terhelés a partíció összes példányának. Ezeket az értékeket a szolgáltatás összes partíciójára program átlagolja, és az automatikus skálázás elindítani ezt az értéket használja. Egyezik a kérésben előző mechanizmus, határozható meg, hogy a szolgáltatás lesznek méretezve, három tényezőt van:

* _Alacsonyabb betöltési küszöb_ érték, amely meghatározza, hogy mikor lesz a szolgáltatás **a méretezhető**. Ha az átlagos terhelés az összes partíció a szolgáltatás nem éri el ezt az értéket, majd a szolgáltatás lesznek méretezve, a.
* _Felső betöltési küszöb_ érték, amely meghatározza, hogy mikor lesz a szolgáltatás **horizontálisan**. Ha az átlagos terhelés az összes partíció a szolgáltatás nem éri el ezt az értéket, majd a szolgáltatás lesznek méretezve ki.
* _Méretezési időköz_ határozza meg, milyen gyakran kell ellenőrizni az eseményindító. Ha az eseményindító be van jelölve, skálázás van szüksége a mechanizmust alkalmazza a rendszer. Ha már nincs szükség a méretezés, semmilyen művelet kerül végrehajtásra. Mindkét esetben eseményindító nem kerül sor újra méretezési időköz újra lejárta előtt.

Ehhez az eseményindítóhoz lehet állapot nélküli és állapotalapú szolgáltatással egyaránt használt. Ehhez az eseményindítóhoz használható csak mechanizmust AddRemoveIncrementalNamedParitionScalingMechanism. Amikor szolgáltatás kimenő méretezhető, majd egy új partíció kerül, és amikor a szolgáltatás valamelyik partíciókat méretezett törlődnek. Nincsenek korlátozásokat, amelyeket a rendszer ellenőrzi a szolgáltatás létrehozása vagy frissítése és a szolgáltatás létrehozása/frissítése sikertelen lesz, ha ezek a feltételek nem teljesülnek:
* A szolgáltatás elnevezett partícióséma kell használni.
* Kell lenniük egymást követő egész számok, például "0", "1",...
* Első partíció nevének kell lennie a "0".

Például egy szolgáltatás három partíciókkal rendelkező először hozza létre, ha a partíció neve csak érvényes lehetősége "0", "1" és "2".

A tényleges automatikus skálázás végzett művelet tiszteletben tartja a elnevezési sémája:
* Ha a szolgáltatás jelenlegi partíciók neve "0", "1" és "2", majd a partíció kiterjesztése a hozzáadott lesznek elnevezve "3".
* Ha a szolgáltatás jelenlegi partíciók neve "0", "1" és "2", a partíció el lesz távolítva az skálázás "2" nevű partíció.

Ugyanaz, mint a módszer, mely a hozzáadásával vagy eltávolításával a példány méretezése, ez az eljárás alkalmazásának a módját meghatározó három paramétert van:
* _Méretezhető biztonsági_ határozza meg, hány partíciók hozzáadni vagy eltávolítani mechanizmus kiváltásakor.
* _Maximális partíciószám_ meghatározza a felső határ méretezéshez. Ha a szolgáltatás partíciók száma eléri ezt a határt, majd a szolgáltatás fogja nem terjeszthető ki, függetlenül a terhelést. Hagyja ki ezt a határt a -1 érték megadásával, és ebben az esetben a szolgáltatás lesznek méretezve, amennyire csak lehet (a határérték a tényleges kapacitási a fürt) kívül.
* _Minimális példányszám_ határozza meg az alsó határ méretezést. Ha a szolgáltatás partíciók száma eléri ezt a határt, majd szolgáltatás fogja nem méretezhető a függetlenül a terhelést.

## <a name="setting-auto-scaling-policy"></a>Az automatikus skálázás házirend beállítása

### <a name="using-application-manifest"></a>Az alkalmazásjegyzék használatával
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedParitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API-k használatával
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedParitionScalingMechanism();
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
### <a name="using-powershell"></a>Powershell-lel
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedParitionScalingMechanism
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

## <a name="auto-scaling-based-on-resources"></a>Automatikus skálázással erőforrások alapján

Ahhoz, hogy az erőforrás-figyelő szolgáltatás méretezése tényleges erőforrások alapján

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Nincsenek két metrikákat, amelyek megfelelnek a tényleges fizikai erőforrásokat. Servicefabric őket egyik: / _CpuCores, amelyek tartalmazzák a tényleges cpu-használat (így 0,5 fele alapszintű jelöli), a másik alatt servicefabric: / _MemoryInMB, amely MB-ban memóriahasználatát.
A felhasználó szolgáltatások cpu és memória használatának követéséről ResourceMonitorService feladata. Ezt a szolgáltatást ahhoz, hogy a fiókot a potenciális rövid élettartamú igényeiben jelentkező veszítik súlyozott mozgóátlaga. Erőforrás-figyelés támogatott indexelése és a nem indexelése-alkalmazást a Windows és Linux indexelése állók közül. Automatikus skálázás erőforrások csak akkor engedélyezett, a szolgáltatások aktivált [kizárólagos folyamatmodell](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>További lépések
További információ [alkalmazás méretezhetőség](service-fabric-concepts-scalability.md).