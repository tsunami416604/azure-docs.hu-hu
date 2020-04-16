---
title: Spark-beállítások konfigurálása – Azure HDInsight
description: Az Apache Spark beállításainak megtekintése és konfigurálása Egy Azure HDInsight-fürthöz
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: e13390067f8767e8c07b9c189264444e6d999a7a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411301"
---
# <a name="configure-apache-spark-settings"></a>Az Apache Spark beállításainak konfigurálása

A HDInsight Spark-fürt az [Apache Spark-függvénytár](https://spark.apache.org/) telepítését tartalmazza.  Minden HDInsight-fürt tartalmazza az összes telepített szolgáltatás alapértelmezett konfigurációs paramétereit, beleértve a Sparkot is.  A HDInsight Apache Hadoop-fürt kezelésének egyik kulcseleme a munkaterhelés figyelése, beleértve a Spark-feladatokat is. A Spark-feladatok legjobb futtatásához vegye figyelembe a fizikai fürt konfigurációját a fürt logikai konfigurációjának meghatározásakor.

Az alapértelmezett HDInsight Apache Spark-fürt a következő csomópontokat tartalmazza: három [Apache ZooKeeper](https://zookeeper.apache.org/) csomópont, két főcsomópont és egy vagy több munkavégző csomópont:

![Spark HDInsight architektúra](./media/apache-spark-settings/spark-hdinsight-arch.png)

A HDInsight-fürt csomópontjainak virtuális gépek és virtuálisgép-méretek száma hatással lehet a Spark-konfigurációra. A nem alapértelmezett HDInsight konfigurációs értékek gyakran nem alapértelmezett Spark-konfigurációs értékeket igényelnek. Amikor létrehoz egy HDInsight Spark-fürt, megjelenik a javasolt virtuális gép méreteaz egyes összetevők. Jelenleg a [memória-optimalizált Linux virtuális gép méretek](../../virtual-machines/linux/sizes-memory.md) az Azure-ban a D12 v2 vagy nagyobb.

## <a name="apache-spark-versions"></a>Apache Spark verziók

Használja a legjobb Spark-verziót a fürthöz.  A HDInsight szolgáltatás a Spark és a HDInsight több verzióját is tartalmazza.  A Spark minden verziója tartalmaz egy sor alapértelmezett fürtbeállításokat.  

Amikor új fürtöt hoz létre, több Spark-verzió közül választhat. A teljes lista megtekintéséhez [HDInsight-összetevők és -verziók](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).

> [!NOTE]  
> Az Apache Spark alapértelmezett verziója a HDInsight szolgáltatásban értesítés nélkül változhat. Ha verziófüggő, a Microsoft azt javasolja, hogy adja meg az adott verziót, amikor fürtöket hoz létre a .NET SDK, az Azure PowerShell és az Azure Classic CLI használatával.

Az Apache Spark három rendszerkonfigurációs hellyel rendelkezik:

* A Spark-tulajdonságok vezérelhetik a legtöbb `SparkConf` alkalmazásparamétert, és beállíthatók egy objektum vagy a Java rendszer tulajdonságai segítségével.
* A környezeti változók segítségével beállíthatja a számítógépenkénti beállításokat, `conf/spark-env.sh` például az IP-címet, az egyes csomópontok parancsfájlján keresztül.
* A naplózás konfigurálható a . `log4j.properties`

Amikor a Spark egy adott verzióját választja ki, a fürt tartalmazza az alapértelmezett konfigurációs beállításokat.  Módosíthatja az alapértelmezett Spark konfigurációs értékeket egy egyéni Spark konfigurációs fájl használatával.  Erre mutat példát az alábbi ábra.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

A fenti példa felülír több alapértelmezett értéket öt Spark konfigurációs paraméterhez.  Ezek az értékek a tömörítési kodek, Apache Hadoop MapReduce osztott minimális mérete és parketta blokk mérete. Emellett a Spar SQL partíció és a megnyitott fájlméretek alapértelmezett értékei is.  Ezek a konfigurációs módosítások azért kerülnek kiválasztásra, mert a kapcsolódó adatok és feladatok (ebben a példában a genomikus adatokban) különleges jellemzőkkel rendelkeznek. Ezek a jellemzők jobban fognak kihasználni ezeket az egyéni konfigurációs beállításokat.

---

## <a name="view-cluster-configuration-settings"></a>Fürtkonfigurációs beállítások megtekintése

Ellenőrizze az aktuális HDInsight-fürt konfigurációs beállításait, mielőtt teljesítményoptimalizálást végezne a fürtön. Indítsa el a HDInsight-irányítópultot az Azure Portalról a Spark-fürt ablaktábláján található **Irányítópult** hivatkozásra kattintva. Jelentkezzen be a fürtrendszergazdának a felhasználónevével és jelszavával.

Megjelenik az Apache Ambari web felhasználói felülete, amely a fürt erőforrás-használati mutatóinak irányítópultját adja meg.  Az Ambari irányítópulton megjelenik az Apache Spark-konfiguráció és más telepített szolgáltatások. Az irányítópult tartalmaz egy **Konfigurációs előzmények** lapot, ahol megtekintheti a telepített szolgáltatások, köztük a Spark adatait.

Az Apache Spark konfigurációs értékeinek megtekintéséhez válassza a **Konfigurációs előzmények**lehetőséget, majd a **Spark2**lehetőséget.  Válassza a **Configs** fület, majd a szolgáltatáslistában a `Spark` (vagy `Spark2`a verziótól függően) hivatkozást.  Megjelenik a fürt konfigurációs értékeinek listája:

![Spark-konfigurációk](./media/apache-spark-settings/spark-configurations.png)

Az egyes Spark-konfigurációs értékek megtekintéséhez és módosításához válassza ki a "szikra" a címben található bármely kapcsolatot.  A Spark konfigurációi az alábbi kategóriákban egyéni és speciális konfigurációs értékeket is tartalmaznak:

* Egyéni Spark2-alapértelmezések
* Egyéni Spark2-metrikák-tulajdonságok
* Speciális Spark2-alapértelmezett értékek
* Fejlett Spark2-env
* Speciális szikra2-hive-site-felülírás

Ha nem alapértelmezett konfigurációs értékeket hoz létre, a frissítési előzmények láthatók lesznek.  Ez a konfigurációs előzmények hasznosak lehetnek annak megtekintéséhez, hogy melyik nem alapértelmezett konfiguráció rendelkezik optimális teljesítménnyel.

> [!NOTE]  
> A Spark-fürt általános konfigurációs beállításainak megtekintéséhez, de nem módosításához válassza a **Környezet** lapot a legfelső szintű **Spark-feladat felhasználói felületén.**

## <a name="configuring-spark-executors"></a>Spark végrehajtóinak konfigurálása

A következő ábrán a legfontosabb Spark-objektumok láthatók: az illesztőprogram-program és a hozzá tartozó Spark-környezet, valamint a fürtkezelő és az *n* munkavégző csomópontok.  Minden munkavégző csomópont tartalmaz egy Végrehajtó, egy gyorsítótár és *n* feladatpéldányokat.

![Fürtobjektumok](./media/apache-spark-settings/hdi-spark-architecture.png)

A Spark-feladatok munkavégző erőforrásokat, különösen a memóriát használnak, ezért gyakori a Spark konfigurációs értékeinek módosítása a feldolgozói csomópont végrehajtóihoz.

A spark-konfigurációk nak az alkalmazáskövetelmények javítása érdekében `spark.executor.instances` `spark.executor.cores`gyakran `spark.executor.memory`beállított három fő paraméter a , és a. A Végrehajtó egy Spark-alkalmazáshoz indított folyamat. A végrehajtó fut a munkavégző csomóponton, és felelős az alkalmazás feladataiért. A munkavégző csomópontok és a feldolgozói csomópont mérete határozza meg a végrehajtók számát és a végrehajtó méretét. Ezek az értékek `spark-defaults.conf` a fürtfőcsomópontokon tárolódnak.  Ezeket az értékeket egy futó fürtben szerkesztheti, ha az Ambari webes felhasználói felületén az **Egyéni szikraalapértelmezések** lehetőséget választja.  A módosítások elvégzése után a felhasználói felület felszólítja az összes érintett szolgáltatás **újraindítására.**

> [!NOTE]  
> Ez a három konfigurációs paraméter konfigurálható a fürt szintjén (a fürtön futó összes alkalmazáshoz), és minden egyes alkalmazáshoz meg adható.

A Spark-végrehajtók által használt erőforrásokkal kapcsolatos információk egy másik forrása a Spark-alkalmazás felhasználói felülete.  A felhasználói felületen a végrehajtók a konfiguráció és a felhasznált erőforrások Összegzés és Részlet nézeteit **jelenítik** meg.  Határozza meg, hogy a végrehajtók értékeit a teljes fürtre vagy a feladatvégrehajtások adott készletére módosítja-e.

![Spark végrehajtók](./media/apache-spark-settings/apache-spark-executors.png)

Vagy használhatja az Ambari REST API-t a HDInsight és a Spark fürt konfigurációs beállításainak programozott ellenőrzéséhez.  További információ az [Apache Ambari API-hivatkozással érhető el a GitHubon.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

A Spark számítási feladattól függően előfordulhat, hogy egy nem alapértelmezett Spark-konfiguráció optimálisabb Spark feladat-végrehajtásokat biztosít.  A nem alapértelmezett fürtkonfigurációk érvényesítéséhez végezze el a számítási feladatok mintaszámítási feladatokkal végzett teljesítményteszteket.  Néhány gyakori paraméter, amelyeket érdemes lehet módosítani:

|Paraméter |Leírás|
|---|---|
|--num-végrehajtók|Beállítja a végrehajtók számát.|
|--végrehajtó-magok|Beállítja az egyes végrehajtók magjainak számát. Közepes méretű végrehajtók használatát javasoljuk, mivel más folyamatok is a rendelkezésre álló memória egy részét használják fel.|
|--végrehajtó-memória|Az [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)egyes végrehajtóinak memóriaméretét (halomméretét) szabályozza, és a végrehajtási terheléshez memóriát kell hagynia.|

Íme egy példa két különböző konfigurációs értékkel rendelkező munkavégző csomópontra:

![Két csomópontkonfiguráció](./media/apache-spark-settings/executor-configuration.png)

Az alábbi lista a Spark végrehajtója legfontosabb memóriaparamétereit tartalmazza.

|Paraméter |Leírás|
|---|---|
|spark.executor.memory|A végrehajtó számára rendelkezésre álló memória teljes mennyiségét határozza meg.|
|spark.storage.memoryFraction|(alapértelmezett ~60%) meghatározza a megőrzött RDD-k tárolására rendelkezésre álló memória mennyiségét.|
|spark.shuffle.memoryFraction|(alapértelmezett ~20%) meghatározza a véletlen sorrendű memória mennyiségét.|
|spark.storage.unrollFraction és spark.storage.safetyFraction|(összesen ~30%-a teljes memória) - ezeket az értékeket a Spark belsőleg használja, és nem kell módosítani.|

A YARN az egyes Spark-csomópontok tárolói által használt memória maximális összegét szabályozza. Az alábbi ábra a YARN konfigurációs objektumok és a Spark-objektumok közötti csomópontonkénti kapcsolatokat mutatja be.

![YARN Spark memóriakezelés](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter-jegyzetfüzetben futó alkalmazás paramétereinek módosítása

A HDInsight Spark-fürtjei alapértelmezés szerint számos összetevőt tartalmaznak. Ezen összetevők mindegyike tartalmazza az alapértelmezett konfigurációs értékeket, amelyek szükség szerint felülbírálhatók.

|Összetevő |Leírás|
|---|---|
|Spark Core|Spark Core, Spark SQL, Spark streamelési API-k, GraphX és Apache Spark MLlib.|
|Anaconda|Egy python csomagkezelő.|
|[Apache Livy](https://livy.incubator.apache.org/)|Az Apache Spark REST API, amely távoli feladatok at küld egy HDInsight Spark-fürtbe.|
|[Jupyter](https://jupyter.org/) és [Apache Zeppelin](https://zeppelin.apache.org/) notebookok|Interaktív böngészőalapú felhasználói felület a Spark-fürttel való interakcióhoz.|
|ODBC-illesztő|A HDInsight Spark-fürtjeit olyan üzletiintelligencia-eszközökhöz kapcsolja, mint a Microsoft Power BI és a Tableau.|

A Jupyter-jegyzetfüzetben futó alkalmazások `%%configure` esetén a paranccsal a jegyzetfüzeten belül módosíthatja a konfigurációt. Ezek a konfigurációs módosítások a Spark-feladatok a notebook példányból futnak. Az alkalmazás elején hajtson végre ilyen módosításokat, mielőtt futtatja az első kódcellát. A módosított konfiguráció a Livy-munkamenetre lesz alkalmazva, amikor létrejön.

> [!NOTE]  
> A konfiguráció módosításához egy későbbi szakaszában `-f` az alkalmazás, használja a (force) paraméter. Az alkalmazás minden előrehaladása azonban elvész.

Az alábbi kód bemutatja, hogyan módosíthatja a Jupyter-jegyzetfüzetben futó alkalmazások konfigurációját.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Összegzés

Figyelje az alapvető konfigurációs beállításokat, hogy a Spark-feladatok kiszámítható és hatékony módon fussanak. Ezek a beállítások segítenek meghatározni a legjobb Spark-fürt konfigurációaz adott számítási feladatokhoz.  Emellett figyelnie kell a hosszú ideig futó és vagy erőforrás-igényes Spark-feladat-végrehajtások végrehajtását.  A leggyakoribb kihívások középpontjában a nem megfelelő konfigurációkból, például a helytelen méretű végrehajtókból eredő memórianyomás áll. Is, hosszú ideig futó műveletek, és a feladatok, amelyek eredményeként a descartes-i műveleteket.

## <a name="next-steps"></a>További lépések

* [Az Apache Hadoop összetevői és verziói elérhetők a HDInsight segítségével?](../hdinsight-component-versioning.md)
* [Apache Spark-fürt erőforrásainak kezelése a HDInsighton](apache-spark-resource-manager.md)
* [Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark-konfiguráció](https://spark.apache.org/docs/latest/configuration.html)
* [Apache Spark futtatása apache hadoop YARN-on](https://spark.apache.org/docs/latest/running-on-yarn.html)
