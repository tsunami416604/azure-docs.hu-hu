---
title: Apache Spark és Apache Hive integrálása a méhkas Warehouse-összekötővel
description: Megtudhatja, hogyan integrálhatja Apache Spark és Apache Hive a kaptár Warehouse-összekötővel az Azure HDInsight.
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: f3a0fa1ecdb2db94b43a5380f9497b4b1c266e47
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441948"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Apache Spark és Apache Hive integrálása a méhkas Warehouse-összekötővel

A Apache Hive Warehouse Connector (ÜZEMELTETHETŐ WEBMAG) egy olyan függvénytár, amely lehetővé teszi, hogy könnyebben működjön együtt Apache Spark és Apache Hive olyan feladatok támogatásával, mint például az adatok áthelyezése a Spark-DataFrames és a kaptár-táblák között, valamint a Spark streaming-adatok struktúrába rendezése. A méhkas Warehouse-összekötő a Spark és a kaptár közötti híddal működik. Támogatja a Scala, a Java és a Python fejlesztését.

A méhkas Warehouse-összekötő lehetővé teszi, hogy kihasználhassa a kaptár és a Spark egyedi funkcióit, és hatékony, nagy adatmennyiségű alkalmazásokat építsen ki. Apache Hive támogatja az atomi, konzisztens, elkülönített és tartós (savas) adatbázis-tranzakciókat. A struktúrával kapcsolatos további információkért lásd: [kaptár-tranzakciók](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). A kaptár az Apache Rangern és az alacsony késésű analitikai feldolgozáson keresztül is biztosít részletes biztonsági ellenőrzéseket Apache Sparkban nem érhető el.

A Apache Spark olyan strukturált streaming API-val rendelkezik, amely a Apache Hiveban nem elérhető folyamatos átviteli képességeket biztosít. A HDInsight 4,0-es verziójától kezdődően Apache Spark 2.3.1 és Apache Hive 3.1.0 külön metaadattárak rendelkezik, amely megnehezítheti az együttműködési képességet. A méhkas Warehouse-összekötő megkönnyíti a Spark és a struktúra együttes használatát. A ÜZEMELTETHETŐ WEBMAG-könyvtár LLAP-démonokból tölti be az adatait a Spark-végrehajtóknak párhuzamosan, így hatékonyabbá és méretezhetővé válik, mint a Spark és a kaptár közötti szabványos JDBC-kapcsolatok használata.

![Architektúra](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

A kaptár-tárház összekötője által támogatott néhány művelet:

* Táblázat leírása
* Táblázat létrehozása az ork-formázott adathoz
* A kaptárak adatainak kiválasztása és a DataFrame beolvasása
* DataFrame írása a struktúrába a Batch szolgáltatásban
* Struktúra-frissítési utasítás végrehajtása
* Táblázat adatainak olvasása a kaptárból, átalakítás a Sparkban, és az új struktúra-táblázatba való írása
* DataFrame vagy Spark stream készítése a kaptárba a HiveStreaming használatával

## <a name="hive-warehouse-connector-setup"></a>A méhkas Warehouse-összekötő beállítása

Az alábbi lépéseket követve beállíthatja a kaptár Warehouse-összekötőt egy Spark és egy interaktív lekérdezési fürt között az Azure HDInsight:

1. Hozzon létre egy HDInsight Spark 4,0-fürtöt egy Storage-fiókkal és egy egyéni Azure-beli virtuális hálózattal rendelkező Azure Portal használatával. A fürtök Azure-beli virtuális hálózatban való létrehozásával kapcsolatos információkért lásd: [HDInsight hozzáadása meglévő virtuális hálózathoz](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).
1. Hozzon létre egy HDInsight interaktív lekérdezési (LLAP) 4,0-Azure Portal fürtöt ugyanazzal a Storage-fiókkal és Azure virtuális hálózattal, mint a Spark-fürttel.
1. Másolja a `/etc/hosts` fájl tartalmát az interaktív lekérdezési fürt headnode0 a Spark `/etc/hosts` -fürt headnode0 található fájlba. Ez a lépés lehetővé teszi, hogy a Spark-fürt feloldja a csomópontok IP-címeit az interaktív lekérdezési fürtben. Tekintse meg a frissített fájl tartalmát a `cat /etc/hosts`alkalmazásban. A kimenetnek az alábbi képernyőképen láthatóhoz hasonlóan kell kinéznie.

    ![a Hosts fájl megtekintése](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Konfigurálja a Spark-fürt beállításait a következő lépések végrehajtásával: 
    1. Nyissa meg a Azure Portal, válassza ki a HDInsight-fürtök elemet, majd kattintson a fürt nevére.
    1. A jobb oldalon, a **fürt irányítópultok**területén válassza a **Ambari Kezdőlap**lehetőséget.
    1. A Ambari webes felhasználói felületén kattintson a **SPARK2** > -**konfigurációk** > **Egyéni SPARK2 – alapértelmezett beállítások**elemre.

        ![Spark2 Ambari-konfiguráció](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. A `spark.hadoop.hive.llap.daemon.service.hosts` **speciális struktúra – interaktív – env**területen állítsa be a tulajdonságot a **LLAP** tulajdonság értékeként. Például: `@llap0`

    1. Állítsa `spark.sql.hive.hiveserver2.jdbc.url` be a JDBC kapcsolati sztringet, amely az interaktív lekérdezési fürt Hiveserver2 kapcsolódik. A fürthöz tartozó kapcsolatok karakterlánca az alábbi URI-hoz hasonlóan fog kinézni. `CLUSTERNAME`a a Spark-fürt neve, a és `user` `password` a paraméter pedig a fürt megfelelő értékeire van beállítva.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > A JDBC URL-címnek tartalmaznia kell a Hiveserver2 való csatlakozáshoz szükséges hitelesítő adatokat, beleértve a felhasználónevet és a jelszót.

    1. A `spark.datasource.hive.warehouse.load.staging.dir` megfelelő HDFS-kompatibilis előkészítési könyvtárra van beállítva. Ha két különböző fürttel rendelkezik, az átmeneti könyvtárnak a LLAP-fürt Storage-fiókjának átmeneti könyvtárában kell lennie, hogy a HiveServer2 hozzáférjen hozzá. Például `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` `STORAGE_CONTAINER_NAME` ahol `STORAGE_ACCOUNT_NAME` a a fürt által használt Storage-fiók neve, a pedig a Storage-tároló neve.

    1. Állítsa `spark.datasource.hive.warehouse.metastoreUri` be az interaktív lekérdezési fürt metaadattár URI azonosítóját. A LLAP-fürt metastoreUri megkereséséhez keresse meg a **kaptár. metaadattár. URI** tulajdonságot a LLAP-fürt Ambari felhasználói felületén a **kaptár** > **speciális** > **általános**részében. Az érték a következő URI-hoz hasonlóan fog kinézni:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. A `spark.security.credentials.hiveserver2.enabled` következőre van beállítva: szál- `false` ügyfél üzembe helyezési módja.
    1. Állítsa `spark.hadoop.hive.zookeeper.quorum` a LLAP-fürt Zookeeper kvórumára. A LLAP-fürt Zookeeper-Kvórumának megkereséséhez keresse meg a **kaptár. Zookeeper. kvórum** tulajdonságot a LLAP-fürt Ambari felhasználói felületén a **kaptár** > **speciális** > **struktúrája – hely**területen. Az érték a következő sztringhez hasonlóan fog kinézni:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

A méhkas Warehouse-összekötő konfigurációjának teszteléséhez hajtsa végre a [lekérdezések csatlakoztatása és futtatása](#connecting-and-running-queries)című szakasz lépéseit.

## <a name="using-the-hive-warehouse-connector"></a>A méhkas Warehouse-összekötő használata

### <a name="connecting-and-running-queries"></a>Lekérdezések csatlakoztatása és futtatása

Néhány különböző módszer közül választhat az interaktív lekérdezési fürthöz való csatlakozáshoz és lekérdezések végrehajtásához a méhkas Warehouse-összekötő használatával. A támogatott módszerek a következő eszközöket tartalmazzák:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* Spark – elküldés
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

A cikkben szereplő összes példát a Spark-Shell használatával hajtja végre a rendszer.

A Spark-Shell-munkamenet elindításához hajtsa végre a következő lépéseket:

1. SSH-t a fürt átjárócsomóponthoz. További információ a fürthöz SSH-val való csatlakozásról: [Csatlakozás HDInsight (Apache Hadoop) SSH használatával](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Váltson át a megfelelő könyvtárba úgy, `cd /usr/hdp/current/hive_warehouse_connector` hogy beírja vagy megadja a Spark-Shell parancsban paraméterként használt JAR-fájlok teljes elérési útját.
1. A Spark Shell elindításához írja be a következő parancsot:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Egy üdvözlő üzenet jelenik meg, amely `scala>` a parancsok megadására használható.

1. A Spark-Shell elindítása után a rendszer elindíthatja a méhkas Warehouse Connector-példányt az alábbi parancsokkal:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Enterprise Security Package-(ESP-) fürtökhöz kapcsolódó lekérdezések csatlakoztatása és futtatása

A Enterprise Security Package (ESP) olyan nagyvállalati szintű képességeket biztosít, mint a Active Directory-alapú hitelesítés, a többfelhasználós támogatás és az Azure HDInsight-beli Apache Hadoop-fürtök szerepköralapú hozzáférés-vezérlése. Az ESP-vel kapcsolatos további információkért lásd: [Enterprise Security Package használata a HDInsight-ben](../domain-joined/apache-domain-joined-architecture.md).

1. A [lekérdezések csatlakoztatása és futtatása](#connecting-and-running-queries)szakaszban kövesse az 1. és a 2. lépést.
1. Írja `kinit` be és jelentkezzen be egy tartományi felhasználóval.
1. Indítsa el a Spark-shellt a konfigurációs paraméterek teljes listájával az alább látható módon. A szögletes zárójelek közötti összes értéket meg kell adni a fürt alapján. Ha meg kell találnia az alábbi paraméterek bármelyikének bemeneti értékeit, tekintse meg a következő szakaszt: [kaptár Warehouse Connector beállítása](#hive-warehouse-connector-setup).

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Spark-DataFrames létrehozása a kaptár-lekérdezésekből

A ÜZEMELTETHETŐ WEBMAG könyvtárat használó összes lekérdezés eredményét DataFrame adja vissza. Az alábbi példák bemutatják, hogyan hozhat létre alapszintű lekérdezést.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

A lekérdezés eredménye Spark DataFrames, amely a Spark-függvénytárak, például a MLIB és a SparkSQL használatával használható.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark-DataFrames kiírása a struktúra tábláiba

A Spark nem támogatja natív módon az írást a kaptár felügyelt savas tábláiba. A ÜZEMELTETHETŐ WEBMAG használatával azonban bármilyen DataFrame kiírhat egy kaptár-táblába. Ezt a funkciót a következő példában tekintheti meg a munkahelyen:

1. Hozzon létre egy `sampletable_colorado` nevű táblát, és határozza meg az oszlopokat a következő parancs használatával:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Szűrje azt a `hivesampletable` táblázatot, ahol `state` az oszlop `Colorado`egyenlő. A struktúra táblázatának ezt a lekérdezését a rendszer Spark DataFrame adja vissza. Ezután a DataFrame a `sampletable_colorado` `write` függvény használatával menti a struktúra táblába.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Az eredményül kapott táblázatot az alábbi képernyőképen tekintheti meg.

![eredményül kapott táblázat megjelenítése](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturált adatfolyam-írások

A méhkas Warehouse-összekötő használatával a Spark streaming használatával írhat adatbevitelt a kaptár tábláiba.

Az alábbi lépéseket követve hozzon létre egy méhkas Warehouse-összekötőt, amely az 9999-as localhost porton lévő Spark streamből származó adatok egy kaptár-táblába való betöltését mutatja be.

1. Nyisson meg egy terminált a Spark-fürtön.
1. Indítsa el a Spark streamet a következő paranccsal:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Hozzon létre adatforrást a létrehozott Spark streamhez a következő lépések végrehajtásával:
    1. Nyisson meg egy másik terminált ugyanazon a Spark-fürtön.
    1. A parancssorba írja be a `nc -lk 9999`következőt:. Ez a parancs a netcat segédprogramot használja az adatok parancssorból a megadott portra történő küldéséhez.
    1. Írja be azt a szót, amelyet a Spark-adatfolyam betöltéséhez szeretne, majd adja meg a kocsivissza értéket.
        ![bemeneti adatok a Spark streambe](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Hozzon létre egy új struktúra-táblázatot a folyamatos átviteli adattároláshoz. A Spark-shellben írja be a következő parancsokat:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. A következő parancs használatával írja a folyamatos átviteli adatátvitelt az újonnan létrehozott táblába:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > A `metastoreUri` és`database` a beállításokat a Apache Spark ismert hibája miatt manuálisan kell beállítani. További információ erről a hibáról: [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Az alábbi paranccsal megtekintheti a táblázatba beszúrt adattartalmat:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Az adatbiztonság biztosítása a Spark ESP-fürtökön

1. Hozzon létre `demo` egy táblázatot néhány mintaadatok használatával a következő parancsok beírásával:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Tekintse meg a tábla tartalmát a következő paranccsal. A szabályzat alkalmazása előtt a `demo` tábla megjeleníti a teljes oszlopot.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![bemutató táblázat a Ranger-szabályzat alkalmazása előtt](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Alkalmazzon egy oszlop maszkolási szabályzatot, amely csak az oszlop utolsó négy karakterét jeleníti meg.  
    1. Nyissa meg a Ranger felügyeleti felhasználói `https://CLUSTERNAME.azurehdinsight.net/ranger/`felületét a következő címen:.
    1. Kattintson a kaptár szolgáltatásra a fürthöz a **struktúra**területen.
        ![bemutató táblázat a Ranger-szabályzat alkalmazása előtt](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Kattintson a **maszkolás** lapra, és **adja hozzá az új házirend** ![-listát](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Adja meg a kívánt szabályzat nevét. Adatbázis kiválasztása: **Alapértelmezett**, struktúra tábla: **bemutató**, kaptár oszlop: **név**, felhasználó: **rsadmin2**, hozzáférési típusok: **Select**, és **részleges maszk: az elmúlt 4 megjelenítése** a **maszkolási beállítások kiválasztása** menüből. Kattintson a **Hozzáadás**lehetőségre.
                ![szabályzatok listája](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Megtekintheti a tábla tartalmát. A Ranger-szabályzat alkalmazása után csak az oszlop utolsó négy karakterét láthatjuk.

    ![bemutató táblázat a Ranger-szabályzat alkalmazása után](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>További lépések

* [Az interaktív lekérdezés használata HDInsighttal](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Példák a méhkas Warehouse-összekötővel való interakcióra a Zeppelin, a Livy, a Spark-Submit és a pyspark használatával](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
