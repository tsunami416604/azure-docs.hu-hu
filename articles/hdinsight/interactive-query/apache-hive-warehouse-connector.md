---
title: Integráció az Apache Spark és Apache Hive, a Hive Warehouse-összekötő
description: Ismerje meg, hogyan integrálható az Apache Spark és Apache Hive-az Azure HDInsight Hive-adatraktár összekötő.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/18/2019
ms.openlocfilehash: b450fe763104adbbd08e4b5f362bd51ffbf82c81
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126545"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integráció az Apache Spark és Apache Hive, a Hive Warehouse-összekötő

Az Apache Hive-adatraktár Connector (WEBMAG) egy könyvtár, amely lehetővé teszi, hogy könnyebben Apache Spark és Apache Hive-feladatokhoz, mint az adatok áthelyezését a Spark adatkerettípusokat jelölhet, és Hive-táblák között, és emellett a Spark streamelési adatok Hive táblákba irányítja támogatásával. Hive-Warehouse-összekötő működik, mint az hidat Spark- és Hive között. Scala, Java és Python fejlesztési támogatja.

A Hive-Warehouse-összekötő lehetővé teszi, hogy a Hive és a Spark hatékony big-data alkalmazások fejlesztése az egyedi funkciókat. Az Apache Hive kínál, amelyek atomi, Consistent, Isolated és tartós (sav) adatbázis-tranzakciók támogatása. További információ az ACID és Hive-tranzakciók: [Hive-tranzakciók](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive is biztosít az Apache Ranger és alacsony késést analitikai feldolgozása nem érhető el, az Apache Spark révén részletes biztonsági vezérlők.

Apache Spark Streaming egy strukturált API, amely lehetővé teszi a közvetítés funkció nem érhető el az Apache Hive rendelkezik. A Hortonworks Data Platform (HDP) 3.0-es verziótól kezdve az Apache Spark és Apache Hive rendelkezik külön metaadattárakat, amely is megnehezítik együttműködés. A Hive-Warehouse-összekötő megkönnyíti a Spark- és Hive együtt használja. Az üzemeltethető WEBMAG library tölt be adatokat LLAP démonok Spark végrehajtóval párhuzamosan, így hatékonyabb és skálázható, mint a Spark, Hive standard JDBC kapcsolat segítségével.

![Architektúra](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Az adatraktár Hive-összekötő által támogatott műveleteket a következők:

* A táblázat leírása
* Az adatok ORC-formátumú, tábla létrehozása
* Hive-adatok kiválasztása és a egy adathalmaz lekérése
* Hive DataFrame írása a Batch szolgáltatásban
* Egy Hive-update utasítás végrehajtása
* Hive táblák adatainak olvasásakor, átalakítja őket a Spark és a egy új Hive-táblába történő írása
* A Hive használatával HiveStreaming DataFrame vagy a Spark stream írása

## <a name="hive-warehouse-connector-setup"></a>Hive-adatraktár-összekötő telepítése

A Hive-Warehouse-összekötő az Azure HDInsight Spark- és interaktív lekérdezési fürt között állíthatja be az alábbi lépéseket követve:

1. Hozzon létre egy HDInsight Spark 4.0 fürtöt egy tárfiókot és a egy egyéni Azure virtuális hálózat az Azure portal használatával. Információk a fürt létrehozása az Azure-beli virtuális hálózathoz: [HDInsight hozzáadása egy meglévő virtuális hálózathoz](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet).
1. HDInsight interaktív lekérdezések (LLAP) 4.0-s fürt ugyanazt a tárfiókot és az Azure virtuális hálózat az Azure portal használatával, a Spark-fürt létrehozása.
1. Másolja ki a tartalmát a `/etc/hosts` headnode0 az interaktív lekérdezési fürt-fájlt a `/etc/hosts` fájlt a Spark-fürt a headnode0. Ez a lépés lehetővé teszi a Spark-fürt csomópontjainak interaktív lekérdezési fürt IP-címek feloldása. A frissített fájlt a tartalma `cat /etc/hosts`. A kimenet hasonlóan kell kinéznie az alábbi képernyőképen is látható.

    ![a hosts fájl megtekintése](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. A Spark-fürt beállítások konfigurálása a következő lépések végrehajtásával: 
    1. Azure portal megnyitása, HDInsight-fürtök közül választhat, és kattintson a fürt nevére.
    1. A jobb oldalon alatt **fürt irányítópultjai**válassza **otthoni Ambari**.
    1. Az Ambari webes felhasználói felületén kattintson **SPARK2** > **CONFIGS** > **egyéni spark2-alapértelmezett**.

        ![Az Ambari Spark2 konfiguráció](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Állítsa be `spark.hadoop.hive.llap.daemon.service.hosts` ugyanazt az értéket a tulajdonság **nevének LLAP** alatt **speciális a hive interaktív env**. Például: `@llap0`

    1. Állítsa be `spark.sql.hive.hiveserver2.jdbc.url` JDBC kapcsolati karakterláncot, amely csatlakozik hiveserver2-n keresztül az interaktív lekérdezési fürt a. A kapcsolati karakterláncot a fürt URI-t az alábbi fog kinézni. `CLUSTERNAME` a Spark-fürt neve és a `user` és `password` paraméterek a fürt számára a helyes értékre van állítva.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > A JDBC URL-CÍMÉT tartalmaznia kell a hiveserver2-n keresztül való kapcsolódáshoz hitelesítő adatok többek között a felhasználónevet és jelszót.

    1. Állítsa be `spark.datasource.hive.warehouse.load.staging.dir` megfelelő HDFS-kompatibilis átmeneti könyvtárba. Ha két külön fürtben, az átmeneti könyvtárban kell lennie az átmeneti könyvtárban, az LLAP fürt storage-fiók egy mappájába, hogy a hiveserver2-n keresztül férhet hozzá. Ha például `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` ahol `STORAGE_ACCOUNT_NAME` neve, a fürt által használt tárfiókok és `STORAGE_CONTAINER_NAME` a tároló neve.

    1. Állítsa be `spark.datasource.hive.warehouse.metastoreUri` a metaadattár URI-ját az interaktív lekérdezési fürt értékét. Keresse meg a metastoreUri LLAP-fürthöz tartozó, keresse meg a **hive.metastore.uris** tulajdonság, az LLAP Ambari felhasználói felületén a fürt alatt **Hive** > **speciális**  >  **Általános**. Az érték a következő URI Azonosítót hasonlót fog kinézni:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Állítsa be `spark.security.credentials.hiveserver2.enabled` való `false` YARN-ügyfél üzembe helyezése mód.
    1. Állítsa be `spark.hadoop.hive.zookeeper.quorum` , az LLAP-fürt kvórum Zookeeper. A Zookeeper kvórum LLAP-fürthöz tartozó megkereséséhez keressen a **hive.zookeeper.quorum** tulajdonság, az LLAP Ambari felhasználói felületén a fürt alatt **Hive** > **speciális**  >  **Hive-hely speciális**. Az érték a következő karakterlánc hasonlót fog kinézni:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

A Hive-Warehouse-összekötő konfigurációja teszteléséhez hajtsa végre a a szakaszban ismertetett lépések [csatlakozó és futó lekérdezések](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>A Hive Warehouse-összekötő használatával

### <a name="connecting-and-running-queries"></a>Csatlakozás és a lekérdezések futtatása

Az interaktív lekérdezési fürt csatlakozhat, és hajtsa végre a lekérdezéseket a Hive-Warehouse-összekötő használatával néhány különböző módszerek közül választhat. Támogatott módszerek az alábbiak az alábbi eszközöket:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* Spark-submit szkripttel
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Ebben a cikkben megadott összes példák keresztül a spark-shell hajtják végre.

A spark-shell munkamenet indításához, tegye a következőket:

1. SSH-t az átjárócsomóponthoz, a fürt számára. Csatlakozás a fürthöz az ssh-val kapcsolatos további információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Írja be a megfelelő könyvtárba módosítása `cd /usr/hdp/current/hive_warehouse_connector` , vagy adja meg a paraméterek a spark-shell-parancsban használt összes jar-fájlok teljes elérési útja.
1. Adja meg a spark-shell elindításához a következő parancsot:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Látni fogja az üdvözlő üzenetet és a egy `scala>` kérdés, ahol megadhatja a parancsok.

1. A spark-shell indítás után egy Hive-adatraktár-Connector-példány indíthatók el a következő parancsokkal:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Csatlakozás és a vállalati biztonsági csomag (ESP) fürtökben-lekérdezések futtatása

A vállalati biztonsági csomag (ESP) például az Active Directory-alapú hitelesítés, a több felhasználó támogatása és a szerepköralapú hozzáférés-vezérlés Apache Hadoop-fürtök Azure HDInsight a vállalati szintű képességet biztosít. ESP további információkért lásd: [Bevezetés az Apache Hadoop-biztonság, a vállalati biztonsági csomag](../domain-joined/apache-domain-joined-introduction.md).

1. Kezdeti lépések az 1. és 2 alatt [csatlakozó és futó lekérdezések](#connecting-and-running-queries).
1. Típus `kinit` és jelentkezzen be egy tartományi felhasználóra.
1. Kezdő a spark-shell a teljes listáját, ahogy az alábbi konfigurációs paramétereket. Összes értéket a csúcsos zárójelek között csupa nagybetűvel kell megadni, a fürt alapján. Ismerje meg az értékeket adjon meg az alábbi paraméterekkel kell, ha lásd [Hive-adatraktár-összekötő telepítése](#hive-warehouse-connector-setup).:

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

### <a name="creating-spark-dataframes-from-hive-queries"></a>A Hive-lekérdezések Spark DataFrames létrehozása

Az üzemeltethető WEBMAG szalagtárat használó összes lekérdezések eredményeit a rendszer adathalmazaként adja vissza. Az alábbi példák bemutatják, hogyan hozzon létre egy egyszerű lekérdezést.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

A lekérdezés eredményeit, olyan DataFrames Spark, Spark-kódtárakat például MLIB és a sparksql-hez használható.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Hive-tábláihoz Spark adatkerettípusokat jelölhet ki írása

A Spark natív módon nem támogatja a Hive a felügyelt ACID táblák való írás. Használja az üzemeltethető WEBMAG, azonban írhat ki bármely DataFrame, egy Hive-táblába. Ez a funkció a munkahelyi hálózatban, az alábbi példában látható:

1. Hozzon létre egy táblát nevű `sampletable_colorado` , és adja meg az oszlopok a következő paranccsal:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Szűrje a táblázatot `hivesampletable` ahol az oszlop `state` egyenlő `Colorado`. Ez a lekérdezés a Hive-táblába, mint a Spark DataFrame adja vissza. Az adathalmaz a Hive-táblába menti a rendszer akkor `sampletable_colorado` használatával a `write` függvény.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Az eredményül kapott tábla az alábbi képernyőképen látható.

![eredményül kapott tábla megjelenítése](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Strukturált streamelés írási műveletek

Hive-Warehouse-összekötő használatával, használhatja a Spark streaming, írja az adatokat, a Hive-táblákat.

Hozhat létre, amelyek a localhost porton 9999 egy Hive-táblába egy Spark stream adatokat fogadnak egy Hive-Warehouse-összekötő például az alábbi lépésekkel.

1. Nyisson meg egy terminált a Spark-fürtön.
1. Kezdje a spark stream a következő paranccsal:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Hozzon létre adatokat a Spark Stream létrehozott, a következő lépések végrehajtásával:
    1. Nyisson meg egy másik terminál ugyanazon a Spark-fürtön.
    1. A parancssorba írja be a `nc -lk 9999`. Ezt a parancsot a parancssorból adatokat küldeni a megadott port a netcat eszközt használja.
    1. Írja be a szavakat, adja meg, hogy, a Spark stream kocsivissza követ.
        ![a spark stream bemeneti adatok](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Hozzon létre egy új Hive-táblába a streamelési adatok tárolásához. A spark-felületen írja be a következő parancsokat:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. A streamelési adatokat írni az újonnan létrehozott tábla a következő paranccsal:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > A `metastoreUri` és `database` beállítások jelenleg manuálisan kell beállítani az Apache Spark ismert problémái következtében. A problémával kapcsolatos további információkért lásd: [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Az alábbi parancsot a táblába beszúrt adatokat tekintheti meg:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Spark ESP-fürtökön az adatok védelme

1. Hozzon létre egy táblát `demo` néhány mintaadatokkal, írja be a következő parancsokat:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. A táblázat tartalma a következő paranccsal tekintheti meg. A szabályzat alkalmazása előtt a `demo` táblázat mutatja a teljes oszlop.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![ranger-házirendet alkalmazása előtt bemutató táblázat](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. A alkalmazni egy olyan szabályzatot, amely csak megjeleníti az utolsó négy karaktert oszlop maszkolási oszlop.  
    1. Nyissa meg a Ranger felügyeleti felhasználói Felületéhez, `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Kattintson a Hive szolgáltatást a fürtöt a **Hive**.
        ![ranger-házirendet alkalmazása előtt bemutató táblázat](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Kattintson a a **maszkolás** fülre, majd **új házirend hozzáadása** ![szabályzatok listája](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Adja meg a kívánt szabályzat nevét. Adatbázis kiválasztása: **Alapértelmezett**, Hive-táblába: **bemutató**, Hive-oszlop: **neve**, felhasználó: **rsadmin2**, hozzáférési típusok: **kiválasztása**, és **Részleges maszk: utolsó 4 megjelenítése** származó a **maszkolás beállítás kijelölése** menü. Kattintson a **Hozzáadás** parancsra.
                ![szabályzatok listája](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. A tábla tartalmának megtekintése újra. Alkalmazása a ranger-házirendet, után látható az oszlop csak az utolsó négy karakter.

    ![ranger házirend alkalmazása után bemutató táblázat](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>További lépések

* [HDInsight interaktív lekérdezés használata](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Példák a használata a Hive-Warehouse-összekötő használatával a Zeppelin Livy, spark-submit szkripttel, és a pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
