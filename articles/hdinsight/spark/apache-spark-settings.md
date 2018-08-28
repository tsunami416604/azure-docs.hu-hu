---
title: Spark-beállítások – Azure HDInsight konfigurálása
description: Hogyan konfigurálhatja a Spark az Azure HDInsight-fürt.
services: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/26/2018
ms.openlocfilehash: 5678cc17cde833c02c21090a7b5f7b70c01eec9e
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045313"
---
# <a name="configure-spark-settings"></a>A Spark beállításainak konfigurálása

Egy HDInsight Spark-fürt telepíteni kell az Apache Spark-kódtár tartalmazza.  Minden egyes HDInsight-fürt alapértelmezett konfigurációs paramétereket a telepített szolgáltatásokhoz, például a Spark tartalmazza.  Egyik fontos szempontja a HDInsight Hadoop-fürt kezelése által figyelt számítási feladatot, beleértve a Spark-feladatok, hogy a feladatok futnak, kiszámítható módon. Legjobb Spark-feladatokat futtathat, vegye figyelembe a fizikai fürtkonfiguráció logikai fürtkonfiguráció optimalizálása érdekében.

Az alapértelmezett HDInsight az Apache Spark-fürt a következő csomópontokat tartalmazza: három ZooKeeper-csomópontok, a két fő csomópont és a egy vagy több munkavégző csomópontok:

![A Spark HDInsight-architektúra](./media/apache-spark-settings/spark-hdinsight-arch.png)

A virtuális gépek számát, és a Virtuálisgép-méretek esetében a HDInsight-fürtben található csomópontok is befolyásolhatja a Spark-konfigurációt. Nem alapértelmezett HDInsight konfigurációs értékek gyakran nem alapértelmezett Spark konfigurációs értékekre van szükség. Egy HDInsight Spark-fürt létrehozásakor jelenik meg a virtuális gépek javasolt mérete az egyes összetevők. Jelenleg a [memóriaoptimalizált Linux Virtuálisgép-méretek](../../virtual-machines/linux/sizes-memory.md) D12 v2 vannak az Azure-hoz vagy nagyobb.

## <a name="spark-versions"></a>Spark-verzió

A legjobb Spark-verziót használja a fürt számára.  A HDInsight szolgáltatás tartalmazza a Spark és a HDInsight magát különböző verzióiban.  Minden Spark verziója alapértelmezett fürtbeállítások készletét tartalmazza.  

Amikor létrehoz egy új fürtöt, az alábbiakban az aktuális Spark-verziók közül választhat:

![Spark-verzió](./media/apache-spark-settings/spark-version.png)

A Spark 2.x sokkal jobb, mint a Spark-1.x futtathatja. A Spark 2.x számos teljesítményoptimalizálás segíti, például a volfrám, katalizáló optimalizálás és más.  

> [!NOTE]
> Az alapértelmezett verzió Apache sparkot a HDInsight szolgáltatásban értesítés nélkül változhatnak. Ha egy függőségi, a Microsoft azt javasolja, adjon meg, hogy adott verziót .NET SDK vagy az Azure PowerShell és az Azure CLI használatával fürtök létrehozásakor.

Az Apache Spark rendelkezik rendszer-konfiguráció három helyen:

* Spark-tulajdonságok a legtöbb alkalmazás paramétereit szabályozzák, és használatával állítható egy `SparkConf` objektumot, vagy a Java rendszer tulajdonságai párbeszédpanelen.
* A környezeti változók segítségével állítsa be a számítógépenkénti beállításokat, például az IP-cím segítségével a `conf/spark-env.sh` szkript minden csomóponton.
* Naplózás segítségével konfigurálható `log4j.properties`.

Amikor kiválaszt egy adott verzióját a Spark, a fürt alapértelmezett konfigurációs beállításait tartalmazza.  Az alapértelmezett Spark konfigurációs értékeket módosíthatja a Spark egyéni konfigurációs fájl használatával.  Az alábbiakban egy példa látható.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

A fenti példában öt Spark konfigurációs paraméterek alapértelmezett értékeinek több felülbírálja.  Ezek a tömörítési kodeket, Hadoop MapReduce minimális mérete és a parquet eszközökben velikostí bloku, valamint a Spar SQL partíciós felosztása, és nyitott fájlméretek alapértelmezett értéket.  Konfigurációs módosítások közül választ, mert a társított adatokat és a feladatok (például a genomikus adatokat), hogy adott jellemzőkkel jobban ezekkel a beállításokkal egyéni konfigurációs hajtja végre.

---

## <a name="view-cluster-configuration-settings"></a>Nézet fürtbeállítások

Ellenőrizze a jelenlegi HDInsight-fürt konfigurációs beállítások, mielőtt végrehajtaná a teljesítmény optimalizálása a fürtön. Az Azure Portalról HDInsight irányítópult elindításához kattintson a **irányítópult** a Spark-fürt panelen található hivatkozásra. Jelentkezzen be a fürt rendszergazdai felhasználónevet és jelszót.

Az Ambari webes Kezelőfelületen jelenik meg, az irányítópult-nézet, a fürt fő erőforrás-kihasználtsági mérőszámokat.  Az Ambari műszerfal az Apache Spark-konfiguráció és egyéb szolgáltatások, amelyen telepítve van. Az irányítópult tartalmaz egy **Config előzmények** lapra, ahol megtekintheti az összes telepített szolgáltatás, így a Spark konfigurációs adatait.

Az Apache Spark konfigurációs értékek megtekintéséhez válasszon **Config előzmények**, majd **Spark2**.  Válassza ki a **Configs** lapfülre, majd válassza ki a `Spark` (vagy `Spark2`verziójától függően) hivatkozásra a szolgáltatások listájában.  A fürt számára a konfigurációs értékek listáját láthatja:

![Spark-konfigurációk](./media/apache-spark-settings/spark-config.png)

Tekintse meg, és módosítsa az egyes Spark konfigurációs értékeket, válassza a hivatkozás cím "spark" szót tartalmazó minden olyan hivatkozás.  Spark konfigurációi ezekben a kategóriákban található mindkét egyéni és speciális konfigurációs értékeket tartalmaznak:

* Egyéni Spark2 – alapértelmezett érték
* Egyéni Spark2-mérőszámok – tulajdonságok
* Speciális Spark2 – alapértelmezett érték
* Speciális Spark2-env
* Speciális spark2 – hive-webhely – felülbírálás

Ha a konfigurációs értékeket, nem alapértelmezett csoportját hozhatja létre, akkor megtekintheti a konfigurációfrissítések előzményeit is.  A korábbi konfigurációkat, mely nem alapértelmezett konfiguráció tartozik-e optimális teljesítmény érdekében hasznos lehet.

> [!NOTE]
> Tekintse meg, de nem módosíthatja, közös Spark fürt konfigurációs beállításokat, jelölje be a **környezet** lapján a legfelső szintű **Spark-feladat felhasználói felület** felületet.

## <a name="configuring-spark-executors"></a>A Spark végrehajtóval konfigurálása

Az alábbi ábrán látható objektumok Spark: az illesztőprogram program és a Spark környezet és a kezelő és a hozzá tartozó *n* feldolgozó csomópontokat.  Minden egyes munkavégző csomópont tartalmaz egy végrehajtó, a gyorsítótár és *n* tevékenység-példányok.

![Fürt objektumok](./media/apache-spark-settings/spark-arch.png)

Spark-feladatok feldolgozó erőforrások, különösen a memória, használ, így a Spark konfigurációs értékek az munkavégző csomópont végrehajtóval gyakori.

Három fő paraméterek, amelyek gyakran módosulnak Spark konfigurációk javítása alkalmazáskövetelmények finomhangolásához `spark.executor.instances`, `spark.executor.cores`, és `spark.executor.memory`. Az Executor egy folyamat egy Spark-alkalmazás esetén. Egy végrehajtó a munkavégző csomóponton fut, és a feladatokat az alkalmazás feladata. Minden egyes fürt az alapértelmezett számát végrehajtóval, és az executor-méretek számítjuk alapján a munkavégző csomópontok és a munkavégző csomópont méretét. Ezek tárolt `spark-defaults.conf` a fő fürtcsomópontokon.  Ezeket az értékeket egy futó fürt kiválasztásával szerkesztheti a **egyéni spark-alapértelmezett** hivatkozásra az Ambari webes felület.  Miután változtatásokat, kér a felhasználói felületen **indítsa újra a** minden érintett szolgáltatást.

> [!NOTE]
> Ezen három konfiguráció paraméterei (az összes alkalmazáshoz a fürtön futó) a fürt szintjén konfigurálhatók és minden egyes alkalmazáshoz is megadott.

A Spark végrehajtóval által használt erőforrások adatait egy másik forrása a Spark-alkalmazás felhasználói felületén.  A Spark felhasználói felületén, válassza ki a **végrehajtóval** lapot, hogy megjelenítse a konfiguráció és a végrehajtóval által használt erőforrások összegzés és részletek nézetét.  Ezek a nézetek segítségével határozza meg, hogy a Spark végrehajtóval az egész fürt számára módosítsa az alapértékeket, vagy egy adott feladat-végrehajtások készlete.

![A Spark végrehajtóval](./media/apache-spark-settings/spark-executors.png)

Az Ambari REST API segítségével azt is megteheti, programozott módon a Spark- és HDInsight-fürt konfigurációs beállításainak ellenőrzése.  További információt a [Ambari API-referencia a Githubon](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Attól függően, a Spark számítási meghatározhatják, hogy egy nem alapértelmezett Spark konfigurációs biztosít több optimalizált Spark feladat-végrehajtások.  Végre kell hajtania a teljesítményteszt minden nem alapértelmezett fürt konfigurációjának ellenőrzése a mintául szolgáló számítási feladatok tesztelésével.  A következő általános paramétereket, akkor előfordulhat, hogy fontolja meg a következők:

* `--num-executors` Beállítja az végrehajtóval számát.
* `--executor-cores` minden egyes végrehajtó állítja be a magok számát. Azt javasoljuk, middle-sized végrehajtóval más folyamatokat is dolgozhat fel néhány elérhető memória részéhez.
* `--executor-memory` vezérlők végrehajtási terhelést a memóriát, hogy minden egyes végrehajtó YARN, és a memória méretét (halommemória mérete) kell.

Íme egy példa két feldolgozó csomóponttal különféle konfigurációs értékeket:

![Két csomópont-konfigurációk](./media/apache-spark-settings/executor-config.png)

Az alábbi lista kulcs Spark-végrehajtó memória paramétereket mutatja be.

* `spark.executor.memory` határozza meg a rendelkezésre álló memória teljes összege egy végrehajtó.
* `spark.storage.memoryFraction` (~ 60 % -os alapértelmezés szerint) a megőrzött rdd-k tárolására szolgáló rendelkezésre álló memória mennyiségét határozza meg.
* `spark.shuffle.memoryFraction` (~ 20 % -os alapértelmezés szerint) a shuffle számára lefoglalt memória mennyiségét határozza meg.
* `spark.storage.unrollFraction` és `spark.storage.safetyFraction` (teljes memória KB. 30 % -át összegzését) – ezek az értékek belsőleg Spark és nem módosítható.

YARN a Spark-csomópontokon a tárolók által használt memória maximális összegét szabályozza. Az alábbi ábrán látható, a csomópontonkénti kapcsolatokat és a Spark a YARN konfigurációs objektumok között.

![YARN Spark memóriájának kezelése](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter notebook-ben futó alkalmazás paramétereinek módosítása

A HDInsight Spark-fürtök számos összetevőnek alapértelmezés szerint tartalmazza. Egyes összetevők magában foglalja az alapértelmezett konfigurációs értékeket, amelyek szükség szerint felülbírálható.

* Spark Core - Spark mag, Spark SQL, Spark streamelési API-k, GraphX és MLlib
* Anaconda - a python Csomagkezelő manager
* Livy - az Apache Spark REST API használata egy HDInsight Spark-fürt távoli feladatok
* A Jupyter és Zeppelin-jegyzetfüzeteket – interaktív böngészőalapú felhasználói Felületet a Spark-fürthöz való interakcióhoz
* ODBC-illesztő – a HDInsight Spark-fürtök csatlakozik az üzleti intelligenciára épülő (BI) eszközökkel, például a Microsoft Power BI és a Tableau

Alkalmazások futtatása a Jupyter notebookot, használja a `%%configure` , hogy a konfigurációs parancs módosítja a belül magát a notebookot. Konfigurációs módosítások alkalmazható lesz a Spark-feladatok futtatása a jegyzetfüzet-példányból. A módosítások az alkalmazás futtatása az első kódcella előtt elején célszerű létrehozni. A módosított konfiguráció alkalmazása a Livy-munkamenethez, amikor létrejön.

> [!NOTE]
> Az alkalmazás egy későbbi időpontban a konfiguráció módosításához használja a `-f` (kényszerített) paramétert. Azonban az alkalmazás folyamatban lévő elvesznek.

Az alábbi kód bemutatja, hogyan módosítható konfigurációja egy Jupyter Notebookban futó alkalmazások.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Összegzés

Létezik néhány alapvető konfigurációs beállításokat, amelyek figyelése és módosíthatja annak érdekében, hogy a Spark-feladatok futtatásához egy előre jelezhető és nagy teljesítményű módon kell. Ezek a beállítások meghatározásához, hogy az adott számítási feladatokhoz ajánlott Spark fürt konfigurációját.  Emellett szüksége a végrehajtása hosszú ideig futó és/vagy erőforrás-igényes Spark feladat-végrehajtások figyelése.  A leggyakoribb kihívások center memóriaprobléma miatt (különösen helytelen méretű végrehajtóval) nem megfelelő konfigurációkat, hosszú ideig futó műveleteket és feladatokat, ami Descartes-féle műveletek körül.

## <a name="next-steps"></a>További lépések

* [Hadoop-összetevők és verziók HDInsight?](../hdinsight-component-versioning.md)
* [A HDInsight Spark-fürt erőforrásainak kezelése](apache-spark-resource-manager.md)
* [A Hadoop, Spark, Kafka és több HDInsight-fürtök beállítása](../hdinsight-hadoop-provision-linux-clusters.md)
* [Az Apache Spark-konfigurációja](https://spark.apache.org/docs/latest/configuration.html)
* [A Spark futó YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
