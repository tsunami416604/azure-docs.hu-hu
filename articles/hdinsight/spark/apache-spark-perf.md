---
title: Spark-feladatok optimalizálása a teljesítmény érdekében – Azure HDInsight
description: Az Azure HDInsightban az Apache Spark-fürtök legjobb teljesítményére vonatkozó közös stratégiák megjelenítése.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/17/2020
ms.openlocfilehash: 5012b5abf12beadbcb18f21fe2fe6ebfb076598a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617975"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Az Apache Spark-feladatok optimalizálása a HDInsightban

Ismerje meg, hogyan optimalizálhatja az Apache Spark fürtkonfigurációját az adott számítási feladathoz.  A leggyakoribb kihívás a memórianyomás, a nem megfelelő konfigurációk (például a nem megfelelő méretű végrehajtók) miatt. Emellett hosszú ideig futó műveletek és a descartes-i műveleteket eredményező feladatok. Felgyorsíthatja a feladatokat a megfelelő gyorsítótárazással, és lehetővé teheti az [adatok elferdítését.](#optimize-joins-and-shuffles) A legjobb teljesítmény érdekében figyelje és tekintse át a hosszú ideig futó és erőforrás-igényes Spark-feladat-végrehajtásokat. Az Apache Spark HDInsight-alapú használatának megkezdéséről az [Apache Spark-fürtök létrehozása az Azure Portalon című témakörben](apache-spark-jupyter-spark-sql-use-portal.md)talál.

A következő szakaszok ismertetik a Spark-feladat-optimalizálásés -javaslatok közös leírását.

## <a name="choose-the-data-abstraction"></a>Az adatasztrakció kiválasztása

A korábbi Spark-verziók RDD-ket használnak az absztrakt adatokhoz, a Spark 1.3 és az 1.6 pedig a DataFrames-et és az Adathalmazokat. Tekintsük a következő relatív érdemeket:

* **Adatkeretek**
    * A legjobb választás a legtöbb helyzetben.
    * A Catalyst-on keresztül biztosítja a lekérdezés optimalizálását.
    * Teljes stádiumú kódgenerálás.
    * Közvetlen memória-hozzáférés.
    * Alacsony szemétgyűjtés (GC) rezsi.
    * Nem olyan fejlesztőbarát, mint a Adatkészletek, mivel nincsenek fordítási idejű ellenőrzések vagy tartományi objektumok programozása.
* **Adatkészletek**
    * Jó összetett ETL-folyamatokban, ahol a teljesítményhatás elfogadható.
    * Nem jó az összesítésekben, ahol a teljesítményre gyakorolt hatás jelentős lehet.
    * A Catalyst-on keresztül biztosítja a lekérdezés optimalizálását.
    * Fejlesztőbarát tartományi objektumprogramozás és fordítási idejű ellenőrzések biztosításával.
    * Szerializálási/deszerializálási többletterheléshozzáadása.
    * Magas GC felső.
    * Megtöri az egész szakaszban lévő kódgenerálást.
* **RDD-k**
    * Nem kell RDD-ket használnia, kivéve, ha új egyéni RDD-t kell készítenie.
    * Nincs lekérdezésoptimalizálás a Catalyst-on keresztül.
    * Nincs teljes stádiumú kódgenerálás.
    * Magas GC felső.
    * A Spark 1.x örökölt API-kat kell használnia.

## <a name="use-optimal-data-format"></a>Optimális adatformátum használata

A Spark számos formátumot támogat, például a csv, a json, az xml, a parketta, az ork és az avro. A Spark kiterjeszthető a külső adatforrásokkal rendelkező formátumok támogatására - további információkért lásd: [Apache Spark-csomagok.](https://spark-packages.org)

A legjobb teljesítményformátum a *lendületes tömörítéssel*ellátott parketta, amely a Spark 2.x alapértelmezett beállítása. A Parketta oszlopos formátumban tárolja az adatokat, és a Sparkban nagymértékben optimalizált.

## <a name="select-default-storage"></a>Alapértelmezett tároló kiválasztása

Amikor új Spark-fürtöt hoz létre, kiválaszthatja az Azure Blob Storage-ot vagy az Azure Data Lake Storage-t a fürt alapértelmezett tárolójaként. Mindkét lehetőség lehetővé teszi a hosszú távú tárolás ta- Így az adatok nem törlődnek automatikusan a fürt törlésekor. Létrehozhat egy átmeneti fürtöt, és továbbra is hozzáférhet az adatokhoz.

| Áruház típusa | Fájlrendszer | Sebesség | Átmeneti | Használati esetek |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//url/ | **Standard** | Igen | Átmeneti fürt |
| Azure Blob Storage (biztonságos) | **wasbs:**//url/ | **Standard** | Igen | Átmeneti fürt |
| Azure Data Lake Storage Gen 2| **abfs:**//url/ | **Gyorsabb** | Igen | Átmeneti fürt |
| Azure Data Lake Storage Gen 1| **adl:**//url/ | **Gyorsabb** | Igen | Átmeneti fürt |
| Helyi nagy számítógép-beállítások | **hdfs:**//url/ | **Leggyorsabb** | Nem | Interaktív 24/7 fürt |

A tárolási lehetőségek teljes leírását az [Azure HDInsight-fürtökkel való tárolási beállítások összehasonlítása témakörben található.](../hdinsight-hadoop-compare-storage-options.md)

## <a name="use-the-cache"></a>A gyorsítótár használata

A Spark saját natív gyorsítótárazási mechanizmusait biztosítja, `.cache()`amelyek `CACHE TABLE`különböző módszerekkel használhatók, például a `.persist()`, és a . Ez a natív gyorsítótárazás kis adatkészletek és ETL-folyamatok esetén hatékony, ahol a köztes eredmények gyorsítótárazására van szükség. Azonban a Spark natív gyorsítótárazása jelenleg nem működik jól a particionálás, mivel a gyorsítótárazott tábla nem tartja a particionálási adatokat. Egy általánosabb és megbízhatóbb gyorsítótárazási technika a *tárolóréteg-gyorsítótárazás.*

* Natív szikra-gyorsítótárazás (nem ajánlott)
    * Jó kis adatkészletek számára.
    * Nem működik a particionálás, amely a jövőbeli Spark-kiadások ban változhat.

* Tárolási szint gyorsítótárazása (ajánlott)
    * A HDInsight az [IO-gyorsítótár](apache-spark-improve-performance-iocache.md) szolgáltatáshasználatával valósítható meg.
    * A memórián belüli és az SSD-gyorsítótárazást használja.

* Helyi HDFS (ajánlott)
    * `hdfs://mycluster`Elérési út.
    * SSD-gyorsítótárazást használ.
    * A gyorsítótárazott adatok elvesznek a fürt törlésekor, és gyorsítótár-újraépítést igényelnek.

## <a name="use-memory-efficiently"></a>A memória hatékony használata

A Spark úgy működik, hogy adatokat helyez el a memóriában. Így a memória-erőforrások kezelése a Spark-feladatok végrehajtásának optimalizálása kulcsfontosságú szempont.  A fürt memóriájának hatékony használatához számos technikát alkalmazhat.

* Előnyben részesíti a kisebb adatpartíciókat, és vegye figyelembe az adatok méretét, típusait és eloszlását a particionálási stratégiában.
* Tekintsük az újabb, hatékonyabb [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo), nem pedig az alapértelmezett Java szerializálás.
* Szívesebben használja a YARN-t, mivel kötegelt. `spark-submit`
* A Spark konfigurációs beállításainak figyelése és finomhangolása.

A referencia, a Spark memória szerkezete és néhány kulcsfontosságú végrehajtó memória paraméterei jelennek meg a következő képen.

### <a name="spark-memory-considerations"></a>A Spark memóriával kapcsolatos szempontok

Ha Apache Hadoop YARN-t használ, majd a YARN szabályozza az egyes Spark-csomópontok összes tárolója által használt memóriát.  Az alábbi ábra a legfontosabb objektumokat és azok kapcsolatait mutatja be.

![YARN Spark memóriakezelés](./media/apache-spark-perf/apache-yarn-spark-memory.png)

A "kevés memóriával" kapcsolatos üzenetek címzéséhez próbálkozzon a következőkkel:

* Tekintse át a DAG Management Shuffles-t. Csökkentse a térképoldali reducting, pre-partition (vagy bucketize) forrásadatok, maximalizálja az egyes véletlen sorrendű, és csökkenti az elküldött adatok mennyiségét.
* Inkább `ReduceByKey` a rögzített memóriakorláttal `GroupByKey`, amely összesítéseket, ablakolást és egyéb függvényeket biztosít, de nem kötött memóriakorláttal rendelkezik.
* Inkább `TreeReduce`, amely nem több munkát a végrehajtók `Reduce`vagy partíciók, a , amely nem minden munkát az illesztőprogram.
* Az alacsonyabb szintű RDD-objektumok helyett használjon DataFrame-eket.
* Hozzon létre olyan ComplexTypes típusokat, amelyek olyan műveleteket foglalnak magukban, mint a "Top N", a különböző összesítések vagy az ablakolási műveletek.

További hibaelhárítási lépéseket az [Azure HDInsight Ban az Apache Spark OutOfMemoryError kivételei című témakörben.](apache-spark-troubleshoot-outofmemory.md)

## <a name="optimize-data-serialization"></a>Adatok szerializálásának optimalizálása

Spark-feladatok vannak elosztva, ezért a megfelelő adatszerializálás fontos a legjobb teljesítmény érdekében.  A Sparknak két szerializálási lehetősége van:

* A Java szerializálás az alapértelmezett.
* `Kryo`a szerializálás egy újabb formátum, és gyorsabb és kompaktabb szerializációt eredményezhet, mint a Java.  `Kryo`megköveteli, hogy regisztrálja az osztályokat a programban, és ez még nem támogatja az összes Szerializálható típusok.

## <a name="use-bucketing"></a>Gyűjtőhasználata

A gyűjtőgyűjtés hasonló az adatparticionáláshoz. De minden gyűjtő képes tárolni egy sor oszlop értékek helyett csak egy. Ez a módszer jól működik nagy (több millió vagy több) értékszám, például termékazonosítók particionálásához. A gyűjtőt a sor gyűjtőkulcsának kivonatolásával határozzuk meg. A gyűjtőtáblák egyedi optimalizálást kínálnak, mivel metaadatokat tárolnak a rról, hogyan vannak bevésve és rendezve.

Néhány speciális bucketing funkciók a következők:

* Lekérdezésoptimalizálás a metaadatok gyűjtőkörén alapuló módon.
* Optimalizált összesítések.
* Optimalizált illesztések.

Használhatja a particionálás és a bucketing egy időben.

## <a name="optimize-joins-and-shuffles"></a>Az illesztések és a véletlen sorrendű lejátszás optimalizálása

Ha lassú feladatok vannak egy illesztésen vagy véletlen sorrendű en, az ok valószínűleg *adatdöntés*. Az adatdöntés aszimmetria a feladatadatokban. Egy térképfeladat például 20 másodpercet is igénybe vehet. Az adatok összeillesztésének vagy véletlen sorrendű lejátszásának futtatásához azonban órákba telik. Az adatok döntésének javításához sózni kell az egész kulcsot, vagy *elszigetelt sót* kell használnia a kulcsok nak csak egy részhalmazához. Ha elszigetelt sót használ, további szűrést kell használnia a sózott kulcsok részhalmazának elkülönítéséhez a térképillesztésekben. Egy másik lehetőség egy gyűjtőoszlop bevezetése és előösszesített a vödrökben.

Egy másik tényező, amely lassú illesztéseket okoz, az illesztés típusa lehet. Alapértelmezés szerint a `SortMerge` Spark az illesztési típust használja. Az ilyen típusú illesztés nagy adatkészletek esetén a legalkalmasabb. De egyébként számításilag drága, mert először rendeznie kell az adatok bal és jobb oldalát, mielőtt összeolvasztana őket.

Az `Broadcast` illesztés kisebb adatkészletek esetén a legalkalmasabb, vagy ha az illesztés egyik oldala sokkal kisebb, mint a másik oldal. Ez a fajta illesztés az egyik oldalon az összes végrehajtónak sugároz, és így általában több memóriát igényel az adások számára.

Az illesztés típusát a konfigurációban `spark.sql.autoBroadcastJoinThreshold`módosíthatja a beállítással, vagy beállíthatja`dataframe.join(broadcast(df2))`az illesztési emlékeztetőt a DataFrame API-k ( használatával ).

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

Ha gyűjtőtáblákat használ, akkor van egy harmadik illesztési típusa, az `Merge` illesztés. A megfelelően előre particionált és előre rendezett adatkészlet kihagyja `SortMerge` a költséges rendezési fázist az illesztésből.

Az illesztések sorrendje számít, különösen az összetettebb lekérdezéseknél. Kezdje a legszelektívebb illesztésekkel. Az illesztések áthelyezése is, amelyek lehetőség szerint növelik a sorok számát az összesítések után.

A Descartes-illesztések párhuzamosságának kezeléséhez hozzáadhat egymásba ágyazott struktúrákat, ablakolást, és esetleg kihagyhat egy vagy több lépést a Spark-feladatban.

## <a name="customize-cluster-configuration"></a>Fürtkonfiguráció testreszabása

A Spark-fürt számítási feladatától függően előfordulhat, hogy egy nem alapértelmezett Spark-konfiguráció tszeretne optimalizált Spark-feladat végrehajtása.  A nem alapértelmezett fürtkonfigurációk érvényesítéséhez végezze el a számítási feladatok mintaszámítási feladatokkal végzett teljesítményteszteket.

Íme néhány gyakori paraméter, amelyet módosíthat:

|Paraméter |Leírás |
|---|---|
|--num-végrehajtók|Beállítja a végrehajtók megfelelő számát.|
|--végrehajtó-magok|Beállítja az egyes végrehajtók magjainak számát. Általában közepes méretű végrehajtókkal kell rendelkeznie, mivel más folyamatok a rendelkezésre álló memória egy részét használják fel.|
|--végrehajtó-memória|Beállítja az egyes végrehajtók memóriaméretét, amely a YARN halommemória méretét szabályozza. Hagyjon némi memóriát a végrehajtáshoz.|

### <a name="select-the-correct-executor-size"></a>A végrehajtó megfelelő méretének kiválasztása

A végrehajtó konfigurációjának meghatározásakor vegye figyelembe a Java szemétgyűjtés (GC) terhelését.

* A végrehajtó méretének csökkentésére játszott tényezők:
    1. Csökkentse a halommemória méretét 32 GB alá, hogy a GC felső < 10%.
    2. Csökkentse a magok számát, hogy a GC < 10%.

* A végrehajtó méretének növelését tényezők:
    1. Csökkentse a végrehajtók közötti kommunikációs terhelést.
    2. Csökkentse a végrehajtók (N2) közötti nyitott kapcsolatok számát nagyobb fürtökön (>100 végrehajtó).
    3. Növelje a halommemória méretét a memóriaigényes feladatok hoz.
    4. Nem kötelező: Csökkentse a végrehajtónkénti memória terhelését.
    5. Nem kötelező: Növelje a használatot és az egyidejűséget a processzor túlírásával.

Általános szabály, hogy a végrehajtó méretének kiválasztásakor:

1. Kezdje a végrehajtónkénti 30 GB-tal, és ossza el a rendelkezésre álló gépmagokat.
2. Növelje a végrehajtó magok számát a nagyobb fürtök (> 100 végrehajtók).
3. Módosítsa a méretet mind a próbafuttatások, mind az előző tényezők, például a globális katalógus terhelése alapján.

Egyidejű lekérdezések futtatásakor vegye figyelembe a következőket:

1. Kezdje a 30 GB/végrehajtóval és az összes gépmaggal.
2. Hozzon létre több párhuzamos Spark-alkalmazást a PROCESSZOR túljelentkezésével (körülbelül 30%-os késés javítása).
3. Lekérdezések elosztása párhuzamos alkalmazások között.
4. Módosítsa a méretet mind a próbafuttatások, mind az előző tényezők, például a globális katalógus terhelése alapján.

Az Ambari végrehajtók konfigurálásával kapcsolatos további információkért lásd: [Apache Spark-beállítások - Spark-végrehajtók.](apache-spark-settings.md#configuring-spark-executors)

Az idővonal nézet megtekintésével figyelheti a lekérdezés teljesítményét a kiugró értékek vagy egyéb teljesítményproblémák miatt. Sql graph, állásstatisztikák és így tovább. A YARN és a Spark History server használatával végzett Spark-feladatok hibakereséséről az [Azure HDInsight-on futó Debug Apache Spark-feladatok](apache-spark-job-debugging.md)című témakörben talál további információt. A YARN Timeline Server használatával kapcsolatos tippek az [Access Apache Hadoop YARN alkalmazásnaplóiban olvashat.](../hdinsight-hadoop-access-yarn-app-logs-linux.md)

Néha az egyik vagy néhány végrehajtó lassabb, mint a többi, és a feladatok végrehajtása sokkal hosszabb időt vesz igénybe. Ez a lassúság gyakran fordul elő nagyobb fürtökön (> 30 csomópont). Ebben az esetben ossza fel a munkát nagyobb számú tevékenységre, hogy az ütemező kompenzálhassa a lassú tevékenységeket. Például legalább kétszer annyi feladatot, mint a végrehajtó magok száma az alkalmazásban. A segítségével is engedélyezheti a `conf: spark.speculation = true`feladatok spekulatív végrehajtását.

## <a name="optimize-job-execution"></a>Feladat-végrehajtás optimalizálása

* Szükség szerint gyorsítótárazza, például ha kétszer használja az adatokat, majd gyorsítótárazza.
* Változók közvetítése az összes végrehajtónak. A változók csak egyszer szerializálódnak, ami gyorsabb keresőket eredményez.
* Használja a szálkészletet az illesztőprogramon, ami számos feladat gyorsabb működését eredményezi.

Rendszeresen figyelheti a futó feladatokat a teljesítményproblémákra. Ha további információkra van szüksége bizonyos problémákba, fontolja meg az alábbi teljesítményprofil-készítő eszközök egyikét:

* [Az Intel PAL Tool](https://github.com/intel-hadoop/PAT) figyeli a CPU- és tárhelyhasználatot és a hálózati sávszélesség-használatot.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profilok Spark és végrehajtó kódot.

A Spark 2.x lekérdezési teljesítménykulcsa a Tungsten motor, amely a teljes fázisú kódgenerálástól függ. Bizonyos esetekben a teljes szakaszban lévő kódgenerálás letiltható. Ha például nem módosítható típust (`string`) használ az összesítési `SortAggregate` `HashAggregate`kifejezésben, akkor a helyett a érték jelenik meg. A jobb teljesítmény érdekében például próbálkozzon a következőkkel, majd engedélyezze újra a kódgenerálást:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>További lépések

* [Azure HDInsighton futó Apache Spark-feladatok hibakeresése](apache-spark-job-debugging.md)
* [Apache Spark-fürt erőforrásainak kezelése a HDInsighton](apache-spark-resource-manager.md)
* [Az Apache Spark beállításainak konfigurálása](apache-spark-settings.md)
* [Az Apache Spark hangolása](https://spark.apache.org/docs/latest/tuning.html)
* [Hogyan lehet ténylegesen tune Your Apache Spark Jobs Így működnek](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [`Kryo Serialization`](https://github.com/EsotericSoftware/kryo)
