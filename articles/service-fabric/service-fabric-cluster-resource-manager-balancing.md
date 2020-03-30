---
title: Az Azure Service Fabric-fürt egyensúlya
description: Bevezetés a fürt és a Service Fabric fürterőforrás-kezelő közötti egyensúly kiegyensúlyozása.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8e170c27923d2bb091c4121e350809b85e4c48a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081692"
---
# <a name="balancing-your-service-fabric-cluster"></a>A szolgáltatásháló-fürt kiegyensúlyozása
A Service Fabric fürterőforrás-kezelő támogatja a dinamikus terhelés változások, reagálva a csomópontok vagy szolgáltatások hozzáadása vagy eltávolítása. Emellett automatikusan kijavítja a megszorítás-megsértéseket, és proaktív módon újraegyensúlyozza a fürtöt. De milyen gyakran történnek ezek a műveletek, és mi váltja ki őket?

A fürterőforrás-kezelő három különböző munkakategóriát hajt végre. Ezek a következők:

1. Elhelyezés – ez a szakasz foglalkozik a hiányzó állapotalapú replikák vagy állapotmentes példányok elhelyezésével. Elhelyezés magában foglalja az új szolgáltatások és az állapotalapú replikák vagy állapotmentes példányok, amelyek nem sikerült. A replikák vagy példányok törlése és eldobása itt kezeli.
2. Megkötési ellenőrzések – ez a szakasz ellenőrzi és kijavítja a rendszeren belüli különböző elhelyezési korlátozások (szabályok) megsértését. Példák a szabályok, például annak biztosítása, hogy a csomópontok nem túlkapacitás, és hogy a szolgáltatás elhelyezési korlátok teljesülnek.
3. Kiegyensúlyozás – ez a szakasz ellenőrzi, hogy szükség van-e az újraegyensúlyozásra a különböző metrikák konfigurált kívánt egyensúlyszintje alapján. Ha igen, megpróbál egy kiegyensúlyozottabb elrendezést találni a fürtben.

## <a name="configuring-cluster-resource-manager-timers"></a>Fürterőforrás-kezelő időzítőinek konfigurálása
A kiegyensúlyozás körüli vezérlők első készlete időzítők ből áll. Ezek az időzítők szabályozzák, hogy a fürterőforrás-kezelő milyen gyakran vizsgálja meg a fürtöt, és milyen korrekciós műveleteket végez.

A fürterőforrás-kezelő által ellátható különböző típusú javítások mindegyikét egy másik időzítő vezérli, amely szabályozza annak gyakoriságát. Amikor minden időzítő aktiválódik, a feladat ütemezése lesz ütemezve. Alapértelmezés szerint az Erőforrás-kezelő:

* ellenőrzi az állapotát, és alkalmazza a frissítéseket (például a felvétel, hogy egy csomópont nem érhető el) minden 1/10th a második
* másodpercenként beállítja az elhelyezés-ellenőrzési jelzőt
* másodpercenként beállítja a megkötés-ellenőrző jelzőt
* öt másodpercenként beállítja a kiegyensúlyozó jelzőt

Példák az időzítőket szabályozó konfigurációra:

ClusterManifest.xml fájl:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

a ClusterConfig.json keresztül önálló telepítésekhez vagy template.json az Azure által üzemeltetett fürtökhöz:

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

Ma a fürterőforrás-kezelő egyszerre csak egy műveletet hajt végre, egymás után. Ez az oka annak, hogy ezeket az időzítőket "minimális intervallumoknak" nevezzük, és azokat a műveleteket, amelyeket akkor hajtanak végre, amikor az időzítők "jelzők beállításaként" jelennek meg. A fürterőforrás-kezelő például gondoskodik a függőben lévő, szolgáltatások létrehozására irányuló kérelmekről a fürt kiegyensúlyozása előtt. Amint azt a megadott alapértelmezett időintervallumok ból láthatja, a fürterőforrás-kezelő mindent megvizsgál, amit gyakran meg kell tennie. Ez általában azt jelenti, hogy az egyes lépcsők során végrehajtott módosítások kicsik. A kis módosítások gyakran lehetővé teszik, hogy a fürterőforrás-kezelő reagáljon, amikor a fürtben történnek dolgok. Az alapértelmezett időzítők némi kötegelést biztosítanak, mivel az azonos típusú események közül sok általában egyidejűleg fordul elő. 

Ha például a csomópontok meghibásodnak, akkor ezt teljes tartalék tartományok egy időben. Mindezek a hibák a *PLBRefreshGap*utáni következő állapotfrissítés során kerülnek rögzítésre. A javítások meghatározása a következő elhelyezés, megkötés-ellenőrzés és kiegyensúlyozási futtatások során kerül meghatározásra. Alapértelmezés szerint a fürterőforrás-kezelő nem vizsgálhatja át a fürt több órányi változását, és nem próbálja meg egyszerre kezelni az összes módosítást. Ha így tennék, az a kavarodás kitöréséhez vezetne.

A fürterőforrás-kezelőnek további információkra is szüksége van annak megállapításához, hogy a fürt kiegyensúlyozatlan-e. Az, hogy van két másik darab konfiguráció: *BalancingThresholds* és *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Kiegyensúlyozási küszöbértékek
A kiegyensúlyozási küszöbérték az egyensúly helyreállításának fő vezérlője. A mérőszám kiegyensúlyozási küszöbértéke _arány._ Ha a legtöbb betöltött csomópont on egy metrika terhelése osztva a legkisebb betöltve csomópont terhelésének összegével meghaladja a metrika *BalancingThreshold-ját,* akkor a fürt kiegyensúlyozatlan. Ennek eredményeképpen az egyenlegezés a fürterőforrás-kezelő következő ellenőrzésekor aktiválódik. A *MinLoadBalancingInterval* időzítő azt határozza meg, hogy a fürterőforrás-kezelő nek milyen gyakran kell ellenőriznie, hogy szükség van-e újraegyensúlyozásra. Az ellenőrzés nem jelenti azt, hogy bármi is történne. 

A kiegyensúlyozási küszöbértékek metrikus alapon vannak meghatározva a fürtdefiníció részeként. A mérőszámokkal kapcsolatos további információkért tekintse meg [ezt a cikket.](service-fabric-cluster-resource-manager-metrics.md)

ClusterManifest.xml fájl

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

a ClusterConfig.json keresztül önálló telepítésekhez vagy template.json az Azure által üzemeltetett fürtökhöz:

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

![Példa kiegyenlítési küszöbértékre][Image1]
</center>

Ebben a példában minden szolgáltatás egy bizonyos metrika egy egységét fogyasztja. A legfelső példában a csomópont maximális terhelése öt, a minimum pedig kettő. Tegyük fel, hogy a kiegyenlítő küszöbértéket a metrika három. Mivel a fürt ben az arány 5/2 = 2,5, és ez kisebb, mint a megadott kiegyenlítő küszöbérték három, a fürt kiegyensúlyozott. A fürterőforrás-kezelő ellenőrzésekkor nem aktiválódik az egyensúlyozás.

Az alsó példában a csomópont maximális terhelése 10, míg a minimum kettő, ami öt arányt eredményez. Öt nagyobb, mint a kijelölt kiegyenlítő küszöbérték három az adott metrika. Ennek eredményeképpen a kiegyensúlyozási futtatás a következő alkalommal lesz ütemezve, amikor a kiegyensúlyozó időzítő aktiválódik. Egy ilyen helyzetben bizonyos terhelésáltalában a Node3 között oszlik meg. Mivel a Service Fabric fürterőforrás-kezelő nem használ egy kapzsi megközelítést, néhány terhelés is elosztható a Node2. 

<center>

![Kiegyensúlyozási küszöbérték példa műveletek][Image2]
</center>

> [!NOTE]
> A "Kiegyensúlyozás" két különböző stratégiát kezel a fürt terhelésének kezelésére. A fürterőforrás-kezelő által használt alapértelmezett stratégia a terhelés elosztása a fürt csomópontjai között. A másik stratégia a [töredezettségmentesítés](service-fabric-cluster-resource-manager-defragmentation-metrics.md). A töredezettségmentesítés ugyanazon kiegyensúlyozási futtatás során történik. A kiegyensúlyozási és töredezettségmentesítési stratégiák különböző metrikákhoz használhatók ugyanazon a fürtön belül. Egy szolgáltatás rendelkezhet kiegyensúlyozási és töredezettségmentesítési metrikákkal is. Töredezettségmentesítési metrikák esetében a fürtterhelések aránya újraegyensúlyoz, ha az a kiegyensúlyozási küszöbérték _alatt_ van. 
>

A kiegyensúlyozási küszöbérték alá jutás nem kifejezett cél. A küszöbértékek kiegyensúlyozása csak egy *eseményindító*. A kiegyensúlyozás futtatásakor a fürterőforrás-kezelő határozza meg, hogy milyen fejlesztéseket érhet el, ha van ilyen. Csak azért, mert a kiegyensúlyozó keresés elindult, nem jelenti azt, hogy bármi mozog. Néha a fürt kiegyensúlyozatlan, de túl korlátozott a helyesbítéshez. Alternatív megoldásként a fejlesztések túl [költséges](service-fabric-cluster-resource-manager-movement-cost.md)mozgásokat igényelnek).

## <a name="activity-thresholds"></a>Tevékenységküszöbértékek
Néha, bár a csomópontok viszonylag kiegyensúlyozatlan, a *teljes* terhelés a fürtben alacsony. A terhelés hiánya lehet átmeneti dip, vagy azért, mert a fürt új, és csak egyre bootstrapped. Mindkét esetben előfordulhat, hogy nem szeretne időt tölteni a fürt kiegyensúlyozása, mert kevés a nyerhető. Ha a fürt kiegyensúlyozáson ment keresztül, akkor a hálózati és számítási erőforrásokat arra fordíthatja, hogy a dolgokat nagy *abszolút* különbség nélkül mozgassa. A szükségtelen lépések elkerülése érdekében van egy másik vezérlő, a tevékenységküszöbértékek. A tevékenységküszöbértékek lehetővé teszik, hogy a tevékenységhez abszolút alsó határértéket adjon meg. Ha nincs csomópont ezen a küszöbértékfelett, a kiegyensúlyozás nem aktiválódik akkor sem, ha a kiegyensúlyozási küszöbérték teljesül.

Tegyük fel, hogy megtartjuk a hárompontos kiegyenlítő küszöbértéket ehhez a mutatóhoz. Tegyük fel, hogy 1536-os aktivitási küszöbünk van. Az első esetben, míg a fürt kiegyensúlyozatlan a kiegyensúlyozási küszöbérték, nincs csomópont megfelel a tevékenység küszöbértéket, így nem történik semmi. Az alsó példában a Node1 a tevékenységküszöbérték felett van. Mivel mind a kiegyensúlyozási küszöbértéket, mind a mérőszám tevékenységi küszöbértékét túllépi, a kiegyensúlyozás ütemezése van ütemezve. Vegyük például az alábbi ábrát: 

<center>

![Példa tevékenységi küszöbértékre][Image3]
</center>

A kiegyensúlyozási küszöbértékekhez hasonlóan a tevékenységküszöbértékek is metpontonként vannak meghatározva a fürtdefiníción keresztül:

ClusterManifest.xml fájl

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

a ClusterConfig.json keresztül önálló telepítésekhez vagy template.json az Azure által üzemeltetett fürtökhöz:

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

A kiegyensúlyozási és tevékenységi küszöbértékek egy adott metrikához vannak kötve – a kiegyensúlyozás csak akkor aktiválódik, ha ugyanazért a metrikához túllépi a kiegyensúlyozási küszöbértéket és a tevékenységküszöbértéket.

> [!NOTE]
> Ha nincs megadva, a metrika kiegyensúlyozási küszöbértéke 1, a tevékenységküszöbérték pedig 0. Ez azt jelenti, hogy a fürterőforrás-kezelő megpróbálja a metrikát tökéletesen kiegyensúlyozottan tartani az adott terheléshez. Ha egyéni metrikákat használ, javasoljuk, hogy explicit módon határozza meg a saját egyensúlyi és tevékenységi küszöbértékeket a metrikákhoz. 
>

## <a name="balancing-services-together"></a>A kiegyenlítő szolgáltatások együttesen
Az, hogy a fürt kiegyensúlyozatlan-e vagy sem, fürtszintű döntés. Azonban a javítási módja az egyes szolgáltatásreplikák és példányok áthelyezése. Ennek van értelme, ugye? Ha a memória egy csomóponton van halmozva, több replikák vagy példányok is hozzájárulnak hozzá. A kiegyensúlyozatlanság kijavítása lehet, hogy az állapotalapú replikák vagy állapotmentes példányok, amelyek a kiegyensúlyozatlan metrika áthelyezése.

Alkalmanként azonban, a szolgáltatás, amely nem maga kiegyensúlyozatlan lesz mozgott (ne feledje, a vita a helyi és globális súlyok korábban). Miért helyeződne el egy szolgáltatás, ha a szolgáltatás összes mérőszáma kiegyensúlyozott volt? Lássunk egy példát:

- Tegyük fel, hogy négy szolgáltatás van: Service1, Service2, Service3 és Service4. 
- Service1 jelentések metrikák 1 és metrika2. 
- A Service2 a Metrika2 és a Metric3 mutatókat jelenti. 
- A Service3 a Metric3 és a Metric4 mutatókat jelenti.
- Service4 jelentések Metric99 metrika. 

Bizonyára látod, hová megyünk itt: van egy lánc! Nem igazán van négy független szolgáltatásunk, három kapcsolódó szolgáltatásunk van, és az egyik önmagában ki van kapcsolva.

<center>

![Kiegyensúlyozó szolgáltatások együtt][Image4]
</center>

Ez a lánc miatt lehetséges, hogy az 1-4 metrikák egyensúlyhiánya miatt az 1-3 szolgáltatásokhoz tartozó replikák vagy példányok mozoghatnak. Azt is tudjuk, hogy az 1-es, 2-es vagy 3-as metrikák közötti egyensúlyhiány nem okozhat mozgásokat a Service4-ben. Nem lenne értelme, mivel a replikák vagy példányok service4 körül nem tehet semmit, hogy befolyásolja az 1-3 metrikák egyenlegét.

A fürterőforrás-kezelő automatikusan kitalálja, hogy milyen szolgáltatások kapcsolódnak egymáshoz. A szolgáltatások metrikáinak hozzáadása, eltávolítása vagy módosítása hatással lehet a kapcsolataikra. Például két kiegyensúlyozó Service2 futtatása között lehet, hogy frissítve lett a Metric2 eltávolításához. Ez megszakítja a Service1 és service2 közötti láncot. Most ahelyett, hogy két csoport kapcsolódó szolgáltatások, három:

<center>

![Kiegyensúlyozó szolgáltatások együtt][Image5]
</center>

## <a name="next-steps"></a>További lépések
* Metrikák, amelyek a Service Fabric fürterőforrás-kezelő kezeli a fürt ben a fogyasztás és a kapacitás. Ha többet szeretne megtudni a metrikákról és azok konfigurálásáról, olvassa el [ezt a cikket.](service-fabric-cluster-resource-manager-metrics.md)
* A mozgásköltség az egyik módja annak, hogy jelezze a fürterőforrás-kezelőnek, hogy bizonyos szolgáltatások áthelyezése drágább, mint másoké. A mozgási költségekről ebben a [cikkben](service-fabric-cluster-resource-manager-movement-cost.md) olvashat bővebben.
* A fürterőforrás-kezelő számos szabályozással rendelkezik, amelyek konfigurálhatók a fürtben a lemorzsolódás lassítására. Általában nincs rájuk szükség, de ha szüksége van rájuk, itt megismerheti [őket.](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
