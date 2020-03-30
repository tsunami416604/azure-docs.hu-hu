---
title: Apache Spark & Hive – Hive-raktárösszekötő - Azure HDInsight
description: Ismerje meg, hogyan integrálhatja az Apache Sparkot és az Apache Hive-t a Hive-raktárösszekötővel az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252405"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrálja az Apache Sparkot és az Apache Hive-t a Hive Raktár összekötővel

Az Apache Hive Warehouse Connector (HWC) egy olyan könyvtár, amely lehetővé teszi, hogy könnyebben működjön együtt az Apache Spark és az Apache Hive használatával olyan feladatok támogatásával, mint például az adatok áthelyezése a Spark DataFrame-ek és a Hive-táblák között, valamint a Spark-streamelési adatok hive-táblákba való átirányítása. A Hive Raktárösszekötő úgy működik, mint egy híd a Spark és a Hive között. Támogatja a Scala, Java és Python fejlesztéshez.

A Hive Raktár-összekötő lehetővé teszi, hogy kihasználja a Hive és a Spark egyedi funkcióit, és hatékony big data-alkalmazásokat hozhat létre. Az Apache Hive támogatja az atomi, konzisztens, izolált és tartós (ACID) adatbázis-tranzakciókat. Az ACID-ről és a Hive-tranzakciókról a [Hive-tranzakciók című](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)témakörben talál további információt. A Hive részletes biztonsági vezérlőket is kínál az Apache Ranger és az alacsony késleltetésű analitikai feldolgozás révén, amely nem érhető el az Apache Sparkban.

Az Apache Spark strukturált streamelési API-val rendelkezik, amely nem biztosít streamelési képességeket az Apache Hive-ban. A HDInsight 4.0-tól kezdve az Apache Spark 2.3.1 és az Apache Hive 3.1.0 külön metaüzletekkel rendelkezik, ami megnehezítheti az interoperabilitást. A Hive raktári összekötő megkönnyíti a Spark és a Hive együttes használatát. A HWC-kódtár az LLAP démonokból a Spark végrehajtóinak származó adatokat tölti be párhuzamosan, így hatékonyabbá és méretezhetőbbé válik, mint a Sparkés Hive között lévő szabványos JDBC-kapcsolat használata.

![struktúra raktári összekötő architektúrája](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

A Hive-raktár összekötő által támogatott műveletek közül néhány a következő:

* Táblázat leírása
* Táblázat létrehozása ORC formátumú adatokhoz
* Hive-adatok kijelölése és DataFrame beolvasása
* DataFrame írása a Hive-ba kötegelt
* Hive-frissítési utasítás végrehajtása
* Táblázatadatok olvasása a Hive-ból, a Spark ban való átalakítása és új Hive-táblába írása
* DataFrame vagy Spark-adatfolyam írása a HiveStreaming használatával a HiveStreaming használatával

## <a name="hive-warehouse-connector-setup"></a>Hive-raktárösszekötő beállítása

Az alábbi lépésekkel állíthatja be a Hive-raktárösszekötőt egy Spark és interaktív lekérdezési fürt között az Azure HDInsightban:

### <a name="create-clusters"></a>Fürtök létrehozása

1. Hozzon létre egy HDInsight Spark **4.0-fürtöt** tárfiókkal és egyéni Azure virtuális hálózattal. A fürt Azure virtuális hálózatban való létrehozásáról a [HDInsight hozzáadása meglévő virtuális hálózathoz](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)című témakörben talál további információt.

1. Hozzon létre egy HDInsight Interactive Query (LLAP) **4.0-s** fürtöt ugyanazzal a tárfiókkal és Azure virtuális hálózattal, mint a Spark-fürt.

### <a name="modify-hosts-file"></a>Hosts fájl módosítása

Másolja a csomópont adatait `/etc/hosts` az interaktív lekérdezési fürt headnode0-fájljából, `/etc/hosts` és fűzze össze az adatokat a Spark-fürt headnode0-es fájljában lévő fájlba. Ez a lépés lehetővé teszi, hogy a Spark-fürt feloldja az interaktív lekérdezési fürt csomópontjainak IP-címeit. A frissített fájl tartalmának `cat /etc/hosts`megtekintése a segítségével. A végső kimenetnek valahogy úgy kell kinéznie, mint ami az alábbi képernyőképen látható.

![struktúraraktár-összekötő gazdagépfájlja](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Előzetes információk gyűjtése

#### <a name="from-your-interactive-query-cluster"></a>Az interaktív lekérdezési fürtből

1. Keresse meg a fürt Apache Ambari `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` Hive lapját az interaktív lekérdezési fürt nevének használatával. `LLAPCLUSTERNAME`

1. Keresse meg az Advanced**General** > **hive.metastore.uris** **(Speciális** > általános struktúra.metastore.uris) területre, és jegyezze fel az értéket. Az érték hasonló lehet `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`a következőkhöz: .

1. Keresse meg **az Advanced** > **Advanced Thive-site** > **hive.zookeeper.quorum webhelyet,** és jegyezze fel az értéket. Az érték hasonló lehet `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`a következőkhöz: .

#### <a name="from-your-apache-spark-cluster"></a>Az Apache Spark-fürtből

1. Keresse meg a fürt Apache Ambari `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` Hive lapját az Apache Spark-fürt nevének használatával. `SPARKCLUSTERNAME`

1. Nyissa meg **a Speciális** > **speciális struktúra-interaktív-site** > **hive.llap.daemon.service.hosts webhelyet,** és jegyezze fel az értéket. Az érték hasonló lehet `@llap0`a következőkhöz: .

### <a name="configure-spark-cluster-settings"></a>Spark-fürt beállításainak konfigurálása

A Spark Ambari webes felhasználói felületén keresse meg a **Spark2** > **CONFIGS** > **Egyéni spark2-alapértelmezett beállításokat.**

![Apache Ambari Spark2 konfiguráció](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Válassza **a Tulajdonság hozzáadása lehetőséget...** szükség szerint a következők hozzáadásához/frissítéséhez:

| Kulcs | Érték |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|A **hive.llap.daemon.service.hosts**oldalról korábban kapott érték .|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Állítsa a JDBC kapcsolati karakterláncra, amely az interaktív lekérdezési fürt Hiveserver2 kiszolgálójához csatlakozik. REPLACE `LLAPCLUSTERNAME` az interaktív lekérdezési fürt nevével. Cserélje `PWD` le a tényleges jelszót.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Állítsa be a megfelelő HDFS-kompatibilis átmeneti könyvtárba. Ha két különböző fürttel rendelkezik, az átmeneti könyvtárnak az LLAP fürt tárfiókjának átmeneti könyvtárában lévő mappának kell lennie, hogy a HiveServer2 hozzáférhessen.  Cserélje `STORAGE_ACCOUNT_NAME` le a fürt által használt tárfiók `STORAGE_CONTAINER_NAME` nevére és a tárolótároló nevére.|
|`spark.datasource.hive.warehouse.metastoreUri`|A **hive.metastore.uris**oldalról korábban kapott érték.|
|`spark.security.credentials.hiveserver2.enabled`|`false`yarn ügyfél üzembe helyezési módban.|
|`spark.hadoop.hive.zookeeper.quorum`|A **hive.zookeeper.quorum**oldalról korábban kapott érték .|

Mentse a módosításokat, és indítsa újra az összetevőket, ha szükséges.

## <a name="using-the-hive-warehouse-connector"></a>A Hive-raktár összekötőjének használata

### <a name="connecting-and-running-queries"></a>Lekérdezések csatlakoztatása és futtatása

Az interaktív lekérdezési fürthöz való csatlakozáshoz és a Hive-raktárösszekötővel történő lekérdezések végrehajtásához néhány különböző módszer közül választhat. A támogatott módszerek a következő eszközöket tartalmazzák:

* [szikrahéj](../spark/apache-spark-shell.md)
* PySpark között
* szikra-benyújtás
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Az ebben a cikkben szereplő összes példa a spark-shell en keresztül lesz végrehajtva.

Spark-shell munkamenet indításához tegye a következő lépéseket:

1. SSH az Apache Spark-fürt fejnokba. A fürthöz az SSH-val való csatlakozásról a [Csatlakozás a HDInsighthoz (Apache Hadoop) ssh használatával című témakörben](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)talál további információt.

1. A spark rendszerhéj elindításához adja meg a következő parancsot:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Megjelenik egy üdvözlő üzenet `scala>` és egy üzenet, ahol parancsokat adhat meg.

1. A spark-shell elindítása után a Hive Warehouse Connector-példány a következő parancsokkal indítható el:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Lekérdezések csatlakoztatása és futtatása vállalati biztonsági csomag (ESP) fürtjein

A vállalati biztonsági csomag (ESP) nagyvállalati szintű képességeket biztosít, például Active Directory-alapú hitelesítést, többfelhasználós támogatást és szerepköralapú hozzáférés-vezérlést az Apache Hadoop-fürtökhöz az Azure HDInsightban. Az ESP-ről a [Vállalati biztonsági csomag használata a HDInsightban](../domain-joined/apache-domain-joined-architecture.md)című témakörben talál további információt.

1. SSH az Apache Spark-fürt fejnokba. A fürthöz az SSH-val való csatlakozásról a [Csatlakozás a HDInsighthoz (Apache Hadoop) ssh használatával című témakörben](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)talál további információt.

1. Írja `kinit` be és jelentkezzen be egy tartományi felhasználóval.

1. Indítsa el a spark-shell-t a konfigurációs paraméterek teljes listájával az alábbiak szerint. A szögletes zárójelek közötti összes nagybetűs értéket a fürt alapján kell megadni. Ha az alábbi paraméterek bármelyikéhez meg szeretné tudni a bevitt értékeket, olvassa el a [Hive Warehouse Connector beállításáról](#hive-warehouse-connector-setup)szóló szakaszt.:

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

### <a name="creating-spark-dataframes-from-hive-queries"></a>Spark-adatkeretek létrehozása Hive-lekérdezésekből

A HWC-kódtárat használó összes lekérdezés eredménye DataFrame-ként kerül visszaadásra. Az alábbi példák bemutatják, hogyan hozhat létre egy egyszerű lekérdezést.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

A lekérdezés eredményei a Spark DataFrames, amely használható Spark-kódtárak, például az MLIB és a SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark-adatkeretek írása Hive-táblákba

A Spark nem támogatja a Hive által felügyelt ACID-táblákba való írást. A HWC használatával azonban bármelyik DataFrame-et kiírhatja egy Hive-táblába. Ezt a funkciót a következő példában láthatja a munkahelyén:

1. Hozzon létre `sampletable_colorado` egy hívott táblát, és adja meg az oszlopait a következő paranccsal:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Szűrje `hivesampletable` a táblázatot, ahol az oszlop `state` egyenlő `Colorado`. A Hive-tábla lekérdezése Spark DataFrame-ként jelenik meg. Ezután a DataFrame menti a `sampletable_colorado` Hive-tábla a `write` függvény használatával.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Tekintse meg az eredményeket a következő paranccsal:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![kaptárraktár-összekötő a struktúratábla megjelenítése](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturált streamelési írások

A Hive-raktárösszekötő használatával a Spark streamelés segítségével adatokat írhat a Hive-táblákba.

Az alábbi lépéseket követve hozzon létre egy Hive-raktárösszekötő példát, amely adatokat fogad be egy Spark-adatfolyamból a 9999-es helyi porton egy Hive-táblába.

1. Kövesse a [Lekérdezések csatlakoztatása és futtatása csoportlépéseit.](#connecting-and-running-queries)

1. Kezdje a szikrafolyamot a következő paranccsal:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Hozzon létre adatokat a létrehozott Spark-adatfolyamhoz az alábbi lépésekkel:
    1. Nyisson meg egy második SSH-munkamenetet ugyanazon a Spark-fürtön.
    1. A parancssorba `nc -lk 9999`írja be a parancsot. Ez a parancs a netcat segédprogramot használja adatok küldésére a parancssorból a megadott portra.

1. Térjen vissza az első SSH-munkamenethez, és hozzon létre egy új Hive-táblát a streamelési adatok tárolására. A szikrahéjnál adja meg a következő parancsot:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Ezután írja be a streamelési adatokat az újonnan létrehozott táblába a következő paranccsal:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > A `metastoreUri` `database` és a beállításokat jelenleg manuálisan kell beállítani az Apache Spark ismert problémája miatt. A problémáról további információt a [SPARK-25460 című](https://issues.apache.org/jira/browse/SPARK-25460)témakörben talál.

1. Térjen vissza a második SSH munkamenethez, és adja meg a következő értékeket:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Térjen vissza az első SSH munkamenethez, és jegyezze fel a rövid tevékenységet. Az adatok megtekintéséhez használja a következő parancsot:

    ```scala
    hive.table("stream_table").show()
    ```

A **Ctrl + C billentyűkombinációval** leállíthatja a netcat-et a második SSH munkamenetben. A `:q` szikrahéjból való kilépéshez használható az első SSH-munkamenetben.

### <a name="securing-data-on-spark-esp-clusters"></a>Adatok védelme a Spark ESP-fürtökön

1. Hozzon `demo` létre egy táblázatot néhány mintaadattal a következő parancsok beírásával:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. A táblázat tartalmának megtekintése a következő paranccsal. A házirend alkalmazása előtt `demo` a táblázat a teljes oszlopot jeleníti meg.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![demótábla a ranger-házirend alkalmazása előtt](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Olyan oszlopmaszkolási házirend alkalmazása, amely csak az oszlop utolsó négy karakterét jeleníti meg.  
    1. Nyissa meg a Ranger `https://CLUSTERNAME.azurehdinsight.net/ranger/`Admin felhasználói felületét a.
    1. Kattintson a **Hive**csoportban a fürt Hive-szolgáltatására.
        ![ranger szolgáltatás vezetője](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Kattintson a **Maszkolás fülre,** majd **az Új házirend hozzáadása parancsra**

        ![hive raktárösszekötő-felügyelő házirend-házirendjének listája](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Adja meg a kívánt házirend nevét. Adatbázis kiválasztása: **Alapértelmezett**, Hive-tábla: **demó**, Hive oszlop: **név**, Felhasználó: **rsadmin2**, Hozzáférési típusok: válassza ki a **lehetőséget,** és részleges maszk: A **Maszkolási lehetőség kiválasztása** menü **utolsó 4 megjelenítése.** Kattintson a **Hozzáadás** gombra.
                ![házirend létrehozása](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Tekintse meg újra a tábla tartalmát. A ranger-szabályzat alkalmazása után csak az oszlop utolsó négy karakterét láthatjuk.

    ![demótábla a ranger-házirend alkalmazása után](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>További lépések

* [Az interaktív lekérdezés használata HDInsighttal](./apache-interactive-query-get-started.md)
* [Példák a Hive Warehouse Connector segítségével zeppelin, Livy, spark-submit és pyspark használatával](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
