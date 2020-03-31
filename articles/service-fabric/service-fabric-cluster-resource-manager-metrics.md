---
title: Az Azure Service Fabric alkalmazásterhelés kezelése metrikák használatával
description: Megtudhatja, hogyan konfigurálhatja és használhatja a metrikák at Service Fabric szolgáltatás erőforrás-felhasználás kezelésére.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ea21502cdab35b261e20af7f23b7b522f77c6667
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452002"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Erőforrás-felhasználás és -betöltés kezelése a Service Fabricben mérőszámokkal
*Metrikák* azok az erőforrások, amelyek a szolgáltatások törődnek, és amelyeket a fürt csomópontjai által biztosított. A mérőszám minden, amit kezelni szeretne a szolgáltatások teljesítményének javítása vagy figyelése érdekében. Előfordulhat például, hogy a memóriafelhasználást figyeli, hogy a szolgáltatás túlterhelt-e. Egy másik felhasználás, hogy kitaláljuk, hogy a szolgáltatás mozoghat máshol, ahol a memória kevésbé korlátozott annak érdekében, hogy jobb teljesítményt.

Az olyan dolgok, mint a memória, a lemez és a processzorhasználat, például metrikák. Ezek a metrikák fizikai metrikák, erőforrások, amelyek megfelelnek a fizikai erőforrások a csomóponton, amelyeket kezelni kell. Metrikák is lehet (és általában azok) logikai metrikák. A logikai metrikák például a "MyWorkQueueDepth" vagy a "MessagesToProcess" vagy a "TotalRecords". Logikai metrikák az alkalmazás által meghatározott, és közvetve megfelelnek bizonyos fizikai erőforrás-felhasználás. A logikai metrikák gyakoriak, mert nehéz lehet mérni és jelenteni a fizikai erőforrások felhasználását szolgáltatásonként. A saját fizikai metrikák mérésének és jelentésének összetettsége is, ezért a Service Fabric néhány alapértelmezett metrikákat biztosít.

## <a name="default-metrics"></a>Alapértelmezett mutatók
Tegyük fel, hogy el szeretné kezdeni a szolgáltatás írását és üzembe helyezését. Ezen a ponton nem tudja, milyen fizikai vagy logikai erőforrásokat használ fel. Jó lesz így! A Service Fabric fürterőforrás-kezelő néhány alapértelmezett metrikákat használ, ha nincs más metrikák vannak megadva. Ezek a következők:

  - PrimaryCount - elsődleges replikák száma a csomóponton 
  - ReplicaCount - a csomópont összes állapotalapú replikájának száma
  - Count - a csomóponton lévő összes szolgáltatásobjektum (állapot nélküli és állapotmentes) száma

| Metrika | Állapotnélküli példánybetöltése | Állapotalapú másodlagos terhelés | Állapotalapú elsődleges terhelés | Tömeg |
| --- | --- | --- | --- | --- |
| Elsődleges szám |0 |0 |1 |Magasság |
| ReplicaCount (Replikaszáma) |0 |1 |1 |Közepes |
| Darabszám |1 |1 |1 |Alacsony |


Az alapvető számítási feladatok hoz az alapértelmezett metrikák a fürtben végzett munka megfelelő elosztását biztosítják. A következő példában nézzük meg, mi történik, ha két szolgáltatást hozunk létre, és az alapértelmezett mutatók at az egyensúlyozáshoz. Az első szolgáltatás egy állapotalapú szolgáltatás három partícióval és egy cél replika készlet három méretével. A második szolgáltatás egy állapotmentes szolgáltatás egy partícióval és egy példány száma három.

A következőkről van szó:

<center>

![Fürtelrendezés alapértelmezett mérőszámokkal][Image1]
</center>

Ügyeljen a következőkre:
  - Az állapotalapú szolgáltatás elsődleges replikái több csomópont között vannak elosztva
  - Ugyanazon partíció replikái különböző csomópontokon találhatók
  - Az elsődleges és másodlagos elsődleges elemek teljes száma a fürtben van elosztva
  - A szolgáltatásobjektumok teljes száma egyenletesen van lefoglalva az egyes csomópontokon

jó!

Az alapértelmezett metrikák jól működnek, mint a kezdet. Az alapértelmezett metrikák azonban csak eddig viszik. Például: Mi a valószínűsége annak, hogy a kiválasztott particionálási séma tökéletesen egyenletes kihasználtságot eredményez az összes partíció által? Mi az esélye annak, hogy egy adott szolgáltatás terhelése állandó az idő múlásával, vagy akár csak ugyanaz több partícióközött most?

Futtathatja csak az alapértelmezett metrikákat. Ez azonban általában azt jelenti, hogy a fürt kihasználtsága alacsonyabb és egyenetlenebb, mint szeretné. Ennek az az oka, hogy az alapértelmezett metrikák nem adaptívak, és feltételezik, hogy minden egyenértékű. Például egy elsődleges, amely elfoglalt, és egy, amely nem egyszerre járul hozzá "1" a PrimaryCount metrika. A legrosszabb esetben csak az alapértelmezett metrikák használata is eredményezhet túlütemezett csomópontok, ami teljesítményproblémákat okozhat. Ha szeretné a legtöbbet kivenni a fürtből, és elkerülni a teljesítményproblémákat, egyéni metrikákat és dinamikus terhelési jelentéseket kell használnia.

## <a name="custom-metrics"></a>Egyéni metrikák
Metrikák vannak konfigurálva egy névvel ellátott-szolgáltatás-példány alapján, amikor létrehozza a szolgáltatást.

Minden metrika rendelkezik néhány tulajdonsággal, amelyek leírják: egy nevet, egy súlyt és egy alapértelmezett terhelést.

* Metrika neve: A metrika neve. A metrikaneve a metrika egyedi azonosítója a fürtön belül az Erőforrás-kezelő szemszögéből.
* Súly: Metrika súlya határozza meg, hogy milyen fontos ez a metrika a szolgáltatás többi metrikákhoz képest.
* Alapértelmezett betöltés: Az alapértelmezett terhelés eltérő módon jelenik meg attól függően, hogy a szolgáltatás állapot nélküli vagy állapotalapú.
  * Állapotnélküli szolgáltatások esetén minden metrika egyetlen DefaultLoad nevű tulajdonsággal rendelkezik.
  * A megadott állapotalapú szolgáltatásokhoz:
    * PrimaryDefaultLoad: A szolgáltatás által felhasznált metrika alapértelmezett mennyisége, ha elsődleges
    * SecondaryDefaultLoad: A szolgáltatás által felhasznált metrika alapértelmezett mennyisége, ha másodlagos

> [!NOTE]
> Ha egyéni metrikákat határoz meg, és az alapértelmezett metrikákat _is_ szeretné használni, _explicit módon_ vissza kell adnia az alapértelmezett mutatókat, és meg kell adnia a súlyokat és az értékeket. Ennek az az oka, hogy meg kell határoznia az alapértelmezett metrikák és az egyéni metrikák közötti kapcsolatot. Például, talán érdekel ConnectionCount vagy WorkQueueDepth több, mint az elsődleges elosztás. Alapértelmezés szerint a PrimaryCount metrika súlya magas, ezért szeretné csökkenteni közepes, amikor hozzáadja a többi metrikák, hogy megbizonyosodjon arról, hogy elsőbbséget élveznek.
>

### <a name="defining-metrics-for-your-service---an-example"></a>A szolgáltatás mutatóinak meghatározása – példa
Tegyük fel, hogy a következő konfigurációt szeretné:

  - A szolgáltatás egy "ConnectionCount" nevű metrikát jelent
  - Az alapértelmezett mutatókat is használni szeretné 
  - Elvégzett néhány mérést, és tudja, hogy általában a szolgáltatás elsődleges replikája 20 egység "ConnectionCount"
  - A másodlagos a "ConnectionCount" 5 egységét használja
  - Tudja, hogy a "ConnectionCount" a legfontosabb mérőszám az adott szolgáltatás teljesítményének kezelése szempontjából
  - Továbbra is szeretné elsődleges replikák kiegyensúlyozott. Az elsődleges replikák kiegyensúlyozása általában jó ötlet, nem számít, mi történik. Ez segít megakadályozni, hogy egyes csomópontok vagy tartalék tartományok elvesztése hatással legyen az elsődleges replikák többségére. 
  - Ellenkező esetben az alapértelmezett mérőszámok rendben vannak

Az adott metrikakonfigurációval rendelkező szolgáltatás létrehozásához az alábbiakat írja:

Kód:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> A fenti példák és a dokumentum többi része ismerteti a metrikák kezelését névszerint szolgáltatásonként. Lehetőség van a szolgáltatások metrikák meghatározására a _szolgáltatástípus_ szintjén. Ez úgy érhető el, hogy megadja őket a szolgáltatásjegyzékekben. A típusszintű metrikák definiálása több okból sem ajánlott. Az első ok az, hogy a metrikanevek gyakran környezetspecifikusak. Hacsak nincs egy szilárd szerződés a helyén, nem lehet biztos abban, hogy a metrikus "Magok" egy környezetben nem "MiliCores" vagy "CoReS" másokban. Ha a metrikák vannak definiálva a jegyzékfájlban létre kell hoznia új jegyzékek környezetenként. Ez általában a különböző manifesztek elterjedéséhez vezet, csak kisebb különbségekkel, ami irányítási nehézségekhez vezethet.  
>
> Metrika terhelések általában rendelt egy névvel ellátott-szolgáltatás-példány alapján. Tegyük fel például, hogy a szolgáltatás egy példányát az Ügyfél számára hozza létre, aki csak könnyedén kívánja használni. Tegyük fel azt is, hogy létrehoz egy másikat a CustomerB számára, akinek nagyobb munkaterhelése van. Ebben az esetben valószínűleg módosítani szeretné az alapértelmezett terheléseket ezekhez a szolgáltatásokhoz. Ha a metrikák és terhelések keresztül definiált jegyzékek, és szeretné támogatni ezt a forgatókönyvet, különböző alkalmazás- és szolgáltatástípusokat igényel az egyes ügyfelek. A szolgáltatás létrehozási ideje alatt meghatározott értékek felülbírálják a jegyzékfájlban definiált értékeket, így ezzel beállíthatja az adott alapértelmezett értékeket. Ennek azonban hatására a jegyzékekben deklarált értékek nem egyeznek meg a szolgáltatás ténylegesen fut. Ez zavart okozhat. 
>

Emlékeztetőül: ha csak az alapértelmezett metrikákat szeretné használni, egyáltalán nem kell megérintenie a metrikagyűjteményt, vagy nem kell valami különlegeset tennie a szolgáltatás létrehozásakor. Az alapértelmezett metrikák automatikusan használatosak, ha nincs enek definiálva. 

Most nézzük át az egyes beállításokat részletesebben, és beszéljünk a viselkedés, hogy befolyásolja.

## <a name="load"></a>Betöltés
A metrikák meghatározásának lényege, hogy bizonyos terhelést képviseljen. *A terhelés* azt, hogy egy adott metrika mekkora részét használja fel egy adott szolgáltatáspéldány vagy replika egy adott csomóponton. A betöltés szinte bármikor konfigurálható. Példa:

  - A terhelés a szolgáltatás létrehozásakor határozható meg. Ezt _nevezzük alapértelmezett terhelésnek_.
  - A metrikainformáció, beleértve az alapértelmezett terheléseket, a szolgáltatás a szolgáltatás létrehozása után frissülhet. Ezt _szolgáltatás frissítésének nevezzük._ 
  - Egy adott partíció terhelése visszaállítható az adott szolgáltatás alapértelmezett értékeire. Ezt _nevezzük a partícióterhelés alaphelyzetbe állításának_.
  - A betöltés szolgáltatásobjektumonként dinamikusan jelenthető futásidőben. Ezt _nevezzük jelentési terhelésnek_. 
  
Mindezek a stratégiák lehet használni ugyanazon a szolgáltatáson belül élettartama alatt. 

## <a name="default-load"></a>Alapértelmezett terhelés
*Az alapértelmezett terhelés* az, hogy az egyes szolgáltatásobjektumok (állapotnélküli példány vagy állapotalapú replika) metrika-metrikából mennyit használ nak fel. A fürterőforrás-kezelő ezt a számot használja a szolgáltatásobjektum terheléséhez, amíg más információkat, például dinamikus betöltési jelentést nem kap. Egyszerűbb szolgáltatások esetén az alapértelmezett terhelés statikus definíció. Az alapértelmezett terhelés soha nem frissül, és a szolgáltatás élettartama alatt használatos. Az alapértelmezett terhelések nagyszerűen működnek az egyszerű kapacitástervezési forgatókönyvekben, ahol bizonyos mennyiségű erőforrás tasza hozzá a különböző számítási feladatokhoz, és nem változnak.

> [!NOTE]
> A kapacitáskezeléssel és a fürt csomópontjainak kapacitásmeghatározásával kapcsolatos további információkért tekintse meg [ezt a cikket.](service-fabric-cluster-resource-manager-cluster-description.md#capacity)
> 

A fürterőforrás-kezelő lehetővé teszi, hogy az állapotalapú szolgáltatások eltérő alapértelmezett terhelést adjanak meg az elsődleges és a másodlagos értékekhez. Az állapotmentes szolgáltatások csak egy olyan értéket adhatnak meg, amely az összes példányra vonatkozik. Állapotalapú szolgáltatások esetén az elsődleges és másodlagos replikák alapértelmezett terhelése általában eltérő, mivel a replikák különböző típusú munkát végeznek az egyes szerepkörökben. Az elsődleges elemek például általában olvasási és írási olvasási és írási műveleteket szolgálnak ki, és kezelik a számítási terhek nagy részét, míg a másodlagos értékek nem. Az elsődleges replika alapértelmezett betöltése általában magasabb, mint a másodlagos replika alapértelmezett betöltése. A valós számoknak a saját mérésektől kell függenie.

## <a name="dynamic-load"></a>Dinamikus terhelés
Tegyük fel, hogy már egy ideje a szolgáltatást vezeti. Némi megfigyeléssel észrevette, hogy:

1. Egy adott szolgáltatás egyes partíciói vagy példányai több erőforrást fogyasztanak, mint mások
2. Egyes szolgáltatások terhelése idővel változik.

Sok dolog van, ami ilyen típusú terhelésingadozást okozhat. Például a különböző szolgáltatások vagy partíciók különböző ügyfelek különböző követelményekkel vannak társítva. A terhelés is változhat, mert a szolgáltatás által végzett munka mennyisége a nap folyamán változik. Az októl függetlenül általában nincs egyetlen szám, amelyet alapértelmezettként használhat. Ez különösen igaz, ha azt szeretné, hogy a legtöbb kihasználtság a fürtből. Az alapértelmezett terheléshez választott értékek az idő egy részében hibásak. A helytelen alapértelmezett betöltések azt eredményezik, hogy a fürterőforrás-kezelő az erőforrások hozzárendelésén felül vagy alatt történik. Ennek eredményeképpen olyan csomópontokkal rendelkezik, amelyek túl vannak használva, még akkor is, ha a fürterőforrás-kezelő úgy véli, hogy a fürt kiegyensúlyozott. Az alapértelmezett terhelések továbbra is jók, mivel a kezdeti elhelyezéshez szolgáltatnak bizonyos információkat, de nem teljes történet a valós munkaterhelésekhez. A változó erőforrás-követelmények pontos rögzítéséhez a fürterőforrás-kezelő lehetővé teszi, hogy minden szolgáltatásobjektum frissítse saját terhelését futásidőben. Ezt nevezzük dinamikus terhelésjelentésnek.

A dinamikus terhelési jelentések lehetővé teszik a replikák vagy példányok számára, hogy módosítsák a metrikák felosztását/jelentett terhelését azok élettartama alatt. A szolgáltatás replika vagy példány, amely hideg volt, és nem végez semmilyen munkát általában azt jelenti, hogy egy adott metrika kis mennyiségű használatával. Egy forgalmas replika vagy példány azt jelenti, hogy többet használnak.

A replika vagy példányonkénti jelentéskészítési terhelés lehetővé teszi, hogy a fürterőforrás-kezelő átszervezze a fürt egyes szolgáltatásobjektumait. A szolgáltatások átszervezése segít biztosítani, hogy megkapják a szükséges erőforrásokat. Foglalt szolgáltatások hatékonyan kap a "visszaszerzése" erőforrások más replikák vagy példányok, amelyek jelenleg hideg, vagy kevesebb munkát végez.

A Megbízható szolgáltatásokon belül a betöltésdinamikusan történő jelentéskódja a következőképpen néz ki:

Kód:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Egy szolgáltatás a létrehozás időpontjában definiált mutatók bármelyikéről jelentést tehet. Ha egy szolgáltatás jelentések betöltése egy metrika, amely nincs konfigurálva a használata, service fabric figyelmen kívül hagyja a jelentést. Ha vannak más, egyidejűleg érvényes mutatók, akkor ezek a jelentések elfogadottak. A szolgáltatáskód mérhető és jelentheti az összes általa használható metrikát, és az operátorok megadhatják a metrika konfigurációját a szolgáltatáskód módosítása nélkül. 

### <a name="updating-a-services-metric-configuration"></a>Egy szolgáltatás metrikakonfigurációjának frissítése
A szolgáltatáshoz társított metrikák listája, és ezek a metrikák tulajdonságai dinamikusan frissíthetők, amíg a szolgáltatás él. Ez lehetővé teszi a kísérletezést és a rugalmasságot. Néhány példa arra, hogy ez mikor hasznos:

  - egy metrika letiltása egy adott szolgáltatás hibás jelentésével
  - a mérőszámok súlyának újrakonfigurálása a kívánt viselkedés alapján
  - új metrika engedélyezése csak azt követően, hogy a kódot már telepítették és más mechanizmusokon keresztül érvényesítették
  - szolgáltatás alapértelmezett terhelésének módosítása a megfigyelt viselkedés és fogyasztás alapján

A fő API-k a `FabricClient.ServiceManagementClient.UpdateServiceAsync` metrikakonfiguráció módosítása a C# és `Update-ServiceFabricService` a PowerShell. Bármilyen információt is ad meg ezekkel az API-kkal, azonnal lecseréli a szolgáltatás meglévő metrikaadatait. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Az alapértelmezett terhelési értékek és a dinamikus terhelési jelentések keverése
Az alapértelmezett terhelés és a dinamikus terhelések ugyanahhoz a szolgáltatáshoz használhatók. Ha egy szolgáltatás az alapértelmezett terhelési és a dinamikus betöltési jelentéseket is használja, az alapértelmezett terhelés becslésként szolgál, amíg a dinamikus jelentések meg nem jelennek. Az alapértelmezett terhelés jó, mert a fürterőforrás-kezelőnek ad valamit, amivel dolgozhat. Az alapértelmezett terhelés lehetővé teszi, hogy a fürterőforrás-kezelő a szolgáltatásobjektumokat létrehozásakor jó helyekre helyezze. Ha nincs megadva alapértelmezett betöltési információ, a szolgáltatások elhelyezése gyakorlatilag véletlenszerű. Amikor a terhelési jelentések később érkeznek, a kezdeti véletlenszerű elhelyezés gyakran hibás, és a fürterőforrás-kezelőnek át kell helyeznie a szolgáltatásokat.

Vegyük az előző példánkat, és nézzük meg, mi történik, ha hozzáadunk néhány egyéni metrikát és dinamikus terhelési jelentést. Ebben a példában a "MemoryInMb" példamérő számot használjuk.

> [!NOTE]
> Memória egyike a rendszer metrikák, amelyek service fabric [erőforrás-szabályozó,](service-fabric-resource-governance.md)és a jelentési magad általában nehéz. Valójában nem várjuk el, hogy számoljon be a memóriafogyasztásról; A memória itt a fürterőforrás-kezelő képességeinek megismerésének elősegítésére szolgál.
>

Tegyük fel, hogy kezdetben a következő paranccsal hoztuk létre az állapotalapú szolgáltatást:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Emlékeztetőül ez a szintaxis a következő: ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Lássuk, hogyan nézhet ki egy lehetséges fürtelrendezés:

<center>

![Fürt kiegyensúlyozott mind az alapértelmezett, mind az egyéni mérőszámokkal][Image2]
</center>

Néhány dolog, amit érdemes megjegyezni:

* A partíción belüli másodlagos replikák mindegyike saját terheléssel rendelkezhet
* Összességében a mérőszámok kiegyensúlyozottnak tűnnek. A memória esetében a legnagyobb és a legkisebb terhelés aránya 1,75 (a legnagyobb terhelésű csomópont N3, a legkisebb N2 és 28/16 = 1,75).

Van néhány dolog, amit még meg kell magyaráznunk:

* Mi határozta meg, hogy az arány 1,75 ésszerű volt-e vagy sem? Honnan tudja a fürterőforrás-kezelő, hogy ez elég jó-e, vagy hogy van-e még tennivaló?
* Mikor történik az egyensúly?
* Mit jelent az, hogy a memória súlya "Magas"?

## <a name="metric-weights"></a>Metrikus súlyok
Fontos, hogy ugyanazokat a mutatókat kövesse nyomon a különböző szolgáltatások között. Ez a globális nézet teszi lehetővé a fürterőforrás-kezelő számára, hogy nyomon kövesse a fürt ben lévő felhasználást, egyensúlyba hozza a csomópontok közötti felhasználást, és biztosítsa, hogy a csomópontok ne menjenek túl a kapacitáson. Előfordulhat azonban, hogy a szolgáltatások eltérő nézeteket vallanak ugyanazon mutató fontosságát illetően. Emellett egy fürtben, sok metrikák és sok szolgáltatás, tökéletesen kiegyensúlyozott megoldások nem léteznek minden metrikák. Hogyan kezelje a fürterőforrás-kezelő ezeket a helyzeteket?

Metrikasúlyok lehetővé teszik, hogy a fürt erőforrás-kezelő dönti el, hogyan egyensúlyozza ki a fürtöt, ha nincs tökéletes válasz. Metrika súlyok is lehetővé teszik a fürt erőforrás-kezelő az egyes szolgáltatások eltérő egyensúlyban. A mérőszámok négy különböző súlyszinttel rendelkezhetnek: Nulla, Alacsony, Közepes és Magas. A Nulla súlyú metrika semmit sem járul hozzá annak mérlegeléséhez, hogy a dolgok kiegyensúlyozottak-e vagy sem. A terhelés azonban továbbra is hozzájárul a kapacitáskezeléshez. A nulla súlyú metrikák továbbra is hasznosak, és gyakran használják a szolgáltatás viselkedésének és teljesítményfigyelésének részeként. [Ez](service-fabric-diagnostics-event-generation-infra.md) a cikk további információt nyújt a metrikák a szolgáltatások figyelése és diagnosztika használatával. 

A fürt különböző metrikasúlyainak valós hatása az, hogy a fürterőforrás-kezelő különböző megoldásokat hoz létre. Metrika súlyok mondja a fürt erőforrás-kezelő, hogy bizonyos metrikák fontosabbak, mint mások. Ha nincs tökéletes megoldás, a fürterőforrás-kezelő előnyben részesítheti azokat a megoldásokat, amelyek jobban kiegyensúlyozzák a nagyobb súlyozott mutatókat. Ha egy szolgáltatás úgy gondolja, hogy egy adott metrika lényegtelen, előfordulhat, hogy a metrika használata kiegyensúlyozatlan. Ez lehetővé teszi, hogy egy másik szolgáltatás egy adott metrika egyenletes eloszlását kapja, amely fontos hozzá.

Nézzünk meg egy példát néhány terhelési jelentések, és hogyan különböző metrika súlyok eredményez i különböző foglalások a fürtben. Ebben a példában azt látjuk, hogy a metrikák relatív súlyának váltása miatt a fürterőforrás-kezelő különböző szolgáltatási megoldásokat hoz létre.

<center>

![Metrikus súly példa és annak hatása a kiegyenlítő megoldásokra][Image3]
</center>

Ebben a példában négy különböző szolgáltatások, az összes jelentéskülönböző értékeket két különböző metrikák, MetricA és MetricB. Egy esetben az összes szolgáltatás határozza meg MetricA a legfontosabb (Súly = Magas) és MetricB lényegtelen (Súly = Alacsony). Ennek eredményeképpen azt látjuk, hogy a fürt erőforrás-kezelő helyezi el a szolgáltatásokat, hogy MetricA kiegyensúlyozottabb, mint a MetricB. A "kiegyensúlyozottabb" azt jelenti, hogy a MetricA alacsonyabb szórással rendelkezik, mint a Metrikus B. A második esetben megfordítjuk a metrikus súlyokat. Ennek eredményeképpen a fürterőforrás-kezelő felcseréli az A és B szolgáltatásokat, hogy olyan felosztást hozzon létre, ahol a MetricB kiegyensúlyozottabb, mint a MetricA.

> [!NOTE]
> Metrika súlyok határozza meg, hogy a fürt erőforrás-kezelő egyensúlyban kell lennie, de nem akkor, ha a kiegyensúlyozás kell történnie. A kiegyensúlyozásról további információt [ebben a cikkben](service-fabric-cluster-resource-manager-balancing.md) talál.
>

### <a name="global-metric-weights"></a>Globális metrikasúlyok
Tegyük fel, hogy a ServiceA a MetricA-t magas súlyként határozza meg, és a B a MetricA súlyát alacsonyra vagy nullára állítja. Mi az a tényleges súly, ami végül hozzászokik?

Vannak több súlyok, amelyek nyomon követik az egyes metrika. Az első súly a metrikához a szolgáltatás létrehozásakor meghatározott súly. A másik súly egy globális súly, amelyet a rendszer automatikusan számít ki. A fürterőforrás-kezelő mindkét súlyozást használja a megoldások pontozásakor. Figyelembe véve mindkét súlyt fontos. Ez lehetővé teszi, hogy a fürterőforrás-kezelő az egyes szolgáltatások saját prioritásai szerint egyensúlyba hozható legyen, és azt is biztosítsa, hogy a fürt egésze megfelelően legyen lefoglalva.

Mi történne, ha a fürterőforrás-kezelőt nem érdekelné mind a globális, sem a helyi egyensúly? Nos, könnyen hozhat létre olyan megoldásokat, amelyek globálisan kiegyensúlyozottak, de amelyek az egyes szolgáltatások gyenge erőforrás-egyensúlyát eredményezik. A következő példában nézzünk meg egy csak az alapértelmezett metrikákkal konfigurált szolgáltatást, és nézzük meg, mi történik, ha csak a globális egyenleget vesszük figyelembe:

<center>

![A hatása a globális csak megoldás][Image4]
</center>

A legfelső példában, amely csak a globális egyensúlyon alapul, a fürt egésze valóban kiegyensúlyozott. Minden csomópont rendelkezik az elsődleges és az azonos számú összes replikák száma. Ha azonban megnézzük a tényleges hatását ez a felosztás nem olyan jó: a veszteség bármely csomópont hatással van egy adott számítási feladatok aránytalanul, mert kiveszi az összes elsődleges. Ha például az első csomópont meghibásodik, a Circle szolgáltatás három különböző partíciójának három elsődleges partíciója mind elveszne. Ezzel szemben a Háromszög és a Hatszög szolgáltatások partíciói elveszítik a replika. Ez nem okoz fennakadást, azon kívül, hogy helyre kell állítania a replikát.

Az alsó példában a fürterőforrás-kezelő elosztotta a replikákat a globális és a szolgáltatásonkénti egyenleg alapján. A megoldás pontszámának kiszámításakor a globális megoldás súlyának nagy részét, az egyes szolgáltatások (konfigurálható) részét pedig a súly nagy részét adja. A metrika globális egyenlegét az egyes szolgáltatások metrikasúlyainak átlaga alapján számítjuk ki. Minden szolgáltatás a saját meghatározott metrikasúlyok szerint kiegyensúlyozott. Ez biztosítja, hogy a szolgáltatások önmagukban saját szükségleteiknek megfelelően kiegyensúlyozottak legyenek. Ennek eredményeképpen, ha ugyanaz az első csomópont sikertelen, a hiba az összes szolgáltatás összes partíciója között oszlik meg. A hatás minden ugyanaz.

## <a name="next-steps"></a>További lépések
- A szolgáltatások konfigurálásával kapcsolatos további [tudnivalók a Szolgáltatások konfigurálásáról](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- A töredezettségmentesítési metrikák definiálása az egyik módja a csomópontok terhelésének konszolidálásának, ahelyett, hogy szétosztana. A töredezettségmentesítés konfigurálásáról a [jelen cikkben](service-fabric-cluster-resource-manager-defragmentation-metrics.md) olvashat.
- Ha meg szeretné tudni, hogy a fürterőforrás-kezelő hogyan kezeli és egyensúlyozza ki a terhelést a fürtben, olvassa el a [terhelés elosztásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
- Kezdje az elejétől, és [kapjon bevezetést a Szolgáltatásháló-fürt erőforrás-kezelőjébe](service-fabric-cluster-resource-manager-introduction.md)
- A mozgásköltség az egyik módja annak, hogy jelezze a fürterőforrás-kezelőnek, hogy bizonyos szolgáltatások áthelyezése drágább, mint másoké. Ha többet szeretne megtudni a mozgási költségekről, olvassa el [ezt a cikket](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
