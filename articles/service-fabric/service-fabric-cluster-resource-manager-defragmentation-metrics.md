---
title: "Az Azure Service Fabric a metrikák töredezettségmentesítését |} Microsoft Docs"
description: "Lemeztöredezettség-mentesítés használatával, vagy a Service Fabric metrikák stratégiáját, csomagolási áttekintése"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0b8b2bad967532bb0040407dc6a3a7b9599576d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>A metrikák és a Service Fabric terheléselosztási töredezettségmentesítését
A Service Fabric fürt Resource Manager alapértelmezett stratégia terhelési metrika a fürt felügyeletéhez, hogy a terhelés elosztása. Győződjön meg arról, hogy a csomópontok egyenletesen felhasználtuk elkerülhető és meleg tesztüzeméhez, amely a versengés és feleslegesen erőforrásokat is vezethet. A fürt munkaterhelések terjesztése egyben tekintetében a még működő sikertelen, mert biztosítja, hogy a hibát az egy adott munkaterhelés nagy része, nem veszi a legbiztonságosabb. 

A Service Fabric fürt erőforrás-kezelő egy másik stratégia támogatása a terhelés, amely töredezettségmentesítés kezelése. Lemeztöredezettség-mentesítés azt jelenti, hogy nem próbálja a metrika használata az szét a fürt, akkor egyesíti. Összevonás csak egy terheléselosztási stratégia – minimalizálja a terhelési metrika átlagos szórását helyett az alapértelmezett invertálásának, a fürt erőforrás-kezelő megpróbálja mivel.

## <a name="when-to-use-defragmentation"></a>Mikor érdemes használni a lemeztöredezettség-mentesítés
A fürt a terhelés elosztása használ fel az erőforrások minden egyes csomóponton. Bizonyos alkalmazások és szolgáltatások létrehozása szolgáltatásokról, amelyek rendkívül nagy, és felhasználhatják a legtöbb vagy az összes csomópont. Ebben az esetben is lehet, hogy első létre, amely nagy munkaterhelések esetén nincs elég hely bármely csomópontján futtatni azokat. Nagy munkaterhelések nem egy problémát a Service Fabric; Ezekben az esetekben a fürt erőforrás-kezelő azt határozza meg, hogy a fürt, így ez nagy terhelést átszervezése kell. Azonban a munkaterhelés rendelkezik vár arra, hogy a fürt ütemezhető.

Ha sok szolgáltatás és állapotba lép, majd eltarthat, el kell helyezni a fürt nagy terheléshez hosszú ideig. Ez az nagyobb a valószínűsége, ha a fürt többi munkaterhelését is nagy, és így a átrendezésével tovább tart. A Service Fabric-csoport létrehozása az ebben a forgatókönyvben szimulációja alkalommal mérése történik. Észleltünk, hogy nagy szolgáltatások létrehozása sokkal több időt vesz igénybe, amint 50 %-a 30 % feletti kapott fürtkihasználtság. Ebben a forgatókönyvben kezelésére, egy terheléselosztási stratégia töredezettségmentesítés bevezetett azt. Észleltünk, hogy nagy munkaterhelések esetén, főleg azokat, ahol létrehozásának ideje volt fontos töredezettségmentesítés valóban segített azokat a munkaterheléseket ütemezhetők a fürtben.

Szeretné, hogy a fürt erőforrás-kezelő arra, hogy a terhelés a szolgáltatások kondenzálására kevesebb csomópontot az proaktív próbáljon töredezettségmentesítés metrikák konfigurálhatja. Ezzel biztosíthatja, hogy van-e minden esetben a fürt átrendezése nélkül nagy szolgáltatások hely. Nem rendelkezik a fürt átszervezése lehetővé teszi a nagy munkaterheléseket gyors létrehozása.

A legtöbben töredezettségmentesítés nem szükséges. Szolgáltatások vannak általában lehet kicsi, ezért nincs rögzített számukra hely található a fürt. Ha átszervezési lehetséges, a tartományvezérlő végrehajtja gyorsan, újra, mert a legtöbb szolgáltatások kicsi, és gyorsan és párhuzamosan lehet áthelyezni. Azonban ha nagy szolgáltatások, és szüksége van rájuk létrehozott majd gyorsan a lemeztöredezettség-mentesítés stratégia van meg. A lemeztöredezettség-mentesítés mellett mellékhatásokkal mutatjuk be. 

## <a name="defragmentation-tradeoffs"></a>Lemeztöredezettség-mentesítés mellékhatásokkal
Lemeztöredezettség-mentesítés növelheti impactfulness sikertelen, mivel több szolgáltatás eleget nem tevő csomópontokon futnak. Lemeztöredezettség-mentesítés növelje költségek, mivel a fürterőforrások kell tárolható tartalék értékét, a nagy munkaterheléseket létrehozására vár.

Az alábbi ábrán két fürt vizuális ábrázolását, amelyet töredezettségmentesíteni van, és, amely nem biztosít. 

<center>
![Elosztott terhelésű, és fürtök töredezettségmentesíteni összehasonlítása][Image1]
</center>

Az elosztott terhelésű esetében vegye figyelembe a típusú áthelyezések helyezhető el egy legnagyobb szolgáltatás objektum szükséges. A töredezettségmentesített fürtben a nagy terhelés sikerült elhelyezni csomópontok négy vagy öt áthelyezése más szolgáltatásokkal várakozás nélkül.

## <a name="defragmentation-pros-and-cons"></a>Lemeztöredezettség-mentesítés előnyei és hátrányai
Ezért Mik ezek más fogalmi mellékhatásokkal? Ez gyors táblázatát gondolja át a következőkre:

| Lemeztöredezettség-mentesítés szakemberek számára | Lemeztöredezettség-mentesítés hátrányait |
| --- | --- |
| Lehetővé teszi, hogy a nagy szolgáltatások gyorsabb létrehozása |Koncentrátumok betöltése alakzatot kevesebb csomópontot versengés növelése |
| Lehetővé teszi, hogy alacsonyabb adatmozgás létrehozása során |Hibák hatással lehet a további szolgáltatásokat, és további forgalom miatt |
| Lehetővé teszi, hogy a követelmények részletes leírását és a hely visszanyerése |Összetettebb teljes erőforrás-kezelés konfigurálása |

Ugyanazon fürt töredezettségmentesített és normál metrikák lehet összekeveri. A fürt erőforrás-kezelő megpróbálja a lehető legnagyobb mértékben közben terjednek ki, a többi töredezettségmentesítés metrikák összesítése. Az eredmények töredezettségmentesítés és terheléselosztási stratégiák számos tényezőtől függ:
  - a töredezettségmentesítés metrikák száma és metrikákat terheléselosztás száma
  - E bármely szolgáltatás használ a metrikák mindkét típusú 
  - a metrika súlyozás
  - aktuális metrika tölt be
  
Kísérletezhet szükség van a pontos konfiguráció szükséges. A munkaterhelések alapos mérése azt javasoljuk, mielőtt engedélyezné a lemeztöredezettség-mentesítés metrikák éles környezetben. Ez akkor különösen igaz olyan esetben, ha keverése töredezettségmentesítése és az elosztott terhelésű metrikák belül ugyanazt a szolgáltatást. 

## <a name="configuring-defragmentation-metrics"></a>Lemeztöredezettség-mentesítés metrikák konfigurálása
Töredezettségmentesítés metrikák konfigurálása a fürt globális döntést, és egyéni metrikák választhatók töredezettségmentesítés. A következő konfigurációs kódtöredékek bemutatják, hogyan töredezettségmentesítés metrikáját konfigurálásához. Ebben az esetben "Metric1" van konfigurálva egy töredezettségmentesítési metrika közben "Metric2" egyenletesen eloszlik a szokásos módon folytatódik. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

az önálló verziója telepítéseinek művelet vagy az Azure-Template.json üzemeltetett fürtök:

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


## <a name="next-steps"></a>Következő lépések
- A fürt erőforrás-kezelő program man a fürtöt leíró. További információkért róluk, tekintse meg a cikk a [leíró a Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md)
- Adatok gyűjtése le hogyan kezeli a Service Fabric fürt erőforrás-kezelő a használati és a fürt teljes kapacitását. A metrikák és konfigurálásuk módját kapcsolatos további tudnivalókért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
