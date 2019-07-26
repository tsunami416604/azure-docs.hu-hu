---
title: A Spark beállításainak konfigurálása – Azure HDInsight
description: A Spark konfigurálása Azure HDInsight-fürthöz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: eb948aa2b683f426831e1b0d34b44f814eab6b9f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441918"
---
# <a name="configure-apache-spark-settings"></a>Az Apache Spark beállításainak konfigurálása

An méretű HDInsight Spark-fürt tartalmazza a [Apache Spark](https://spark.apache.org/) könyvtár telepítését.  Minden HDInsight-fürt tartalmaz alapértelmezett konfigurációs paramétereket az összes telepített szolgáltatásához, beleértve a Sparkot is.  A HDInsight Apache Hadoop-fürt felügyeletének egyik fő aspektusa a munkaterhelések figyelése, beleértve a Spark-feladatokat is, így biztosítva, hogy a feladatok kiszámítható módon futnak. A Spark-feladatok legjobb futtatásához vegye fontolóra a fizikai fürt konfigurációját a fürt logikai konfigurációjának optimalizálása során.

Az alapértelmezett HDInsight Apache Spark-fürt a következő csomópontokat tartalmazza: három [Apache ZooKeeper](https://zookeeper.apache.org/) csomópont, két fő csomópont és egy vagy több munkavégző csomópont:

![Spark HDInsight architektúra](./media/apache-spark-settings/spark-hdinsight-arch.png)

A HDInsight-fürt csomópontjai számára a virtuális gépek száma és a virtuálisgép-méretek is befolyásolhatják a Spark konfigurációját. A nem alapértelmezett HDInsight-konfigurációs értékek gyakran nem alapértelmezett Spark-konfigurációs értékeket igényelnek. HDInsight Spark-fürt létrehozásakor az egyes összetevőknél javasolt virtuálisgép-méretek láthatók. Jelenleg az Azure-hoz készült [memória-optimalizált Linux virtuális gépek mérete](../../virtual-machines/linux/sizes-memory.md) D12 v2 vagy újabb.

## <a name="apache-spark-versions"></a>Apache Spark verziók

Használja a fürt legjobb Spark-verzióját.  A HDInsight szolgáltatás a Spark és a HDInsight több verzióját is tartalmazza.  A Spark minden verziója tartalmaz egy alapértelmezett fürtkonfiguráció-készletet.  

Új fürt létrehozásakor több Spark-verzió közül választhat. A teljes lista megjelenítéséhez [HDInsight összetevők és verziók](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> A HDInsight szolgáltatás Apache Spark alapértelmezett verziója figyelmeztetés nélkül változhat. Ha a verziótól függ, a Microsoft azt javasolja, hogy a .NET SDK, a Azure PowerShell és a klasszikus Azure parancssori felület használatával hozzon létre egy adott verziót.

Apache Spark három rendszerkonfigurációs hellyel rendelkezik:

* A Spark-tulajdonságok vezérlik a legtöbb alkalmazás-paramétert, `SparkConf` és egy objektum vagy Java-rendszertulajdonság használatával állíthatók be.
* A környezeti változók az egyes csomópontokon futtatott `conf/spark-env.sh` parancsfájlokon keresztül, például az IP-cím beállítására használhatók.
* A naplózás a használatával `log4j.properties`konfigurálható.

A Spark egy adott verziójának kiválasztásakor a fürt tartalmazza az alapértelmezett konfigurációs beállításokat.  Az alapértelmezett Spark-konfigurációs értékeket egyéni Spark-konfigurációs fájl használatával módosíthatja.  Alább látható egy példa.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

A fenti példában szereplő példa több alapértelmezett értéket felülbírál az öt Spark konfigurációs paraméternél.  Ezek a tömörítési kodek, Apache Hadoop a MapReduce felosztásának minimális mérete és a parketta-blokkok mérete, valamint a Spar SQL-partíció és a megnyíló fájlméret alapértelmezett értékei is.  Ezek a konfigurációs változások úgy vannak kiválasztva, hogy a kapcsolódó adatok és feladatok (ebben a példában a genomikai adatok) bizonyos jellemzőkkel rendelkezzenek, ami jobban teljesíti ezeket az egyéni konfigurációs beállításokat.

---

## <a name="view-cluster-configuration-settings"></a>Fürtkonfiguráció beállításainak megtekintése

A fürtön a teljesítmény optimalizálása előtt ellenőrizze az aktuális HDInsight-fürt konfigurációs beállításait. Indítsa el a HDInsight-irányítópultot a Azure Portal a Spark-fürt ablaktábláján található **irányítópult** hivatkozásra kattintva. Jelentkezzen be a fürt rendszergazdai felhasználónevével és jelszavával.

Megjelenik az Apache Ambari webes KEZELŐFELÜLETe, amely a fő fürterőforrás-kihasználtság metrikáinak irányítópult-nézetét jeleníti meg.  A Ambari-irányítópulton látható a Apache Spark-konfiguráció és a telepített egyéb szolgáltatások. Az irányítópult tartalmaz egy **konfigurációs előzmények** lapot, ahol megtekintheti az összes telepített szolgáltatás konfigurációs adatait, beleértve a Sparkot is.

A Apache Spark konfigurációs értékeinek megtekintéséhez válassza a konfiguráció **előzményei**lehetőséget, majd válassza a **Spark2**lehetőséget.  Válassza a **konfigurációk** fület, majd a szolgáltatás `Spark` listában válassza `Spark2`a (vagy a verziótól függően) hivatkozást.  Ekkor megjelenik a fürthöz tartozó konfigurációs értékek listája:

![Spark-konfigurációk](./media/apache-spark-settings/spark-config.png)

Az egyes Spark-konfigurációs értékek megtekintéséhez és módosításához válassza a "Spark" szót a hivatkozás címében.  A Spark-konfigurációk a következő kategóriákban tartalmazzák az egyéni és a speciális konfigurációs értékeket is:

* Egyéni Spark2 – alapértékek
* Egyéni Spark2 – mérőszámok – tulajdonságok
* Speciális Spark2 – alapértékek
* Speciális Spark2 – env
* Speciális spark2 – kaptár-site – felülbírálás

Ha nem alapértelmezett konfigurációs értékeket hoz létre, akkor a konfigurációs frissítések előzményeit is megtekintheti.  Ez a konfigurációs előzmények segíthetnek megtekinteni, hogy melyik nem alapértelmezett konfiguráció optimális teljesítményű.

> [!NOTE]  
> Ha szeretné megtekinteni, de nem módosítja a Spark-fürt általános konfigurációs beállításait, válassza a **környezet** fület a legfelső szintű **Spark-feladatok felhasználói** felületén.

## <a name="configuring-spark-executors"></a>Spark-végrehajtók konfigurálása

A következő ábrán a legfontosabb Spark-objektumok láthatók: az illesztőprogram-program és a hozzá tartozó Spark-környezet, valamint a Fürtfelügyelő és az *n* feldolgozó csomópontok.  Minden feldolgozói csomópont tartalmaz egy végrehajtót, egy gyorsítótárat és egy *n* Task instances-példányt.

![Fürtözött objektumok](./media/apache-spark-settings/spark-arch.png)

A Spark-feladatok a feldolgozói erőforrásokat, különösen a memóriát használják, ezért gyakori a Spark-konfigurációs értékek beállítása a feldolgozó csomópont-végrehajtók számára.

Három fő paraméter, amelyet gyakran a Spark-konfigurációk hangolására módosítanak az alkalmazási `spark.executor.cores`követelmények `spark.executor.instances`javítása `spark.executor.memory`érdekében, és. A végrehajtó egy Spark-alkalmazáshoz indított folyamat. Egy végrehajtó fut a munkavégző csomóponton, és felelős az alkalmazás feladataiért. Minden egyes fürt esetében a végrehajtók alapértelmezett száma és a végrehajtó mérete a munkavégző csomópontok száma és a feldolgozó csomópont mérete alapján számítható ki. Ezeket a rendszer a `spark-defaults.conf` fürt fő csomópontjain tárolja.  Ezeket az értékeket egy futó fürtben is szerkesztheti, ha kiválasztja az **Egyéni Spark-** alapértékek hivatkozást a Ambari webes felhasználói felületén.  A módosítások elvégzése után a felhasználói felület az összes érintett  szolgáltatás újraindítását kéri.

> [!NOTE]  
> Ez a három konfigurációs paraméter konfigurálható a fürt szintjén (a fürtön futó összes alkalmazás esetében), valamint az egyes alkalmazásokhoz is.

A Spark-végrehajtók által használt erőforrásokkal kapcsolatos további információforrás a Spark-alkalmazás felhasználói felülete.  A Spark felhasználói felületén válassza a **végrehajtók** fület a konfiguráció és a végrehajtók által felhasznált erőforrások összesített és részletes nézeteinek megjelenítéséhez.  Ezek a nézetek segítenek eldönteni, hogy a teljes fürthöz tartozó Spark-végrehajtók alapértelmezett értékeit vagy a feladatok egy adott készletét kell-e módosítani.

![Spark-végrehajtók](./media/apache-spark-settings/spark-executors.png)

Azt is megteheti, hogy a Ambari REST API használatával programozottan ellenőrizheti a HDInsight és a Spark-fürt konfigurációs beállításait.  További információ a GitHubon elérhető [Apache AMBARI API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)-referenciában található.

A Spark számítási feladattól függően megállapíthatja, hogy egy nem alapértelmezett Spark-konfiguráció optimalizálja a Spark-feladatok optimalizált végrehajtását.  A nem alapértelmezett fürtkonfiguráció érvényesítéséhez teljesítményteszt-tesztelést kell végezni a minta számítási feladatokkal.  A módosítani kívánt általános paraméterek némelyike a következőkre használható:

* `--num-executors`beállítja a végrehajtók számát.
* `--executor-cores`beállítja a magok számát az egyes végrehajtók számára. Javasoljuk, hogy a közepes méretű végrehajtók használatával más folyamatok is használják a rendelkezésre álló memória egy részét.
* `--executor-memory`az egyes végrehajtók memória-méretének (halom méretének) szabályozása [Apache HADOOP fonalon](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), és némi memóriát kell hagyni a végrehajtáshoz.

Íme egy példa két feldolgozói csomópontra, különböző konfigurációs értékekkel:

![Két csomópontos konfiguráció](./media/apache-spark-settings/executor-config.png)

Az alábbi lista a fő Spark-végrehajtó memória paramétereit mutatja be.

* `spark.executor.memory`meghatározza a végrehajtó számára elérhető memória teljes mennyiségét.
* `spark.storage.memoryFraction`(alapértelmezett ~ 60%) a megőrzött RDD tárolására rendelkezésre álló memória mennyiségét határozza meg.
* `spark.shuffle.memoryFraction`(alapértelmezett ~ 20%) meghatározza a shuffle számára fenntartott memória mennyiségét.
* `spark.storage.unrollFraction`és `spark.storage.safetyFraction` (összesen ~ 30%-a teljes memória) – ezeket az értékeket a Spark belsőleg használja, és nem módosítható.

A fonal az egyes Spark-csomópontokon lévő tárolók által használt memória maximális számát szabályozza. Az alábbi ábrán a szálak konfigurációs objektumai és a Spark-objektumok közötti csomópont-kapcsolatok láthatók.

![A fonal Spark memóriájának kezelése](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter-jegyzetfüzetben futó alkalmazás paramétereinek módosítása

A HDInsight-alapú Spark-fürtök több összetevőt is tartalmaznak alapértelmezés szerint. Ezen összetevők mindegyike alapértelmezett konfigurációs értékeket tartalmaz, amelyeket szükség esetén felül lehet bírálni.

* Spark Core – Spark Core, Spark SQL, Spark streaming API-k, GraphX és Apache Spark MLlib.
* Anaconda – egy Python csomagkezelő.
* [Apache Livy](https://livy.incubator.apache.org/) – a Apache Spark REST API, amely távoli feladatok küldésére szolgál egy HDInsight Spark-fürtön.
* [Jupyter](https://jupyter.org/) és [Apache Zeppelin](https://zeppelin.apache.org/) jegyzetfüzetek – interaktív böngészőalapú felhasználói felület a Spark-fürttel való interakcióhoz.
* ODBC-illesztő – a Spark-fürtök összekapcsolása a HDInsight és az üzleti intelligencia (BI) eszközeivel, például a Microsoft Power BI és a tabló használatával.

A Jupyter notebookon futó alkalmazások esetében használja a `%%configure` parancsot a konfiguráció módosítására a jegyzetfüzetből. Ezek a konfigurációs változások a notebook-példányról futtatott Spark-feladatokra lesznek alkalmazva. Ezeket a módosításokat az alkalmazás elején kell megtennie az első kód cellájának futtatása előtt. A rendszer a módosított konfigurációt alkalmazza a Livy-munkamenetre a létrehozásakor.

> [!NOTE]  
> Ha az alkalmazás egy későbbi szakaszában szeretné módosítani a konfigurációt, használja a `-f` (Force) paramétert. Azonban az alkalmazás minden folyamata el fog veszni.

Az alábbi kód azt mutatja be, hogyan lehet módosítani egy Jupyter-jegyzetfüzetben futó alkalmazás konfigurációját.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Összegzés

Számos alapvető konfigurációs beállítással kell figyelnie és módosítania, hogy a Spark-feladatok kiszámítható és elvégezhető módon fussanak. Ezek a beállítások segítenek meghatározni a legjobb Spark-fürt konfigurációját az adott számítási feladatokhoz.  Emellett figyelnie kell a hosszan futó és/vagy az erőforrás-igényes Spark-feladatok végrehajtását.  A nem megfelelő konfigurációk (különösen a nem megfelelő méretű végrehajtók), a hosszan futó műveletek és a feladatok, amelyek a Descartes-as műveletek eredményeként a leggyakoribb kihívások körét a memória terhelése miatt.

## <a name="next-steps"></a>További lépések

* [A HDInsight-ben elérhető összetevők és verziók Apache Hadoop?](../hdinsight-component-versioning.md)
* [Apache Spark-fürt erőforrásainak kezelése a HDInsight-ben](apache-spark-resource-manager.md)
* [Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark konfiguráció](https://spark.apache.org/docs/latest/configuration.html)
* [Apache Spark futtatása Apache Hadoop-SZÁLon](https://spark.apache.org/docs/latest/running-on-yarn.html)
