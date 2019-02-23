---
title: 'Service Fabric fürterőforrás-kezelő: A mozgás költsége |} A Microsoft Docs'
description: A mozgás költsége a Service Fabric-szolgáltatások áttekintése
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 58147f5c0c3c7203aa6978e284cf5bdc90fa6971
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737775"
---
# <a name="service-movement-cost"></a>Szolgáltatás a mozgás költsége
A Service Fabric fürterőforrás-kezelő úgy ítéli meg, amikor megpróbálja meghatározni, hogy mi változik a fürt tényezővel mennyibe kerül a módosításokat. "Cost" fogalma forog ki ellen, hogy mekkora a fürt javítása érdekében. Költség Beleszámítja a terheléselosztást, töredezettségmentesítése és egyéb követelmények szolgáltatások áthelyezésekor. A cél, hogy megfeleljen a követelményeknek a legkevésbé zavart okozó vagy költséges módon. 

Szolgáltatások költségek CPU time áthelyezése, és a hálózati sávszélesség minimális. Az állapotalapú szolgáltatások esetében ez szükséges másolja ezeket a szolgáltatásokat, memóriájának és a lemez állapotát. Hogy az Azure Service Fabric fürt Resource Manager-megoldások költségeinek csökkentése biztosíthatja, hogy a fürt erőforrásainak feleslegesen nem töltött. Azonban még nem szeretné figyelmen kívül hagyja a megoldásokat, amelyek jelentősen javíthatják a lefoglalt erőforrások a fürt.

A fürterőforrás-kezelő kétféleképpen számítási költségeit, és korlátozza őket, amíg próbál meg a fürt kezeléséhez rendelkezik. Az első eljárás, amely biztosítja, hogy minden áthelyezés egyszerűen számolása. Ha a két megoldást akkor jönnek létre a nagyjából megegyeztek egyenleg (pontszám), majd a fürterőforrás-kezelő részesíti előnyben, amelyiket a legalacsonyabb költségű (a kurzor teljes száma).

Ez a stratégia jól működik. De az alapértelmezett vagy statikus, nem valószínű, hogy az összes lépés megegyeznek bármely összetett rendszerben. Néhány várhatóan sok egyéb mellett költséges lehet.

## <a name="setting-move-costs"></a>A beállítás áthelyezési költségei 
Az alapértelmezett áthelyezése költségeket a szolgáltatás létrehozásakor adhatók meg:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Adja meg, vagy MoveCost dinamikusan frissülnek a szolgáltatás a szolgáltatás létrehozása után is: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dinamikusan adja meg a replika alapon áthelyezési költség

Az előző kódrészletek az összes MoveCost megadására a szolgáltatás kívül egyszerre a teljes szolgáltatás. Azonban a költsége a leghasznosabb a következő esetekben áthelyezése a gyűjteményszintű időbeli változásainak egy adott service objektum áthelyezése költségét. Mivel a szolgáltatások valószínűleg a legjobb kiindulópont annak megértéséhez, hogy költséges azok áthelyezése egy adott idő alatt, van egy jelentéshez a saját egyéni költség áthelyezése során futásidejű szolgáltatások API. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Áthelyezés költség hatása
MoveCost négy szintje van: Nulla, alacsony, közepes és nagy. MoveCosts vannak egymással, viszonyított nulla kivételével. Áthelyezés díjmentesen azt jelenti, hogy a adatátviteli ingyenes, és nem kell csökkentik a pontszámot a megoldás. Az áthelyezés magas does költség beállítás *nem* garantálja, hogy a replika egy helyen tárolhatja.

<center>
![Helyezze át a költség tényezőként adatátviteli replikái kiválasztása][Image1]
</center>

MoveCost segít, hogy általános zavartalanul okoznak, és közben továbbra is továbbítótól egyenértékű egyenleg legegyszerűbb megoldhatók. Költség a szolgáltatás fogalmát viszonyított sok-sok dolog is lehet. A leggyakoribb tényezők az áthelyezési költségszámítás a következők:

- Állam vagy olyan adatok, amelyek a szolgáltatás áthelyezése mennyisége.
- Az ügyfelek a leválasztás költsége. Egy elsődleges replika áthelyezése általában költségesebb, mint az áthelyezi egy másodlagos másodpéldány költsége.
- Az átvitel közben a művelet megszakítása költsége. Bizonyos műveleteket az adatok tárolására szintet vagy egy ügyfél hívásra válaszul végrehajtott műveletek költséges. Egy bizonyos ponton után nem kívánja le őket, ha nem kell. Ezért amíg a művelet történik, növelheti a áthelyezési költségek csökkentése érdekében a valószínűsége, hogy áthelyezi azt a szolgáltatás-objektum. A művelet befejeződik, amikor a költségek állítsa vissza a normál.

## <a name="enabling-move-cost-in-your-cluster"></a>A fürt áthelyezési költségei engedélyezése
Ahhoz, hogy a részletesebb MoveCosts figyelembe kell venni, a MoveCost a fürtben engedélyezni kell. Ez a beállítás alapértelmezett módja annak, hogy a kurzor számbavételi MoveCost kiszámításához, és MoveCost jelentéseket a rendszer figyelmen kívül hagyja.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

az önálló verziója telepítéseinek ClusterConfig.json vagy Template.json az Azure-ban futó fürtök:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>További lépések
- Service Fabric fürt Resource Manager metrikák használatával használat és a fürt kapacitásának kezelése. Metrikák és a konfigurálásukról kapcsolatos további információkért tekintse meg [kezelése erőforrás-használat és a metrikák a Service Fabric terhelés](service-fabric-cluster-resource-manager-metrics.md).
- Hogyan kezeli a fürterőforrás-kezelő, és elosztja a terhelést a fürtben kapcsolatos további információkért tekintse meg [terheléselosztás a Service Fabric-fürt](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
