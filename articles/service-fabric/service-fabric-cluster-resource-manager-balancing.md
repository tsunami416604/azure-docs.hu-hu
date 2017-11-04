---
title: "Az Azure Service Fabric-fürt elosztása |} Microsoft Docs"
description: "A fürt és a Service Fabric fürt erőforrás-kezelő terheléselosztás bemutatása."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 06d65878d84fb845cf0c4c333a1e2d12b0aaec2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="balancing-your-service-fabric-cluster"></a>A service fabric-fürt terheléselosztás
A Service Fabric fürt erőforrás-kezelő támogatja a dinamikus terheléselosztó módosítások reagál a hozzáadást, illetve a csomópontok vagy szolgáltatások eltávolítására. Azt is automatikusan javítja a megkötés megsértésének, és proaktív újra egyensúlyba hozza a fürt. De gyakoriságát. Ezek a műveletek készít, és mi váltja ki őket?

A fürt erőforrás-kezelő végző munkahelyi három különböző kategóriába sorolhatók. Ezek a következők:

1. Elhelyezését – ebben a szakaszban foglalkozik bármely állapot-nyilvántartó replikák vagy hiányzó állapotmentes példányok. Elhelyezési tartalmazza az új szolgáltatásokat és a kezelési állapot-nyilvántartó replikák vagy állapotmentes példányokat, amelyek nem tudták. Itt törlése és a replikák és példányok eldobása történik.
2. Korlátozás ellenőrzi – ebben a fázisban ellenőrzi, és kijavítja a másik egy elhelyezési korlátozás (szabály) a rendszerben megsértése. Szabályok Példák többek között a győződjön meg arról, hogy a csomópontok nem: keresztül kapacitás és, hogy teljesülnek-e a szolgáltatás egy elhelyezési korlátozás.
3. Terheléselosztás – ebben a fázisban ellenőrzi, hogy újraelosztás szükséges konfigurált kívánt szintje különböző metrikák elosztás alapján. Ha igen megpróbálja megkeresni a elrendezést a fürt, amely több elosztott terhelésű.

## <a name="configuring-cluster-resource-manager-timers"></a>Erőforrás-kezelő fürt időzítők konfigurálása
Terheléselosztás körül első meg olyan időzítőket. Ezek időzítők szabályozására, milyen gyakran a fürt erőforrás-kezelő megvizsgálja-e a fürt és korrekciós műveleteket hajtja végre.

Egy másik számlálót gyakoriságát szabályzó egyes a fürt erőforrás-kezelő tehet javításokat különböző típusaival vezérli. Minden egyes időzítő következik be, amikor a feladat ütemezése. Az erőforrás-kezelő. Alapértelmezés:

* megvizsgálja az állapotát, és alkalmazza a frissítéseket (például, hogy a csomópont nem működik a rögzítés) minden 1/10 másodperc.
* az elhelyezési ellenőrzést jelzőjének beállítása 
* Beállítja a korlátozás-ellenőrzés jelző másodpercenként
* Beállítja azt a terheléselosztási jelzőt, ötpercenként.

Ezek időzítők vonatkozó konfigurációs többek között az alábbi:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

az önálló verziója telepítéseinek művelet vagy az Azure-Template.json üzemeltetett fürtök:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Ma a fürt erőforrás-kezelő csak műveletek egyikét hajtja végre ezek egyszerre, egymás után. Ezért ezek időzítők "minimális időközönként" és az beszerzése hajt végre, amikor az időzítő jelzőként"beállítást" nyissa meg a műveleteket néven hivatkozunk. Például a fürt erőforrás-kezelő gondoskodik függőben lévő kérelmek szolgáltatások előtt terheléselosztás a fürt létrehozásához. Ahogy látja, a megadott alapértelmezett időszakonkénti, a fürt erőforrás-kezelő keres a semmit elvégzéséhez gyakran kell. Általában ez azt jelenti, hogy minden lépése során végrehajtott módosításokat a készlete kis. Kis módosítása gyakran lehetővé teszi, hogy a rugalmas kell, amikor dolgokat okozhat a fürt erőforrás-kezelőt. Az alapértelmezett időzítők adja meg, néhány kötegelés óta az azonos típusú eseményeket számos általában egy időben történik. 

Például ha csomópontok nem végezhetnek úgy teljes tartalék tartományok egyszerre. Minden ilyen hibához során a következő állapotban vannak rögzített frissítés után a *PLBRefreshGap*. A határozzák meg a következő elhelyezési korlátozás ellenőrzése során, és a terheléselosztás futtatja. Alapértelmezés szerint a fürt erőforrás-kezelő nincs keresztül a fürt módosítások órányi vizsgálata és minden módosítás egyszerre megoldására tett kísérlet. Ezzel a forgalom felszakadásáig vezetne.

A fürt erőforrás-kezelő is annak meghatározásához, hogy néhány további információra van szüksége a fürt imbalanced. Az adott konfigurációs két darabjai tudunk: *BalancingThresholds* és *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Terheléselosztás küszöbértékek
A terheléselosztás küszöbértéke a fő helyvezérlő újraelosztás indítására. A terheléselosztás küszöb olyan metrikajelentés egy _arány_. Ha a terhelést a mérőszám a legtöbb betöltött csomóponton osztva a legalább betöltött csomópont terhelés mennyisége meghaladja a metrika *BalancingThreshold*, akkor a fürt imbalanced. Emiatt terheléselosztás akkor váltódik ki, amikor legközelebb a fürt erőforrás-kezelő ellenőrzi. A *MinLoadBalancingInterval* időzítő határozza meg, milyen gyakran a fürt erőforrás-kezelő ellenőrizni kell, hogy ha újraelosztás szükség. Ellenőrzése nem jelenti azt, hogy bármi Baja. 

Terheléselosztási küszöbértékek határozzák meg a fürt definíciójának részeként metrika alapon. A metrikák további információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

az önálló verziója telepítéseinek művelet vagy az Azure-Template.json üzemeltetett fürtök:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>
![Terheléselosztási küszöbérték – példa][Image1]
</center>

Ebben a példában minden szolgáltatás van fel néhány metrika egységárát. A legfontosabb példa egy csomópont maximális terhelése öt pedig legalább két. Tegyük fel, hogy a terheléselosztási a Ez a metrika küszöbértéke három. Mivel a fürt arány 5/2 = 2.5 és, amely kisebb, mint a megadott terheléselosztási három küszöbértéket, a fürt átgondolni. Nincs terheléselosztás akkor lesz kiváltva, ha a fürt erőforrás-kezelő ellenőrzi.

Az alsó példában egy csomópont maximális terhelése: 10, pedig legalább két, ami öt aránya. Öt értéke nagyobb, mint a kijelölt terheléselosztási küszöbérték három adott mérőszám. Emiatt egy újraelosztás futtatása lesz ütemezett legközelebb a terheléselosztási időzítő következik be. Ilyen helyzetben néhány terhelés általában oszlik meg a 3. Mivel a Service Fabric fürt erőforrás-kezelő nem egy mohó módszert alkalmaz, néhány terhelés is osztják csomópont2. 

<center>
![Terheléselosztási küszöbérték példa műveletek][Image2]
</center>

> [!NOTE]
> Két különböző stratégiák a terhelést a fürt kezelése "Terheléselosztás" kezeli. Az alapértelmezett stratégia a erőforrás-kezelőt használó osszák szét a fürt csomópontjaihoz. A más stratégia [töredezettségmentesítés](service-fabric-cluster-resource-manager-defragmentation-metrics.md). A töredezettségmentesítéssel futtatása ugyanazon terheléselosztás során. A terheléselosztás és a lemeztöredezettség-mentesítés stratégiák ugyanabban a fürtben levő különböző metrikák használható. Egy szolgáltatás terheléselosztási és töredezettségmentesítési metrikák is rendelkezik. A lemeztöredezettség-mentesítés metrika, a fürt a terhelés aránya elindítja a újraelosztás, ha a _alatt_ a terheléselosztási küszöbértéket. 
>

A terheléselosztási küszöbérték alatt első célja nem egy explicit. Terheléselosztási küszöbértékek vannak csak egy *eseményindító*. Terheléselosztás fut, a fürt erőforrás-kezelő határozza meg azt is ellenőrizze, milyen fejlesztéseket ha van ilyen. Most, mert egy terheléselosztási keresési van kezdődött el nem jelenti azt semmit helyezi át. A fürt néha imbalanced, de túl korlátozott megoldására. Azt is megteheti, fejlesztést igényel, amelyek túl áthelyezések [költséges](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Tevékenység küszöbértékek
Egyes esetekben, annak ellenére, hogy csomópontok viszonylag imbalanced, a *teljes* terhelést a fürt mérete alacsony. A betöltési hiánya lehet egy átmeneti dip, vagy mert a fürt új, és csak az első bootstrapped. Mindkét esetben előfordulhat, hogy nem kívánt terheléselosztási a fürthöz, mert kevés a kell szerzett időt. Ha a fürt mentek keresztül, terheléselosztás, ehhez töltött hálózati és számítási erőforrásokat dolgot Navigálás anélkül, hogy minden nagy *abszolút* különbség. Elkerülése érdekében szükségtelen helyezi, nincs egy másik vezérlő tevékenység küszöbértékek néven ismert. Tevékenység küszöbértékek lehetővé teszi bizonyos abszolút alsó határa tevékenység megadását. Ha nem a csomópont a küszöbérték feletti, terheléselosztás nem elindul, még akkor is, ha teljesítik a terheléselosztás küszöbértéket.

Tegyük fel, azt megőrizze-e a három, ez a mérőszám a terheléselosztás küszöbértéket. Tételezzük is fel kell egy tevékenység 1536 küszöbértéket. Az első esetben a fürt imbalanced / nincs terheléselosztás küszöb pedig nincs csomópont megfelel-e tevékenység a küszöbérték, semmi nem történik. Alsó példában 1. csomópont: a tevékenység küszöbérték feletti. Mivel a terheléselosztás küszöbértéket, mind a metrika tevékenység küszöbértéke túllépve, terheléselosztás van ütemezve. Tegyük fel vizsgáljuk meg a következő ábra: 

<center>
![Tevékenység küszöbérték – példa][Image3]
</center>

Terheléselosztás küszöbértékeket, mint például tevékenység küszöbértékek meghatározott /-metrika keresztül a fürt definíciója:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

az önálló verziója telepítéseinek művelet vagy az Azure-Template.json üzemeltetett fürtök:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Terheléselosztás és a tevékenység küszöbértékek mindkét kötve az adott metrika - terheléselosztás csak akkor, ha a terheléselosztás küszöbérték és a tevékenység küszöbérték túllépése azonos metrika elindul.

> [!NOTE]
> Ha nincs megadva, a terheléselosztás a metrika küszöbértéke 1, és a tevékenység küszöbértéket 0. Ez azt jelenti, hogy a fürt erőforrás-kezelő megpróbálja tartani, hogy bármely adott teher tökéletesen kiegyensúlyozott metrika. Egyéni metrikák használata javasolt, hogy explicit módon kell megadni a saját terheléselosztás és a tevékenység küszöbértékek a metrikákat. 
>

## <a name="balancing-services-together"></a>Terheléselosztás együtt szolgáltatások
A fürt-e imbalanced vagy nem-e a fürtre vonatkozó döntés. Azonban azt érhetők el az javítsa ki a módon szolgáltatás replikákat és a példányt körüli mozgatása. Ez így érthető, ugye? Memória az egyik csomópont van halmozott, ha több replikák és példányok sikerült azonosítása érdekében azt. A egyensúlyhiány kijavítása igényel, az állapot-nyilvántartó replikák vagy állapotmentes példányok a imbalanced metrikus áthelyezését.

Alkalmanként, ha egy szolgáltatás, amelynek maga imbalanced nem lekérdezi áthelyezése (ne feledje a vitafórum a helyi és globális súlyozza korábban). Miért kellene szolgáltatás első helye, amikor, hogy a szolgáltatás metrikák kiegyensúlyozott volt? Nézzük meg, például:

- Tegyük fel, négy szolgáltatások, Service1, Service2, Service3 és Service4 van. 
- A jelentés metrikák Metric1 és Metric2 service1. 
- A jelentés metrikák Metric2 és Metric3 Service2. 
- A jelentés metrikák Metric3 és Metric4 Service3.
- A jelentés metrika Metric99 Service4. 

Minden bizonnyal láthatja, ahol az oktatóanyagban módosítjuk itt: a lánc! Valóban nincsenek négy független szolgáltatások, a kapcsolódó három szolgáltatást, a másik önállóan ki van kapcsolva van.

<center>
![Terheléselosztás együtt szolgáltatások][Image4]
</center>

A tanúsítványlánc miatt is lehet, hogy metrikák 1-4 egyenlőtlenség okozhat a replikák és a szolgáltatások 1-3 történő navigálás tartozó példányok. Is tudjuk, hogy egyenlőtlenség metrikák 1, 2 vagy 3 típusú áthelyezések nem okozhat Service4. Volna az egyetlen pont óta a replikák áthelyezése vagy annak egy példányt körüli Service4 tartozó feltétlenül nincs mit hatással lehet az egyenleg a mérőszámok 1 – 3.

A fürt erőforrás-kezelő automatikusan ki, milyen szolgáltatások kapcsolatos adatok. Hozzáadása, eltávolítása vagy módosítása a metrikák szolgáltatások hatással lehet a kapcsolatokat. Például Service2 terheléselosztás két kísérletei közötti lehet, hogy frissített Metric2 eltávolítása. Ez megszünteti a lánc Service1 és Service2 között. Most helyett a kapcsolódó szolgáltatások két csoportok nincsenek három:

<center>
![Terheléselosztás együtt szolgáltatások][Image5]
</center>

## <a name="next-steps"></a>Következő lépések
* Adatok gyűjtése le hogyan kezeli a Service Fabric fürt erőforrás-kezelő a használati és a fürt teljes kapacitását. A metrikák és konfigurálásuk módját kapcsolatos további tudnivalókért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md)
* A mozgás költsége az egyik módja az, hogy egyes szolgáltatások drágább, mint a többire áthelyezése a fürt erőforrás-kezelő jelzés. További információt a mozgás költsége hivatkoznak [Ez a cikk](service-fabric-cluster-resource-manager-movement-cost.md)
* A fürt erőforrás-kezelő rendelkezik több szabályozások lassítsa le a fürt forgalmának konfigurálható. Nem fontosságúak normál esetben szükséges, de ha szüksége van rájuk megismerheti azokat [Itt](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
