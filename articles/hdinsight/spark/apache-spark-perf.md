---
title: A teljesítmény – Azure HDInsight Spark-feladatok optimalizálása
description: A legjobb teljesítmény érdekében a Spark-fürtök gyakori stratégiát mutatja.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: dc1fe8a3d9a1f0da0a190275b4fbb8bd18fff610
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499150"
---
# <a name="optimize-apache-spark-jobs"></a>Az Apache Spark-feladatok optimalizálása

Ismerje meg, hogyan optimalizálható [Apache Spark](https://spark.apache.org/) az adott számítási feladathoz tartozó fürtkonfiguráció.  A leggyakoribb kihívás abban áll memóriaprobléma miatt (különösen a nem megfelelő méretű végrehajtóval) nem megfelelő konfigurációkat, hosszú ideig futó műveletek és feladatok, amelyek a Descartes-féle műveletek. Felgyorsíthatja a megfelelő gyorsítótárazási és azáltal, hogy a feladatok [Adateltérés](#optimize-joins-and-shuffles). A legjobb teljesítmény figyelésére, és tekintse át a hosszú ideig futó és erőforrás-igényes Spark feladatvégrehajtások.

A következő szakaszok ismertetik a Spark-feladat közös optimalizálása és javaslatok.

## <a name="choose-the-data-abstraction"></a>Válassza ki az adatok absztrakciós

Spark-1.x használt rdd-kként absztrakt adatokat, majd a Spark 2.x bevezetett adatkerettípusokat jelölhet, és adatkészletek. Vegye figyelembe a következő relatív érdemi:

* **DataFrames**
    * A legtöbb esetben a legjobb választás
    * Optimalizálás katalizáló keresztül biztosít
    * Egész szakaszból álló kódgenerálás
    * Közvetlen memória-hozzáférés
    * Alacsony többletterhelést okoz a szemétgyűjtési (GC)
    * Nem, Fejlesztőbarát adatkészletek, mivel nem ellenőrzések fordításkor vagy tartományi objektum programozási
* **Adatkészletek**
    * Megfelelő választás az összetett ETL-adatcsatornák, ahol a teljesítményre gyakorolt hatás fogadható el.
    * Nem jó, ahol a teljesítményre gyakorolt jelentős lehet összesítésben
    * Optimalizálás katalizáló keresztül biztosít
    * Fejlesztőbarát azáltal, hogy tartományi objektum programozási és fordításkor ellenőrzése
    * Hozzáadja a szerializálás/deszerializálás terhelés
    * Globális Katalógus magas terhelés
    * Megszakítja az egész szakaszból álló kódgenerálás
* **RDDs**
    * A Spark 2.x, nincs szüksége rdd-k, kivéve, ha egy új egyéni RDD létrehozásához szükséges
    * Nincs lekérdezés optimalizálás katalizáló keresztül
    * Nem egész szakaszból álló kódgenerálás
    * Globális Katalógus magas terhelés
    * Kell használnia a Spark 1.x örökölt API-k

## <a name="use-optimal-data-format"></a>Optimális formátum használata

A Spark számos formátumok, mint például a fürt megosztott kötetei szolgáltatás, json, xml, parquet, orc és avro támogat. Spark kiterjeszthető támogatja a külső adatforrásokkal - számos további formátumok további információt találhat [Spark-csomagok](https://spark-packages.org).

A legjobb teljesítmény formátuma a parquet eszközökben *snappy tömörítést*, az alapértelmezett Spark 2.x. Parquet oszlopos formátumban tárolja az adatokat, és optimalizálhatja a Sparkban.

## <a name="select-default-storage"></a>Válassza ki az alapértelmezett tároló

Amikor létrehoz egy új Spark-fürtöt, lehetősége van, válassza ki az Azure Blob Storage vagy az Azure Data Lake Store a fürt alapértelmezett tárolóként. Mindkét lehetőség biztosítanak az előnye, hogy a hosszú távú tárolás átmeneti fürtökben, így az adatok nem automatikusan törlődnek a fürt törlésekor. Hozza létre újra egy átmeneti fürtöt, és továbbra is hozzáférhet az adataihoz.

| Store típusa | Fájlrendszer | Sebesség | Átmeneti | Használati példák |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standard** | Igen | Átmeneti fürt |
| Azure Data Lake Store | **Adl:**//url/ | **Gyorsabb** | Igen | Átmeneti fürt |
| Helyi HDFS | **hdfs:**//url/ | **Leggyorsabb** | Nem | Interaktív 24/7 fürt |

## <a name="use-the-cache"></a>A gyorsítótár használata

A Spark biztosít a saját natív gyorsítótárazási mechanizmust, például a különböző módszereken keresztül használható `.persist()`, `.cache()`, és `CACHE TABLE`. A natív gyorsítótárazás érvényben, a kis adatkészletekhez, valamint látható módon kell gyorsítótár köztes eredményeket ETL-adatcsatornák. Azonban Spark natív gyorsítótárazás jelenleg nem működik a particionálást, mivel egy gyorsítótárazott tábla nem őriz meg az particionálási adatok. Egy általános, és megbízható a gyorsítótárazási módszer *tárolási réteg gyorsítótárazás*.

* Natív Spark gyorsítótárazás (nem ajánlott)
    * Kisebb adatkészletek esetében megfelelő választás.
    * Nem működik particionálás, amely Spark kiadások megváltozhatnak a jövőben.

* Tárolási szint gyorsítótárazás (ajánlott)
    * Segítségével valósítható meg [Alluxio](http://www.alluxio.org/).
    * A memóriában, és a gyorsítótárazás SSD használja.

* Helyi HDFS (ajánlott)
    * `hdfs://mycluster` elérési út.
    * Használja a SSD-gyorsítótárat.
    * A fürt törlésekor a gyorsítótár-Újraépítés igénylő a gyorsítótárazott adatok elvesznek.

## <a name="use-memory-efficiently"></a>Hatékonyan használhatják a memória

A Spark működik, úgy, hogy adatokat a memóriában, így a memória-erőforrások felügyelete a kulcsfontosságú az optimalizálás, Spark-feladatok végrehajtását.  Számos különböző módszereket vetnek hatékonyan használhatják a fürt memória alkalmazhat.

* Inkább a kisebb méretű adatok a partíciók és az adatok mérete, típusok és terjesztését a particionálási stratégia.
* Vegye figyelembe az újabb hatékonyabb [Kryo adatok szerializálása](https://github.com/EsotericSoftware/kryo), ahelyett, hogy az alapértelmezett Java szerializálási.
* Inkább a YARN, használatával, hogy elkülöníti `spark-submit` Batch.
* Figyelése és finomhangolása a Spark-konfigurációs beállításokat.

Referenciaként a Spark memóriában szerkezetének és néhány kulcsfontosságú végrehajtó memória paraméter látható a következő képen.

### <a name="spark-memory-considerations"></a>A Spark a memóriával kapcsolatos megfontolások

Ha használ [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), majd a YARN minden Spark csomópontján az összes tárolót által használt memória maximális összegét szabályozza.  Az alábbi ábrán látható, az objektumok és azok.

![YARN Spark memóriájának kezelése](./media/apache-spark-perf/yarn-spark-memory.png)

Oldja meg a "nincs elegendő memória" üzeneteket, próbálja meg:

* Tekintse át a DAG felügyeleti Shuffles. Térkép melletti reducting csökkenthető, előre partíció (vagy csoportosításához) forrásadat, egyetlen shuffles maximalizálása és a küldött adatok mennyiségének csökkentésére.
* Inkább `ReduceByKey` a rögzített méretű memória maximális mennyiségét, a `GroupByKey`, amely biztosítja, más függvények, összesítések és ablakkezelési, de rendelkezik Reino korlátlan streameken működő memória felső korlátja.
* Inkább `TreeReduce`, amely nem munka, partíciók vagy illesztőprogramként indít a `Reduce`, amely működik minden az illesztőprogramok.
* Használja ki az alacsonyabb szintű RDD-objektumok helyett adatkerettípusokat jelölhet.
* Hozzon létre, amely magába foglalja a műveletek, például a "Legfelső N", különböző összesítések vagy ablakkezelési operations ComplexTypes.

## <a name="optimize-data-serialization"></a>Adatok szerializálása optimalizálása

Spark-feladatok vannak osztva, ezért fontos a megfelelő adatok szerializálása a legjobb teljesítmény érdekében.  A Spark két szerializálási lehetőség van:

* Java-szerializálás az alapértelmezett érték.
* Kryo szerializálási egy újabb formátum, és gyorsabban eredményezhet, és több kisebb méretű szerializálási, mint a Java.  Kryo megköveteli, hogy az osztályok regisztrálnia a programban, és még nem támogatja az összes szerializálható típusok.

## <a name="use-bucketing"></a>Bucketing használata

Bucketing hasonló az adatparticionálás, de az egyes gyűjtők tartalmazhat egy értékhalmazt oszlop helyett csak az egyik. Bucketing esetén működik hatékonyan értékeket, például a termék azonosítók (a vagy még több milliós) nagy számú a particionálást. A gyűjtő kulcs sor kivonatoláshoz kérelemegységeket határozza meg. Bucketed táblák olyan egyedi optimalizálások biztosítanak, mert a metaadatok bucketed és rendezve is tárolnak.

Néhány speciális bucketing funkciói a következők:

* Optimalizálás bucketing metaadat-információi alapján
* Optimalizált összesítések
* Optimalizált illesztések

Particionálás és a egy időben bucketing is használhatja.

## <a name="optimize-joins-and-shuffles"></a>Illesztés és shuffles optimalizálása

Ha a csatlakozás vagy Shuffle lassú feladatokat, ennek oka valószínűleg *Adateltérés*, azaz aszimmetria a feladat adatait. Például egy térkép feladat 20 percet is igénybe vehet, de fut egy feladat, ahol az adatok csatlakoztatott vagy összekeverve órát vesz igénybe.   Adatok torzulása, meg kell sója a teljes kulcsot, vagy használhatja egy *só elkülönített* a kulcsok csak bizonyos része.  Ha egy elkülönített só használ, a kell további szűréséhez elkülönítése a térkép illesztésekben sózott kulcsok részhalmazát. Egy másik lehetőség, hogy vezessen be egy gyűjtőbe oszlopot, és előzetes összesítésre először a gyűjtőkbe.

Lassú illesztések okozó egy másik tényező az illesztési típus lehet. Alapértelmezés szerint a Spark használja a `SortMerge` csatlakozás típusa. Az ilyen típusú join nagy méretű adatkészleteket számára a leginkább megfelelő, de egyébként igényű, mivel azt először rendezni kell és a bal oldalon az adatok őket az egyesítés előtt.

A `Broadcast` illesztési leginkább megfelelő, kisebb adatkészletek esetében, vagy ha az illesztés oldalán egy sokkal kisebb, mint a másik oldalon. Az ilyen típusú join közzéteszi az egyik oldalán az összes végrehajtóval, és így több memóriát igényel a szóráshoz általában.

Módosíthatja az illesztés típusának a konfiguráció beállításával `spark.sql.autoBroadcastJoinThreshold`, vagy beállíthatja, hogy egy illesztésmutatót DataFrame API-val (`dataframe.join(broadcast(df2))`).

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

Bucketed táblát használ, akkor a csatlakozás típusa, külső a `Merge` való csatlakozás. Helyes előre particionált és előre rendezett adatkészlet kihagyja a költséges rendezési fázis a egy `SortMerge` való csatlakozás.

A rendelés illesztések fontos szempont, különösen az összetettebb lekérdezéseket. Indítsa el a legtöbb szelektív illesztéseket. Is helyezze át az illesztések, amelyek növelik a sorok száma után összesítések, amikor csak lehetséges.

Kezelheti a párhuzamosságot, kifejezetten a Descartes-féle illesztések esetén adja hozzá beágyazott struktúrák ablakkezelési, és talán az a Spark-feladat egy vagy több lépés kihagyható.

## <a name="customize-cluster-configuration"></a>Fürtkonfiguráció testreszabása

A Spark-fürt számítási függően előfordulhat, hogy meghatározni, hogy egy nem alapértelmezett Spark konfigurációt eredményez további optimalizált Spark-feladat végrehajtása.  Hajtsa végre a teljesítményteszt minden nem alapértelmezett fürt konfigurációjának ellenőrzése a mintául szolgáló számítási feladatok tesztelésével.

Az alábbiakban néhány gyakori paramétereket módosíthatja:

* `--num-executors` Beállítja a megfelelő számú végrehajtóval.
* `--executor-cores` minden egyes végrehajtó állítja be a magok számát. Általában más folyamatok dolgozhat fel a rendelkezésre álló memória némelyike middle-sized végrehajtóval kell rendelkeznie.
* `--executor-memory` minden egyes végrehajtó, amely azt vezérli, a YARN rendszerén. generace állítja be a memória méretét. Végrehajtási terhelést a memóriát kell hagyni.

### <a name="select-the-correct-executor-size"></a>Válassza ki a megfelelő végrehajtó mérete

A végrehajtó konfigurációs meghatározásakor vegye figyelembe a Java szemétgyűjtési (GC) gyűjtemény terhelését.

* Tényezők végrehajtó méretének csökkentése érdekében:
    1. Csökkentse a halommemória mérete 32 GB-os, hogy a globális Katalógus többletterhelési < 10 % alatt.
    2. Csökkentse a globális Katalógus többletterhelési < 10 % tartani a magok számát.

* Növelhető a méret végrehajtó tényezők:
    1. Csökkentheti a terhelés végrehajtóval közötti kommunikációt.
    2. Nagyobb fürtök közötti végrehajtóval (N2) megnyitott kapcsolatok számát csökkentheti (> 100 végrehajtóval).
    3. Növelje a memória-igényes feladatokhoz befogadásához. generace.
    4. Választható lehetőség: Csökkenteni az-executor memória terhelést.
    5. Választható lehetőség: Növelése kihasználtságát és a párhuzamosság oversubscribing Processzor.

Mint az általános tapasztalatok végrehajtó méretének kiválasztásakor:
    
1. Végrehajtó / 30 GB kezdődhet, és elérhető machine magok terjesztése.
2. Növelje a nagyobb fürtök (> 100 végrehajtóval) végrehajtó magok számát.
3. Növelheti vagy csökkentheti a méretek próbafuttatások és az előző tényező befolyásolja, például a globális Katalógus terhelés alapján.

Amikor lekérdezést futtat, vegye figyelembe a következőket:

1. Indítsa el a/30 GB végrehajtó és az összes gép magok száma.
2. Hozzon létre több párhuzamos Spark applications oversubscribing CPU (körülbelül 30 %-os késés fokozása).
3. Lekérdezések párhuzamos alkalmazások elosztása.
4. Növelheti vagy csökkentheti a méretek próbafuttatások és az előző tényező befolyásolja, például a globális Katalógus terhelés alapján.

A lekérdezési teljesítmény kiugró adatokat vagy más teljesítménnyel kapcsolatos problémák figyelése megnézzük a idővonalnézetét, SQL graph, feladatstatisztika és így tovább. Néha egy vagy néhány a végrehajtóval a lassabb, mint a többi, és feladatok végrehajtása sokkal hosszabb ideig. Ez gyakran a nagyobb fürtök (> 30 csomópontok) történik. A munkahelyi ebben az esetben osztása feladatok nagyobb számú, ezért a scheduler képes meghiúsult lépések kompenzációjához lassú feladatokat. Például rendelkeznek legalább kétszer annyi feladatokat végrehajtó magok száma az alkalmazásban. Spekulatív végrehajtás kockázatának csökkentése a feladatok is engedélyezheti `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Feladat-végrehajtási optimalizálása

* Szükség szerint, például ha kétszer használhatja az adatokat, majd gyorsítótárazza azt a gyorsítótárba.
* Az összes végrehajtóval szórási változókat. A változók csak szerializálva vannak, miután gyorsabb keresések eredményez.
* A szálkészlet használja az illesztőprogram, ami számos feladattípushoz gyorsabb áttelepítést eredményez.

A futó feladatok rendszeresen a teljesítménnyel kapcsolatos problémák figyelése. Ha részletesebb elemzéseket kaphat az egyes problémák van szüksége, fontolja meg a következő teljesítmény profilkészítés eszközök egyikét:

* [Intel-PAL eszköz](https://github.com/intel-hadoop/PAT) figyeli a Processzor, a storage és a hálózati sávszélesség felhasználásával.
* [Oracle Java 8 Mission Control](http://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profilt készít a Spark és a feladat-végrehajtó kód.

A Spark 2.x lekérdezési teljesítmény kulcsa a volfrám motor, amellyel egész szakaszból álló kódgenerálás függ. Bizonyos esetekben egész szakaszból álló kódgenerálás van tiltva. Ha például egy nem változtatható típus használata (`string`) az összesítő kifejezésben `SortAggregate` jelenik meg, hanem `HashAggregate`. Ha például a jobb teljesítmény érdekében megpróbálkozhat a következőkkel, majd újra engedélyeznie generování kódu:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>További lépések

* [Futó Azure HDInsight az Apache Spark-feladatok hibakereséséhez](apache-spark-job-debugging.md)
* [A HDInsight Apache Spark-fürt erőforrásainak kezelése](apache-spark-resource-manager.md)
* [Apache Spark-fürt távoli feladatokat küldhessen az Apache Spark REST API használatával](apache-spark-livy-rest-interface.md)
* [Az Apache Spark hangolása](https://spark.apache.org/docs/latest/tuning.html)
* [Hogyan ténylegesen finomhangolása az Apache Spark-feladatok úgy működnek](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo szerializáció](https://github.com/EsotericSoftware/kryo)
