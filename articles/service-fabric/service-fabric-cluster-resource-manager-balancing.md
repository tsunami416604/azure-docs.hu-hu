---
title: Az Azure Service Fabric-fürt terheléselosztása |} A Microsoft Docs
description: A fürt és a Service Fabric fürterőforrás-kezelő terheléselosztás bemutatása.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 74fe4f7c4c231f80c7555f39f840a85baae310e9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662023"
---
# <a name="balancing-your-service-fabric-cluster"></a>Terheléselosztás a service fabric-fürt
A Service Fabric-fürt Resource Manager támogatja a dinamikus terhelésének változásaihoz igazodik, bővítésekkel vagy a csomópontok vagy szolgáltatások eltávolítására való reagálás. Automatikusan kijavítja a megkötés megsértésének, és proaktív módon rebalances a fürtöt. De gyakoriságát. Ezek a műveletek megnyílik, és mi elindítja őket?

Az alábbi három különböző kategória, amely végrehajtja a fürterőforrás-kezelő munka. Ezek a következők:

1. Elhelyezését – ebben a szakaszban olyan bármilyen állapot-nyilvántartó replikák vagy hiányzó állapotmentes példányok foglalkozik. Az Elhelyezés tartalmaz az új szolgáltatásokról és kezelési állapot-nyilvántartó replikák vagy állapotmentes példányok, amelyek nem tudták. Törlése és a replikák és példányok elvetését itt kezelése.
2. Megkötés ellenőrzi – ebben a fázisban ellenőrzi és kijavítja a megsértésének a különböző elhelyezési korlátozások (szabályok) a rendszeren belül. Szabályok Példák többek között annak biztosítására, hogy csomópont feletti kapacitás nem állnak, és, hogy teljesülnek-e a szolgáltatás-elhelyezési korlátozások.
3. Terheléselosztás – ebben a fázisban ellenőrzi, hogy újraegyensúlyozása e szükséges különböző metrikák elosztás beállított kívánt szintje alapján. Ha igen, megkísérli megtalálni a elrendezést a fürtben, amely több elosztott terhelésű.

## <a name="configuring-cluster-resource-manager-timers"></a>A fürterőforrás-kezelő időzítők konfigurálása
Terheléselosztás körül első meg olyan időzítők. Ezek időzítők szabályozzák, milyen gyakran a fürterőforrás-kezelő megvizsgálja a fürt és a szükséges javítási műveleteket.

Minden ezeket a fürterőforrás-kezelő győződjön meg arról is, javításokat különböző típusú szabályozza egy másik számlálót, amely annak gyakoriságát szabályozza. Minden egyes időzítő akkor aktiválódik, amikor a feladat ütemezése. Alapértelmezés szerint az erőforrás-kezelő:

* megvizsgálja az állapotba, és alkalmazza a frissítéseket (például, hogy a csomópont nem rögzítés) minden 1/10 másodperc
* az elhelyezési ellenőrzést jelzőjének beállítása 
* Beállítja azt a korlátozást ellenőrzés jelzőt másodpercenként
* Beállítja azt a terheléselosztási jelzőt öt másodpercenként.

A konfiguráció ezek időzítők vonatkozó példák alatt van:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

az önálló verziója telepítéseinek ClusterConfig.json vagy Template.json az Azure-ban futó fürtök:

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

Ma a fürterőforrás-kezelő csak hajtja végre az alábbi műveletek egyikét egyszerre, egymás után. Ezért ezek időzítők "minimális időközönként" és a művelet, amelyet az első amikor az időzítő lépjen "beállítás jelzőként" nevezzük. Ha például a fürterőforrás-kezelő gondoskodik a függőben lévő kérések előtt a fürt terheléselosztási szolgáltatások létrehozására. Amint láthatja, hogy a megadott alapértelmezett időszakonkénti, a fürterőforrás-kezelő bármit tennie gyakran kell vizsgálatokat végez. Általában ez azt jelenti, hogy minden lépés során végrehajtott módosításokat kisebb. Kisebb módosítások gyakran lehetővé teszi, hogy a fürterőforrás-kezelő, legyen elérhető, amikor a fürt dolog történik. Az alapértelmezett időzítők adjon meg néhány kötegelés, mivel számos, az azonos típusú eseményeket általában egy időben történnek. 

Például ha csomópontok nem egyszerre Ez nem így teljes tartalék tartományokat. Ezek a hibák rögzítve lesznek a következő állapotban során az összes frissítése után a *PLBRefreshGap*. A szükséges javítások elvégzése során a következő elhelyezési korlátozás check, határozza meg, és a terheléselosztás futtatja. Alapértelmezés szerint a fürterőforrás-kezelő nem keresztül a fürt módosításait órányi vizsgálatát és próbál cím egyszerre az összes módosítást. Ez a szolgáltatás adatforgalom vezetne.

A fürterőforrás-kezelő is szüksége van annak meghatározásához, hogy további információkat a fürt imbalanced. Az, hogy más kétféle konfigurációs van: *BalancingThresholds* és *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Terheléselosztás küszöbértékek
A terheléselosztás küszöbértéket a fő vezérlő újraegyensúlyozása indítására. A terheléselosztás küszöbértéke egy metrika egy _arány_. Ha a terhelés egy metrika a legtöbb betöltött csomóponton elosztja a terhelést a legkevésbé betöltött csomóponton mennyisége meghaladja a metrika *BalancingThreshold*, akkor a fürt imbalanced. Ennek eredményeképpen a terheléselosztás akkor aktiválódik, a következő alkalommal, amikor a fürterőforrás-kezelő ellenőrzi. A *MinLoadBalancingInterval* időzítő határozza meg, milyen gyakran ellenőrizze a fürterőforrás-kezelő Ha újraegyensúlyozása szükség. Ellenőrzése nem jelenti azt, hogy bármi Baja. 

Terheléselosztási küszöbértékek határozzák meg a fürt definíciója részeként metrika alapon. A metrikák további információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

az önálló verziója telepítéseinek ClusterConfig.json vagy Template.json az Azure-ban futó fürtök:

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

![Terheléselosztási küszöbérték példa][Image1]
</center>

Ebben a példában minden egyes szolgáltatás néhány metrika egy egységet is használja. Az első példában egy csomópont maximális terhelése öt pedig legalább két. Tegyük fel, hogy ez a metrika terheléselosztási küszöbértéke három. Mivel a a fürtben lévő aránytól 5/2 = 2.5-ös és, amely kisebb, mint a megadott küszöbérték három terheléselosztás, a fürt kiegyensúlyozott. Nincs terheléselosztás akkor aktiválódik, ha a fürterőforrás-kezelő ellenőrzi.

Az utolsó példában egy csomópont maximális terhelése 10, pedig legalább két, öt-es eredményez. 5 akkor nagyobb, mint az adott metrika három terheléselosztási küszöbértéket. Ennek eredményeképpen egy újraegyensúlyozása futtatása lesz akkor következik be, a terheléselosztási időzítő ütemezett legközelebb. Egy ilyen helyzetben bizonyos betöltés általában csomópont3 terjesztése. A Service Fabric fürterőforrás-kezelő nem használ a mohó megközelítést, mert néhány terhelés is csomópont2 juttatni. 

<center>

![Terheléselosztási küszöbérték példa műveletek][Image2]
</center>

> [!NOTE]
> A fürt a terhelés kezeléséhez két különböző stratégiák "Terheléselosztási" kezeli. Az alapértelmezett stratégiát, amely a fürterőforrás-kezelő használja, hogy a fürt csomópontjai között oszthatja el a terhelést. A többi stratégia [töredezettségmentesítési](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Lemeztöredezettség-mentesítés futtatása ugyanazon terheléselosztási során történik. A terheléselosztás és a lemeztöredezettség-mentesítés stratégiák is használható ugyanazon a fürtön belül különböző mérőszámokat. A szolgáltatás terheléselosztást és töredezettségmentesítési metrikák is rendelkezhet. Lemeztöredezettség-mentesítés metrikákhoz, a fürt a terhelés aránya aktivál újraegyensúlyozása, ha az _alábbi_ a terheléselosztási küszöbértéket. 
>

A terheléselosztási küszöbérték alatt első nem-explicit cél. Terheléselosztási küszöbértékeket is csak egy *eseményindító*. Terheléselosztás fut, amikor a fürterőforrás-kezelő azt határozza meg, javítások, azt is ellenőrizze, ha van ilyen. Megkezdődik a terheléselosztási keresés nem jelenti bármit helyezi át. Egyes esetekben a fürt nem imbalanced, de a túl korlátozott kijavítása érdekében. Azt is megteheti, a fejlesztést szükséges áthelyezések száma –, amelyek túl [költséges](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Tevékenység küszöbértékek
Néha, bár a csomópontok viszonylag imbalanced a *teljes* mennyiségű terhelést a fürtben értéke alacsony. Betöltés hiánya lehet egy átmeneti dedikált IP-címmel, vagy georeplikációra, mert a fürt létrejött, új, és csak a kezdeti lépéseket ismertető. Mindkét esetben előfordulhat, hogy nem szeretné a fürt terheléselosztás, mert kevés helymegtakarítás időt. Ha a fürt mentek keresztül terheléselosztás, ehhez hálózati költségek és számítási erőforrásokat, anélkül, hogy minden nagy dolog mozgatására *abszolút* különbség. Elkerülése érdekében a szükségtelen helyez át, egy másik vezérlő tevékenység küszöbértékek néven. Tevékenység küszöbértékek néhány abszolút alsó határérték tevékenység megadását teszi lehetővé. Ha nem csomópont nem, ez meghaladja a küszöbértéket, terheléselosztási nem indul el, akkor is, ha a terheléselosztási küszöbértéket.

Tegyük fel, hogy azt megőrizni három a mérőszám a terheléselosztás küszöbértéket. Is tegyük fel, van egy tevékenység 1536 küszöbértéket. Az első esetben imbalanced száma a terheléselosztás küszöbérték nincs a fürt pedig nem csomópont megfelel-e a tevékenység küszöbérték, semmi nem történik. Az utolsó példában csomópont1 van, a tevékenység meghaladja a küszöbértéket. Mivel a terheléselosztás küszöbérték és a tevékenység a metrika küszöbértéke túllépve, terheléselosztási van ütemezve. Példaként nézzük meg a következő ábra: 

<center>

![Tevékenységi küszöbérték példa][Image3]
</center>

Terheléselosztás küszöbértékeket, mint a tevékenység küszöbértékek /-mérőszámok keresztül a fürt definíciója:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

az önálló verziója telepítéseinek ClusterConfig.json vagy Template.json az Azure-ban futó fürtök:

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

Terheléselosztás és a tevékenység küszöbértékek is kapcsolódik egy adott mérőszám - terheléselosztás akkor indul el, csak akkor, ha ugyanazt a metrika túllépte a a küszöbérték terheléselosztás és a tevékenység küszöbértéket.

> [!NOTE]
> Ha nincs megadva, a terheléselosztás egy metrika küszöbértéke: 1, és a tevékenység küszöbértéket 0. Ez azt jelenti, hogy a fürterőforrás-kezelő megpróbálja megőrizni, ennek a mutatónak tökéletesen számára bármely adott load balanced. Egyéni metrikák használata ajánlott, hogy explicit módon kell megadni a saját és a tevékenység küszöbértékeket a metrikákat. 
>

## <a name="balancing-services-together"></a>Terheléselosztás a szolgáltatások együtt
A fürt-e imbalanced vagy nincs-e egy fürtre kiterjedő döntés. Azonban meg is vagyunk, javításával kapcsolatos módja mozgatása adott szolgáltatás replikák és példányok körül. Ez így érthető, ugye? Memória van halmozott egy csomóponton, ha több replika vagy példányok sikerült azonosítása érdekében azt. A egyenetlenségének kijavítása igényel, az állapot-nyilvántartó replikákat vagy a imbalanced metrikus állapotmentes példányok áthelyezése.

Esetenként azonban semmi ok, egy szolgáltatás, amelynek nem magát imbalanced áthelyezett lekérdezi (ne felejtse el a hozzászólás, helyi és globális súlyozza korábban). Miért érdemes lenne egy szolgáltatás első helye, amikor, hogy a szolgáltatás metrikák kiegyensúlyozott is? Nézzük meg, például:

- Tegyük fel, négy szolgáltatások, Service1, Service2, Service3 és Service4. 
- Service1 jelentések Metric1 és Metric2 metrikákat. 
- Service2 metrikák Metric2 és Metric3 jelentések. 
- Service3 metrikák Metric3 és Metric4 jelentések.
- Service4 metrika Metric99 jelentések. 

Minden bizonnyal, láthatja, ahol most be fogjuk itt: A láncban van! Nem igazán kell, hogy független a négy szolgáltatáshoz, három kapcsolódó szolgáltatásokat el, amely önállóan ki van kapcsolva.

<center>

![Terheléselosztás a szolgáltatások együtt][Image4]
</center>

A tanúsítványlánc miatt lehetséges, hogy a metrikák 1 és 4 közötti egyensúlyhiány okozhat a replikák és példányok szolgáltatások 1-3 való mozgáshoz tartozó. Is tudjuk, hogy a metrikák 1, 2 vagy 3 egyenlőtlenség Service4 nem okozhat a áthelyezések száma –. Lenne nincs pont óta a replikák áthelyezése vagy példány körüli Service4 tartozó is figyelmen kívül hagyás feltétlenül hatással lehetnek az egyenleg metrikák 1 – 3.

A fürterőforrás-kezelő automatikusan kitalálja, hogy mely szolgáltatások tartoznak. Hozzáadása, eltávolítása vagy módosítása a szolgáltatások mérőszámait hatással lehet a kapcsolatok. Ha például Service2 terheléselosztási két futtatásai között előfordulhat, hogy frissítve lett-e Metric2 eltávolítása. Ez megszünteti a lánc Service1 és Service2 között. Most már két csoportját a kapcsolódó szolgáltatások helyett háromféle:

<center>

![Terheléselosztás a szolgáltatások együtt][Image5]
</center>

## <a name="next-steps"></a>További lépések
* Metrikák, hogyan kezeli a Service Fabric-fürt erőforrás-kezelő a használat és a kapacitás a fürtben. Metrikák és a konfigurálásukról kapcsolatos további információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md)
* A mozgás költsége egy módja, hogy bizonyos funkciók drágább, mint a többi áthelyezése a fürt Resource Manager jelzés. A mozgás költsége kapcsolatos további információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-movement-cost.md)
* A fürterőforrás-kezelő rendelkezik, amelyeket a fürt lemorzsolódási lassítani konfigurálhat több szabályozások. Ezek még nem normál esetben szükséges, de szükség esetén tudhat meg róluk [Itt](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
