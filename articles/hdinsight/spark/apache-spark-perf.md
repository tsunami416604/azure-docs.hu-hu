---
title: A Spark-feladatok optimalizálása a teljesítmény érdekében – Azure HDInsight
description: Az Azure HDInsight Apache Spark-fürtök legjobb teljesítményére vonatkozó általános stratégiák megjelenítése.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 3d8f4a28961be7e0ece517e00026d9711d8f67e9
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198871"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Apache Spark feladatok optimalizálása a HDInsight-ben

Megtudhatja, hogyan optimalizálhatja [Apache Spark](https://spark.apache.org/) -fürt konfigurációját az adott számítási feladathoz.  A leggyakoribb kihívás a memória nyomása, a nem megfelelő konfigurációk (különösen a nem megfelelő méretű végrehajtók), a hosszan futó műveletek, valamint a Descartes műveletet eredményező feladatok miatt. Felgyorsíthatja a feladatokat a megfelelő gyorsítótárazással, és engedélyezheti az [adatok eldöntését](#optimize-joins-and-shuffles). A legjobb teljesítmény érdekében figyelje és tekintse át a hosszú ideig futó és az erőforrás-igényes Spark-feladatok végrehajtását. A HDInsight Apache Spark használatának első lépéseivel kapcsolatos információkért lásd: [Apache Spark-fürt létrehozása Azure Portal használatával](apache-spark-jupyter-spark-sql-use-portal.md).

A következő szakaszok ismertetik a Spark-feladatok gyakori optimalizálásait és javaslatait.

## <a name="choose-the-data-abstraction"></a>Válassza ki az adatabsztrakciót

A korábbi Spark-verziók a RDD és az absztrakt adatokat, a Spark 1,3-es és a 1,6-es verzióját használták a DataFrames és adatkészletek használatára. Vegye figyelembe a következő relatív Érdemeiket:

* **DataFrames**
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

Új Spark-fürt létrehozásakor kiválaszthatja az Azure Blob Storage vagy Azure Data Lake Storaget a fürt alapértelmezett tárolója. Mindkét lehetőség biztosítja a hosszú távú tárolás előnyeit az átmeneti fürtök esetében, így az adatai nem törlődnek automatikusan a fürt törlésekor. Újra létrehozhat egy átmeneti fürtöt, és továbbra is hozzáférhet az adataihoz.

| Áruház típusa | Fájlrendszer | Sebesség | Átmeneti | Használati esetek |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:** //URL/ | **Standard** | Igen | Átmeneti fürt |
| Azure Blob Storage (biztonságos) | **wasbs:** //URL/ | **Standard** | Igen | Átmeneti fürt |
| 2\. generációs Azure Data Lake Storage| **abfs:** //URL/ | **Gyorsabb** | Igen | Átmeneti fürt |
| 1\. generációs Azure Data Lake Storage| **ADL:** //URL/ | **Gyorsabb** | Igen | Átmeneti fürt |
| Helyi HDFS | **hdfs:** //URL/ | **Leggyorsabb** | Nem | Interaktív 24/7-fürt |

A HDInsight-fürtökhöz elérhető tárolási lehetőségek teljes leírását lásd: a [tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használatra](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>A gyorsítótár használata

A Spark saját natív gyorsítótárazási mechanizmusokat biztosít, amelyek különböző módszerekkel használhatók, például `.persist()`, `.cache()`és `CACHE TABLE`. Ez a natív gyorsítótárazás a kis adatkészletek és az ETL-folyamatok esetében érvényes, ahol a közbenső eredmények gyorsítótárazására van szükség. A Spark natív gyorsítótárazás azonban jelenleg nem működik megfelelően a particionálással, mivel a gyorsítótárazott táblák nem őrzik meg a particionálási adatmennyiséget. Egy általánosabb és megbízható gyorsítótárazási módszer a *tárolási réteg gyorsítótárazása*.

* Natív Spark-gyorsítótárazás (nem ajánlott)
    * Jó kis adatkészletekhez.
    * Nem működik a particionálással, ami megváltozhat a jövőbeli Spark-kiadásokban.

* Tárolási szint gyorsítótárazása (ajánlott)
    * Az [i/o-gyorsítótár](apache-spark-improve-performance-iocache.md) funkciójának használatával valósítható meg a HDInsight.
    * Memóriabeli és SSD-gyorsítótárazást használ.

* Helyi HDFS (ajánlott)
    * `hdfs://mycluster` elérési út.
    * SSD-gyorsítótárazást használ.
    * A gyorsítótárazott adatvesztés a fürt törlésekor elveszik, a gyorsítótár újraépítését igényli.

## <a name="use-memory-efficiently"></a>Hatékony memória használata

A Spark úgy működik, hogy a memóriába helyezi az adatok mennyiségét, így a memória erőforrásainak kezelése kulcsfontosságú szempont a Spark-feladatok végrehajtásának optimalizálásához.  Több módszer is alkalmazható a fürt memóriájának hatékony használatára.

* A particionálási stratégiában a kisebb adatpartíciók és a fiókok mérete, típusai és eloszlása is előnyben részesített.
* Az alapértelmezett Java-szerializálás helyett vegye figyelembe az újabb, hatékonyabb [Kryo-adatszerializálást](https://github.com/EsotericSoftware/kryo).
* Inkább a FONALat használja, mivel elkülöníti a Batch által `spark-submit`.
* A Spark konfigurációs beállításainak figyelése és finomhangolása.

A következő képen látható a Spark-memória szerkezete és néhány kulcsfontosságú végrehajtói memória paraméter.

### <a name="spark-memory-considerations"></a>A Spark memória szempontjai

Ha [Apache HADOOP fonalat](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)használ, akkor a fonalak az egyes Spark-csomópontokon lévő összes tároló által felhasznált memória maximális számát vezérlik.  A következő ábrán a legfontosabb objektumok és azok kapcsolatai láthatók.

![A fonal Spark memóriájának kezelése](./media/apache-spark-perf/apache-yarn-spark-memory.png)

A "memórián kívüli" üzenetek megoldásához próbálkozzon a következővel:

* Tekintse át a DAG felügyeletének véletlenszerű működését. Csökkentse a leképezési oldali újrabontást, a partíció előtti (vagy bucketize) adatforrásokat, maximalizálja az egyetlen véletlenszerű sorrendet, és csökkentse a továbbított adatmennyiséget.
* Inkább `ReduceByKey` a rögzített memória korlátját `GroupByKey`, amely összesítéseket, ablakokat és egyéb funkciókat biztosít, de Ann unbounded memória korláttal rendelkezik.
* Inkább `TreeReduce`, amely több munkát hajt végre a végrehajtók vagy a partíciók számára, hogy `Reduce`, amely az illesztőprogramon működik.
* Az alsó szintű RDD-objektumok helyett használja a DataFrames.
* Hozzon létre olyan ComplexTypes, amelyek műveleteket (például "Top N"), különböző összesítéseket vagy ablakkezelő műveleteket ágyaznak be.

További hibaelhárítási lépésekért lásd: [működése OutOfMemoryError-kivételek Apache Spark az Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="optimize-data-serialization"></a>Az adatszerializálás optimalizálása

A Spark-feladatok terjesztése megtörténik, ezért a megfelelő adatszerializálás fontos a legjobb teljesítmény érdekében.  A Spark két szerializálási lehetőséggel rendelkezik:

* A Java-szerializálás az alapértelmezett.
* A Kryo szerializálása egy újabb formátum, amely gyorsabb és kompakt szerializálást eredményezhet a Javánál.  A Kryo használatához regisztrálnia kell az osztályokat a programban, és még nem támogatja az összes szerializálható típust.

## <a name="use-bucketing"></a>A gyűjtő használata

A gyűjtő az adatparticionáláshoz hasonló, de az egyes gyűjtők nem csupán egy oszlop értékét tárolhatják. A gyűjtő nagy mennyiségű (több millió vagy több) értékben, például termékazonosítóban is működik. A gyűjtőt a sor gyűjtő kulcsának kivonatolásával határozzuk meg. A gyűjtő táblák egyedi optimalizációkat biztosítanak, mert metaadatokat tárolnak a gyűjtők és a rendezésük módjával kapcsolatban.

Bizonyos speciális gyűjtő funkciók a következők:

* Lekérdezés optimalizálása a meta-adatok gyűjtője alapján.
* Optimalizált összesítések.
* Optimalizált illesztések.

Egyszerre használhatja a particionálást és a gyűjtőt.

## <a name="optimize-joins-and-shuffles"></a>Az illesztések és a shufflek optimalizálása

Ha lassú feladatokkal van összekapcsolva vagy shuffle, az OK valószínűleg az *adatok eldöntése*, ami a feladat adataiban található aszimmetria. A térképes feladatok például 20 másodpercet is igénybe vehetnek, de egy olyan feladatot futtatnak, ahol az adatok csatlakoztatva vannak, vagy a csoszogott órákat vesz igénybe. Az adatdöntés kijavításához a teljes kulcsot kell megállapítania, vagy egy *elkülönített sót* kell használnia a kulcsok csak néhány részhalmaza számára. Ha izolált sót használ, érdemes tovább szűrnie, hogy elkülönítse a sós kulcsok részhalmazát a térképi illesztésekben. Egy másik lehetőség egy gyűjtő oszlop bevezetése és a gyűjtők előzetes összesítése.

A lassú illesztéseket okozó másik tényező lehet az illesztés típusa. Alapértelmezés szerint a Spark a `SortMerge` illesztési típust használja. Az ilyen típusú illesztések leginkább nagy adatkészletekhez használhatók, de más számítási szempontból költségesek, mert először a bal és a jobb oldalon kell rendezni az adatok egyesítését.

A `Broadcast` illesztés a kisebb adatkészletekhez ideális, vagy ha az illesztés egyik oldala sokkal kisebb, mint a másik oldal. Az ilyen típusú illesztések az egyik oldalról az összes végrehajtóra mutatnak, ezért általában több memóriát igényelnek a szórások számára.

A konfigurációhoz tartozó illesztési típust a `spark.sql.autoBroadcastJoinThreshold`beállításával módosíthatja, vagy a DataFrame API-k (`dataframe.join(broadcast(df2))`) használatával is beállíthat illesztési mutatót.

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

Ha gyűjtő táblákat használ, akkor egy harmadik illesztési típussal rendelkezik, a `Merge` csatlakozhat. Egy megfelelően előre particionált és előre rendezett adatkészlet kihagyja a költséges rendezési fázist egy `SortMerge` illesztésből.

Az illesztések sorrendje, különösen az összetettebb lekérdezésekben. Kezdje a legszelektívebb illesztésekkel. Emellett olyan illesztéseket is helyezhet át, amelyek a sorok számát a lehetséges összesítések után növelhetik.

A Descartes-féle illesztések párhuzamosságának kezeléséhez beágyazott struktúrákat, ablakokat adhat hozzá, és lehet, hogy kihagy egy vagy több lépést a Spark-feladatokban.

## <a name="customize-cluster-configuration"></a>Fürtkonfiguráció testreszabása

A Spark-fürt számítási feladataitól függően megállapíthatja, hogy egy nem alapértelmezett Spark-konfiguráció a Spark-feladatok jobbá tételéhez vezetne.  Teljesítményteszt-tesztelés végrehajtása számítási feladatokkal a nem alapértelmezett fürtkonfiguráció ellenőrzéséhez.

Az alábbiakban néhány gyakori paramétert módosíthat:

* `--num-executors` beállítja a megfelelő számú végrehajtót.
* `--executor-cores` beállítja a magok számát az egyes végrehajtók számára. Általában közepes méretű végrehajtókkal kell rendelkeznie, mivel más folyamatok a rendelkezésre álló memóriát használják.
* `--executor-memory` beállítja az egyes végrehajtók számára a memória méretét, amely a FONALon megjelenő halom méretét vezérli. Hagyjon némi memóriát a végrehajtás terheléséhez.

### <a name="select-the-correct-executor-size"></a>A megfelelő végrehajtó méretének kiválasztása

A végrehajtó konfigurációjának meghatározásakor vegye figyelembe a Java Garbage Collection (GC) terhelését.

* A végrehajtó méretének csökkentésére szolgáló tényezők:
    1. Csökkentse a 32 GB alatti halom méretét a GC terhelésének < 10%-os megtartásához.
    2. Csökkentse a magok számát, hogy a GC terhelése < 10% legyen.

* A végrehajtó méretének növelésére szolgáló tényezők:
    1. A végrehajtók közötti kommunikációs terhelés csökkentése.
    2. Csökkentse a nyitott kapcsolatok számát a végrehajtók (N2) között nagyobb fürtökön (> 100 végrehajtó).
    3. Növelje a nagy mennyiségű memóriát, hogy megfeleljen a memória-igényes feladatok számára.
    4. Nem kötelező: csökkentse a végrehajtó memória terhelését.
    5. Nem kötelező: növelje a kihasználtságot és a párhuzamosságot a processzor túllépésével.

Általános ökölszabály a végrehajtó méretének kiválasztásakor:

1. Indítson el 30 GB-ot végrehajtóként, és terjesszen rendelkezésre álló gépi magokat.
2. Növelje a végrehajtó magok számát a nagyobb fürtöknél (> 100 végrehajtók).
3. A méretet a próbaverziós futtatások és az előző tényezők, például a GC terhelése alapján módosíthatja.

Az egyidejű lekérdezések futtatásakor vegye figyelembe a következőket:

1. Indítson el 30 GB-ot végrehajtóként és minden gépi magot.
2. Több párhuzamos Spark-alkalmazás létrehozása a processzor túllépésével (körülbelül 30%-os késéssel).
3. Lekérdezések terjesztése párhuzamos alkalmazások között.
4. A méretet a próbaverziós futtatások és az előző tényezők, például a GC terhelése alapján módosíthatja.

További információ a végrehajtók konfigurálásáról a Ambari használatával: [Apache Spark Settings-Spark végrehajtók](apache-spark-settings.md#configuring-spark-executors).

A lekérdezés teljesítményének figyelése kiugró vagy egyéb teljesítménnyel kapcsolatos problémák esetén, az Idősor nézet, az SQL Graph, a feladatok statisztikái és így tovább. A (z) és a Spark-előzményeket használó Spark-feladatok hibakeresésével kapcsolatos információkért lásd: [Apache Spark Azure HDInsight futó feladatok hibakeresése](apache-spark-job-debugging.md). A fonal idővonal-kiszolgálójának használatáról a következő témakörben talál további információt: [Apache HADOOP fonalak alkalmazási naplói](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Előfordulhat, hogy a végrehajtók közül egy vagy több lassabb, mint a többi, és a feladatok végrehajtása sokkal hosszabb ideig tart. Ez gyakran előfordul a nagyobb fürtökön (> 30 csomópont). Ebben az esetben a munkát nagyobb számú feladatra osztja fel, így az ütemező kompenzálhatja a lassú feladatokat. Például legalább kétszer annyi feladatnak kell lennie, mint a végrehajtó magok száma az alkalmazásban. A `conf: spark.speculation = true`használatával is engedélyezheti a feladatok spekulációs végrehajtását.

## <a name="optimize-job-execution"></a>Feladatok végrehajtásának optimalizálása

* Szükség szerint gyorsítótárazza, például ha kétszer használja az adatkészletet, majd gyorsítótárazza.
* Szórási változók az összes végrehajtóra. A változók csak egyszer szerializáltak, ami gyorsabb keresést eredményez.
* Használja a szál készletét az illesztőprogramon, ami gyorsabb műveletet eredményez számos feladathoz.

A futó feladatok rendszeres figyelése teljesítménnyel kapcsolatos problémák esetén. Ha további információkra van szüksége az egyes problémákkal kapcsolatban, vegye figyelembe az alábbi teljesítmény-profilkészítési eszközök egyikét:

* Az [Intel PAL eszköz](https://github.com/intel-hadoop/PAT) FIGYELI a CPU-t, a tárterületet és a hálózati sávszélesség-kihasználtságot.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) -profilok Spark és végrehajtó kód.

A Spark 2. x lekérdezési teljesítménye a Wolfram motor, amely a teljes fázisú programkódok generálásának függvénye. Bizonyos esetekben előfordulhat, hogy a teljes fázisú kód létrehozása le lesz tiltva. Ha például nem változtatható típust (`string`) használ az összesítési kifejezésben, akkor a `HashAggregate`helyett `SortAggregate` jelenik meg. Például a jobb teljesítmény érdekében próbálkozzon a következőkkel, majd engedélyezze újra a kód generálását:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Következő lépések

* [Azure HDInsighton futó Apache Spark-feladatok hibakeresése](apache-spark-job-debugging.md)
* [Apache Spark-fürt erőforrásainak kezelése a HDInsight-ben](apache-spark-resource-manager.md)
* [Távoli feladatok küldése Apache Spark-fürtre a Apache Spark REST API használatával](apache-spark-livy-rest-interface.md)
* [Hangolás Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [A Apache Spark-feladatok tényleges finomhangolása](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo szerializálás](https://github.com/EsotericSoftware/kryo)
