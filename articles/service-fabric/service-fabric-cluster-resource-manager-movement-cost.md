---
title: 'Service Fabric fürt erőforrás-kezelő: a mozgás költsége |} Microsoft Docs'
description: A mozgás költsége Service Fabric-szolgáltatások áttekintése
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 74b61967a796fca22ab86918235f1def27a22f91
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204923"
---
# <a name="service-movement-cost"></a>A mozgás költsége szolgáltatás
A Service Fabric fürt erőforrás-kezelő úgy ítéli meg, amikor megpróbálja megállapítani, hogy milyen módosítások, hogy a fürt tényezővel a költségét és ezeket a módosításokat. A "költség" fogalmát ellen, hogy mekkora a fürt növelhető a forog ki. Költség Beleszámítja a terheléselosztás, töredezettségmentesítés és egyéb követelmények szolgáltatások áthelyezésekor. A cél, hogy legalább zavaró vagy drága módon követelményeinek. 

Helyezze át a szolgáltatások költségek CPU-idő, és a hálózati sávszélesség minimális. Állapotalapú szolgáltatások esetén igényel azon szolgáltatásokat, ezzel további memóriát és lemez állapotának másolása. Győződjön meg arról, hogy a fürt erőforrásait feleslegesen nem töltött megoldások, hogy az Azure Service Fabric fürt erőforrás-kezelő a költségek minimalizálása segít. Azonban még nem szeretnénk figyelmen kívül hagyása megoldások, amelyek jelentősen javíthatják a a fürt-erőforrások elosztását.

A fürt erőforrás-kezelő rendelkezik kétféleképpen számítástechnikai költségeket, és korlátozza azokat próbál meg a fürt kezelése közben. Az első eljárás egyszerűen számolása minden elindított áthelyezésre is volna. Ha a két megoldás akkor jönnek létre, az azonos kapcsolatos egyenleg (pontszám), akkor a fürt erőforrás-kezelő inkább egy, a legalacsonyabb költség (a kurzor teljes száma).

Ezt a stratégiát akkor is működik. De az alapértelmezett vagy statikus, nem valószínű, hogy az összes lépés egyenlőek bármely összetett rendszerben. Néhány valószínűleg sokkal drága lehet.

## <a name="setting-move-costs"></a>A beállítás költségek áthelyezése 
Az alapértelmezett áthelyezése költség szolgáltatás létrehozásakor adhatók meg:

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

Adja meg vagy frissítés MoveCost dinamikusan a szolgáltatás a szolgáltatás létrehozása után is: 

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dinamikusan adja meg a replika alapon áthelyezés költség

Az előző kódtöredékek segédanyagokra MoveCost megadásának egyszerre a teljes szolgáltatás kívül a szolgáltatást. Azonban áthelyezése költsége leghasznosabb kell, ha az élettartamot időbeli változásainak egy adott szolgáltatás objektum áthelyezése költségét. A szolgáltatásoknak valószínűleg a legjobb meghatározni, hogy hogyan költséges azok helyezze át egy adott idő alatt, mert nincs a jelentés a saját egyéni költség áthelyezése futásidőben szolgáltatásainak API. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Áthelyezési költség hatása
MoveCost négy szintje van: nulla, alacsony, közepes és magas. MoveCosts vannak egymáshoz, kivéve a nulla. Nulla áthelyezés költség azt jelenti, hogy a mozgás szabad, nem kell csökkenti a pontszám megoldás. A magas does költsége move beállítást *nem* biztosítja, hogy a replika egy helyen tárolhatja.

<center>
![Helyezze át a költség tényezőként adatátviteli replikák kiválasztása][Image1]
</center>

Keresse meg a a megoldást, amely átfogó zavartalanul okozza, és közben továbbra is érkező egyenértékű egyenleg eléréséhez legegyszerűbb MoveCost segítségével. A szolgáltatás fogalmát költség lehet számos elemet viszonyítva. A leggyakoribb tényezők az áthelyezés költsége kiszámításakor a következők:

- Állam vagy olyan adatok, amelyek a szolgáltatás áthelyezése mennyisége.
- Ügyfelek leválasztása a költségét. Helyezze át az elsődleges replika általában költségesebb, mint a áthelyezi egy másodlagos másodpéldány költségét.
- Az üzenetsoroktól művelet megszakítása költsége. Bizonyos műveletek, az adatok tárolásához szint, és egy ügyfél hívást végre műveletek esetében költséges. Egy bizonyos mértékig után nem kívánja azokat le, ha nincs. Ezért amíg a művelet van folyamatban, növelheti a move költségek csökkentése érdekében, hogy ez a szolgáltatás-objektum. Amikor a műveletet hajtja végre, a költség be visszaállítása a szokásos módon folytatódik.

## <a name="enabling-move-cost-in-your-cluster"></a>Áthelyezési költség, a fürt engedélyezése
Ahhoz, hogy a részletesebb MoveCosts figyelembe kell venni MoveCost a fürtben engedélyezni kell. Ha ez a beállítás számbavételi helyezi át az alapértelmezett mód MoveCost kiszámításához, és MoveCost jelentéseket a rendszer figyelmen kívül hagyja.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

az önálló verziója telepítéseinek művelet vagy az Azure-Template.json üzemeltetett fürtök:

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
- Service Fabric fürt erőforrás-kezelő metrikákat használ a használat és a kapacitás, a fürt kezeléséhez. A metrikák és konfigurálásuk módját kapcsolatos további tudnivalókért tekintse meg [hálózatierőforrás-fogyasztás kezelése és a metrikák a Service Fabric terheléselosztási](service-fabric-cluster-resource-manager-metrics.md).
- Hogyan kezeli a fürt erőforrás-kezelő, és elosztja a terhelést a fürt kapcsolatos információkért tekintse meg [a Service Fabric-fürt terheléselosztási](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
