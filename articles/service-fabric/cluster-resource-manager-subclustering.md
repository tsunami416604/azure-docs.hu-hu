---
title: A részhalmazban tartott metrikák kiegyensúlyozása
description: Az elhelyezési korlátok hatása a kiegyensúlyozásra és annak kezelésére
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430642"
---
# <a name="balancing-of-subclustered-metrics"></a>A részhalmazban tartott metrikák kiegyensúlyozása

## <a name="what-is-subclustering"></a>Mi az alfürtözés

Az alfürtözés akkor történik, ha a különböző elhelyezési korlátokkal rendelkező szolgáltatások közös metrikával rendelkeznek, és mindkettő terhelést jelent hozzá. Ha a szolgáltatások által jelentett terhelés jelentősen eltér, a csomópontok teljes terhelése nagy szórással fog rendelkezni, és úgy tűnik, hogy a fürt kiegyensúlyozatlan, még akkor is, ha a lehető legjobb egyensúlyban van.

## <a name="how-subclustering-affects-load-balancing"></a>Az alfürtözés hatása a terheléselosztásra?

Ha a szolgáltatások által jelentett terhelés különböző csomópontokon jelentősen eltér, úgy tűnhet, hogy nagy egyensúlyhiány van, ahol nincs. Továbbá, ha az alfürtözés által okozott hamis kiegyensúlyozatlanság nagyobb, mint a tényleges egyensúlyhiány, akkor képes összekeverni az Erőforrás-kezelő kiegyensúlyozó algoritmust, és a fürt optimálistól elmaradó egyensúlyának létrehozásához.

Tegyük fel például, hogy négy szolgáltatásunk van, és mindegyik terhelést jelent a Metric1 metrika esetében:

* "NodeType==Type1" elhelyezési megkötéssel rendelkezik, 10-es terhelést jelent
* B szolgáltatás – elhelyezési megkötése "NodeType==Type1", 10-es terhelést jelent
* Service C – elhelyezési megkötéssel rendelkezik: "NodeType==Type2", 100-as terhelést jelent
* D szolgáltatás – elhelyezési megkötése "NodeType==Type2", 100-as terhelést jelent
* És négy csomónk van. Kettő közülük NodeType beállítása "Type1", a másik kettő pedig "Type2"

És a következő elhelyezést látjuk:

<center>

![Például részhalmazos elhelyezési példa][Image1]
</center>

A fürt tűnhet kiegyensúlyozatlan, van egy nagy terhelés csomópontok 3 és 4, de ez az elhelyezés megteremti a lehető legjobb egyensúlyt ebben a helyzetben.

Az Erőforrás-kezelő képes felismerni az alfürtözési helyzeteket, és szinte minden esetben képes az adott helyzet optimális egyensúlyára.

Egyes kivételes helyzetekben, amikor az Erőforrás-kezelő nem képes optimálisan kiegyensúlyozni egy alfürtben lévő metrikát, továbbra is észleli az alfürtözést, és állapotjelentést hoz létre, amely a probléma megoldásához nyújt tanácsot.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Az alhalmazok típusai és azok kezelésének megócsulása

Az alhalmazhelyzetek három különböző kategóriába sorolhatók. Egy adott alfürtözési helyzet kategóriája határozza meg, hogy az Erőforrás-kezelő hogyan kezelje azt.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Első kategória – lapos alklaszter különálló csomópontcsoportokkal

Ez a kategória rendelkezik az alfürtözés legegyszerűbb formájával, ahol a csomópontok különböző csoportokra oszthatók, és minden szolgáltatás csak az adott csoportok egyikének csomópontjaira helyezhető el. Minden csomópont csak egy és egy csoporthoz tartozik. A fent leírt helyzet ebbe a kategóriába tartozik, akárcsak a legtöbb alhalmazosítási helyzet. 

Ebben a kategóriában az erőforrás-kezelő képes az optimális egyensúlyt, és nincs szükség további beavatkozásra.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Második kategória – hierarchikus csomópontcsoportokkal rendelkező alfürtözés

Ez a helyzet akkor fordul elő, ha egy szolgáltatáshoz engedélyezett csomópontok csoportja egy másik szolgáltatásszámára engedélyezett csomópontcsoport egy részhalmaza. A leggyakoribb példa erre a helyzetre, amikor néhány szolgáltatás rendelkezik elhelyezési megkötés definiálva, és egy másik szolgáltatás nem rendelkezik elhelyezési megkötéssel, és bármely csomópontra helyezhető.

Példa:

* A szolgáltatás: nincs elhelyezési megkötés
* B szolgáltatás: elhelyezési megkötés :"NodeType==Type1"
* C szolgáltatás: elhelyezési megkötés :"NodeType==Type2"

Ez a konfiguráció egy részhalmaz-szuperset kapcsolatot hoz létre a csomópontcsoportok között a különböző szolgáltatásokhoz.

<center>

![Részhalmazú szuperhalmazú alhalmazú alhalmazok][Image2]
</center>

Ebben a helyzetben fennáll annak az esélye, hogy az optimálisnál rosszabb egyensúly jön.

Az Erőforrás-kezelő felismeri ezt a helyzetet, és egy állapotjelentést készít, amely azt tanácsolja, hogy az A szolgáltatást két szolgáltatásra ossza fel – az A1 szolgáltatásra, amely elhelyezhető a Type1 csomópontokon és az A2-es szolgáltatáson, amelyek a Type2 csomópontokon helyezhetők el. Ez visszavisz minket az első olyan helyzetbe, amely optimálisan kiegyensúlyozható.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Harmadik kategória – csomópontkészletek közötti részleges átfedéssel rendelkező alfürtözés

Ez a helyzet akkor fordul elő, ha részleges átfedés van a csomópontok halmazai között, amelyekre egyes szolgáltatások elhelyezhetők.

Ha például van egy NodeColor nevű csomóponttulajdonságunk, és három csomónk van:

* 1. csomópont: NodeColor=Piros
* 2. csomópont: NodeColor=Kék
* 2. csomópont: NodeColor=Zöld

És van két szolgáltatás:

* A szolgáltatás: elhelyezési megkötéssel: "Color==Red || Szín==Kék"
* B szolgáltatás: elhelyezési megkötéssel: "Color==Blue || Szín==zöld"

Emiatt az A szolgáltatás az 1- es és 2-es csomópontra, a B szolgáltatás pedig a 2- es és 3-as csomópontra helyezhető.

Ebben a helyzetben fennáll annak az esélye, hogy az optimálisnál rosszabb egyensúly jön.

Az Erőforrás-kezelő felismeri ezt a helyzetet, és létrehoz egy állapotjelentést, amely azt tanácsolja, hogy ossza fel a szolgáltatások egy részét.

Ebben a helyzetben az Erőforrás-kezelő nem tud javaslatot adni a szolgáltatások felosztására, mivel több felosztás is elvégezhető, és nincs mód annak becslésére, hogy milyen módon lenne az optimális a szolgáltatások felosztása.

## <a name="configuring-subclustering"></a>Alfürtkonfigurálás konfigurálása

Az Erőforrás-kezelő alfürtözéssel kapcsolatos viselkedése a következő konfigurációs paraméterek módosításával módosítható:
* SubclusteringEnabled - paraméter határozza meg, hogy az Erőforrás-kezelő figyelembe veszi-e az alfürtözést a terheléselosztás során. Ha ez a paraméter ki van kapcsolva, az Erőforrás-kezelő figyelmen kívül hagyja az alfürtözést, és globális szinten megpróbálja elérni az optimális egyensúlyt. A paraméter alapértelmezett értéke hamis.
* SubclusteringReportingPolicy – meghatározza, hogy az Erőforrás-kezelő hogyan bocsátki állapotjelentéseket a hierarchikus és részleges átfedési alfürtözéshez. A nulla érték azt jelenti, hogy az alfürtről szóló állapotjelentések ki vannak kapcsolva, az "1" azt jelenti, hogy az optimálistól elmaradó szubklaszteres helyzetekben figyelmeztető állapotjelentések készülnek, és a "2" érték "OK" állapotjelentéseket eredményez. A paraméter alapértelmezett értéke "1".

ClusterManifest.xml fájl:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

a ClusterConfig.json keresztül önálló telepítésekhez vagy template.json az Azure által üzemeltetett fürtökhöz:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>További lépések
* Ha meg szeretné tudni, hogy a fürterőforrás-kezelő hogyan kezeli és egyensúlyozza ki a terhelést a fürtben, olvassa el a [terhelés elosztásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
* Ha meg szeretné tudni, hogy a szolgáltatások hogyan korlátozhatók úgy, hogy csak bizonyos csomópontokon legyenek elhelyezve, [lásd: Csomóponttulajdonságok és elhelyezési korlátok](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
