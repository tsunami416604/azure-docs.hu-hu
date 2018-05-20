---
title: A teljesítmény - Azure HDInsight Spark-feladatok optimalizálása |} Microsoft Docs
description: A legjobb teljesítmény érdekében a Spark-fürtök közös stratégiák jeleníti meg.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: f35ed98efb26dfa0d75a57ca3646f567a7949dae
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="optimize-spark-jobs"></a>Spark-feladatok optimalizálása

Ismerje meg, hogyan optimalizálható a Spark fürtkonfiguráció az adott munkaterhelés számára.  A leggyakoribb probléma memóriaprobléma miatt nem megfelelő konfigurációk (különösen a nem megfelelő méretű végrehajtója), hosszú ideig futó műveletek és feladatok derékszögű műveleteket eredményez. Felgyorsíthatja a megfelelő gyorsítótárazás, és lehetővé teszi a feladatok [adatok döntés](#optimize-joins-and-shuffles). A legjobb teljesítmény érdekében figyelése, és tekintse át a hosszan futó és erőforrás-igényes Spark feladat végrehajtások.

A következő szakaszok ismertetik a közös Spark feladat optimalizálása és javaslatokat.

## <a name="choose-the-data-abstraction"></a>Válassza ki az adatok kivételére

Spark 1.x használt RDDs absztrakt adatokat, majd a Spark 2.x bevezetett DataFrames és adatkészletek. Vegye figyelembe a következő relatív érdemi:

* **DataFrames**
    * A legtöbb esetben a legjobb választás
    * Optimalizálás katalizátor keresztül biztosít
    * Egész szakaszból álló kód generálása
    * Közvetlen memória-hozzáférés
    * Kevés szemétgyűjtési (GC)
    * Nem, fejlesztői mobilbarát adatkészleteket, mint szerint nincsenek fordítási időben ellenőrzések vagy tartományi objektum programozás
* **Adatkészletek**
    * Az összetett, ahol a teljesítményre gyakorolt hatás elfogadható az ETL folyamatok jó
    * Nem helyes, ahol a teljesítményre gyakorolt hatás jelentős lehet összesítésben
    * Optimalizálás katalizátor keresztül biztosít
    * Fejlesztői mobilbarát, adja meg a tartomány objektum programozási és fordítási időben ellenőrzése
    * Hozzáadja a szerializálással/deszerializálással terhelés
    * A globális Katalógus magas terhelés
    * Megszakítja az egész szakaszból álló kód generálása
* **RDDs**
    * A Spark 2.x, nem kell használnia RDDs, kivéve, ha egy új egyéni RDD készítéséhez szükséges
    * Nincs optimalizálás katalizátor keresztül
    * Nem egész szakaszból álló kód generálása
    * A globális Katalógus magas terhelés
    * Kell használnia a Spark 1.x örökölt API-k

## <a name="use-optimal-data-format"></a>Optimális adatformátum használata

A Spark számos formátumban, például a fürt megosztott kötetei szolgáltatás, json, xml, parquet, orc és az avro támogatja. Spark kiterjeszthető támogatja a külső adatforrások - sok további formátumok további információt [csomagok Spark](https://spark-packages.org).

A legjobb teljesítmény formátuma a parquet *klassz kis tömörítési*, a Spark on az alapértelmezett 2.x. Parquet oszlopos formátumban tárolja az adatokat, és a Spark optimalizálhatja.

## <a name="select-default-storage"></a>Válassza ki az alapértelmezett tároló

Amikor létrehoz egy új Spark-fürt, lehetősége van az Azure Blob Storage vagy az Azure Data Lake Store jelölje ki a fürt alapértelmezett tárolási. Mindkét beállítások segítségével az előnye, hogy a hosszú távú tárolás átmeneti fürtök, így az adatok nem automatikusan törlődnek a fürt törlésekor. Hozza létre újra egy átmeneti fürtöt, és továbbra is hozzáférhet az adatokhoz.

| Olyan tárolási típus | Fájlrendszer | Sebesség | Átmeneti | Használati esetek |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standard** | Igen | Átmeneti fürt |
| Azure Data Lake Store | **Adl:**//url/ | **Gyorsabb** | Igen | Átmeneti fürt |
| Helyi HDFS | **hdfs:**//url/ | **Leggyorsabb** | Nincs | Interaktív 24/7 fürt |

## <a name="use-the-cache"></a>A gyorsítótár

Spark nyújt a saját natív gyorsítótárazási mechanizmusok, például a különböző módszerrel használható `.persist()`, `.cache()`, és `CACHE TABLE`. A natív gyorsítótárazását a következő időponttól érvényes, valamint a kisebb adatkészletek rendelkező hasonlóan kell gyorsítótár köztes eredmények ETL folyamatok. Azonban Spark natív gyorsítótárazás jelenleg nem működnek jól particionálás, mivel egy gyorsítótárazott tábla nem őriz meg a particionálási adatokat. Egy általános, és megbízható a gyorsítótárazási technológia *tárolási réteg gyorsítótárazás*.

* Natív Spark gyorsítótárazás (nem ajánlott)
    * Jó kisebb adatkészletek esetében.
    * Nem működik a particionálás, amely megváltozhatnak a jövőben Spark kiadásokban.

* Tárolási szintű gyorsítótárazás (ajánlott)
    * Segítségével valósítható [Alluxio](http://www.alluxio.org/).
    * A memória és SSD-gyorsítótárazás használja.

* Helyi HDFS (ajánlott)
    * `hdfs://mycluster` elérési út.
    * Használja az SSD-gyorsítótárazás.
    * A fürt törlésekor a gyorsítótár rebuild igénylő a gyorsítótárazott adatok elvesznek.

## <a name="use-memory-efficiently"></a>Hatékonyan használhatják fel a memória

Spark úgy, hogy adatokat a memóriában, így a memória-erőforrások kezelése egyik fontos szempontja a Spark feladatok végrehajtásának optimalizálása működik.  Nincsenek több technikák hatékonyan használhatják fel a fürt memória alkalmazhat.

* Inkább kisebb adatok partíciót és az adatok mérete, típusok és az a jó particionálási stratégia terjesztési.
* Vegye figyelembe a újabb hatékonyabb [Kryo adatszerializálás](https://github.com/EsotericSoftware/kryo), ahelyett, hogy az alapértelmezett Java szerializálást.
* Inkább a YARN mellett használatával, akkor elválasztja `spark-submit` kötegelt.
* Figyelhető meg és hangolható a Spark-konfigurációs beállításokat.

Referenciaként a Spark memóriában szerkezetének és néhány főbb végrehajtó memória paraméterek jelennek meg a következő kép.

### <a name="spark-memory-considerations"></a>Spark a memóriával kapcsolatos megfontolások

Ha YARN használ, YARN mindegyik Spark csomópontján az összes tároló által használt memória maximális összegét szabályozza.  Az alábbi ábrán látható, az objektumok és azok.

![YARN Spark memóriakezelés](./media/apache-spark-perf/yarn-spark-memory.png)

"Nincs elegendő szabad memória" üzenet címmel, próbálja meg:

* Tekintse át az adatbázis-elérhetőségi csoport felügyeleti Shuffles. Csökkentik a térkép-oldali reducting, előre partícióazonosító (vagy bucketize) forrásadatok, egyetlen shuffles maximalizálása és küldött adatok mennyiségének csökkentésére.
* Inkább `ReduceByKey` a rögzített méretű memória maximális mennyiségét, hogy a `GroupByKey`, összesítéseket, Ablakozó és egyéb funkciókat biztosító, de rendelkezik Reino unbounded memóriára vonatkozó korlátozást.
* Inkább `TreeReduce`, amely nem több munka a végrehajtója vagy partíciót, `Reduce`, amelynek szerepe, hogy az illesztőprogram az összes munkahelyi.
* Használja ki az DataFrames helyett az alacsonyabb szintű RDD objektumokat.
* Hozzon létre, amely foglalják magukban a műveletek, például a "Legfontosabb N", különböző összesítések vagy Ablakozó műveletek ComplexTypes.

## <a name="optimize-data-serialization"></a>Adatszerializálás optimalizálása

Spark feladatok vannak osztva, ezért fontos a megfelelő adatok szerializálása a legjobb teljesítmény érdekében.  A Spark két szerializálási lehetőség áll rendelkezésre:

* Java-szerializálási az alapértelmezett beállítás.
* Kryo szerializálási újabb formátumra, és gyorsabban eredményezhet, és több szerializálási tömörítésével Java-nál.  Kryo megköveteli, hogy a program kíván regisztrálni az osztályokat, és még nem támogatja az összes szerializálható típusok.

## <a name="use-bucketing"></a>Használja a bucketing

Bucketing adatparticionálás hasonló, de minden gyűjtő oszlop értékek helyett csak egy tárolására képes. Bucketing működik (a több milliót is elérheti vagy több) nagyszámú értékek, például a termék azonosítók a particionálást. A gyűjtő a gyűjtő kulcsot a sor kivonatoláshoz határozza meg. Összegyűjtött táblák egyedi optimalizálásokat kínálnak, mivel ezek tárolják a metaadatok arról, hogyan azok volt összegyűjtött és rendezve.

Néhány speciális bucketing szolgáltatások a következők:

* Optimalizálás bucketing metaadatok alapján
* Optimalizált összesítések
* Optimalizált illesztések

Particionálás és bucketing egyszerre is használhatja.

## <a name="optimize-joins-and-shuffles"></a>Illesztések és shuffles optimalizálása

Ha lassú feladatok Join vagy véletlen rendelkezik, ennek oka valószínűleg *adatok döntés*, vagyis aszimmetria a feladat adatait. Például egy térkép feladat 20 percet is igénybe vehet, de fut egy feladat, ha az adatok csatlakoztatott vagy átrendezve óráig tart.   Adatok eltérésére megoldása érdekében meg kell sója a teljes kulcsot, vagy használjon egy *védőérték elkülönített* kulcsok csak néhány részhalmaza.  Ha egy elkülönített védőérték használ, további kell szűrheti különítse el a térkép illesztésekben sózott kulcsok részhalmazát. Egy másik lehetőség egy gyűjtő oszlop esetében, és előre összesített először a gyűjtők.

Lassú csatlakozik, amely egy másik tényező az illesztési típus lehet. Alapértelmezés szerint a Spark használja a `SortMerge` összekapcsolás típusa. Az ilyen típusú illesztés leginkább a nagy méretű adatkészletekhez, de egyébként számításilag költséges mivel azt először rendezni kell és a bal oldalon az adatok egyesítése őket előtt.

A `Broadcast` illesztési leginkább megfelelő, a kisebb adatkészletek, vagy ha egy kiszolgálóoldali csatlakozási sokkal kisebb, mint a másik oldalon. Az ilyen típusú illesztés az összes végrehajtója közzéteszi az egyik oldalról, és így több memóriát igényel, a szóráshoz általában.

Módosíthatja az illesztés típusát a konfiguráció beállításával `spark.sql.autoBroadcastJoinThreshold`, vagy beállíthat egy illesztésmutatót DataFrame API-val (`dataframe.join(broadcast(df2))`).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")
sql("SELECT col1, col2 FROM V_JOIN")
```

Ha összegyűjtött táblák használ, akkor a külső illesztési típus, a `Merge` illesztési. Egy megfelelően előre particionált és előre rendezett dataset kihagyja a költséges rendezési fázis a egy `SortMerge` illesztési.

Illesztések sorrendje számít, különösen a összetettebb lekérdezések. A legtöbb szelektív illesztések kezdődik. Is helyezze át az illesztés, sorok számának növelése után összesítéseket, amikor lehetséges.

Párhuzamossági kifejezetten esetén derékszögű illesztések kezelésére is hozzáadhat beágyazott struktúrákat ablak, és lehet, hogy kihagyja a Spark feladat egy vagy több lépéseit.

## <a name="customize-cluster-configuration"></a>Fürtkonfiguráció testreszabása

Attól függően, hogy a Spark-fürt terhelést, dönthet, hogy egy nem alapértelmezett Spark konfigurációs eredményeznének több optimalizált Spark feladat végrehajtása.  Hajtsa végre a referenciaalap tesztelték minta munkaterhelések minden nem alapértelmezett fürt konfigurációjának ellenőrzése.

Az alábbiakban néhány általános paraméterek módosíthatja:

* `--num-executors` Beállítja a megfelelő számú végrehajtója.
* `--executor-cores` magok száma minden végrehajtó beállítása. Általában middle-sized végrehajtója kell rendelkezésre állnia, más folyamatok néhány, a rendelkezésre álló memória felhasználását.
* `--executor-memory` minden egyes végrehajtó, amely vezérli a halommemória mérete, a yarn RENDSZERÉN beállítja a memória méretét. Hagyja meg a végrehajtási terhelés memóriát.

### <a name="select-the-correct-executor-size"></a>Válassza ki a megfelelő végrehajtó mérete

A végrehajtó konfigurációs meghatározásakor vegye figyelembe a Java szemétgyűjtési (GC) gyűjtemény terhelést.

* Végrehajtó méretének csökkentésére tényezői:
    1. Csökkentse a halommemória mérete 32 GB-os tartani a globális Katalógus általános < 10 % alatt.
    2. Csökkentse a GC általános < 10 % tartása magok száma.

* Tényezők végrehajtó méretének növelése céljából:
    1. Protokollterhelés végrehajtója közötti kommunikáció csökkentése érdekében.
    2. Adjon meg kevesebb végrehajtója (N2) közötti kapcsolatok megnyitása a nagyobb fürtökkel (> 100 végrehajtója).
    3. Növelje a halommemória mérete memóriaigényes feladatok befogadásához.
    4. Választható lehetőség: Csökkentse az-executor Memóriaterhelést.
    5. Választható lehetőség: Megnövelheti kihasználtságát és a párhuzamosság oversubscribing CPU.

Mint általános tapasztalatok végrehajtó méretének kiválasztása:
    
1. Végrehajtó / 30 GB kezdődnie, és ossza ki rendelkezésre gép magok.
2. Az executor magos nagyobb fürtökkel (> 100 végrehajtója) számának növeléséhez.
3. Növelheti vagy csökkentheti a vizsgálat futtatása és az előző tényezőkkel, például a globális Katalógus terhelést a méretet.

Egyidejű lekérdezéseket futtat, vegye figyelembe a következőket:

1. Első lépésként 30 GB végrehajtó és minden gép magok száma.
2. Hozzon létre több párhuzamos Spark applications oversubscribing CPU (körülbelül 30 %-os késés növekedés).
3. Lekérdezések párhuzamos alkalmazások elosztása.
4. Növelheti vagy csökkentheti a vizsgálat futtatása és az előző tényezőkkel, például a globális Katalógus terhelést a méretet.

A lekérdezési teljesítmény kiugró vagy más teljesítményproblémák figyelése megnézzük a idősor nézetének megnyitására, SQL graph, Projekt statisztika és így tovább. Néha egy vagy néhány a végrehajtója a lassabb, mint a többi, és feladatok elvégzése hosszabb hajtható végre. Ez gyakran nagyobb fürtökkel (> 30 csomópontok) történik. A munkahelyi ebben az esetben felosztani feladatok nagyobb számú, így az ütemező pótolhatja lassú feladatokhoz. Például vannak-e legalább kétszer annyi feladatok végrehajtó magok száma az alkalmazásban. Engedélyezheti a feladatok végrehajtásának spekulatív `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Feladat-végrehajtási optimalizálása

* Gyorsítótár szükség szerint, például ha az adatok felhasználásával kétszer, majd gyorsítótárba azt.
* Minden végrehajtója szórási változókat. A változók csak szerializálva vannak, ha gyorsabb keresések eredményez.
* Az illesztőprogram, így ez gyorsabb áttelepítést tesz lehetővé a számos feladat a szálkészlet használja.

A futó feladatok rendszeresen teljesítményproblémák figyelése. Ha bizonyos problémák további betekintést van szüksége, fontolja meg a következő teljesítmény profilkészítési eszközök egyikét:

* [Intel PAL eszköz](https://github.com/intel-hadoop/PAT) figyeli a CPU, a tároló és a hálózati sávszélesség-használatot.
* [Oracle Java 8 küldetési vezérlő](http://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profilok Spark és végrehajtó kódot.

A Spark 2.x lekérdezési teljesítmény kulcsa a volfrám motort tartalmaz, amely teljes szakaszból álló kódgenerálás függ. Bizonyos esetekben egész szakaszból álló kódgenerálás is tiltható le. Ha például egy nem változtatható típus használata (`string`) összesítő kifejezésben `SortAggregate` jelenik meg ahelyett, hogy `HashAggregate`. Például a jobb teljesítmény érdekében a következőket, és majd engedélyezze újra kód generálása:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>További lépések

* [Debug futó Azure HDInsight Spark-feladatok](apache-spark-job-debugging.md)
* [A HDInsight Spark-fürt erőforrásainak kezelése](apache-spark-resource-manager.md)
* [A Spark REST API használatával Spark-fürt távoli feladatok elküldéséhez](apache-spark-livy-rest-interface.md)
* [Spark hangolása](https://spark.apache.org/docs/latest/tuning.html)
* [A Spark ténylegesen hangolására feladatok, hogyan működnek](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo szerializálási](https://github.com/EsotericSoftware/kryo)
