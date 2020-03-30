---
title: 'Szolgáltatásháló fürterőforrás-kezelője: Mozgásköltsége'
description: Ismerje meg a Service Fabric-szolgáltatások mozgási költségét, és hogyan adható meg, hogy megfeleljen bármilyen architekturális igénynek, beleértve a dinamikus konfigurációt is.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: af3e01d0d5a605c052be24eed8e14ee3449e2c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563343"
---
# <a name="service-movement-cost"></a>A szolgáltatás mozgásának költsége
A Service Fabric fürterőforrás-kezelő je a fürtön végrehajtott módosítások meghatározásakor figyelembe veszi a módosítások költségét. A "költség" fogalmát a fürt továbbfejleszthetőségével szemben cserélik ki. A költségeket figyelembe veszik a kiegyensúlyozási, töredezettségmentesítési és egyéb követelményekhez szükséges szolgáltatások áthelyezésekénél. A cél az, hogy megfeleljen a követelményeknek a legkevésbé zavaró vagy drága módon.

A szolgáltatások áthelyezése legalább a PROCESSZOR-idő és a hálózati sávszélesség bekerül. Állapotalapú szolgáltatások esetén a szolgáltatások állapotát kell másolni, további memóriát és lemezt fogyasztva. Az Azure Service Fabric fürterőforrás-kezelője által létrehozott megoldások költségeinek minimalizálása segít biztosítani, hogy a fürt erőforrásait ne költsék feleslegesen. Azonban azt is nem szeretné figyelmen kívül hagyni a megoldásokat, amelyek jelentősen javítanák a fürt erőforrásainak elosztását.

A fürterőforrás-kezelő kétféleképpen számolja ki a költségeket, és korlátozza azokat, miközben megpróbálja kezelni a fürtöt. Az első mechanizmus egyszerűen számol minden mozdulatát, hogy tenné. Ha két megoldás jön létre körülbelül azonos egyenleg (pontszám), majd a fürt erőforrás-kezelő inkább az egyik a legalacsonyabb költség (összes mozog).

Ez a stratégia jól működik. De mint az alapértelmezett vagy statikus terheléseknél, nem valószínű, hogy minden összetett rendszerben minden lépés egyenlő. Néhány valószínűleg sokkal drágább.

## <a name="setting-move-costs"></a>Áthelyezési költségek beállítása 
A szolgáltatás létrehozásakor megadhatja a szolgáltatás alapértelmezett áthelyezési költségét:

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

A MoveCost-ot a szolgáltatás létrehozása után dinamikusan is megadhatja vagy frissítheti: 

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Az áthelyezési költség dinamikus megadása replikaalapon

Az előző kódrészletek mind a MoveCost megadására szolgálnak egy teljes szolgáltatáshoz egyszerre a szolgáltatáson kívülről. Az áthelyezési költség azonban akkor a leghasznosabb, ha egy adott szolgáltatásobjektum áthelyezési költsége az élettartama alatt megváltozik. Mivel maguk a szolgáltatások valószínűleg a legjobb ötlet, hogy milyen költséges azok áthelyezése egy adott időpontban, van egy API-t a szolgáltatások jelentés saját egyéni lépés költsége futásidőben. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>A költözési költség hatása
A MoveCost öt szinttel rendelkezik: Nulla, Alacsony, Közepes, Magas és NagyonMagas. A következő szabályok érvényesek:

* A MoveCosts egymáshoz viszonyítva van, kivéve a Nulla és a VeryHigh értéket. 
* A nulla mozgásköltség azt jelenti, hogy a mozgás szabad, és nem számít bele a megoldás pontszáma.
* Az áthelyezési költség magas vagy nagyonmagas *beállításnem* garantálja, hogy a replika *soha nem* kerül áthelyezésre.
* A VeryHigh áthelyezési költséggel rendelkező replikák csak akkor kerülnek áthelyezésre, ha a fürtben olyan megszorítás-megsértés van, amely et semmilyen más módon nem lehet kijavítani (még akkor is, ha sok más replikát kell áthelyezni a megsértés kijavításához)



<center>

![Költség áthelyezése tényezőként a kópiák mozgáshoz való kiválasztásában][Image1]
</center>

A MoveCost segítségével megtalálhatja azokat a megoldásokat, amelyek összességében a legkisebb fennakadást okozzák, és a legkönnyebben elérhetők, miközben továbbra is azonos egyensúlyban vannak. A szolgáltatás fogalma költség lehet képest sok mindent. A költözési költség kiszámításának leggyakoribb tényezői a következők:

- A szolgáltatás által áthelyezandó állapot vagy adatok mennyisége.
- Az ügyfelek leválasztásának költsége. Az elsődleges replika áthelyezése általában költségesebb, mint a másodlagos replika áthelyezése.
- Egy repülés közbeni művelet megszakításának költsége. Az adattár szintjén végrehajtott egyes műveletek vagy az ügyfélhívásra adott válaszként végrehajtott műveletek költségesek. Egy bizonyos pont után, nem akarod megállítani őket, ha nem kell. Így a művelet folyamatos működése közben növeli a szolgáltatásobjektum áthelyezési költségét, hogy csökkentse annak valószínűségét, hogy mozog. Amikor a művelet befejeződött, állítsa vissza a költséget a normál.

> [!IMPORTANT]
> A VeryHigh áthelyezési költséget gondosan meg kell fontolni, mivel jelentősen korlátozza a fürterőforrás-kezelő azon képességét, hogy globálisan optimális elhelyezési megoldást találjon a fürtben. A VeryHigh áthelyezési költséggel rendelkező replikák csak akkor kerülnek áthelyezésre, ha a fürtben olyan megszorítás-megsértés van, amely et semmilyen más módon nem lehet kijavítani (még akkor is, ha sok más replikát kell áthelyezni a megsértés kijavításához)

## <a name="enabling-move-cost-in-your-cluster"></a>Áthelyezési költség engedélyezése a fürtben
Annak érdekében, hogy a részletesebb MoveCosts figyelembe kell venni, MoveCost engedélyezni kell a fürtben. E beállítás nélkül a számlálási áthelyezések alapértelmezett módja a MoveCost kiszámításához használatos, és a MoveCost jelentéseket figyelmen kívül hagyja.


ClusterManifest.xml fájl:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

a ClusterConfig.json keresztül önálló telepítésekhez vagy template.json az Azure által üzemeltetett fürtökhöz:

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
- A Service Fabric fürterőforrás-felügyelő metrikákat használ a fürt ben a fogyasztás és a kapacitás kezeléséhez. Ha többet szeretne megtudni a metrikákról és azok konfigurálásáról, olvassa el [az Erőforrás-felhasználás és -betöltés kezelése a Service Fabric metrikákkal](service-fabric-cluster-resource-manager-metrics.md)című része című részt.
- Ha többet szeretne tudni arról, hogy a fürterőforrás-kezelő hogyan kezeli és egyensúlyozza ki a terhelést a fürtben, olvassa el [a Service Fabric-fürt kiegyensúlyozása](service-fabric-cluster-resource-manager-balancing.md)című részben.

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
