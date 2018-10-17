---
title: Az Azure Service Fabric az automatikus skálázási szolgáltatások és tárolók |} A Microsoft Docs
description: Az Azure Service Fabric lehetővé teszi, hogy automatikus méretezési szolgáltatások és a tárolók szabályzatok beállítását.
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
ms.openlocfilehash: fbaf6b92a2605d284a749365d542c223e09f730d
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362602"
---
# <a name="introduction-to-auto-scaling"></a>Automatikus skálázás bemutatása
Automatikus skálázás egy további lehetőség a Service Fabric dinamikusan méretezheti az services szolgáltatásokat jelent, vagy az erőforrások használat alapján a terhelés alapján. Automatikus skálázás nagy rugalmasságot biztosít, és lehetővé teszi, hogy további példányok vagy a partíciók az igény szerinti szolgáltatás kiépítése. A teljes automatikus skálázást folyamat automatizált és átlátható, és a szabályzatok a szolgáltatás beállítása után esetén nem kell a szolgáltatási szintű manuális méretezési műveletekhez. Automatikus skálázás is be kell kapcsolni vagy szolgáltatás-létrehozás időpontjában, vagy bármikor a szolgáltatás frissítésével.

Egy gyakori forgatókönyv, ahol automatikus skálázást hasznos akkor, ha a idővel változik egy adott szolgáltatás terhelését. Például egy szolgáltatás, mint például az átjáró méretezhetők alapján a bejövő kérelmek kezeléséhez szükséges erőforrások mennyiségét. Vessünk egy pillantást egy példát, hogy milyen nézhet ki ezeket a skálázási szabályokat:
* Ha az átjáró összes példánya átlagosan több mint két magot használ, majd ki az átjáró szolgáltatás hozzáadásával méretezhető egy további példányt. Ehhez minden órában, de soha ne legyen több mint hét példányok összesen.
* Ha saját átjáró összes példánya használ a átlagosan kevesebb mint 0,5 mag, majd a szolgáltatás a méretezhető egy példány eltávolításával. Ehhez minden órában, de soha ne legyen kevesebb mint három példányban összesen.

Automatikus skálázás tárolók és a Service Fabric-szolgáltatások rendszeres is támogatott. Ez a cikk további részének a skálázási szabályzattal, engedélyezése vagy letiltása az automatikus skálázás, módszereket ismerteti, és példákkal Ez a funkció használatához.

## <a name="describing-auto-scaling"></a>Ismertető az automatikus skálázás
A Service Fabric-fürt minden egyes szolgáltatás automatikus skálázást házirendeket lehet definiálni. Minden egyes méretezési szabályzat két részből áll:
* **Skálázás eseményindító** írja le, amikor a szolgáltatás méretezése hajt végre. Az eseményindító a meghatározott feltételek meghatározni, ha egy szolgáltatás legyen horizontálisan le- vagy nem rendszeres időközönként ellenőrzi.

* **Skálázási mechanizmus** ismerteti, hogyan skálázás történik aktiválásakor. Mechanizmus csak akkor érvényes, ha a trigger által a feltételek teljesülnek.

Összes eseményindító által jelenleg támogatott működnek a [logikai terhelési mérőszámok](service-fabric-cluster-resource-manager-metrics.md), vagy a fizikai, például a CPU és memória használati metrikákkal. Mindkét módszer esetén a Service Fabric figyelni fogja a metrika a jelentett terhelés, és az eseményindító határozza meg, hogy szükség van-e a skálázás, rendszeresen kiértékelik.

Nincsenek kétféle által jelenleg támogatott automatikus skálázás. Az első egy kifejezés az állapotmentes szolgáltatások esetében vagy for containers szolgáltatásban, ahol az automatikus skálázás történik hozzáadásával vagy eltávolításával [példányok](service-fabric-concepts-replica-lifecycle.md). A egyaránt állapotalapú és állapotmentes szolgáltatások esetében az automatikus skálázás is végrehajtható hozzáadásával vagy eltávolításával nevű [partíciók](service-fabric-concepts-partitioning.md) a szolgáltatás.

> [!NOTE]
> Jelenleg nincs szolgáltatásonként csak egy méretezési szabályzat támogatása.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Átlagos partíció terhelés eseményindító-alapú példányok skálázása
Az első típusú trigger állapotmentes szolgáltatás partíció-példánya a terhelés alapján történik. Metrika terhelések vannak először Görbített beszerzése a terhelés egy partíció minden példányát, és majd ezeket az értékeket átlagolja az a partíció összes példányát. Nincsenek mindhárom tényezőt, amelyek meghatározzák, hogy mikor lesz skálázva a szolgáltatást:

* _Alsó betöltési küszöb_ érték, amely azt határozza meg, ha a szolgáltatás lesz **skálázva**. Ha az átlagos terhelés szoftverpéldányok a partíciók száma nem éri el ezt az értéket, majd a szolgáltatás a program átméretezi a.
* _Felső betöltési küszöb_ érték, amely azt határozza meg, ha a szolgáltatás lesz **horizontálisan felskálázott**. Ha magasabb, mint ezt az értéket partíció összes példány átlagos terhelés, majd a szolgáltatás fogja terjeszthető ki.
* _Méretezési időközhöz_ határozza meg, hogy milyen gyakran kell ellenőrizni az eseményindító. Amint az eseményindító be van jelölve, ha szükség van a méretezés a mechanizmus lépnek érvénybe. Ha már nincs szükség a méretezés, semmilyen művelet nem lesz végrehajtva. Mindkét esetben az eseményindító nem kerül sor újra méretezési időközhöz újra lejárta előtt.

Ez az eseményindító csak állapotmentes szolgáltatások (állapotmentes tárolók vagy Service Fabric-szolgáltatások) használható. Abban az esetben, ha egy szolgáltatás több partícióval rendelkezik, az eseményindító abban az esetben minden partíció esetében külön-külön, és mindegyik partíció egymástól függetlenül alkalmazza, a megadott mechanizmus lesz. Ezért ebben az esetben is lehet, hogy a szolgáltatás a partíciók némelyike ki lesz skálázva, néhány lesz skálázva a, és néhány nem kell méretezni minden egyszerre, a terhelés alapján.

A csak mechanizmus, amely erre az eseményindítóra használható PartitionInstanceCountScaleMechanism. Nincsenek mindhárom tényezőt, amelyek meghatározzák, hogyan kell alkalmazni ezt a mechanizmust:
* _Növekmény méretezése_ meghatározza, hogy hány példányt fog hozzáadásának vagy eltávolításának mechanizmus aktiválásakor.
* _Példányok maximális száma_ határozza meg a felső határ méretezését. Ha a partíció-példányok száma eléri a korlátot, majd a szolgáltatás fogja nem terjeszthető ki, függetlenül a terhelés. Hagyja ki ezt a korlátot a -1 érték megadásával, és ebben az esetben a szolgáltatás lesz skálázva vette a lehető legnagyobb mértékben (a korlátot, akkor elérhető, a fürtben található csomópontok számát) lehetőség.
* _Minimális példányszám_ határozza meg az alsó határ méretezését. Ha a partíció-példányok száma eléri a korlátot, majd szolgáltatás fog nem növelhető a függetlenül a terhelés.

## <a name="setting-auto-scaling-policy"></a>Az automatikus skálázási szabályzat beállítása

### <a name="using-application-manifest"></a>Alkalmazásjegyzék használatával
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Átlagos szolgáltatás betöltési eseményindító-alapú partícióskálázás
A második eseményindító a terhelés az egyik szolgáltatás összes partíció alapul. Metrika terhelések először szerezze be a terhelés minden replika és a egy partíció példánya van Görbített. Az állapotalapú szolgáltatások esetében a terhelést a partíció tekinthető a terhelés, az elsődleges másodpéldány, amíg az állapotmentes szolgáltatások esetében a terhelést a partíció a partíció összes példány átlagos terhelés. Ezek az értékek vannak átlagolja az a szolgáltatás összes partíció, és ez az érték használható az automatikus skálázás aktiválásához. Egyezik a kérésben előző mechanizmust, mindhárom tényezőt, amelyek meghatározzák, amikor a program átméretezi a szolgáltatás van:

* _Alsó betöltési küszöb_ érték, amely azt határozza meg, ha a szolgáltatás lesz **skálázva**. Ha az átlagos terhelés, a szolgáltatás összes partíció nem éri el ezt az értéket, majd a szolgáltatás lesz skálázva a.
* _Felső betöltési küszöb_ érték, amely azt határozza meg, ha a szolgáltatás lesz **horizontálisan felskálázott**. Ha az átlagos terhelés, a szolgáltatás összes partíció nagyobb, mint ezt az értéket, majd a szolgáltatás fogja terjeszthető ki.
* _Méretezési időközhöz_ határozza meg, hogy milyen gyakran kell ellenőrizni az eseményindító. Amint az eseményindító be van jelölve, ha szükség van a méretezés a mechanizmus lépnek érvénybe. Ha már nincs szükség a méretezés, semmilyen művelet nem lesz végrehajtva. Mindkét esetben az eseményindító nem kerül sor újra méretezési időközhöz újra lejárta előtt.

Ez az eseményindító lehet használt mindkettőt, állapotalapú és állapotmentes szolgáltatások. A csak mechanizmus, amely erre az eseményindítóra használható AddRemoveIncrementalNamedPartitionScalingMechanism. Szolgáltatás horizontálisan felskálázott, majd egy új partíciót hozzáadásakor, illetve egy meglévő partíciók méretezett szolgáltatás törlődik. Szolgáltatás létrehozásakor vagy frissítésekor és a szolgáltatás létrehozása és frissítése sikertelen lesz, ha ezek a feltételek nem teljesülnek ellenőrzött korlátozások vonatkoznak:
* A szolgáltatás elnevezett partícióséma kell használni.
* Kell állnia egymást követő egész számok, mint "0", "1",...
* Első partíció neve "0" kell lennie.

Például ha egy szolgáltatás három partícióval rendelkező már létrehozták, partíciónevek csak érvényes lehetőségét, "0", "1" és "2".

A tényleges automatikus skálázást a végrehajtandó műveletet, valamint az elnevezési sémája veszi figyelembe:
* Ha a szolgáltatás aktuális partíciókat neve "0", "1" és "2", majd, amely belekerül a horizontális felskálázás a partíció neve lesz "3".
* Ha az aktuális partíciókat a szolgáltatás neve "0", "1" és "2", a partíció, amely el lesz távolítva az skálázás az "2" nevű partíció.

Azonos mechanizmust, amely használja a példányok hozzáadását és eltávolítását skálázása a paramétereket, amelyek meghatározzák, hogyan kell alkalmazni ezt a mechanizmust van:
* _Növekmény méretezése_ határozza meg, hány partíciók fog hozzáadásának vagy eltávolításának mechanizmus aktiválásakor.
* _Maximális partíciószám_ határozza meg a felső határ méretezését. Ha a szolgáltatás a partíciók száma eléri ezt a határt, majd a szolgáltatás fog nem terjeszthető ki, függetlenül a terhelés. Hagyja ki ezt a korlátot a -1 érték megadásával, és ebben az esetben a szolgáltatás lesz skálázva vette a lehető legnagyobb mértékben (a korlátot, akkor a fürt a tényleges kapacitási) lehetőség.
* _Minimális példányszám_ határozza meg az alsó határ méretezését. Ha a szolgáltatás a partíciók száma eléri ezt a korlátot, majd szolgáltatás fogja nem növelhető a függetlenül a terhelés.

> [!WARNING] 
> AddRemoveIncrementalNamedPartitionScalingMechanism használata az állapotalapú szolgáltatások esetén a Service Fabric hozzáadása, vagy távolítsa el a partíciók **értesítési hiba vagy figyelmeztetés nélkül**. Az adatok újraparticionálása nem történik méretezés mechanizmus aktiválásakor. Abban az esetben a vertikális felskálázási művelete, új partíció üres lesz, és vertikális leskálázási művelet esetén **partíció és a benne található összes adat törlődik**.

## <a name="setting-auto-scaling-policy"></a>Az automatikus skálázási szabályzat beállítása

### <a name="using-application-manifest"></a>Alkalmazásjegyzék használatával
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API-k használatával
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
### <a name="using-powershell"></a>Powershell-lel
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

## <a name="auto-scaling-based-on-resources"></a>Automatikus skálázás erőforrások alapján

Ahhoz, hogy az erőforrás-figyelő szolgáltatás méretezése tényleges erőforrások alapján

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Nincsenek két mérőszám, amely a tényleges fizikai erőforrásokat jelölik. Servicefabric őket egyik: / _CpuCores, amelyek tartalmazzák a tényleges processzorhasználat (tehát 0,5 fél alapszintű jelöl), a másik servicefabric folyamatban: / _MemoryInMB jelölő a MB memória használata.
ResourceMonitorService felelős felhasználó szolgáltatások processzor- és használat nyomon követése. Ezt a szolgáltatást annak érdekében, hogy a fiók számára lehetséges rövid ideig tartó ugrásszerűen súlyozott mozgóátlag érvényes lesz. Erőforrások monitorozása támogatott mind a tárolóalapú, mind a – a konténeralapú alkalmazások a Windows és Linux rendszeren tárolóalapú azokat. Automatikus skálázást az erőforrások csak akkor engedélyezett, a szolgáltatás aktiválva [kizárólagos folyamatmodell](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>További lépések
Tudjon meg többet [alkalmazás méretezhetőségi](service-fabric-concepts-scalability.md).
