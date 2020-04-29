---
title: Apache Spark & kaptár-kaptár Warehouse-összekötő – Azure HDInsight
description: Megtudhatja, hogyan integrálhatja Apache Spark és Apache Hive a kaptár Warehouse-összekötővel az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 77623a89e52a5e15fbb4159ff49d9377e53e7d4c
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509533"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Apache Spark és Apache Hive integrálása a méhkas Warehouse-összekötővel

A Apache Hive Warehouse Connector (ÜZEMELTETHETŐ WEBMAG) egy olyan függvénytár, amely megkönnyíti a Apache Spark és a Apache Hive használatát. Megkönnyíti az olyan feladatok támogatását, mint például az adatok áthelyezése a Spark DataFrames és a kaptár táblái között. És a Spark stream-adatátvitelt a kaptár tábláiba. A méhkas Warehouse-összekötő a Spark és a kaptár közötti híddal működik. Támogatja a Scala, a Java és a Python fejlesztését.

A méhkas Warehouse-összekötő segítségével kihasználhatja a kaptár és a Spark egyedi funkcióinak előnyeit. Hatékony Big-adatalkalmazások létrehozásához használt funkciók. Apache Hive támogatja az atomi, konzisztens, elkülönített és tartós (savas) adatbázis-tranzakciókat. A struktúrával kapcsolatos további információkért lásd: [kaptár-tranzakciók](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). A kaptár az Apache Rangern és az alacsony késésű analitikai feldolgozáson keresztül is biztosít részletes biztonsági ellenőrzéseket Apache Sparkban nem érhető el.

A Apache Spark olyan strukturált streaming API-val rendelkezik, amely a Apache Hiveban nem elérhető folyamatos átviteli képességeket biztosít. A HDInsight 4,0-es verziójától kezdve a Apache Spark 2.3.1 és Apache Hive 3.1.0 külön metaadattárak rendelkeznek. Ezek a különálló metaadattárak nehézkessé tehetik az együttműködési képességet. A méhkas Warehouse-összekötő megkönnyíti a Spark és a struktúra együttes használatát. A ÜZEMELTETHETŐ WEBMAG-könyvtár a LLAP (kis késleltetésű analitikai feldolgozási) démonok adatait a Spark-végrehajtóknak párhuzamosan tölti be. Ez a művelet hatékonyabbá és alkalmazkodóképesvé teszi a Spark-ból a kaptárba való normál JDBC-kapcsolat használatát.

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

Másolja a csomópont adatait az `/etc/hosts` interaktív lekérdezési fürt headnode0 lévő fájlból, és fűzze össze az adatokat a Spark `/etc/hosts` -fürt headnode0 található fájlba. Ez a lépés lehetővé teszi, hogy a Spark-fürt feloldja a csomópontok IP-címeit az interaktív lekérdezési fürtben. Tekintse meg a frissített fájl tartalmát a `cat /etc/hosts`alkalmazásban. A végső kimenetnek az alábbi képernyőképen láthatóhoz hasonlóan kell kinéznie.

![a méhkas Warehouse-összekötő fájlját tárolja](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Előzetes információk gyűjtése

#### <a name="from-your-interactive-query-cluster"></a>Az interaktív lekérdezési fürtből

1. Navigáljon a fürt Apache Ambari kaptár oldalára, `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` ahol `LLAPCLUSTERNAME` az az interaktív lekérdezési fürt neve.

1. Navigáljon a **speciális** > **General** > **kaptár. metaadattár. URI azonosítóhoz** , és jegyezze fel az értéket. Az érték a következőhöz hasonló lehet `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`:.

1. Navigáljon a **speciális** > **speciális kaptár-site** > **kaptár. Zookeeper. kvórum** elemre, és jegyezze fel az értéket. Az érték a következőhöz hasonló lehet `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`:.

#### <a name="from-your-apache-spark-cluster"></a>A Apache Spark-fürtből

1. Navigáljon a fürt Apache Ambari kaptár oldalára, `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` ahol `SPARKCLUSTERNAME` a a Apache Spark-fürt neve.

1. Navigáljon a **speciális** > **speciális kaptár-Interactive-site** > **kaptár. llap. Daemon. Service. hosts** elemre, és jegyezze fel az értéket. Az érték a következőhöz hasonló lehet `@llap0`:.

### <a name="configure-spark-cluster-settings"></a>A Spark-fürt beállításainak konfigurálása

A Spark Ambari webes felhasználói felületén navigáljon a **Spark2** > **konfigurációk** > **Egyéni Spark2 – alapértelmezett beállítások**elemre.

![Apache Ambari Spark2-konfiguráció](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Válassza a **tulajdonság hozzáadása...** lehetőséget a következő érték hozzáadásához/frissítéséhez:

| Kulcs | Érték |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|A **kaptár. llap. Daemon. Service. hosts**által korábban beszerzett érték.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Állítsa be a JDBC kapcsolati sztringet, amely az interaktív lekérdezési fürt Hiveserver2 kapcsolódik. Cserélje `LLAPCLUSTERNAME` le az nevet az interaktív lekérdezési fürt nevére. Cserélje `PWD` le a és a tényleges jelszót.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. A megfelelő HDFS-kompatibilis előkészítési könyvtárra van beállítva. Ha két különböző fürttel rendelkezik, az átmeneti könyvtárnak a LLAP-fürt Storage-fiókjának átmeneti könyvtárában kell lennie, hogy a HiveServer2 hozzáférjen hozzá.  Cserélje `STORAGE_ACCOUNT_NAME` le a nevet a fürt által használt Storage-fiók nevére és `STORAGE_CONTAINER_NAME` a tároló nevére.|
|`spark.datasource.hive.warehouse.metastoreUri`|A **kaptár. metaadattár. URI**-k által korábban beszerzett érték.|
|`spark.security.credentials.hiveserver2.enabled`|`false`a FONÁL ügyfél-üzembe helyezési módja.|
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
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Ekkor megjelenik egy üdvözlő üzenet és egy `scala>` kérdés, ahol megadhatja a parancsokat.

1. A Spark-Shell elindítása után a rendszer elindíthatja a méhkas Warehouse Connector-példányt az alábbi parancsokkal:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Enterprise Security Package-(ESP-) fürtökhöz kapcsolódó lekérdezések csatlakoztatása és futtatása

A Enterprise Security Package (ESP) olyan nagyvállalati szintű képességeket biztosít, mint a Active Directory-alapú hitelesítés. A többfelhasználós támogatás és a szerepköralapú hozzáférés-vezérlés Apache Hadoop fürtökhöz az Azure HDInsight-ben. Az ESP-vel kapcsolatos további információkért lásd: [Enterprise Security Package használata a HDInsight-ben](../domain-joined/apache-domain-joined-architecture.md).

1. SSH-t a Apache Spark-fürt átjárócsomóponthoz.

1. Írja `kinit` be és jelentkezzen be egy tartományi felhasználóval.

1. Indítsa el a Spark-shellt a konfigurációs paraméterek teljes listájával az alább látható módon. A szögletes zárójelek közötti összes értéket meg kell adni a fürt alapján. Ha meg kell találnia az alábbi paraméterek bármelyikének bemeneti értékeit, tekintse meg a [kaptár-összekötő beállítása](#hive-warehouse-connector-setup)című szakaszt.

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
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

1. Szűrje azt a `hivesampletable` táblázatot, ahol `state` az oszlop `Colorado`egyenlő. A struktúra táblázatának ezt a lekérdezését a rendszer Spark DataFrame adja vissza. Ezután a DataFrame a `sampletable_colorado` `write` függvény használatával menti a struktúra táblába.

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

Az alábbi lépéseket követve hozzon létre egy méhkas Warehouse-összekötőt. A példa a 9999-as localhost porton lévő Spark streamből származó adatok betöltését egy struktúra-táblázatba.

1. Kövesse a [lekérdezések csatlakoztatása és futtatása](#connecting-and-running-queries)című témakör lépéseit.

1. Indítsa el a Spark streamet a következő paranccsal:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Hozzon létre adatforrást a létrehozott Spark streamhez a következő lépések végrehajtásával:
    1. Nyisson meg egy második SSH-munkamenetet ugyanazon a Spark-fürtön.
    1. A parancssorba írja be a `nc -lk 9999`következőt:. Ez a parancs a `netcat` segédprogram használatával küldi el az adatok a parancssorból a megadott portra.

1. Térjen vissza az első SSH-munkamenethez, és hozzon létre egy új struktúra-táblázatot a folyamatos átviteli adattároláshoz. A Spark-shellben adja meg a következő parancsot:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Ezután írja be az adatfolyam-továbbítási adataikat az újonnan létrehozott táblába a következő parancs használatával:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > A `metastoreUri` és `database` a beállításokat a Apache Spark ismert hibája miatt manuálisan kell beállítani. További információ erről a hibáról: [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

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

A **CTRL + C** billentyűkombinációval `netcat` állítsa le a második SSH-munkamenetet. Az `:q` első SSH-munkamenetből való kilépéshez használja a Spark-shellt.

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
        ![Ranger Service Manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Kattintson a **maszkolás** lapra, és **adja hozzá az új házirendet**

        ![kaptár-raktár összekötő Ranger-struktúra szabályzatának listája](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Adja meg a szabályzat nevét. Adatbázis kiválasztása: **alapértelmezett**, kaptár-tábla **: bemutató**, struktúra oszlop **: név**, felhasználó: **Rsadmin2**, hozzáférési típusok: **Select**és **részleges maszk: az elmúlt 4 megjelenítése** a **maszkolási beállítások kiválasztása** menüből. Kattintson a **Hozzáadás** parancsra.
                ![házirend létrehozása](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Megtekintheti a tábla tartalmát. A Ranger-szabályzat alkalmazása után csak az oszlop utolsó négy karakterét láthatjuk.

    ![bemutató táblázat a Ranger-szabályzat alkalmazása után](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>További lépések

* [Az interaktív lekérdezés használata HDInsighttal](./apache-interactive-query-get-started.md)
* [Példák a méhkas Warehouse-összekötővel való interakcióra a Zeppelin, a Livy, a Spark-Submit és a pyspark használatával](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
