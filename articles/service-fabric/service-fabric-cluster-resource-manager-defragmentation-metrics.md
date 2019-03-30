---
title: A mérőszámok az Azure Service Fabric töredezettségmentesítését |} A Microsoft Docs
description: Töredezettségmentesítési használatával, vagy a Service Fabric metrikákhoz stratégiánk csomagolás áttekintése
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6e041e41372c72c6792c1fb4a1fbdc3bbe475b21
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661650"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Lemeztöredezettség-mentesítés metrikái és betöltése a Service Fabricben
A Service Fabric fürt Resource Manager alapértelmezett stratégiát terhelési mérőszámok a fürt kezelésére szolgáló, hogy a terhelés. Annak biztosítása, hogy a csomópontok egyenletesen felhasználtuk elkerülhető a gyakran és ritkán használt kritikus pontok elkerülése érdekében, hogy a versengés és az elpazarolt erőforrások mennyisége is. Terjesztése a számítási feladatokat a fürt akkor is a legbiztonságosabb fennmaradó hibák, mivel biztosítja, hogy a hiba nem használ egy adott számítási feladatok nagy részét meg tekintetében. 

A Service Fabric-fürt Resource Manager támogatja a más stratégiával terhelés, amely töredezettségmentesítési kezeléséhez. Lemeztöredezettség-mentesítés azt jelenti, hogy a fürt között oszthatja el a metrika felhasználása helyett, konszolidált. Összevonás az alapértelmezett terheléselosztás stratégia – minimalizálja a metrika terhelés átlagos szórását helyett csak egy invertálásának, a növeléséhez, megpróbálja a fürterőforrás-kezelő.

## <a name="when-to-use-defragmentation"></a>Mikor érdemes használni a töredezettségmentesítése
A fürt a terhelés elosztása néhány, minden egyes csomóponton erőforrást használ fel. Bizonyos munkaterhelések kivételesen nagy és felhasználását a legtöbb vagy az összes csomópont szolgáltatások létrehozása. Ebben az esetben is lehet, hogy ha első hoz létre, amely nagy mennyiségű számítási feladatok nincs elég hely az üzemeltetésükre bármely csomópontján. Nagy méretű számítási feladatok nem Service Fabric; hiba Ezekben az esetekben a fürterőforrás-kezelő azt határozza meg, hogy a fürt, hogy helyet biztosítson a nagy számítási átszervezése kell. Azonban addig adott számítási feladathoz tartozik várnia kell ütemezni a fürtben.

Ha sok szolgáltatás és Navigálás állapotra, majd azt sikerült időbe telik a nagy terheléshez, el kell helyezni a fürt. Ez akkor nagyobb valószínűséggel, ha a fürt más számítási feladatok is nagy, és így hosszabb időt vesz igénybe átrendezéséhez. A Service Fabric csapata mért létrehozási alkalommal szimulációt is ebben a forgatókönyvben. Úgy találtuk, hogy nagyméretű szolgáltatások létrehozásáról sokkal tovább tartott, amint az 50 %-a 30 % felett van fürtkihasználtság. Ebben a forgatókönyvben kezelése érdekében a lemeztöredezettség-mentesítés terheléselosztási stratégiánk bevezettünk. Úgy találtuk, hogy nagy számítási feladatokhoz, különösen azokról, ahol létrehozáskor volt fontos töredezettségmentesítési valóban segítségével új számítási feladatok beolvasása ütemezve a fürtben.

Töredezettségmentesítési metrikák a fürterőforrás-kezelő proaktív módon próbálja a terhelést a szolgáltatások sűrítse kevesebb csomópontot be, hogy konfigurálhatja. Ezzel biztosíthatja, hogy van-e szinte mindig nagyméretű szolgáltatásokhoz anélkül, hogy a fürt átrendezése hely. Nem rendelkezik a fürt átszervezése lehetővé teszi a nagy méretű számítási feladatok gyors létrehozása.

A legtöbb ember töredezettségmentesítési nem szükséges. Szolgáltatások általában kell kicsi, így nem nehéz megtalálni a hely számára őket a fürt. Ha átszervezési lehetséges, kerül, újra, mert a legtöbb szolgáltatások kicsik, és gyorsan és párhuzamosan is áthelyezhetők. Azonban ha nagy szolgáltatásokat, és szükség van rájuk a létrehozott majd gyorsan a lemeztöredezettség-mentesítés stratégia Önnek szól. Szó lesz a hátrányairól töredezettségmentesítési használata mellett. 

## <a name="defragmentation-tradeoffs"></a>Lemeztöredezettség-mentesítés kompromisszumot kínál a
Lemeztöredezettség-mentesítés növelheti impactfulness hibák, mivel további szolgáltatások, amelyek nem csomópontokon futnak. Töredezettségmentesítési is növelheti a költségeket, mivel a fürtben lévő erőforrásokat kell tartani a tartalék, a nagy méretű számítási feladatok létrehozására vár.

Az alábbi ábrán két fürt vizuális ábrázolását, amely töredezettségmentesíteni van, és nem biztosít. 

<center>

![Összehasonlítása az elosztott terhelésű, és a fürt töredezettségmentesítése][Image1]
</center>

Az elosztott terhelésű esetben fontolja meg a szükséges elhelyezni az egyik legnagyobb service objektum típusú áthelyezések száma. A töredezettségmentesített fürtben a nagy számítási sikerült elhelyezni négy vagy öt csomópont nem kell megvárnia áthelyezése bármely más szolgáltatásokhoz.

## <a name="defragmentation-pros-and-cons"></a>Lemeztöredezettség-mentesítés és hátrányai
Ezért Mik ezek más fogalmi kompromisszumot kínál a? A következő gyors tábláját gondolja át az alábbiakkal:

| Lemeztöredezettség-mentesítés szakemberek számára | Lemeztöredezettség-mentesítés hátrányai |
| --- | --- |
| Lehetővé teszi, hogy a nagyméretű szolgáltatások gyorsabb létrehozása |Koncentrátumok kevesebb csomópontot, a versengés növelése az alakzatot betöltése |
| Lehetővé teszi, hogy alacsonyabb adatáthelyezés létrehozása során |Hibák is hatással van a további szolgáltatások és a további adatforgalom miatt |
| Lehetővé teszi a követelmények részletes leírását és a terület-visszaigénylést |Összetettebb teljes erőforrás-kezelés konfigurálása |

Ugyanazon fürt töredezettségmentesített és normál metrikák kombinálhatja. A fürterőforrás-kezelő megpróbálja a lehető legnagyobb mértékben közben terjedhetnek ki a többi töredezettségmentesítési metrikák összesítése. Az eredmények töredezettségmentesítése és terheléselosztási stratégiák attól függ, hogy számos tényező befolyásolja, többek között:
  - a metrikák töredezettségmentesítési metrikák száma és terheléselosztási száma
  - Minden olyan szolgáltatást használ-e mindkét típusú metrikák 
  - a metrika súlyok
  - a mérőszám aktuális tölt be
  
Kísérletezési határozza meg a pontos szükséges konfiguráció szükséges. Javasoljuk, hogy a számítási feladatok alapos mérése éles környezetben töredezettségmentesítési metrikák engedélyezése előtt. Ez akkor különösen igaz olyankor, amikor keverése töredezettségmentesítése és az elosztott terhelésű metrikák belül ugyanazt a szolgáltatást. 

## <a name="configuring-defragmentation-metrics"></a>Lemeztöredezettség-mentesítés metrikák konfigurálása
Töredezettségmentesítési mérőszámok konfigurálását a fürt egy globális döntés, és egyéni metrikákat is kijelölhető töredezettségmentesítéshez. Az alábbi konfigurációs kódrészletek bemutatják, hogyan konfigurálhatja a lemeztöredezettség-mentesítés metrikáit. Ebben az esetben "Metric1" van konfigurálva, töredezettségmentesítési metrikát, míg a "Metric2" továbbra is egyenletesen eloszlik a szokásos módon. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

az önálló verziója telepítéseinek ClusterConfig.json vagy Template.json az Azure-ban futó fürtök:

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
- A fürterőforrás-kezelő rendelkezik a fürt leíró man lehetőségei. A velük kapcsolatos további információért tekintse meg a cikk a [leíró, Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md)
- Metrikák, hogyan kezeli a Service Fabric-fürt erőforrás-kezelő a használat és a kapacitás a fürtben. Metrikák és a konfigurálásukról kapcsolatos további információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
