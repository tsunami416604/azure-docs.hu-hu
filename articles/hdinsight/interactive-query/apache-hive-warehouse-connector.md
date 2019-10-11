---
title: Apache Spark és Apache Hive integrálása a méhkas Warehouse-összekötővel
description: Megtudhatja, hogyan integrálhatja Apache Spark és Apache Hive a kaptár Warehouse-összekötővel az Azure HDInsight.
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 440820b7772d8edeb43ce328b8393789d7ba2973
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264307"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Apache Spark és Apache Hive integrálása a méhkas Warehouse-összekötővel

A Apache Hive Warehouse Connector (ÜZEMELTETHETŐ WEBMAG) egy olyan függvénytár, amely lehetővé teszi, hogy könnyebben működjön együtt Apache Spark és Apache Hive olyan feladatok támogatásával, mint például az adatok áthelyezése a Spark-DataFrames és a kaptár-táblák között, valamint a Spark streaming-adatok struktúrába rendezése. A méhkas Warehouse-összekötő a Spark és a kaptár közötti híddal működik. Támogatja a Scala, a Java és a Python fejlesztését.

A méhkas Warehouse-összekötő lehetővé teszi, hogy kihasználhassa a kaptár és a Spark egyedi funkcióit, és hatékony, nagy adatmennyiségű alkalmazásokat építsen ki. Apache Hive támogatja az atomi, konzisztens, elkülönített és tartós (savas) adatbázis-tranzakciókat. A struktúrával kapcsolatos további információkért lásd: [kaptár-tranzakciók](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). A kaptár az Apache Rangern és az alacsony késésű analitikai feldolgozáson keresztül is biztosít részletes biztonsági ellenőrzéseket Apache Sparkban nem érhető el.

A Apache Spark olyan strukturált streaming API-val rendelkezik, amely a Apache Hiveban nem elérhető folyamatos átviteli képességeket biztosít. A HDInsight 4,0-es verziójától kezdődően Apache Spark 2.3.1 és Apache Hive 3.1.0 külön metaadattárak rendelkezik, amely megnehezítheti az együttműködési képességet. A méhkas Warehouse-összekötő megkönnyíti a Spark és a struktúra együttes használatát. A ÜZEMELTETHETŐ WEBMAG-könyvtár LLAP-démonokból tölti be az adatait a Spark-végrehajtóknak párhuzamosan, így hatékonyabbá és méretezhetővé válik, mint a Spark és a kaptár közötti szabványos JDBC-kapcsolatok használata.

![a méhkas Warehouse-összekötő architektúrája](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

A kaptár-tárház összekötője által támogatott néhány művelet:

* Táblázat leírása
* Táblázat létrehozása az ork-formázott adathoz
* A kaptárak adatainak kiválasztása és a DataFrame beolvasása
* DataFrame írása a struktúrába a Batch szolgáltatásban
* Struktúra-frissítési utasítás végrehajtása
* Táblázat adatainak olvasása a kaptárból, átalakítás a Sparkban, és az új struktúra-táblázatba való írása
* DataFrame vagy Spark stream készítése a kaptárba a HiveStreaming használatával

## <a name="hive-warehouse-connector-setup"></a>A méhkas Warehouse-összekötő beállítása

Az alábbi lépéseket követve állíthatja be a kaptár Warehouse-összekötőt egy Spark és egy interaktív lekérdezési fürt között az Azure HDInsight:

### <a name="create-clusters"></a>Fürtök létrehozása

1. Hozzon létre egy HDInsight Spark **4,0** -fürtöt egy Storage-fiókkal és egy egyéni Azure-beli virtuális hálózattal. A fürtök Azure-beli virtuális hálózatban való létrehozásával kapcsolatos információkért lásd: [HDInsight hozzáadása meglévő virtuális hálózathoz](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Hozzon létre egy HDInsight interaktív lekérdezési (LLAP) **4,0** -fürtöt ugyanazzal a Storage-fiókkal és Azure-beli virtuális hálózattal, mint a Spark-fürt.

### <a name="modify-hosts-file"></a>Gazdagépek fájljának módosítása

Másolja a csomópont adatait az interaktív lekérdezési fürt headnode0 lévő `/etc/hosts` fájlból, és fűzze össze az adatokat a Spark-fürt headnode0 lévő `/etc/hosts` fájlba. Ez a lépés lehetővé teszi, hogy a Spark-fürt feloldja a csomópontok IP-címeit az interaktív lekérdezési fürtben. Tekintse meg a frissített fájl tartalmát `cat /etc/hosts` értékkel. A végső kimenetnek az alábbi képernyőképen láthatóhoz hasonlóan kell kinéznie.

![a méhkas Warehouse-összekötő fájlját tárolja](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Előzetes információk gyűjtése

#### <a name="from-your-interactive-query-cluster"></a>Az interaktív lekérdezési fürtből

1. Navigáljon a fürt Apache Ambari kezdőlapjára a `https://LLAPCLUSTERNAME.azurehdinsight.net` használatával, ahol a `LLAPCLUSTERNAME` az interaktív lekérdezési fürt neve.

1. Navigáljon **a kaptár** > **konfigurációhoz** > **Advanced** > **Advanced kaptár-site** > **kaptár. Zookeeper. kvórum** , és jegyezze fel az értéket. Az érték a következőhöz hasonló lehet: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

1. Navigáljon a **kaptár**@no__t-**1 konfigurációhoz** > **Advanced** > **általános** > **kaptár. metaadattár. URI** és jegyezze fel az értéket. Az érték a következőhöz hasonló lehet: `thrift://hn0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

#### <a name="from-your-apache-spark-cluster"></a>A Apache Spark-fürtből

1. Navigáljon a fürt Apache Ambari kezdőlapjára a `https://SPARKCLUSTERNAME.azurehdinsight.net` használatával, ahol a `SPARKCLUSTERNAME` a Apache Spark-fürt neve.

1. Navigáljon **a kaptár** > **konfigurációhoz** > **Advanced** > **Advanced kaptár-Interactive-site** > **kaptár. llap. Daemon. Service. hosts** , és jegyezze fel az értéket. Az érték a következőhöz hasonló lehet: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>A Spark-fürt beállításainak konfigurálása

A Spark Ambari webes felhasználói felületén navigáljon a **Spark2** > **konfigurációk** > **Egyéni Spark2 – alapértelmezett értékekre**.

![Apache Ambari Spark2-konfiguráció](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Válassza a **tulajdonság hozzáadása...** lehetőséget a következők hozzáadásához/frissítéséhez:

| Jelmagyarázat | Value (Díj) |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|A **kaptár. llap. Daemon. Service. hosts**által korábban beszerzett érték.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2` kérdésre adott válaszban foglalt lépéseket. Állítsa be a JDBC kapcsolati sztringet, amely az interaktív lekérdezési fürt Hiveserver2 kapcsolódik. CSERÉLJE le a `LLAPCLUSTERNAME` értéket az interaktív lekérdezési fürt nevére. Cserélje le a `PWD` értéket a tényleges jelszóval.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` kérdésre adott válaszban foglalt lépéseket. A megfelelő HDFS-kompatibilis előkészítési könyvtárra van beállítva. Ha két különböző fürttel rendelkezik, az átmeneti könyvtárnak a LLAP-fürt Storage-fiókjának átmeneti könyvtárában kell lennie, hogy a HiveServer2 hozzáférjen hozzá.  Cserélje le a `STORAGE_ACCOUNT_NAME` értéket a fürt által használt Storage-fiók nevével, és `STORAGE_CONTAINER_NAME` értéket a Storage-tároló nevével.|
|`spark.datasource.hive.warehouse.metastoreUri`|A **kaptár. metaadattár. URI**-k által korábban beszerzett érték.|
|`spark.security.credentials.hiveserver2.enabled`|@no__t – 0 a fonal-ügyfél üzembe helyezési módjához.|
|`spark.hadoop.hive.zookeeper.quorum`|A **kaptár. Zookeeper. kvórum**által korábban beszerzett érték.|

Mentse a módosításokat, és szükség szerint indítsa újra az összetevőket.

## <a name="using-the-hive-warehouse-connector"></a>A méhkas Warehouse-összekötő használata

### <a name="connecting-and-running-queries"></a>Lekérdezések csatlakoztatása és futtatása

Néhány különböző módszer közül választhat az interaktív lekérdezési fürthöz való csatlakozáshoz és lekérdezések végrehajtásához a méhkas Warehouse-összekötő használatával. A támogatott módszerek a következő eszközöket tartalmazzák:

* [Spark – Shell](../spark/apache-spark-shell.md)
* PySpark
* Spark – elküldés
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

A cikkben szereplő összes példát a Spark-Shell használatával hajtja végre a rendszer.

A Spark-Shell-munkamenet elindításához hajtsa végre a következő lépéseket:

1. SSH-t a Apache Spark-fürt átjárócsomóponthoz. További információ a fürthöz SSH-val való csatlakozásról: [Csatlakozás HDInsight (Apache Hadoop) SSH használatával](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. A Spark Shell elindításához írja be a következő parancsot:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.2.1-8.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Ekkor megjelenik egy üdvözlő üzenet, és egy `scala>` prompt, ahol megadhatja a parancsokat.

1. A Spark-Shell elindítása után a rendszer elindíthatja a méhkas Warehouse Connector-példányt az alábbi parancsokkal:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Enterprise Security Package-(ESP-) fürtökhöz kapcsolódó lekérdezések csatlakoztatása és futtatása

A Enterprise Security Package (ESP) olyan nagyvállalati szintű képességeket biztosít, mint a Active Directory-alapú hitelesítés, a többfelhasználós támogatás és az Azure HDInsight-beli Apache Hadoop-fürtök szerepköralapú hozzáférés-vezérlése. Az ESP-vel kapcsolatos további információkért lásd: [Enterprise Security Package használata a HDInsight-ben](../domain-joined/apache-domain-joined-architecture.md).

1. SSH-t a Apache Spark-fürt átjárócsomóponthoz. További információ a fürthöz SSH-val való csatlakozásról: [Csatlakozás HDInsight (Apache Hadoop) SSH használatával](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Írja be a `kinit` értéket, és jelentkezzen be egy tartományi felhasználóval.

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

1. Hozzon létre egy `sampletable_colorado` nevű táblázatot, és határozza meg az oszlopait a következő paranccsal:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. A `hivesampletable` tábla szűrése, ahol a `state` oszlop értéke `Colorado`. A struktúra táblázatának ezt a lekérdezését a rendszer Spark DataFrame adja vissza. Ezt követően a DataFrame a `write` függvény használatával menti a `sampletable_colorado` struktúra táblába.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Tekintse meg az eredményeket a következő paranccsal:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![méhkas-raktár összekötő a struktúra-táblázat megjelenítése](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturált adatfolyam-írások

A méhkas Warehouse-összekötő használatával a Spark streaming használatával írhat adatbevitelt a kaptár tábláiba.

Az alábbi lépéseket követve hozzon létre egy méhkas Warehouse-összekötőt, amely az 9999-as localhost porton lévő Spark streamből származó adatok egy kaptár-táblába való betöltését mutatja be.

1. Kövesse a [lekérdezések csatlakoztatása és futtatása](#connecting-and-running-queries)című témakör lépéseit.

1. Indítsa el a Spark streamet a következő paranccsal:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Hozzon létre adatforrást a létrehozott Spark streamhez a következő lépések végrehajtásával:
    1. Nyisson meg egy második SSH-munkamenetet ugyanazon a Spark-fürtön.
    1. A parancssorba írja be a következőt: `nc -lk 9999`. Ez a parancs a netcat segédprogramot használja az adatok parancssorból a megadott portra történő küldéséhez.

1. Térjen vissza az első SSH-munkamenethez, és hozzon létre egy új struktúra-táblázatot a folyamatos átviteli adattároláshoz. A Spark-shellben adja meg a következő parancsot:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Ezután írja be az adatfolyam-továbbítási adataikat az újonnan létrehozott táblába a következő parancs használatával:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > A `metastoreUri` és a `database` beállítást manuálisan kell beállítani a Apache Spark ismert hibája miatt. További információ erről a hibáról: [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Térjen vissza a második SSH-munkamenethez, és adja meg a következő értékeket:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Térjen vissza az első SSH-munkamenethez, és jegyezze fel a rövid tevékenységet. Az alábbi parancs használatával tekintheti meg az adatfájlokat:

    ```scala
    hive.table("stream_table").show()
    ```

A **CTRL + C** billentyűkombinációval állítsa le a netcat a második SSH-munkamenetben. A `:q` használatával lépjen ki a Spark-shellből az első SSH-munkamenetből.

### <a name="securing-data-on-spark-esp-clusters"></a>Az adatbiztonság biztosítása a Spark ESP-fürtökön

1. A következő parancsok megadásával hozzon létre egy táblázatot `demo` és néhány mintaadatok használatával:

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
    1. Nyissa meg a Ranger felügyeleti felhasználói felületét `https://CLUSTERNAME.azurehdinsight.net/ranger/` címen.
    1. Kattintson a kaptár szolgáltatásra a fürthöz a **struktúra**területen.
        ![ranger Service Manager @ no__t-1
    1. Kattintson a **maszkolás** lapra, és **adja hozzá az új házirendet**

        ![kaptár-raktár összekötő Ranger-struktúra szabályzatának listája](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Adja meg a kívánt szabályzat nevét. Adatbázis kiválasztása: **alapértelmezett**, kaptár-tábla **: bemutató**, struktúra oszlop **: név**, felhasználó: **Rsadmin2**, hozzáférési típusok: **Select**és **részleges maszk: az elmúlt 4 megjelenítése** a **maszkolási beállítások kiválasztása** menüből. Kattintson a **Hozzáadás** parancsra.
                ![Webrögzítés létrehozása házirend @ no__t-1
1. Megtekintheti a tábla tartalmát. A Ranger-szabályzat alkalmazása után csak az oszlop utolsó négy karakterét láthatjuk.

    ![bemutató táblázat a Ranger-szabályzat alkalmazása után](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Következő lépések

* [Az interaktív lekérdezés használata HDInsighttal](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Példák a méhkas Warehouse-összekötővel való interakcióra a Zeppelin, a Livy, a Spark-Submit és a pyspark használatával](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
