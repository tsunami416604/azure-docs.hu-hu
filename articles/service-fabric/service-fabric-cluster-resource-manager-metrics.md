---
title: Azure mikroszolgáltatási terhelés mérőszámainkat kezelése |} Microsoft Docs
description: További tudnivalók konfigurálásával és a Service Fabric mérőszámok segítségével erőforrás-felhasználás kezelésére.
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
ms.openlocfilehash: 26dffa7e57da2ef383f078c7c5cbb7b9664923ee
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Kezelése erőforrás-felhasználás és a metrikák a Service Fabric terheléselosztási
*Metrikák* az erőforrás, a szolgáltatások ítélt információkat, és amely a fürt csomópontja által biztosított. Egy metrika, amelyeket szeretne kezelni, javítása vagy a szolgáltatások teljesítményének figyeléséhez. Például előfordulhat, hogy figyelje a rendszermemóriát tudni, hogy ha a szolgáltatás túlterhelt. Egy másik rendeltetése mérje fel, hogy a szolgáltatás tudta áthelyezni máshol ahol memória kisebb korlátozott ahhoz, hogy nagyobb teljesítményre van szüksége-e.

Többek között a memória, a lemez és a CPU-használati metrikák példák. Ezen adatok gyűjtése le fizikai metrika, erőforrásokat, amelyek megfelelnek a csomópontot kell kezelni, a fizikai erőforrásokat. Metrikák is lehet (és gyakran) logikai metrikákat. Logikai metrikák többek között a "MyWorkQueueDepth" vagy "MessagesToProcess" vagy "TotalRecords". Logikai metrikák alkalmazás által meghatározott, és bizonyos fizikai erőforrás-felhasználás közvetve tartozik. Logikai metrikák megegyeznek, mert a szolgáltatás alapon mérték és a jelentés által felhasznált fizikai erőforrások nehéz lehet. Miért Service Fabric biztosít bizonyos alapértelmezett metrikák méri, és jelentéskészítés a saját fizikai metrikák összetettsége is.

## <a name="default-metrics"></a>Alapértelmezett metrikák
Tegyük fel, hogy szeretné-e a kezdéshez írást, és a szolgáltatás telepítéséhez. Ezen a ponton nem tudható, hogy milyen fizikai vagy logikai erőforrások. Ez ideális! A Service Fabric fürt erőforrás-kezelő bizonyos alapértelmezett metrikákat használ, ha nincs más metrikákkal meg van adva. Ezek a következők:

  - PrimaryCount - a csomóponton elsődleges replikák száma 
  - ReplicaCount - csomóponton teljes állapot-nyilvántartó replikák száma
  - Szám - szolgáltatás-objektumok (állapotmentes és állapotalapú) azon a csomóponton száma

| Metrika | Állapot nélküli példány betöltése | Állapot-nyilvántartó másodlagos betöltése | Állapot-nyilvántartó elsődleges betöltése |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| Darabszám |1 |1 |1 |

Alapszintű munkaterhelések esetén az alapértelmezett metrikák adja meg a fürt munka decent terjesztési. A következő példában nézzük meg, mi történik, ha azt hozzon létre két szolgáltatást, és a terheléselosztás alapértelmezett metrikáit. Az első szolgáltatás egy állapotalapú szolgáltatás három partíciókat és a cél replika három méretének beállítása. A második szolgáltatás egy olyan állapot nélküli szolgáltatás egy partíciót és három példányszám.

Íme tartalmával:

<center>
![Alapértelmezett metrikák fürt elrendezése][Image1]
</center>

Ügyeljen a következőkre:
  - Az állapotalapú szolgáltatás elsődleges replikára változott elosztott számos csomópont
  - Replikák partícióra vannak különböző csomópontokon
  - Az elsődleges és másodlagos adatbázis teljes száma a fürtben terjesztése
  - Minden egyes csomóponton egyenletesen jutnak a szolgáltatás objektumok teljes száma

jó!

Az alapértelmezett metrikák nagy egy indításakor működik. Azonban az alapértelmezett metrikák csak végezze, amennyiben. Például: mekkora a valószínűsége, hogy Ön a particionálási sémáját kivételezett a tökéletesen még akkor is terhelése eredmények összes partíció? Mi az esélye, hogy egy adott szolgáltatáshoz a terhelés időbeli állandó, vagy akkor is igaz, ugyanúgy, mint korábban között több partíciót most?

Csak az alapértelmezett metrikák futtathat. Azonban ez általában azt jelenti, hogy a fürtkihasználtság kevesebb, illetve több egyenetlen, mint szeretné. Ennek az az oka az alapértelmezett metrikák nem adaptív, és feltételezik, hogy minden megegyezik. Például egy elsődleges foglalt, és, amely nem mindkét hozzájárul "1", hogy a PrimaryCount mérték. A legrosszabb esetben csak az alapértelmezett mérőszámainkat is eredményezhet overscheduled csomópontok teljesítményproblémák eredményez. Ha érdekli ki a fürt és a teljesítménnyel kapcsolatos problémák elkerülése, akkor egyéni metrikák és a dinamikus terheléselosztó jelentéskészítés használata.

## <a name="custom-metrics"></a>Egyéni metrikák
Metrikák létrehozásakor a szolgáltatás egy nevű-szolgáltatás-példányonként kell beállítani.

Bármely metrika rendelkezik néhány tulajdonság, amely azt írják le: a neve, a súly és egy alapértelmezett betöltési.

* Metrika neve: A metrika neve. A név egyedi azonosító a metrika az erőforrás-kezelő szempontjából a fürtön belül.
* Súly: Metrika súlyának határozza meg, hogy mennyire fontos ez a mérőszám a szolgáltatás más metrikákkal viszonyítva.
* Alapértelmezett betöltési: Az alapértelmezett betöltési képviselt másképp attól függően, hogy a szolgáltatás állapot nélküli és állapotalapú.
  * Az állapotmentes szolgáltatások mindegyik metrikát DefaultLoad nevű egyetlen tulajdonsággal rendelkezik
  * Az állapotalapú szolgáltatások adhat meg:
    * PrimaryDefaultLoad: Az alapértelmezett időt, ez a mérőszám a szolgáltatás fogyaszt elsődleges
    * SecondaryDefaultLoad: Az alapértelmezett időt, ez a mérőszám a szolgáltatás fogyaszt másodlagos

> [!NOTE]
> Ha egyéni metrikáinak definiálása és a kívánt _is_ az alapértelmezett metrikák használja, akkor kell _explicit módon_ adja hozzá az alapértelmezett metrikák biztonsági, és az őket súlyok és értékek meghatározása. Ennek oka az, meg kell adnia az alapértelmezett metrikák és a egyéni metrikákat közötti kapcsolat. Például lehet, hogy az Ön számára legfontosabb ConnectionCount vagy WorkQueueDepth több mint elsődleges elosztása. Alapértelmezés szerint a PrimaryCount metrika súlyának magas,, így szeretné csökkenteni közepes, a más metrikákat, győződjön meg arról, hogy elsőbbséget való hozzáadásakor.
>

### <a name="defining-metrics-for-your-service---an-example"></a>A szolgáltatás - példa metrikák meghatározása
Tegyük fel, azt szeretné, hogy a következő konfigurációt:

  - A szolgáltatás "ConnectionCount" nevű metrika jelentések
  - Szeretné használni az alapértelmezett metrikák 
  - Régebben már kötöttek néhány mérések és tudja, hogy általában egy elsődleges replika az adott szolgáltatás foglalja el "ConnectionCount" a 20 egység
  - Másodlagos adatbázis használja a "ConnectionCount" 5 egység
  - Hogy "ConnectionCount" a legfontosabb mérőszám szempontjából az adott szolgáltatás a teljesítmény kezelése
  - Továbbra is szeretné az elosztott terhelésű elsődleges replikára változott. Terheléselosztás elsődleges replikára változott érdemes általában függetlenül attól, milyen. Ez segít az elsődleges replikára változott, többsége érintő néhány csomópont vagy a tartalék tartomány az adatvesztés elkerülése érdekében. 
  - Ellenkező esetben az alapértelmezett metrikák rendben.

A kódot, hogy kellene írni, hogy a mérték konfigurálása a szolgáltatás létrehozása a következő:

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
> A fenti példák és a dokumentum többi részén ismertetik kezelése metrikák /-nevű-szolgáltatás alapon. Akkor is lehet a szolgáltatások, a szolgáltatás-metrikáinak definiálása _típus_ szintjét. Ez a szolgáltatás jegyzékfájlokban megadásával valósítható meg. Több okból nem ajánlott a típus szintű metrikák meghatározása. Az első ilyen OK állapota, hogy gyakran környezetre specifikus metrika neve. Kivéve, ha egy fixen szerződés helyen, nem lehet arról, hogy egy környezetben a metrika "Magok" nem "MiliCores" vagy "Magok" mások. Ha a metrikákat vannak meghatározva a jegyzékfájlban kell létrehoznia egy környezetben új jegyzékfájljai. Ez általában a csak apróbb eltérés, amely felügyeleti nehézségeket okozhat a különböző jegyzékfájlokat egy elterjedése vezet.  
>
> Metrika terhelések általában a egy nevű-szolgáltatás-példányonként vannak hozzárendelve. Például tegyük fel, a szolgáltatás CustomerA ki fogja használni a könnyebb csak egy példányát létrehozni. Tételezzük is fel számára hoz létre egy másik CustomerB, aki rendelkezik egy nagyobb munkaterhelés. Ebben az esetben akkor érdemes lehet tudjon végezni az alapértelmezett terhelések ezekbe a szolgáltatásokba. Ha metrikák és terhelések definiált keresztül jegyzékfájlokban, és szeretné, hogy a forgatókönyv támogatása, azt másik alkalmazás és a szükséges szolgáltatástípusok minden ügyfél esetében. A szolgáltatás-létrehozás időpontjában megadott felülbírálják a meghatározva a jegyzékfájlban, segítségével, amely a megadott alapértelmezett értékeinek beállítása. Azonban, amely ennek hatására a a szolgáltatás ténylegesen fut, nem egyeznek a jegyzékfájlban deklarált értékek. Ez zavart eredményezhet. 
>

Ne feledje: Ha most szeretné használni az alapértelmezett metrikák, akkor nem kell a metrikák gyűjtemény minden touch vagy bármi különleges a szolgáltatás létrehozásakor. Az alapértelmezett metrikák beolvasása automatikusan használja, ha nincs más vannak definiálva. 

Most ugorjunk végig részletesebben ezeket a beállításokat, és szolgáltatással kapcsolatban, annak hatását viselkedését.

## <a name="load"></a>Betöltés
A teljes pont metrikák meghatározásának néhány terhelés képviseli. *Betöltési* van mennyi egy metrika fel néhány szolgáltatáspéldány vagy egy adott csomópont a replikát. Betöltési szinte minden ponton konfigurálható. Példa:

  - Betöltési szolgáltatás létrehozásakor adható meg. Ez a lehetőség _alapértelmezett betöltési_.
  - A szolgáltatás létrehozása után a kapcsolatos metrika-információkat, alapértelmezett terhelések, beleértve a szolgáltatás képes frissíteni. Ez a lehetőség _szolgáltatás frissítése_. 
  - Az alapértelmezett értékeket, hogy a szolgáltatás egyes partíciók eseménysorozatában terhelések állítható vissza. Ez a lehetőség _partícióterhelés_.
  - Betöltési jelenthetők a egy szolgáltatás objektum alapján dinamikusan futásidőben száma. Ez a lehetőség _terhelés reporting_. 
  
Az összes alábbi stratégiák használhatja ugyanazt a szolgáltatást az élettartama során. 

## <a name="default-load"></a>Alapértelmezett betöltése
*Alapértelmezett betöltési* mekkora a mérték, akkor minden szolgáltatás objektum (állapot nélküli példány vagy állapot-nyilvántartó replika) szolgáltatás. A fürt erőforrás-kezelő ezt a számot használja a szolgáltatás objektum betöltése amíg nem kap további információkat, például a dinamikus terheléselosztó jelentés. Egyszerűbb szolgáltatások esetén az alapértelmezett betöltési egy statikus definíciója. Az alapértelmezett betöltési soha nem frissül, és a szolgáltatás élettartama használható. Alapértelmezett betölti működik nagy az egyszerű kapacitástervezési esetek, amikor bizonyos mennyiségű erőforrást különböző munkaterhelések számára vannak fenntartva, így nem módosítható.

> [!NOTE]
> A kapacitás kezelése és a fürt a csomópontok kapacitások meghatározása további információkért lásd: [Ez a cikk](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

A fürt erőforrás-kezelő lehetővé teszi, hogy egy másik alapértelmezett betöltési megadása az elsődleges és másodlagos állapotalapú szolgáltatások. Állapotmentes szolgáltatásokhoz csak adhat meg egy értéket, amely az összes példányára vonatkozik. Állapotalapú szolgáltatások, az alapértelmezett betöltési elsődleges és másodlagos replikák eltérőek általában óta replikák hajtsa végre az egyes szerepkörökhöz munkahelyi különböző típusú. Például elsődleges általában kiszolgálására, mind az Olvasás, mind az írás, és a számítási terhet, a legtöbb kezelni, miközben a másodlagos adatbázisok azonban nem. Általában az alapértelmezett betöltési egy elsődleges replika értéke magasabb, mint az alapértelmezett betöltési a másodlagos replikákon. A valós számok saját mérések függ.

## <a name="dynamic-load"></a>Dinamikus terheléselosztó
Tegyük fel, hogy Ön már futott a szolgáltatás egy ideig. Az egyes figyeléssel, amikor észrevette, hogy:

1. Mint mások egyes partíciók vagy egy megadott szolgáltatás példányának több erőforrást
2. Egyes szolgáltatások időnként eltérő terhelést rendelkeznek.

Nincs nagy mennyiségű dolog, ami ilyen típusú terheléselosztási ingadozását. Például különböző szolgáltatásokat vagy partíciók társított eltérő követelményekkel rendelkező, különböző ügyfelektől. Betöltési is volt módosítani, mert a szolgáltatásnak nincs munkahelyi függ a nap folyamán. Emiatt függetlenül nincs általában egyetlen szám nem használhatja az alapértelmezett. Ez különösen igaz, ha le szeretné kérdezni a legtöbb kihasználtságát a fürtből. Az alapértelmezett betöltési helytelen máskor válasszon értéket. Helytelen alapértelmezett betölti az eredmény a fürt erőforrás-kezelőben erőforrások lefoglalása alatt vagy fölött. Ennek eredményeképpen a csomópontokra, amelyeket felett vagy alatt felhasználtuk annak ellenére, hogy a fürt erőforrás-kezelő úgy értelmezi a fürt kiegyensúlyozott rendelkezik. Alapértelmezett terhelés továbbra is megfelelőek, mivel a kezdeti elhelyezésre néhány információt biztosítanak, de nem valódi munkaterhelések teljes szövegegység fontosságúak. Változó erőforrásigények pontosan rögzítéséhez, a fürt erőforrás-kezelő lehetővé teszi, hogy minden egyes szolgáltatás objektum frissítése a saját terhelést futásidőben. Ezt nevezik a dinamikus terheléselosztó jelentéskészítési.

A dinamikus terheléselosztási jelentések lehetővé a replikák és az élettartamuk során úgy, hogy a foglalási/jelentett betöltése a mérőszámok példányok. A szolgáltatás replika vagy a cold és a nem végzett munka volt példány általában küldenek, hogy egy metrika kis mennyiségű használta. Egy foglalt replika- vagy küldenek, hogy használják több.

Betöltés / replika- vagy jelentéskészítő lehetővé teszi, hogy a fürt erőforrás-kezelő átszervezése a fürtben a szolgáltatás objektumok. A szolgáltatások átrendezése segít biztosítjuk, hogy a szükséges erőforrásokat. Foglalt szolgáltatások hatékonyan "visszaigénylésének" erőforrásokat más replikákat vagy a számlálókat, amelyek jelenleg a gyenge vagy kevesebb munkát végző beolvasása.

Belül Reliable Services a kód betöltésének jelentéséhez dinamikusan néz ki:

Kód:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

A szolgáltatás bármely a létrehozáskor definiálva hozzá metrikák is tud jelentéseket. Ha olyan metrikajelentés, amely nem egy szolgáltatás jelentések terheléselosztási konfigurálva, a Service Fabric, amelyek jelentést készítenek figyelmen kívül hagyja. Ha más metrikák egyszerre jelentett érvényes, a jelentések használata engedélyezett. Szolgáltatás kódot mérésére és jelenteni tudja, minden metrikákat, hogyan és operátorok adhat meg a metrika konfigurációt szeretne használni a kódjának módosítása nélkül. 

### <a name="updating-a-services-metric-configuration"></a>A szolgáltatási metrika-konfigurációjának frissítése
A szolgáltatással kapcsolatos metrikákat listáját, és ezeket metrikák tulajdonságainak frissíthető dinamikusan közben a szolgáltatás élő. Ez lehetővé teszi, hogy kísérletezhet és rugalmasságot. Néhány példa, ha ez akkor hasznos, amelyek:

  - a metrika egy adott szolgáltatáshoz buggy jelentést letiltása
  - a kívánt viselkedés alapján metrikák súlyozását újrakonfigurálása
  - csak a kódot már telepítve és más mechanizmusok keresztül érvényesítése után, amely lehetővé teszi, hogy egy új metrika
  - az alapértelmezett betöltési megfigyelt viselkedést és fogyasztás alapján szolgáltatás módosítása

A metrika-konfiguráció megváltoztatását a fő API-jainak `FabricClient.ServiceManagementClient.UpdateServiceAsync` C# és `Update-ServiceFabricService` a PowerShellben. Ezen API-k a megadott információkat azonnal lecseréli a meglévő kapcsolatos metrika-információkat a szolgáltatáshoz. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Alapértelmezett betöltése értékeket és a dinamikus terheléselosztó jelentések keverése
Alapértelmezett betöltési és dinamikus terhelések alkalmas ugyanazt a szolgáltatást. Egy szolgáltatás használja az alapértelmezett betöltési és a dinamikus terheléselosztó jelentések, amikor alapértelmezett betöltési lesz becsült csak dinamikus jelentéseket jelenik meg. Alapértelmezett betöltési nem megfelelő, mert biztosít a fürt erőforrás-kezelő valami történő együttműködésre. Az alapértelmezett betöltési lehetővé teszi, hogy a fürt erőforrás-kezelő jó helyek helyezhető el a szolgáltatás-objektumok azok létrehozásakor. Ha nem alapértelmezett betöltési információ áll rendelkezésre, hatékonyan véletlenszerű szolgáltatások elhelyezését. Betöltési jelentések érkezésekor később a kezdeti véletlenszerű elhelyezési gyakran helytelen és a fürt erőforrás-kezelő szolgáltatások áthelyezése.

Most eltarthat korábbi példában, és tekintse meg, mi történik, ha jelenleg az egyes egyéni metrikák és a dinamikus terheléselosztó jelentéskészítési felvenni. Ebben a példában a "MemoryInMb", egy példa a metrika használjuk.

> [!NOTE]
> Memória egyike a rendszer metrikákat, amelyek a Service Fabric [erőforrás szabályozására](service-fabric-resource-governance.md), és a saját kezűleg reporting általában nehéz. Nem ténylegesen elvárjuk, hogy a jelentés a memória-felhasználás; Felhasznált memória ide biztonságával kapcsolatos képességeit, a fürt erőforrás-kezelő segítségével.
>

Most feltételezik, hogy kezdetben létrehozott az állapotalapú szolgáltatásból a következő paranccsal:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Ne feledje a szintaxisa ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Nézzük meg, milyen egyik lehetséges fürt elrendezés nézhet:

<center>
![Az alapértelmezett és egyéni metrikák fürt kiegyensúlyozott][Image2]
</center>

Bizonyos tényezőket érdemes megjegyezni vannak:

* Másodlagos replikák a partíción belüli egyes rendelkezhet saját betöltése
* A metrikák teljes elosztott terhelésű keressen. A memória, a maximális és minimális hányadosa 1,75 összeget (a legtöbb terheléselosztási csomópont N3, a legkevesebb N2 és 28/16 = 1,75 összeget).

Néhány dolgot még mindig szükséges ismertetik:

* Mi határozza meg, hogy 1,75 összeget arányú logikus volt-e vagy sem? Hogyan nem a erőforrás-kezelőt, hogy ha elég jó, vagy ha nincs meg az ehhez a további munkahelyi?
* Ha nem terheléselosztás fordulhat elő?
* Mit jelent, hogy memória "Magas" súlyozott-e?

## <a name="metric-weights"></a>Metrika súlyozás
Fontos, nyomon követése a metrikák különböző szolgáltatásban. Globális nincs mit lehetővé teszi, hogy a fürt erőforrás-kezelő nyomon a fürt fogyasztás, fogyasztás egyensúlyba csomópontok, és győződjön meg arról, hogy a csomópontok nem ismerteti a kapacitás. Szolgáltatások azonban előfordulhat, hogy ugyanazt a metrika fontosságát különböző nézeteket. Is számos metrikák és szolgáltatásokat nagyszámú a fürtben, tökéletesen kiegyensúlyozott megoldások nem létezik az összes metrikát. Hogyan kezelje a fürt erőforrás-kezelő ezekben a helyzetekben?

Metrika súlyok eldöntheti, hogyan egyensúlyba a fürt teljes válasz esetén a fürt erőforrás-kezelő engedélyezése. Metrika súlyok is lehetővé teszi a meghatározott szolgáltatásokat másképp elosztása érdekében a fürt erőforrás-kezelő. Metrikák rendelkezhet négy különböző súly szint: nulla, alacsony, közepes és magas. Az egyik súlya nulla metrika hozzájárul semmi annak eldöntéséhez, hogy e dolgok vagy nem elosztott terhelésű. A betöltési azonban továbbra is hozzájárul a kapacitás kezelése. Nulla súly metrikák továbbra is hasznosak, és gyakran használják a szolgáltatás-viselkedéshez és a teljesítmény figyelése részeként. [Ez a cikk](service-fabric-diagnostics-event-generation-infra.md) figyelés mérőszámok használatát és a szolgáltatások diagnosztikai nyújt részletesebb információt. 

A fürt különböző metrika súlya valós hatása, hogy a fürt erőforrás-kezelő hoz létre a különböző megoldások. Metrika súlyozással adja meg a erőforrás-kezelőt, hogy bizonyos metrikák fontosabbak, mint a többire. Ha tökéletes megoldás nincs a fürt erőforrás-kezelő megoldások, amelyek a magasabb súlyozott metrikák jobb elosztása is inkább. Ha a szolgáltatás úgy értelmezi, egy adott metrika nem lényeges, csak, akkor imbalanced tapasztalhatja, hogy a metrika használatát. Ez lehetővé teszi, hogy egy másik szolgáltatás, amely fontos, hogy az egyes mérőszám az még akkor is, terjesztési lekérése.

Példa néhány terhelés jelentések és a különböző metrika nézzük súlyozza a fürt másik azonosítóból eredményez. Ebben a példában látható, hogy a relatív súly a metrikák a Váltás hatására a szolgáltatások különböző szabályok létrehozásához erőforrás-kezelőt.

<center>
![Metrika súlyának példa és a hatása a terheléselosztási megoldások][Image3]
</center>

Ebben a példában nincsenek négy különböző szolgáltatások, a két különböző metrika, MetricA és MetricB összes jelentéskészítési eltérő értékeket. Egyetlen esetet, a szolgáltatások MetricA határozza meg, a legfontosabb egy (súly = kiváló) és MetricB, mint a lényeges, csak (súly = alacsony). Ennek eredményeképpen látható, hogy a fürt erőforrás-kezelő a szolgáltatásokat helyezi el, hogy MetricA jobban kiegyensúlyozott MetricB-nál. "Jobban kiegyensúlyozott" azt jelenti, hogy rendelkezik-e alsó MetricA a alacsonyabb, mint MetricB szórás rendelkezik. A második esetben azt a metrika súlyok fordított irányú. Emiatt a fürt erőforrás-kezelő cseréje A és B, így kapja meg a felosztás ahol jobban az elosztott terhelésű mint MetricA MetricB szolgáltatások.

> [!NOTE]
> Metrika súlyok határozza meg, hogy a fürt erőforrás-kezelő kell terheléselosztást, de nem amikor terheléselosztás történjen. A terheléselosztás további információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globális metrika súlyozás
Tegyük fel, ServiceA meghatározása szerint nagy súlyozási MetricA, és ServiceB MetricA súlya beállítása alacsony vagy nulla. Mi az a tényleges súly, amely említi használnak?

Nincsenek minden mérőszám nyomon követett több súlyt. Az első súly megadva a metrika a szolgáltatás létrehozásakor. A más súly a globális weight kiszámítása automatikusan történik. A fürt erőforrás-kezelő használja is a súlyozás pontozási megoldások. Fontos, mindkét súlyok figyelembe véve. Ez lehetővé teszi, hogy a fürt erőforrás-kezelő minden egyes szolgáltatás megfelelően saját prioritások egyensúlyba, és gondoskodjon arról is, hogy az egész fürt megfelelően van lefoglalva.

Mi történne a fürt erőforrás-kezelő nem érdeklik a globális és a helyi egyenleg? Akkor is, is könnyen megoldásokat, amely globálisan elosztott terhelésű, de az erőforrások egyensúlyára az egyes szolgáltatások eredményező összeállításához. A következő példában most nézze meg a szolgáltatás csak az alapértelmezett metrikák konfigurálva, és mi történik, ha csak globális egyenleg tekinthető. lásd:

<center>
![A globális csak megoldás gyakorolt hatása][Image4]
</center>

A felső példában csak a globális elosztás alapján az egész fürt valóban átgondolni. Összes csomópontja rendelkezik az elsődleges és a azonos számú teljes replikák azonos száma. Azonban ha megnézi a tényleges hatását a foglalási nincs így jó: csomópontok elvesztését befolyásolja egy adott munkaterhelés aránytalanul, mert az elsődleges kijelentkezik vesz igénybe. Például ha az első csomóponton hiba történik a kör szolgáltatás három különböző partíciók három elsődleges összes lenne elveszett. Ezzel ellentétben a háromszög és hatszög szolgáltatások rendelkeznek a replika elveszítik partícióikba. Ez azt eredményezi, hogy nincs megszűnésének eltérő kellene a lefelé replika helyreállításához.

Az alsó példában a fürt erőforrás-kezelő rendelkezik elosztott a replikákat a globális és a szolgáltatás elosztás alapján. A megoldás a pontszám kiszámítása során a súlyozás a legtöbb biztosít a globális megoldást, és az egyes szolgáltatásokhoz (konfigurálható) része. Metrika globális elosztás alapján van kiszámítva átlagának kiszámítása a metrika súlyok minden szolgáltatásból. Minden szolgáltatás kiegyensúlyozott saját meghatározott metrika súlyok alapján. Ez biztosítja, hogy a szolgáltatások elosztását belül magát a saját igényeinek megfelelően. Ennek eredményeképpen ha azonos első csomópont meghibásodik, a hiba történik az összes olyan szolgáltatás összes partíciójára. Minden egyes szempontjából azonos.

## <a name="next-steps"></a>További lépések
- A szolgáltatások konfigurálásáról [szolgáltatások konfigurálásával kapcsolatos tudnivalók](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Lemeztöredezettség-mentesítés metrikák meghatározása az összevonni helyett ezzel azt csomópontok terhelése egyik módja. Lemeztöredezettség-mentesítés konfigurálásáról további tudnivalókért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Hogyan kezeli a fürt erőforrás-kezelő, és elosztja a terhelést a fürt kapcsolatos további tudnivalókért tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)
- Indítsa el az elejétől és [Bevezetés a Service Fabric fürt Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- A mozgás költsége az egyik módja az, hogy egyes szolgáltatások drágább, mint a többire áthelyezése a fürt erőforrás-kezelő jelzés. A mozgás költsége kapcsolatos további tudnivalókért lásd [Ez a cikk](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
