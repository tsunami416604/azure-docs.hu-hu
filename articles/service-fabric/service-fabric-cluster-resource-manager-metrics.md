---
title: Mérőszámok segítségével az Azure Service Fabric-alkalmazás terhelés kezelése |} A Microsoft Docs
description: Ismerje meg hogyan konfigurálhatja és használhatja a metrikák a Service Fabric service erőforrás-használat kezelésére.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7a7d3ad59d743287e5fe13c52c6c6a1a115d53f3
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053312"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Kezelését erőforrás-használat és a terhelés a Service Fabric-metrikák
*Metrikák* az erőforrások, a szolgáltatások ellátás kapcsolatban, és amely a fürt csomópontjainak által biztosított. Egy metrika, amelyeket szeretne kezelni, annak érdekében, hogy monitorozza a szolgáltatások teljesítményét és tökéletesítéséhez. Például előfordulhat, hogy tekintse meg memóriát tudni, hogy ha a szolgáltatás túlterhelt. Egy másik használata döntse el, hogy a szolgáltatás áthelyezhetők máshol ahol memória mérete kisebb jobb teljesítmény érdekében korlátozott.

Példák többek között a memória, lemez és a CPU-használati metrikák. Ezek a metrikák olyan fizikai mérőszámokat, erőforrások, amelyek megfelelnek a fizikai erőforrásokat, amelyeket kezelni kell a csomóponton. Metrikák is lehet (és gyakran) logikai metrikákat. Logikai metrikák dolgot, például a "MyWorkQueueDepth" vagy "MessagesToProcess" vagy "TotalRecords". Metrikák logikai alkalmazás által meghatározott és közvetve megfelelnek bizonyos fizikai erőforrás-használat. Logikai metrikák gyakoriak, mert a szolgáltatás alapon fizikai erőforrásokat az mértékcsoport és a jelentés felhasználását nehézkes lehet. Miért érdemes a Service Fabric biztosít néhány alapértelmezett mérőszámok összetettségétől méréséhez és jelentéskészítési saját fizikai metrikákat is.

## <a name="default-metrics"></a>Alapértelmezett mérőszámok
Tegyük fel, hogy szeretné-e a bevezetés írása, és a szolgáltatás telepítéséhez. Milyen fizikai vagy logikai erőforrások ezen a ponton nem tudja. Ez rendben! A Service Fabric fürterőforrás-kezelő néhány alapértelmezett mérőszámok használja, ha nincs más metrikákkal meg vannak adva. Ezek a következők:

  - PrimaryCount – a csomóponton elsődleges replikák száma 
  - ReplicaCount – a csomóponton teljes állapotalapú replikák száma
  - Darabszám - száma minden szolgáltatási objektumok (állapot nélküli és állapotalapú) a csomóponton

| Metrika | Állapot nélküli példány betöltése | Állapot-nyilvántartó másodlagos betöltése | Állapot-nyilvántartó elsődleges betöltése | Tömeg |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Magas |
| ReplicaCount |0 |1 |1 |Közepes |
| Darabszám |1 |1 |1 |Alacsony |


Alapszintű számítási feladatokat az alapértelmezett mérőszámok adja meg a fürt munkahelyi finomat eloszlása. A következő példában lássuk, mi történik, ha hozunk létre két szolgáltatást, és az alapértelmezett metrikáit terheléselosztási támaszkodnak. Az első szolgáltatás az állapotalapú szolgáltatás három partícióval rendelkező, és a egy cél replika három méretének beállítása. A második service az állapotmentes szolgáltatás, amely egy partíciót és három példányszámot.

Itt látható, hogy mit kap:

<center>
![Fürt elrendezés alapértelmezett metrikákkal][Image1]
</center>

Vegye figyelembe, hogy néhány dolgot:
  - Az állapotalapú szolgáltatás elsődleges replikára több csomópont vannak elosztva.
  - Ugyanazon a partíción replikái különböző csomópontokon vannak
  - Az elsődleges és másodlagos példány hozható létre teljes száma a fürtben terjesztése
  - A szolgáltatás-objektumok teljes száma egyenletesen vannak lefoglalva minden egyes csomóponton

jó!

Az alapértelmezett mérőszámok nagyszerű, Kezdés működik. Azonban az alapértelmezett mérőszámok csak képviselik, eddig. Például: mekkora a valószínűsége, hogy a particionálási séma, kivételezett még akkor is, tökéletesen kihasználtság az eredményeket az összes partíció? Mi az esélye annak, hogy a terhelés egy adott szolgáltatáshoz idő múlásával állandó, vagy akár ugyanúgy több partíción most?

Csak az alapértelmezett metrikákkal futtatható. Azonban ez általában azt jelenti, hogy a fürtkihasználtság alacsonyabb és több mint szeretné egyenetlen. Ez azért, mert az alapértelmezett metrikák nem adaptív, és feltételezik, hogy minden rendben egyenértékű. Például, amely éppen lefoglalja egy elsődleges és, amely nem is hozzájárul a PrimaryCount mérőszámhoz "1". A legrosszabb esetben csak az alapértelmezett mérőszámok segítségével is eredményezhet overscheduled csomópontok teljesítményproblémák eredményez. Ha érdekli a legtöbbet hozhassa ki a fürt és a teljesítménybeli problémák elkerülése, egyéni metrikák és a dinamikus terhelésjelentés-készítés szeretné.

## <a name="custom-metrics"></a>Egyéni metrikák
Metrikák konfigurálása – nevű-service-példány a szolgáltatás létrehozásakor.

Bármilyen mérőszám néhány tulajdonság tartozik, amely azt írja le: a neve, a másik és a egy alapértelmezett betöltése.

* Metrika neve: A metrika neve. A metrika neve az a Resource Manager szempontjából a fürtön belül a metrika egyedi azonosító.
* Súly: Metrika súlyának határozza meg, mennyire fontos metrika, ezt a szolgáltatást a metrikák viszonyítva.
* Alapértelmezett betöltése: Az alapértelmezett terhelés jelölt eltérően attól függően, hogy a szolgáltatási állapot nélküli vagy állapotalapú.
  * Az állapotmentes szolgáltatások esetében minden egyes metrika DefaultLoad nevű egyetlen tulajdonsággal rendelkezik
  * Az állapotalapú szolgáltatások esetében definiálása:
    * PrimaryDefaultLoad: Ez a metrika az alapértelmezés szerinti ezt a szolgáltatást használ fel, ha az elsődleges
    * SecondaryDefaultLoad: Ez a metrika az alapértelmezés szerinti ezt a szolgáltatást fogyaszt másodlagos

> [!NOTE]
> Ha egyéni metrikákat határoz meg, és szeretné _is_ használja az alapértelmezett mérőszámok, meg kell _explicit módon_ adja hozzá az alapértelmezett mérőszámok biztonsági és súlyok és értékeket adjon meg a számukra. Ennek oka az, meg kell határoznia az alapértelmezett mérőszámok és az egyéni metrikákat közötti kapcsolatot. Ha például talán Önt érdeklő ConnectionCount vagy WorkQueueDepth több mint elsődleges terjesztési. Alapértelmezés szerint a PrimaryCount metrika súlyának magas,, így szeretné csökkenteni a közepes, a más metrikák, győződjön meg arról, hogy elsőbbséget hozzáadásakor.
>

### <a name="defining-metrics-for-your-service---an-example"></a>A szolgáltatás – például metrikák meghatározása
Vegyük például azt szeretné, hogy a következő konfigurációt:

  - A szolgáltatás jelentéseket "ConnectionCount" nevű metrika
  - Is szeretné használni az alapértelmezett metrikák 
  - Végezze el az egyes mérések, és tudja, hogy általában egy adott szolgáltatás elsődleges replikája foglal 20 elszámolási egység "ConnectionCount"
  - Másodlagos példány hozható létre a "ConnectionCount" 5 egységek használata
  - Hogy "ConnectionCount" a legfontosabb mérőszám tekintetében az adott szolgáltatás teljesítményének kezelése
  - Biztosan elosztott terhelésű elsődleges replikára. Terheléselosztás elsődleges replikára, általában célszerű függetlenül attól, hogy mi. Ez segít megakadályozza, hogy néhány csomópont- vagy tartalék tartomány elvesztését, valamint az elsődleges replikára többségét, mely negatív hatással. 
  - Ellenkező esetben az alapértelmezett mérőszámok rendben.

A szolgáltatás létrehozása a metrika-konfigurációval szeretne írt kód itt látható:

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

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> A fenti példák, és ez a dokumentum többi részén írja le kezelését metrikák nevű-szolgáltatási történik. Akkor is lehet a szolgáltatások a szolgáltatás-metrikáinak definiálása _típus_ szintjét. Ez a szolgáltatás manifestech megadásával történik. Típus metrikáit meghatározása több okból nem ajánlott. Az első oka, hogy a metrikák nevei gyakran környezetspecifikus. Kivéve, ha van egy cég szerződés helyen, nem lehet róla, hogy egy adott környezetben a "Magok" metrika "MiliCores" vagy "Magok" nem a mások. Ha a metrikák vannak meghatározva a jegyzékfájlban, hozzon létre új jegyzékek környezetenként szeretné. Ez általában egy másik jegyzékek csak kisebb eltéréssel, amely felügyeleti nehézségeket okozhat elterjedése vezet.  
>
> Metrika terhelések általában hozzá vannak rendelve nevű-service-példányonként történik. Például tegyük fel, hoz létre, hogy a szolgáltatás CustomerA ki fogja használni a kisebb terhelésű csak egy példánya. Is tegyük hoz létre egy másik CustomerB, akik nagyobb méretű számítási feladatokat. Ebben az esetben valószínűleg érdemes a Teljesítménybeállítások az alapértelmezett terhelések ezeket a szolgáltatásokat. Ha rendelkezik metrikákat, és terhelések keresztül jegyzékek, és meghatározott szeretné támogatja ezt a forgatókönyvet, szükséges különböző alkalmazás- és szolgáltatástípusok minden egyes ügyfél számára. Szolgáltatás létrehozáskor kell meghatározni az értékek felülbírálják a meghatározva a jegyzékfájlban, így használhatja, amely a megadott alapértelmezett értékeinek beállítása. Azonban ennek során, amely hatására az a jegyzékek nem egyeznek azokkal a, a szolgáltatás ténylegesen fut a deklarált értékeket. Ez zavart vezethet. 
>

Megjegyzés: Ha csak át szeretné használni az alapértelmezett mérőszámok, nem kell a metrikák gyűjtemény minden érintéssel, vagy bármit speciális a szolgáltatás létrehozásakor. Az alapértelmezett mérőszámok automatikusan lekérése használja, ha nincs más vannak definiálva. 

Most vegyünk át ezeket a beállításokat a részletesebben mindegyike, és beszélni a viselkedés, amely hatással van.

## <a name="load"></a>Betöltés
Pontján metrikák meghatározása, hogy néhány terhelés jelölik. *Betöltés* mennyi egy metrika által felhasznált néhány szolgáltatáspéldány vagy egy adott csomópont a replika van. Betöltés szinte bármely ponton konfigurálható. Példa:

  - Betöltés a szolgáltatás létrehozásakor lehet definiálni. Ezt nevezzük _alapértelmezett betöltési_.
  - A metrika-információkat, többek között az alapértelmezett terhelés esetén, az szolgáltatásként is frissítése után a szolgáltatás létrehozása. Ezt nevezzük _szolgáltatás frissítése_. 
  - A terhelés egy adott partíció is lehet alaphelyzetbe állítani, hogy a szolgáltatás az alapértelmezett értékükre. Ezt nevezzük _alaphelyzetbe állítása a partíció terhelés_.
  - Betöltés jelenteni lehet a egy dinamikusan futásidőben service objektum alapon történik. Ezt nevezzük _terhelés reporting_. 
  
Ezek a stratégiák mindegyike használható belül ugyanazt a szolgáltatást az élettartamuk. 

## <a name="default-load"></a>Alapértelmezett betöltése
*Alapértelmezett betöltése* mekkora igényel minden egyes szolgáltatás objektum (példány állapot nélküli vagy állapot-nyilvántartó replika): Ez a mérőszám. A fürterőforrás-kezelő mindaddig, amíg az egyéb információkat, például egy dinamikus terhelésjelentés jelentést kap használja a service objektum a betöltés ezt a számot. Egyszerűbb szolgáltatások esetén az alapértelmezett terhelés egy statikus definíciója. Az alapértelmezett terhelés soha nem frissül, és a szolgáltatás teljes élettartama szolgál. Alapértelmezett betölti működik nagy egyszerű kapacitástervezéséhez forgatókönyvek, ahol bizonyos mennyiségű erőforrást különböző számítási feladatokhoz van dedikálva, és ne módosítsa.

> [!NOTE]
> A kapacitáskezelés és meghatározása a kapacitások a csomópontok a fürtben lévő további információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

A fürterőforrás-kezelő lehetővé teszi az állapotalapú szolgáltatások egy másik alapértelmezett betöltési adja meg az elsődleges és másodlagos példány hozható létre. Állapotmentes szolgáltatások csak meg egy értéket, amely az összes példányra vonatkozik. Az állapotalapú szolgáltatások, az alapértelmezett betöltési elsődleges és másodlagos replikák általában különböző mivel, replikák hajtsa végre a különféle munkahelyi minden szerepkörben. Ha például elsődleges általában szolgálja ki olvasásokat és az írásokat, és kezelni a számítási feladatot jelent, a legtöbb, miközben másodlagos példány hozható létre nem. Az elsődleges replika alapértelmezett betöltés általában a magasabb, mint a másodlagos replikákra vonatkozó alapértelmezett terhelést. A valós számmá saját mértékek függ.

## <a name="dynamic-load"></a>Dinamikus terhelésjelentés
Tegyük fel, hogy futtatja, a szolgáltatás egy ideig. Az egyes figyeléssel, észrevette, hogy:

1. Mint a többi egyes partíciók vagy egy adott szolgáltatás példánya több erőforrást
2. Egyes szolgáltatások rendelkeznek be, amely az idő függvényében változik.

Nincs sok dolog, ami ilyen típusú betöltési ingadozások által megkövetelt. Például különböző szolgáltatások vagy partíciókat társítva, különböző igényeket ügyfelek. Terhelés is változhat, mert az a nap folyamán változik a szolgáltatásnak nincs munkamennyiség. Emiatt függetlenül nincs általában nincs egyetlen szám, amely az alapértelmezett is használhat. Ez különösen igaz, ha szeretne kapni a legtöbb kihasználtság a fürtből. Válasszon ki alapértelmezett terhelés bármilyen érték helytelen máskor. Helytelen alapértelmezett betölti az eredmény a fürt Resource Manager lefoglalja az erőforrásokat alatt vagy fölött. Ennek eredményeképpen, amely felett vagy alatt használ annak ellenére, hogy a fürterőforrás-kezelő fenyegetésként észlel, a fürt kiegyensúlyozott csomóponttal rendelkezik. Alapértelmezett terhelések továbbra is jók, mivel a kezdeti elhelyezésre néhány információt biztosítanak, de nem egy teljes története a valós számítási feladatokhoz. Erőforrás követelményeknek megfelelően pontosan rögzítéséhez a fürterőforrás-kezelő lehetővé teszi, hogy minden egyes szolgáltatás objektum frissítése a saját terhelést futásidőben. Dinamikus terhelésjelentés-készítés nevezik.

Dinamikus terhelésjelentés jelentései lehetővé teszik a replikákat, vagy állítsa be a foglalási/jelentett terhelés mérőszámok élettartamuk-példányok. Szolgáltatás replikaként vagy -példányt, amely a ritka elérésű, és ezt nem teszi meg minden olyan munkahelyi lett volna általában jelentést, hogy egy metrika kis mennyiségű korábban használt. Egy foglalt replika- vagy jelentést szeretné használják több.

A fürterőforrás-kezelő a fürtben az egyes szolgáltatási objektumok átszervezése terhelés replika- vagy jelentéskészítő lehetővé teszi. A szolgáltatások átszervezése segít biztosítjuk, hogy a szükséges erőforrásokat. Foglalt szolgáltatások hatékonyan lekérése "erőforrásokat kíván felszabadítani" más replikák vagy-példányok jelenleg offline vagy kevesebb munka során.

A Reliable Services a kód betöltésének jelentéséhez dinamikusan a következőhöz hasonló:

Kód:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

A szolgáltatás bármely, a létrehozáskor kell meghatározni a metrikák is jelentést. A jelentések terhelését egy metrika, amely nincs konfigurálva, ha a Service Fabric figyelmen kívül hagyja ezt a jelentést. Ha más metrikákkal egyszerre jelentett érvényes, ezeket a jelentéseket fogadja. Szolgáltatás kódot mérjük, és jelentse a mérőszámok, tudni fogja, hogyan és operátorok azt a metrika konfigurációt használni a szolgáltatást kód módosítása nélkül. 

### <a name="updating-a-services-metric-configuration"></a>A szolgáltatási metrika konfiguráció frissítése
A szolgáltatáshoz tartozó mérőszámok listája, és ezeket a metrikákat tulajdonságai frissíthetők dinamikusan a szolgáltatás viszont élő. Ez lehetővé teszi a Kísérletezési és rugalmasságot biztosít. Néhány példa, ha ez akkor hasznos, amelyek:

  - egy metrika egy adott szolgáltatáshoz buggy jelentés letiltása
  - a kívánt viselkedés alapján mérőszámok súlyok újrakonfigurálása
  - csak a kód már üzembe helyezett és más mechanizmusok használatával érvényesítése után, amely lehetővé teszi, hogy egy új metrika
  - a megfigyelt viselkedését és a használat alapján a szolgáltatás alapértelmezett terhelés módosítása

A változó konfigurációja a fő API-k `FabricClient.ServiceManagementClient.UpdateServiceAsync` a C# és `Update-ServiceFabricService` a PowerShellben. Bármilyen API-k a megadott információk azonnal lecseréli a meglévő metrika-információkat a szolgáltatás. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Betöltés alapértékeket és dinamikus terhelésjelentés-jelentések
Alapértelmezett terhelését és a dinamikus terhelések ugyanazt a szolgáltatást is használható. Ha egy szolgáltatás használja az alapértelmezett terhelés és a dinamikus terhelésjelentés-jelentések, alapértelmezett betöltése szolgál becsült mindaddig, amíg meg dinamikus jelentéseket. Alapértelmezett betöltése jó, mert biztosítja a fürterőforrás-kezelő valami dolgozhat. Az alapértelmezett terhelés lehetővé teszi, hogy a fürterőforrás-kezelő jó helyen helyezi el a szolgáltatási objektumok azok létrehozásakor. Nincs alapértelmezett betöltési információ áll rendelkezésre, ha a szolgáltatások elhelyezését hatékonyan véletlenszerű. Terhelés jelentések érkezésekor később a kezdeti véletlenszerű elhelyezési gyakran nem megfelelő, és a fürterőforrás-kezelő szolgáltatások áthelyezése rendelkezik.

Nézzük az előző példában igénybe, és lássuk, mi történik, ha hozzáadunk néhány egyéni metrikák és a dinamikus terhelésjelentés-készítés. Ebben a példában a "MemoryInMb" mint egy példa a metrika használjuk.

> [!NOTE]
> Memória egyike a rendszer metrikák, amelyeket a Service Fabric [erőforrás-szabályozása](service-fabric-resource-governance.md), és saját kezűleg reporting általában nehéz. Nem ténylegesen Terveink szerint már, hogy a jelentés a memóriát; Felhasznált memória egy támogatási lehetőségekről, a fürterőforrás-kezelő Learning itt.
>

Nézzük feltételezik, hogy kezdetben létrehozott az állapotalapú szolgáltatásból a következő paranccsal:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Ne feledje Ez a szintaxis használata ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Nézzük meg, milyen egy lehetséges fürt elrendezés nézhet:

<center>
![Fürt kiegyensúlyozott alapértelmezett és egyéni metrikákkal][Image2]
</center>

Néhány dolgot, amelyek megjegyezni:

* Másodlagos replikák egy partíción belül minden egyes rendelkezhet saját betöltése
* A metrikák teljes elosztott terhelésű meg. A memória, a maximális és minimális terhelés között arány, 1,75 összeget (a csomópont és a legtöbb terhelés nem N3, a legkevésbé N2 és 28/16 = 1,75 összeget).

Néhány dolog, hogy továbbra is el kell magyarázniuk:

* Mi határozza meg, hogy 1,75 összeget-es ésszerű volt-e vagy sem? Hogyan nem a fürterőforrás-kezelő, hogy ha ez elég jó, vagy ha nincs a munka tegye?
* Nem terheléselosztási mikor?
* Mit jelent, hogy memória "Nagy" súlyozott-e?

## <a name="metric-weights"></a>Metrika súlyok
Fontos, metrikák követése különböző szolgáltatások között. Globális nézet, hogy milyen tevékenységeket engedélyez a fürt használat nyomon, használatalapú elosztása a csomópontok között, és győződjön meg arról, hogy a csomópont feletti kapacitás nem halad a fürterőforrás-kezelő. Előfordulhat azonban, szolgáltatások különböző nézeteket a helyrendszerszerepkörökre ugyanazt a metrika fontossága. Emellett számos metrikákkal és nagy mennyiségű szolgáltatások fürtben, tökéletesen elosztott terhelésű megoldások nem létezik az összes metrikát. Hogyan kezelje a fürterőforrás-kezelő ezekben a helyzetekben?

Metrika súlyok lehetővé teszik a fürterőforrás-kezelő dönthet arról, hogy a fürt egyensúlyának kialakításához, amikor nincs tökéletes válasz. Metrika súlyok is lehetővé teszi a fürterőforrás-kezelő adott szolgáltatások eltérően elosztása érdekében. A metrikákhoz négy különböző súly szint is: nulla, alacsony, közepes és nagy. Egy metrika egy nulla-as súlyozással rendelkező járul hozzá semmi, amikor a mérlegeli, hogy dolog, vagy nem elosztott terhelésű. A betöltés azonban továbbra is járulnak hozzá a kapacitáskezelés. Metrikák nulla súlyú továbbra is hasznosak, és gyakran használt viselkedéséről, és az alkalmazásteljesítmény-figyelő részeként. [Ez a cikk](service-fabric-diagnostics-event-generation-infra.md) a metrikákat a figyelés és diagnosztika a szolgáltatások további információkat biztosít. 

A fürt másik mérőszám súlyok valós hatása, hogy a fürterőforrás-kezelő hoz létre a különböző megoldások. Metrika súlyok tájékoztatnia kell a fürterőforrás-kezelő, hogy bizonyos metrikák fontosabbak, mint mások. Amikor tökéletes megoldás nincs a fürterőforrás-kezelő megoldások, amelyek a magasabb jobb súlyozott metrikák elosztása is inkább. Ha egy szolgáltatás fenyegetésként észlel, egy adott metrika nem lényeges, imbalanced talál használata során ennek a mutatónak. Ez lehetővé teszi az egyenletes eloszlás néhány mérőszám, amely fontos, hogy azt egy másik szolgáltatásnak.

Vegyünk egy példát néhány terhelés jelentések és a különböző metrikát súlyozza a fürt másik hozzárendelések eredményez. Ebben a példában látható, hogy a relatív súly metrikák váltás hatására a fürterőforrás-kezelő szolgáltatások különböző szabályok létrehozásához.

<center>
![Metrika súlyának példa és a hatása a terheléselosztási megoldások][Image3]
</center>

Ebben a példában nincsenek négy különböző szolgáltatást, két különböző mérőszámokat, MetricA és MetricB minden jelentéskészítési eltérő értékeket. Egyetlen esetet tartalmaz, az összes szolgáltatás MetricA határozhat meg a kiszolgáló, a legfontosabb (súly nagy =) és MetricB, jelentéktelennek tartott (súly = alacsony). Ennek eredményeképpen láthatjuk, hogy a fürterőforrás-kezelő szolgáltatásokat helyezi el, hogy MetricA jobban, mint a MetricB elosztott terhelésű. "Jobban elosztott terhelésű" azt jelenti, hogy rendelkezik-e egy alacsonyabb MetricA MetricB, mint egy alacsonyabb szórás rendelkezik. A második esetben metrika végpontkészletben névkeresési azt. Ennek eredményeképpen a fürterőforrás-kezelő felcserélése szolgáltatások, így kapja meg a felosztás ahol MetricB jobban az elosztott terhelésű MetricA, mint A és B.

> [!NOTE]
> Metrika súlyok határozza meg, hogyan a fürterőforrás-kezelő kell terheléselosztást végeznie, de nem terheléselosztási kell mikor. A terheléselosztás további információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globális metrika súlyok
Tegyük fel, ServiceA meghatározása szerint nagy tömegű MetricA és ServiceB MetricA súlyát beállítja az alacsony vagy nulla. Mi a tényleges súly, amely kap?

Nincsenek minden metrika nyomon követett több súlyok. Az első súly megadva a metrika a szolgáltatás létrehozásakor. A többi súly egy globális súly, amely automatikusan számítja ki. A fürterőforrás-kezelő megoldások pontozási mindkét e súlyok használja. Fontos, mindkét súlyok figyelembe véve. Ez lehetővé teszi a fürterőforrás-kezelő elosztása az egyes szolgáltatások saját prioritásoknak megfelelően, és bizonyosodjon meg arról, hogy a fürt teljes megfelelően van lefoglalva.

Mi történne a fürterőforrás-kezelő nem érdeklik a globális és a helyi egyenleg? Könnyebbé vált, amely globálisan elosztott terhelésű, de egyes szolgáltatások gyenge erőforrás elosztás eredményező megoldások létrehozásához. A következő példában pedig tekintse meg a szolgáltatás csak az alapértelmezett metrikákkal konfigurálva, és lássuk, mi történik, ha csak a globális elosztása minősül:

<center>
![Csak globális megoldást hatása][Image4]
</center>

A felső a példában csak a globális elosztása alapján a fürt teljes valóban kiegyensúlyozott. Összes csomópontja rendelkezik az elsődleges és a azonos számú teljes replikák azonos száma. Azonban, ha megtekinti a tényleges hatását a felosztás nem tanácsos tehát: elvesztését, bármely csomópontról milyen hatással van egy adott munkaterhelés aránytalanul, mert ki az összes hozzá tartozó elsődleges vesz igénybe. Például ha az első csomópont meghibásodik, a kör szolgáltatás három különböző partíciók három eredményezi az összes elveszhetnek. Ezzel szemben a háromszög és hatszög szolgáltatásokhoz érhető el a partíciók elveszíti a replika. Ennek hatására nem megszakadása, hogy a lefelé replika helyreállítása.

Az utolsó példában a fürterőforrás-kezelő a replikákat a globális és a szolgáltatási egyenleg alapján van elosztva. A megoldás a pontszámok kiszámítása során a súlyozás a legtöbb biztosít a globális megoldást, és a egy egyéni szolgáltatások (konfigurálható) részt. Metrika globális elosztás alapján lesz kiszámítva az egyes szolgáltatások metrikus súlyozású átlagát. Minden egyes szolgáltatás kiegyensúlyozott a saját meghatározott metrika súlyok megfelelően. Ez biztosítja, hogy a szolgáltatások rendszer elosztott terhelésű belül magukat a saját igényeinek megfelelően. Ennek eredményeképpen ha az azonos első csomópont meghibásodik, a hiba legyen elosztva a összes szolgáltatás összes partíció. A hatás az egyes megegyezik.

## <a name="next-steps"></a>További lépések
- További információt a szolgáltatások konfigurálása [szolgáltatások konfigurálásával kapcsolatos további](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Lemeztöredezettség-mentesítés metrikák meghatározása módja egy konszolidálhatja helyett ezzel azt csomópontok terhelése. Lemeztöredezettség-mentesítés konfigurálása, lásd: [Ez a cikk](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Ismerje meg hogyan a fürterőforrás-kezelő felügyeli, és elosztja a terhelést a fürtben, tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)
- Elölről kezdődik, és [, a Service Fabric fürterőforrás-kezelő bemutatása](service-fabric-cluster-resource-manager-introduction.md)
- A mozgás költsége egy módja, hogy bizonyos funkciók drágább, mint a többi áthelyezése a fürt Resource Manager jelzés. A mozgás költsége kapcsolatos további információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
