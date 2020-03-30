---
title: Metrikák töredezettségmentesítése az Azure Service Fabricben
description: Ismerje meg a töredezettségmentesítés, vagy a csomagolás, a szolgáltatás fabric metrikák stratégiájaként. Ez a technika nagyon nagy szolgáltatások esetén hasznos.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bba459be4408f4a4bc438bb33b0570a91e84f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563360"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Metrikák töredezettségmentesítése és terhelése a Service Fabricben
A Service Fabric fürterőforrás-kezelő alapértelmezett stratégiája a terhelési metrikák kezelésére a fürtben a terhelés elosztása. Annak biztosítása, hogy a csomópontok egyenletesen használják elkerüli a hideg és meleg foltok, amelyek mind a versengés és az elpazarolt erőforrások. A fürtben a számítási feladatok elosztása is a legbiztonságosabb a túlélő hibák szempontjából, mivel biztosítja, hogy a hiba nem veszi ki az adott számítási feladatok nagy százalékát. 

A Service Fabric fürterőforrás-kezelő támogatja a terhelés kezelésére, amely töredezettségmentesítés egy másik stratégia kezelésére. Töredezettségmentesítés azt jelenti, hogy ahelyett, hogy elosztja a kihasználtságot a fürtön, ez konszolidált. A konszolidáció csak az alapértelmezett kiegyensúlyozási stratégia inverziója – ahelyett, hogy minimalizálni szeretné a metrikaterhelés átlagos szórását, a fürterőforrás-kezelő megpróbálja növelni azt.

## <a name="when-to-use-defragmentation"></a>Mikor kell használni a töredezettségmentesítést?
A fürt terhelésének elosztása az egyes csomópontok on-k ban lévő erőforrások egy részét használja fel. Egyes számítási feladatok olyan szolgáltatásokat hoznak létre, amelyek kivételesen nagyok, és egy csomópont nagy részét vagy egészét használják. Ezekben az esetekben lehetséges, hogy ha nagy számítási feladatok jönnek létre, hogy nincs elég hely a csomóponton, hogy futtassa őket. A nagy munkaterhelések nem jelentenek problémát a Service Fabricben; ezekben az esetekben a fürterőforrás-kezelő megállapítja, hogy át kell szerveznie a fürtöt, hogy helyet adjon ennek a nagy munkaterhelésnek. Addig azonban, hogy a számítási feladatok nak meg kell várni, hogy ütemezze a fürtben.

Ha sok szolgáltatás és állapot van a mozgáshoz, akkor hosszú időt vehet igénybe a nagy munkaterhelés a fürtbe való elhelyezése. Ez nagyobb valószínűséggel, ha a fürt más számítási feladatai is nagyok, és így hosszabb időt vesz igénybe az átszervezés. A Service Fabric csapata ebben a forgatókönyvben a létrehozási időket mérte. Azt találtuk, hogy a nagy szolgáltatások létrehozása sokkal tovább tartott, amint a fürtkihasználtság 30% és 50% között van. Ennek a forgatókönyvnek a kezeléséhez kiegyenlítő stratégiaként vezettük be a töredezettségmentesítést. Azt találtuk, hogy a nagy számítási feladatok, különösen azok, ahol a létrehozási idő fontos volt, töredezettségmentesítés valóban segített az új számítási feladatok ütemezése a fürtben.

Konfigurálhatja töredezettségmentesítésmetriók, hogy a fürt erőforrás-kezelő proaktív módon próbálja tömöríteni a terhelést a szolgáltatások kevesebb csomópont. Ez segít annak biztosításában, hogy szinte mindig legyen hely a nagy szolgáltatások számára a fürt átszervezése nélkül. A fürt átrendezésének lehetővé teszi a nagy munkaterhelések gyors létrehozását.

A legtöbb embernek nincs szüksége töredezettségmentesítésre. A szolgáltatások általában kicsik, így nem nehéz helyet találni számukra a fürtben. Ha az átszervezés lehetséges, akkor gyorsan megy, ismét, mert a legtöbb szolgáltatás kicsi, és gyorsan és párhuzamosan mozgatható. Azonban, ha nagy szolgáltatásokat, és szükség van rájuk létre gyorsan, akkor a töredezettségmentesítés stratégia az Ön számára. Megbeszéljük a kompromisszumokat a töredezettségmentesítés következő. 

## <a name="defragmentation-tradeoffs"></a>Töredezettségmentesítés kompromisszumok
Töredezettségmentesítés növelheti a hibák hatása, mivel több szolgáltatás fut a csomópontokon, hogy nem. Töredezettségmentesítés is növelheti a költségeket, mivel a fürt erőforrásait tartalékban kell tartani, várva a nagy számítási feladatok létrehozására.

Az alábbi ábra két fürt vizuális ábrázolását ábrázolja, amelyek közül az egyik töredezettségmentesített, a másik nem. 

<center>

![Kiegyensúlyozott és töredezettségmentesített fürtök összehasonlítása][Image1]
</center>

A kiegyensúlyozott esetben vegye figyelembe az egyik legnagyobb szolgáltatásobjektum helyének helyéhez szükséges mozgások számát. A töredezettségmentesített fürtben a nagy számítási feladatok at lehet helyezni a csomópontok négy vagy öt anélkül, hogy megvárja, amíg más szolgáltatások áthelyezése.

## <a name="defragmentation-pros-and-cons"></a>Töredezettségmentesítés előnyei és hátrányai
Szóval mik azok a többi koncepcionális kompromisszum? Íme egy gyors táblázat a dolgokat gondolni:

| Töredezettségmentesítés Profik | Töredezettségmentesítés hátrányok |
| --- | --- |
| Lehetővé teszi a nagyobb szolgáltatások gyorsabb létrehozását |A koncentrátumok kevesebb csomópontra töltődnek be, növelve a versengést |
| Kisebb adatmozgást tesz lehetővé a létrehozás során |A hibák több szolgáltatást is érinthetnek, és több lemorzsolódást okozhatnak |
| Lehetővé teszi a követelmények gazdag leírását és a tér visszanyerését |Összetettebb általános erőforrás-kezelési konfiguráció |

A töredezettségmentesített és a normál metrikák at ugyanabban a fürtben keverheti. A fürterőforrás-kezelő megpróbálja a lehető legnagyobb mértékben konszolidálni a töredezettségmentesítési mutatókat, miközben szétosztja a többieket. A töredezettségmentesítés és a kiegyensúlyozási stratégiák keverésének eredményei több tényezőtől függnek, többek között:
  - a kiegyensúlyozási mutatók száma és a töredezettségmentesítési metrikák száma
  - Azt jelzi, hogy bármely szolgáltatás mindkét típusú mutatót használja-e 
  - a metrikus súlyok
  - aktuális metrikaterhelések
  
Kísérletezés szükséges a szükséges konfiguráció meghatározásához. Azt javasoljuk, hogy alaposan mérje a számítási feladatok, mielőtt engedélyezi töredezettségmentesítés metrikák éles környezetben. Ez különösen igaz, ha a töredezettségmentesítés és a kiegyensúlyozott metrikák ugyanazon a szolgáltatáson belül. 

## <a name="configuring-defragmentation-metrics"></a>Töredezettségmentesítési mérőszámok konfigurálása
A töredezettségmentesítési metrikák konfigurálása globális döntés a fürtön, és az egyes metrikák kiválaszthatók töredezettségmentesítéshez. A következő konfigurációs kódrészletek bemutatják, hogyan konfigurálhatja a metrikákat a töredezettségmentesítéshez. Ebben az esetben a "Metric1" van konfigurálva töredezettségmentesítési metrikaként, míg a "Metric2" továbbra is kiegyensúlyozott marad a szokásos módon. 

ClusterManifest.xml fájl:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

a ClusterConfig.json keresztül önálló telepítésekhez vagy template.json az Azure által üzemeltetett fürtökhöz:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>További lépések
- A fürterőforrás-kezelő man-beállításokkal rendelkezik a fürt leírásához. Ha többet szeretne megtudni róluk, olvassa el ezt a cikket a [Service Fabric-fürt leírásáról](service-fabric-cluster-resource-manager-cluster-description.md)
- Metrikák, amelyek a Service Fabric fürterőforrás-kezelő kezeli a fürt ben a fogyasztás és a kapacitás. Ha többet szeretne megtudni a metrikákról és azok konfigurálásáról, olvassa el [ezt a cikket.](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
