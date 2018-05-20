---
title: A Spark-beállítások – Azure HDInsight konfigurálása |} Microsoft Docs
description: Hogyan konfigurálható a Spark HDInsight-fürthöz.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: maxluk
ms.openlocfilehash: db61cc81f51772aa98c034f1bfdf51777cfd68e7
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="configure-spark-settings"></a>A Spark beállításainak konfigurálása

Egy HDInsight Spark-fürt magában foglalja az Apache Spark-könyvtár egy telepítés.  Minden HDInsight-fürt a telepített szolgáltatásokkal, beleértve a Spark on az alapértelmezett konfigurációs paramétereket tartalmazza.  Egyik fontos szempontja a HDInsight Hadoop-fürt kezelése munkaterhelés, beleértve a Spark feladatok, győződjön meg arról, hogy a feladat egy előre jelezhető módon fut figyel. Feladat legjobb futtatására a Spark, fontolja meg a fizikai fürtkonfiguráció logikai fürtkonfiguráció optimalizálása meghatározásakor.

Az alapértelmezett HDInsight Apache Spark-fürt a következő csomópontokat tartalmazza: három ZooKeeper csomópontok, két átjárócsomópontokkal és egy vagy több munkavégző csomópontokhoz:

![A Spark HDInsight-architektúra](./media/apache-spark-settings/spark-hdinsight-arch.png)

A virtuális gépek számát, és a Virtuálisgép-méretek, a HDInsight-fürt csomópontjaihoz is hatással lehet a Spark-konfigurációt. Nem alapértelmezett HDInsight konfigurációs értékeket gyakran nem alapértelmezett Spark konfigurációs értékeket igényelnek. HDInsight Spark-fürtöt hoz létre, amikor Ön javasolt Virtuálisgép-méretek jelennek meg az egyes összetevők. Jelenleg a [memóriaoptimalizált Linux Virtuálisgép-méretek](../../virtual-machines/linux/sizes-memory.md) Azure rendszer D12 v2 vagy nagyobb.

## <a name="spark-versions"></a>A Spark-verziók

A legjobb Spark verzióját használja a fürt számára.  A HDInsight-szolgáltatás tartalmazza a Spark- és magát a HDInsight különböző verzióiban.  Spark verziói alapértelmezett fürtbeállítások készletét tartalmazza.  

Amikor létrehoz egy új fürt, az alábbiakban az aktuális Spark verziók közül választhat:

![A Spark-verziók](./media/apache-spark-settings/spark-version.png)

Spark 2.x sokkal nagyobb, mint a Spark 1.x futtatható. Spark 2.x teljesítményoptimalizálás, mint az volfrám, katalizátor optimalizálás, számos rendelkezik.  

> [!NOTE]
> Az alapértelmezett verzió az Apache Spark on HDInsight szolgáltatási értesítés nélkül változhatnak. Ha verzió függőségei, a Microsoft azt javasolja, hogy megadja, hogy adott verziót .NET SDK vagy az Azure PowerShell és az Azure CLI-fürtök létrehozásakor.

Apache Spark rendelkezik rendszer-konfiguráció három helyét:

* Spark tulajdonságok szabályozzák a legtöbb alkalmazás paraméterei, és használatával állítható be egy `SparkConf` objektum, vagy Java rendszer tulajdonságai párbeszédpanelen.
* Környezeti változók segítségével számítógépenkénti beállításokat, például az IP-cím segítségével állíthatók be a `conf/spark-env.sh` parancsfájl minden egyes csomóponton.
* Naplózási konfigurálható `log4j.properties`.

Amikor kiválaszt egy Spark adott verzióját, a fürt alapértelmezett konfigurációs beállításait tartalmazza.  Spark egyéni konfigurációs fájl használatával módosíthatja az alapértelmezett Spark konfigurációs értékeket.  Az alábbiakban egy példa látható.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

A fenti példában felülbírálja öt Spark konfigurációs paraméterek több alapértelmezett értékeit.  Ezek azok a tömörítési kodek, Hadoop-MapReduce ossza fel a minimális méret és parquet blokkméretek, valamint a Spar SQL partíció, és nyitott fájlok mérete alapértelmezett értékeket.  Ezek a konfigurációs módosítások vannak választva, mert a társított adatokat, a feladatok (a példában genomikus adatokat), hogy jobban ezekkel a beállításokkal egyéni konfigurációs hajt végre bizonyos jellemzőkkel.

---

## <a name="view-cluster-configuration-settings"></a>Fürt konfigurációs beállítások megjelenítése

Ellenőrizze a jelenlegi HDInsight-fürt konfigurációs beállítások, mielőtt elvégezné a teljesítmény optimalizálása a fürtön. Az Azure-portálon a HDInsight-irányítópult kattintva indíthatja el a **irányítópult** a Spark-fürt ablaktábla hivatkozásra kattintva. Jelentkezzen be a fürt rendszergazdája felhasználónevet és jelszót.

Az Ambari webes felhasználói felületén jelenik meg, a kulcs fürt erőforrás kihasználtsága mérőszámok irányítópult-nézetet.  Az Ambari irányítópulton látható az Apache Spark konfigurációs és egyéb szolgáltatások telepítése. Az irányítópult tartalmaz egy **konfigurációs előzmények** lap, ahol megtekintheti a telepített szolgáltatásokkal, beleértve a Spark konfigurációs adatait.

Konfigurációs értékeket az Apache Spark, jelölje ki a **konfigurációs előzmények**, majd jelölje be **Spark2**.  Válassza ki a **Configs** lapra, majd válassza ki a `Spark` (vagy `Spark2`verziójától függően, hogy) a lista hivatkozásra.  A fürt számára jelenik meg a konfigurációs értékek listáját:

![A Spark-konfigurációk](./media/apache-spark-settings/spark-config.png)

Lásd: Spark-konfiguráció egyedi értékek módosítása, jelölje be minden hivatkozás, amelyen a "külső" szó a hivatkozás címben.  Spark konfigurációi ezekben a kategóriákban található mindkét egyéni és speciális konfigurációs értékeket tartalmaznak:

* Egyéni Spark2-alapértelmezései
* Egyéni Spark2-metrikák-tulajdonságok
* Speciális Spark2-alapértelmezései
* Speciális Spark2-env
* Speciális spark2-hive-webhely – felülbírálás

Ha a konfigurációs értékek, nem alapértelmezett készletét hoz létre, akkor azt is láthatja, a konfigurációs módosítások előzményeit.  A konfigurációs előzmények nyilvántartása, mely nem alapértelmezett konfiguráció tartozik-e optimális teljesítményének hasznos lehet.

> [!NOTE]
> Tekintse meg, de nem módosíthatja, közös Spark fürt konfigurációs beállításokat, válassza ki a **környezet** lapját, amelyen a legfelső szintű **Spark feladat felhasználói felület** felületet.

## <a name="configuring-spark-executors"></a>Spark végrehajtója konfigurálása

Az alábbi ábrán látható objektumok Spark: az illesztőprogram program és a kapcsolódó Spark-környezet és a kezelő és a *n* munkavégző csomópontokhoz.  Egyes feldolgozó csomópontok tartalmaz egy művelettípus végrehajtója, a gyorsítótár és *n* példányok feladat.

![Fürt objektumok](./media/apache-spark-settings/spark-arch.png)

Spark feladatok feldolgozó erőforrások, különösen a memória, használ, így a gyakori, úgy, hogy a munkavégző csomópont végrehajtója Spark konfigurációs értékeket.

Három fő paraméterek, amelyek gyakran módosulnak az alkalmazás javítására Spark konfigurációk finomhangolásához `spark.executor.instances`, `spark.executor.cores`, és `spark.executor.memory`. Egy művelettípus végrehajtója az a folyamat egy Spark-alkalmazáshoz elindítva. Az Executor a munkavégző csomópont fut, és a feladatokat az alkalmazás felelős. Az egyes fürtökön végrehajtója, és a végrehajtó, alapértelmezett száma alapján van kiszámítva a feldolgozó csomópontok és a munkavégző csomópont méretének száma. Ezek tárolják `spark-defaults.conf` központi fürtcsomópontokon.  Ezek az értékek egy működő fürthöz szerkesztheti kiválasztásával a **egyéni spark-alapértelmezett** az Ambari webes felhasználói felület hivatkozásra.  Módosítása után kéri, hogy a felhasználói felület által **indítsa újra a** minden érintett szolgáltatást.

> [!NOTE]
> Ezen három konfiguráció paraméterek szintjén a fürt (a fürtön futó összes alkalmazást) konfigurálhatók, és minden egyes alkalmazáshoz is megadott.

A Spark végrehajtója által használt erőforrások adatait egy másik forrása a Spark-alkalmazás felhasználói felületén.  A Spark felhasználói felületén válassza ki a **végrehajtója** lapon, a konfigurációs és a végrehajtója által felhasznált erőforrások összegzés és részletek nézetek megjelenítésére.  Ezek a nézetek segítségével eldöntheti, hogy a teljes fürt Spark végrehajtója alapértelmezett értékeinek módosítása, vagy egy adott feladat végrehajtások készlete.

![Spark végrehajtója](./media/apache-spark-settings/spark-executors.png)

Az Ambari REST API használatával azt is megteheti, programozott módon a HDInsight- és Spark fürt konfigurációs beállításainak ellenőrzése.  További információt a [Ambari API-hoz a Githubon](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Attól függően, hogy a külső alkalmazások és szolgáltatások dönthet, hogy egy nem alapértelmezett Spark konfiguráció biztosít több optimalizált Spark feladat végrehajtások.  Minden nem alapértelmezett fürtkonfigurációk érvényesítéséhez minta munkaterhelések tesztelték teljesítményteszt végre kell hajtania.  A következő általános paramétereket, akkor fontolja meg értékének a következők:

* `--num-executors` Megadja az időtartamot végrehajtója.
* `--executor-cores` magok száma minden végrehajtó beállítása. Azt javasoljuk, middle-sized végrehajtója, mint más folyamatokkal is felhasználhatják a rendelkezésre álló memória egy részét.
* `--executor-memory` vezérlők minden végrehajtó YARN, és a memória méretét (halommemória mérete) kell, hogy a végrehajtási terhelés memóriát.

Itt látható egy példa két munkavégző csomópontokhoz különböző konfigurációs értékekkel:

![Két csomópont-konfiguráció](./media/apache-spark-settings/executor-config.png)

Az alábbi listában láthatók kulcs Spark végrehajtó memória paraméterek.

* `spark.executor.memory` határozza meg a rendelkezésre álló memória teljes mennyiségétől egy művelettípus végrehajtója.
* `spark.storage.memoryFraction` (alapértelmezett ~ 60 %) megőrzött RDDs rendelkezésre álló memória mennyisége határozza meg.
* `spark.shuffle.memoryFraction` (alapértelmezett ~ 20 % -át) sorrendű számára fenntartott memória mennyisége határozza meg.
* `spark.storage.unrollFraction` és `spark.storage.safetyFraction` (összesítés KB. 30 %-a teljes memória) – ezek az értékek belsőleg Spark és nem módosítható.

YARN a Spark-csomópontokon a tárolók által használt memória maximális összegét szabályozza. Az alábbi ábrán látható, a csomópont kapcsolatok Spark és YARN konfigurációs objektumok között.

![YARN Spark memóriakezelés](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter notebook alkalmazás paramétereinek módosítása

A HDInsight Spark-fürtjei összetevők számos alapértelmezés szerint tartalmazza. Ezeket az összetevőket tartalmazza, alapértelmezett konfigurációs értékeket, amelyek szükség szerint felülbírálható.

* Spark mag - Spark mag, Spark SQL, Spark streamelési API-k, GraphX és MLlib
* Anaconda - python csomagot kezelő
* Livy - Apache Spark REST API használatával egy HDInsight Spark-fürt távoli feladatok elküldéséhez
* Jupyter és Zeppelin notebookok - interaktív böngészőalapú felhasználói felülete a Spark-fürthöz való interakció
* Az ODBC illesztőprogram - hdinsight Spark-fürtök kapcsolódik az üzleti intelligenciával eszközök, például a Microsoft Power BI és a Tableau

Az alkalmazások a Jupyter notebook, használja a `%%configure` belül a notebook magát a változik konfigurációs parancsot. Ezek a konfigurációs módosítások a notebook példányból futtatása Spark feladatok lépnek érvénybe. Az alkalmazást, mielőtt újra lefuttatja az első kódcella elején meg kell győződnie változások. A módosított konfiguráció alkalmazása a Livy munkamenethez, ha végrehajtásakor létrejön.

> [!NOTE]
> Az alkalmazás egy későbbi időpontban a konfiguráció módosításához használja a `-f` (kényszerített) paraméter. Azonban az összes folyamatban van az alkalmazás elvesznek.

Az alábbi kód bemutatja, hogyan Jupyter notebook egy alkalmazáskészlet konfigurációját módosítja.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Összegzés

Számos core konfigurációs beállítások és annak érdekében, a Spark feladatok futnak, kiszámítható és performant módon kiigazítása szükséges. Ezek a beállítások meghatározásához, hogy az adott munkaterhelésekhez ajánlott Spark fürtkonfiguráció.  Is hosszan futó és/vagy az erőforrás-igényes Spark feladat végrehajtások végrehajtása figyelni kell.  A leggyakoribb kihívást center körül memóriaprobléma miatt nem megfelelő konfigurációk (különösen helytelen méretű végrehajtója), hosszú futású műveleteket és feladatokat, melyekhez derékszögű műveleteket eredményez.

## <a name="next-steps"></a>További lépések

* [Hadoop-összetevők és a hdinsight eszközzel verziók?](../hdinsight-component-versioning.md)
* [A HDInsight Spark-fürt erőforrásainak kezelése](apache-spark-resource-manager.md)
* [Hdinsight Hadoop, Spark, Kafka és több fürt beállítása](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark-konfiguráció](https://spark.apache.org/docs/latest/configuration.html)
* [Spark futó YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
