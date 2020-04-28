---
title: A Spark-feladatok optimalizálása a teljesítmény érdekében – Azure HDInsight
description: Az Azure HDInsight Apache Spark-fürtök legjobb teljesítményére vonatkozó általános stratégiák megjelenítése.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 736653e82f753341fbbdfb795f229145bba96162
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188191"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Apache Spark feladatok optimalizálása a HDInsight-ben

Megtudhatja, hogyan optimalizálhatja Apache Spark-fürt konfigurációját az adott számítási feladathoz.  A leggyakoribb kihívás a memória nyomása, mert nem megfelelő konfigurációk (például hibás méretű végrehajtók) miatt. Emellett a hosszan futó műveleteket és a Descartes-műveletet eredményező feladatokat is. Felgyorsíthatja a feladatokat a megfelelő gyorsítótárazással, és engedélyezheti az [adatok eldöntését](#optimize-joins-and-shuffles). A legjobb teljesítmény érdekében figyelje és tekintse át a hosszú ideig futó és az erőforrás-igényes Spark-feladatok végrehajtását. A HDInsight Apache Spark használatának első lépéseivel kapcsolatos információkért lásd: [Apache Spark-fürt létrehozása Azure Portal használatával](apache-spark-jupyter-spark-sql-use-portal.md).

A következő szakaszok ismertetik a Spark-feladatok gyakori optimalizálásait és javaslatait.

## <a name="choose-the-data-abstraction"></a>Válassza ki az adatabsztrakciót

A korábbi Spark-verziók a RDD és az absztrakt adatokat, a Spark 1,3-es és a 1,6-es verzióját használták a DataFrames és adatkészletek használatára. Vegye figyelembe a következő relatív Érdemeiket:

* **Adatkeretek**
    * Legjobb választás a legtöbb esetben.
    * Lekérdezési optimalizálást biztosít a katalizátoron keresztül.
    * Teljes fázisú programkódok létrehozása.
    * Közvetlen memória-hozzáférés.
    * Alacsony terhelésű gyűjtés (GC) terhelése.
    * Nem fejlesztőként, mint adatkészletek, mivel nincsenek fordítási idejű ellenőrzések vagy tartományi objektumok programozása.
* **Adatkészletek**
    * Olyan összetett ETL-folyamatokban jó, ahol a teljesítményre gyakorolt hatás elfogadható.
    * Nem jó olyan összesítésekben, ahol a teljesítményre gyakorolt hatás jelentős lehet.
    * Lekérdezési optimalizálást biztosít a katalizátoron keresztül.
    * Fejlesztőknek – a tartományi objektumok programozása és a fordítási idő ellenőrzésének biztosításával.
    * Szerializálási/deszerializálási terhelés hozzáadására szolgál.
    * Magas GC-terhelés.
    * A teljes fázisú programkódok létrehozásának megszakítása.
* **RDD**
    * Nem kell RDD használnia, hacsak nem kell új egyéni RDD létrehoznia.
    * Nincs lekérdezés optimalizálása a katalizátoron keresztül.
    * Nincs egész fázist generáló kód.
    * Magas GC-terhelés.
    * A Spark 1. x örökölt API-kat kell használnia.

## <a name="use-optimal-data-format"></a>Optimális adatformátum használata

A Spark számos formátumot támogat, például a CSV-t, a JSON-t, az XML-t, a parketta, az ork és a Avro. A Spark bővíthető a külső adatforrásokkal rendelkező több formátum támogatásához – további információ: [Apache Spark csomagok](https://spark-packages.org).

A teljesítmény legjobb formátuma a Parquet és a *Snappy Compression*, amely az alapértelmezett a Spark 2. x verzióban. A Parquet oszlopos formátumban tárolja az adatok, és a Spark-ban is nagyon optimalizált.

## <a name="select-default-storage"></a>Alapértelmezett tároló kiválasztása

Új Spark-fürt létrehozásakor kiválaszthatja az Azure Blob Storage vagy Azure Data Lake Storaget a fürt alapértelmezett tárolója. Mindkét lehetőség biztosítja a hosszú távú tárolás előnyeit az átmeneti fürtök esetében. Így az adatai nem törlődnek automatikusan, amikor törli a fürtöt. Újra létrehozhat egy átmeneti fürtöt, és továbbra is hozzáférhet az adataihoz.

| Áruház típusa | Fájlrendszer | Sebesség | Átmeneti | Használati esetek |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//URL/ | **Standard** | Igen | Átmeneti fürt |
| Azure Blob Storage (biztonságos) | **wasbs:**//URL/ | **Standard** | Igen | Átmeneti fürt |
| Azure Data Lake Storage Gen 2| **abfs:**//URL/ | **Gyorsabb** | Igen | Átmeneti fürt |
| 1. generációs Azure Data Lake Storage| **ADL:**//URL/ | **Gyorsabb** | Igen | Átmeneti fürt |
| Helyi HDFS | **hdfs:**//URL/ | **Leggyorsabb** | Nem | Interaktív 24/7-fürt |

A tárolási lehetőségek teljes leírását lásd: [tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>A gyorsítótár használata

A Spark saját natív gyorsítótárazási mechanizmusokat biztosít, amelyek különböző módszerekkel használhatók, `.persist()`például `.cache()`:, `CACHE TABLE`és. Ez a natív gyorsítótárazás a kis adathalmazok és az ETL-folyamatok esetében érvényes, ahol a közbenső eredmények gyorsítótárazására van szükség. A Spark natív gyorsítótárazás azonban jelenleg nem működik megfelelően a particionálással, mivel a gyorsítótárazott táblák nem őrzik meg a particionálási adatmennyiséget. Egy általánosabb és megbízható gyorsítótárazási módszer a *tárolási réteg gyorsítótárazása*.

* Natív Spark-gyorsítótárazás (nem ajánlott)
    * Jó kis adatkészletekhez.
    * Nem működik a particionálással, ami megváltozhat a jövőbeli Spark-kiadásokban.

* Tárolási szint gyorsítótárazása (ajánlott)
    * Az [i/o-gyorsítótár](apache-spark-improve-performance-iocache.md) funkciójának használatával valósítható meg a HDInsight.
    * Memóriabeli és SSD-gyorsítótárazást használ.

* Helyi HDFS (ajánlott)
    * `hdfs://mycluster`elérési útja.
    * SSD-gyorsítótárazást használ.
    * A gyorsítótárazott adatvesztés a fürt törlésekor elveszik, a gyorsítótár újraépítését igényli.

## <a name="use-memory-efficiently"></a>Hatékony memória használata

A Spark úgy működik, hogy a memóriába helyezi az adatforgalmat. Így a memória erőforrásainak kezelése kulcsfontosságú szempont a Spark-feladatok végrehajtásának optimalizálásához.  Több módszer is alkalmazható a fürt memóriájának hatékony használatára.

* A particionálási stratégiában a kisebb adatpartíciók és a fiókok mérete, típusai és eloszlása is előnyben részesített.
* Vegye figyelembe az újabb, hatékonyabb [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo), az alapértelmezett Java-szerializálás helyett.
* A FONALat inkább a Batch `spark-submit` által elválasztva használja.
* A Spark konfigurációs beállításainak figyelése és finomhangolása.

A következő képen látható a Spark-memória szerkezete és néhány kulcsfontosságú végrehajtói memória paraméter.

### <a name="spark-memory-considerations"></a>A Spark memória szempontjai

Ha Apache Hadoop FONALat használ, akkor a FONALak az egyes Spark-csomópontokon lévő összes tároló által használt memóriát vezérlik.  A következő ábrán a legfontosabb objektumok és azok kapcsolatai láthatók.

![A fonal Spark memóriájának kezelése](./media/apache-spark-perf/apache-yarn-spark-memory.png)

A "memórián kívüli" üzenetek megoldásához próbálkozzon a következővel:

* Tekintse át a DAG felügyeletének véletlenszerű működését. Csökkentse a leképezési oldali újrabontást, a partíció előtti (vagy bucketize) adatforrásokat, maximalizálja az egyetlen véletlenszerű sorrendet, és csökkentse a továbbított adatmennyiséget.
* Inkább `ReduceByKey` a rögzített memória korlátja, `GroupByKey`amely az összesítéseket, az ablakokat és más funkciókat tartalmaz, de Ann nem kötött memória korlátja van.
* A `TreeReduce`rendszer inkább a végrehajtók vagy a partíciók több munkáját hajtja `Reduce`végre a (z) rendszeren, amely az illesztőprogramon végzett összes munkát végzi.
* Az alsó szintű RDD-objektumok helyett használjon DataFrames.
* Hozzon létre olyan ComplexTypes, amelyek műveleteket (például "Top N"), különböző összesítéseket vagy ablakkezelő műveleteket ágyaznak be.

További hibaelhárítási lépésekért lásd: [működése OutOfMemoryError-kivételek Apache Spark az Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="optimize-data-serialization"></a>Az adatszerializálás optimalizálása

A Spark-feladatok terjesztése megtörténik, ezért a megfelelő adatszerializálás fontos a legjobb teljesítmény érdekében.  A Spark két szerializálási lehetőséggel rendelkezik:

* A Java-szerializálás az alapértelmezett.
* `Kryo`a szerializálás egy újabb formátum, amely gyorsabb és kompakt szerializálást eredményezhet a Javánál.  `Kryo`Ehhez regisztrálnia kell az osztályokat a programban, és még nem támogatja az összes szerializálható típust.

## <a name="use-bucketing"></a>A gyűjtő használata

A gyűjtő hasonló az adatparticionáláshoz. Az egyes gyűjtők azonban nem csupán egy oszlop értékeit tárolhatják. Ez a módszer jól működik, ha nagy (millió vagy több) értéket, például termékazonosítókat kíván particionálni. A gyűjtőt a sor gyűjtő kulcsának kivonatolásával határozzuk meg. A gyűjtő táblák egyedi optimalizációkat biztosítanak, mert metaadatokat tárolnak a gyűjtők és a rendezésük módjával kapcsolatban.

Bizonyos speciális gyűjtő funkciók a következők:

* Lekérdezés optimalizálása a meta-adatok gyűjtője alapján.
* Optimalizált összesítések.
* Optimalizált illesztések.

Egyszerre használhatja a particionálást és a gyűjtőt.

## <a name="optimize-joins-and-shuffles"></a>Az illesztések és a shufflek optimalizálása

Ha lassú feladattal rendelkezik egy JOIN vagy shuffle utasításban, az OK valószínűleg *adattorzítást*eredményez. Az adatok eldöntése az aszimmetria a feladatok adataiban. A térképes feladatok például 20 másodpercet is igénybe vehetnek. Ha azonban egy olyan feladatot futtat, amelyben az adatok csatlakoztatva vannak, vagy az adatkeverő órákat vesz igénybe. Az adatdöntés kijavításához a teljes kulcsot kell megállapítania, vagy egy *elkülönített sót* kell használnia a kulcsok csak néhány részhalmaza számára. Ha izolált sót használ, érdemes tovább szűrnie, hogy elkülönítse a sós kulcsok részhalmazát a térképi illesztésekben. Egy másik lehetőség egy gyűjtő oszlop bevezetése és a gyűjtők előzetes összesítése.

A lassú illesztéseket okozó másik tényező lehet az illesztés típusa. Alapértelmezés szerint a Spark az `SortMerge` illesztés típusát használja. Ez a fajta JOIN a legmegfelelőbb a nagyméretű adathalmazokhoz. Ez azonban egyébként költséges, mert először a bal és a jobb oldalt kell rendeznie az Adategyesítés előtt.

Az `Broadcast` illesztés a kisebb adatkészletekhez ideális, vagy ha az illesztés egyik oldala sokkal kisebb, mint a másik oldal. Az ilyen típusú illesztések az egyik oldalról az összes végrehajtóra mutatnak, ezért általában több memóriát igényelnek a szórások számára.

Az illesztési típust beállíthatja a konfigurációban `spark.sql.autoBroadcastJoinThreshold`, vagy beállíthatja az illesztési mutatót a DataFrame API-k`dataframe.join(broadcast(df2))`() használatával.

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

Ha gyűjtő táblákat használ, akkor egy harmadik illesztési típussal rendelkezik, amelyhez `Merge` csatlakozik. Egy megfelelően előre particionált és előre rendezett adatkészlet kihagyja a költséges rendezési szakaszt egy `SortMerge` illesztésből.

Az illesztések sorrendje, különösen az összetettebb lekérdezésekben. Kezdje a legszelektívebb illesztésekkel. Emellett olyan illesztéseket is helyezhet át, amelyek a sorok számát a lehetséges összesítések után növelhetik.

A Descartes-féle illesztések párhuzamosságának kezeléséhez beágyazott struktúrákat, ablakokat adhat hozzá, és lehet, hogy kihagy egy vagy több lépést a Spark-feladatokban.

## <a name="customize-cluster-configuration"></a>Fürtkonfiguráció testreszabása

A Spark-fürt számítási feladataitól függően előfordulhat, hogy egy nem alapértelmezett Spark-konfigurációt határoz meg, amely a Spark-feladatok fejlettebb végrehajtását eredményezi.  A nem alapértelmezett fürtkonfiguráció érvényesítéséhez végezzen teljesítményteszt-tesztelést a minta számítási feladatokkal.

Az alábbiakban néhány gyakori paramétert módosíthat:

|Paraméter |Leírás |
|---|---|
|--NUM-végrehajtók|Beállítja a megfelelő számú végrehajtót.|
|– végrehajtó – magok|Beállítja a magok számát az egyes végrehajtók számára. Általában közepes méretű végrehajtókkal kell rendelkeznie, mivel más folyamatok a rendelkezésre álló memóriát használják.|
|--végrehajtó – memória|Beállítja az egyes végrehajtók számára a memória méretét, amely a FONALon megjelenő halom méretét vezérli. Hagyjon némi memóriát a végrehajtás terheléséhez.|

### <a name="select-the-correct-executor-size"></a>A megfelelő végrehajtó méretének kiválasztása

A végrehajtó konfigurációjának meghatározásakor vegye figyelembe a Java Garbage Collection (GC) terhelését.

* A végrehajtó méretének csökkentésére szolgáló tényezők:
    1. Csökkentse a 32 GB alatti halom méretét a GC terhelésének < 10%-os megtartásához.
    2. Csökkentse a magok számát, hogy a GC terhelése < 10% legyen.

* A végrehajtó méretének növelésére szolgáló tényezők:
    1. A végrehajtók közötti kommunikációs terhelés csökkentése.
    2. Csökkentse a nyitott kapcsolatok számát a végrehajtók (N2) között nagyobb fürtökön (>100 végrehajtók).
    3. Növelje a nagy mennyiségű memóriát, hogy megfeleljen a memória-igényes feladatok számára.
    4. Nem kötelező: csökkentse a végrehajtó memória terhelését.
    5. Nem kötelező: növelje a használatot és a párhuzamosságot a processzor túllépésével.

Általános szabály a végrehajtó méretének kiválasztásakor:

1. Indítson el 30 GB-ot végrehajtóként, és terjesszen rendelkezésre álló gépi magokat.
2. Növelje a végrehajtó magok számát a nagyobb fürtöknél (> 100 végrehajtók).
3. A méretet a próbaverziós futtatások és az előző tényezők, például a GC terhelése alapján módosíthatja.

Az egyidejű lekérdezések futtatásakor vegye figyelembe a következőket:

1. Indítson el 30 GB-ot végrehajtóként és minden gépi magot.
2. Több párhuzamos Spark-alkalmazás létrehozása a processzor túllépésével (körülbelül 30%-os késéssel).
3. Lekérdezések terjesztése párhuzamos alkalmazások között.
4. A méretet a próbaverziós futtatások és az előző tényezők, például a GC terhelése alapján módosíthatja.

További információ a végrehajtók konfigurálásáról a Ambari használatával: [Apache Spark Settings-Spark végrehajtók](apache-spark-settings.md#configuring-spark-executors).

A lekérdezési teljesítmény figyelése a kiugró vagy más teljesítménnyel kapcsolatos problémák esetében az Idősor nézet alapján. Emellett az SQL Graph, a feladatok statisztikái és így tovább. A (z) és a Spark-előzményeket használó Spark-feladatok hibakeresésével kapcsolatos információkért lásd: [Apache Spark Azure HDInsight futó feladatok hibakeresése](apache-spark-job-debugging.md). A fonal idővonal-kiszolgálójának használatáról a következő témakörben talál további információt: [Apache HADOOP fonalak alkalmazási naplói](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Előfordulhat, hogy a végrehajtók közül egy vagy több lassabb, mint a többi, és a feladatok végrehajtása sokkal hosszabb ideig tart. Ez a lassulás gyakran fordul elő nagyobb fürtökön (> 30 csomópont). Ebben az esetben a munkát nagyobb számú feladatra osztja fel, így az ütemező kompenzálhatja a lassú feladatokat. Például legalább kétszer annyi feladatnak kell lennie, mint a végrehajtó magok száma az alkalmazásban. A `conf: spark.speculation = true`feladatokhoz tartozó spekulatív végrehajtást is engedélyezheti.

## <a name="optimize-job-execution"></a>Feladatok végrehajtásának optimalizálása

* Szükség szerint gyorsítótárazza, például ha kétszer használja az adatkészletet, majd gyorsítótárazza.
* Szórási változók az összes végrehajtóra. A változók csak egyszer szerializáltak, ami gyorsabb keresést eredményez.
* Használja a szál készletét az illesztőprogramon, ami gyorsabb műveletet eredményez számos feladathoz.

A futó feladatok rendszeres figyelése teljesítménnyel kapcsolatos problémák esetén. Ha további információkra van szüksége az egyes problémákkal kapcsolatban, vegye figyelembe az alábbi teljesítmény-profilkészítési eszközök egyikét:

* Az [Intel PAL eszköz](https://github.com/intel-hadoop/PAT) FIGYELI a CPU-t, a tárterületet és a hálózati sávszélesség-használatot.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) -profilok Spark és végrehajtó kód.

A Spark 2. x lekérdezési teljesítménye a Wolfram motor, amely a teljes fázisú programkódok generálásának függvénye. Bizonyos esetekben előfordulhat, hogy a teljes fázisú kód létrehozása le lesz tiltva. Ha például nem megváltoztathatatlan típust () használ az`string`összesítési kifejezésben, `SortAggregate` a () helyett a `HashAggregate`következő jelenik meg:. Például a jobb teljesítmény érdekében próbálkozzon a következőkkel, majd engedélyezze újra a kód generálását:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>További lépések

* [Azure HDInsighton futó Apache Spark-feladatok hibakeresése](apache-spark-job-debugging.md)
* [Apache Spark-fürt erőforrásainak kezelése a HDInsight-ben](apache-spark-resource-manager.md)
* [Az Apache Spark beállításainak konfigurálása](apache-spark-settings.md)
* [Hangolás Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [A Apache Spark-feladatok tényleges finomhangolása](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [`Kryo Serialization`](https://github.com/EsotericSoftware/kryo)
