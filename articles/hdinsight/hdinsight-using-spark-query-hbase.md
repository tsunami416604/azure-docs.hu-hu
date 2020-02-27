---
title: A Spark használata HBase-alapú adatolvasásra és-írásra – Azure HDInsight
description: A Spark HBase-összekötővel adatok olvashatók és írhatók egy Spark-fürtről egy HBase-fürtre.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/24/2020
ms.openlocfilehash: 888f24e13ce67c878592068927383dd8cbfefa60
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623102"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Az Apache Spark használata Apache HBase-adatok írására és olvasására

Az Apache HBase általában az alacsony szintű API-val (vizsgálatok, lekérések és a beolvasás) vagy egy SQL-szintaxissal, Apache Phoenix használatával kérdezi le. Az Apache emellett biztosítja a Apache Spark HBase-összekötőt is, amely a HBase által tárolt adatok lekérdezésére és módosítására szolgáló kényelmes és nagy teljesítményű alternatíva.

## <a name="prerequisites"></a>Előfeltételek

* Két különálló, ugyanazon a [virtuális hálózaton](./hdinsight-plan-virtual-network-deployment.md)üzembe helyezett HDInsight-fürt. Egy HBase és egy Spark, amely legalább Spark 2,1 (HDInsight 3,6) van telepítve. További információ: [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure Portal használatával](hdinsight-hadoop-create-linux-clusters-portal.md).

* Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md).

* A fürtök elsődleges tárolójának [URI-sémája](hdinsight-hadoop-linux-information.md#URI-and-scheme) . Ezt a sémát wasb://az Azure Blob Storage, abfs://for Azure Data Lake Storage Gen2 vagy adl://for Azure Data Lake Storage Gen1. Ha a biztonságos átvitel engedélyezve van a Blob Storage számára, akkor az URI `wasbs://`.  Lásd még: [biztonságos átvitel](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Teljes folyamat

A Spark-fürt a HDInsight-fürt lekérdezésére való engedélyezésének magas szintű folyamata a következő:

1. Készítse elő a mintaadatok némelyikét a HBase-ben.
2. Szerezze be a hbase-site. xml fájlt a HBase-fürt konfigurációs mappájából (/etc/hbase/conf).
3. Helyezzen egy másolatot a hbase-site. XML fájlról a Spark 2 konfigurációs mappájába (/etc/spark2/conf).
4. Futtasson `spark-shell` a Spark HBase-összekötőre hivatkozva a Maven-koordinátáival a `packages` beállításban.
5. Definiáljon egy katalógust, amely leképezi a sémát a Spark és a HBase között.
6. A HBase adatai a RDD vagy a DataFrame API-k használatával működnek.

## <a name="prepare-sample-data-in-apache-hbase"></a>Mintaadatok előkészítése az Apache HBase

Ebben a lépésben létrehoz és feltölt egy táblázatot az Apache HBase, amelyet aztán a Spark használatával tud lekérdezni.

1. A `ssh` parancs használatával csatlakozhat a HBase-fürthöz. Szerkessze az alábbi parancsot úgy, hogy lecseréli `HBASECLUSTER`t a HBase-fürt nevére, majd beírja a következő parancsot:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. A HBase interaktív rendszerhéj elindításához használja a `hbase shell` parancsot. Adja meg az alábbi parancsot az SSH-kapcsolatban:

    ```bash
    hbase shell
    ```

3. A `create` parancs használatával hozzon létre egy HBase táblát kétoszlopos családokkal. Írja be a következő parancsot:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Az `put` parancs használatával szúrhat be értékeket egy adott tábla megadott sorában található adott oszlopba. Írja be a következő parancsot:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. A HBase interaktív rendszerhéj leállításához használja a `exit` parancsot. Írja be a következő parancsot:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>A hbase-site. xml fájl másolása a Spark-fürtbe

Másolja a hbase-site. xml fájlt a helyi tárolóból a Spark-fürt alapértelmezett tárolójának gyökerébe.  Szerkessze az alábbi parancsot, hogy tükrözze a konfigurációt.  Ezután a megnyitott SSH-munkamenetből a HBase-fürtbe írja be a következő parancsot:

| Szintaxis értéke | Új érték|
|---|---|
|[URI-séma](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Módosítsa a tárolót.  Az alábbi szintaxis a biztonságos átvitelt engedélyező blob Storage.|
|`SPARK_STORAGE_CONTAINER`|Cserélje le a értéket a Spark-fürthöz használt alapértelmezett Storage-tároló nevére.|
|`SPARK_STORAGE_ACCOUNT`|Cserélje le a értéket a Spark-fürthöz használt alapértelmezett Storage-fiók nevére.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Ezután lépjen ki az SSH-kapcsolatban a HBase-fürttel.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>A hbase-site. xml fájl elhelyezése a Spark-fürtön

1. Kapcsolódjon a Spark-fürt fő csomópontjához az SSH használatával. Szerkessze az alábbi parancsot úgy, hogy lecseréli `SPARKCLUSTER` a Spark-fürt nevére, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Adja meg az alábbi parancsot a Spark-fürt alapértelmezett tárolójának `hbase-site.xml` a fürt helyi tárolójában lévő Spark 2 konfigurációs mappájába való másolásához:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>A Spark HBase-összekötőre hivatkozó Spark Shell futtatása

1. Az Open SSH-munkamenetből a Spark-fürtbe írja be az alábbi parancsot a Spark Shell indításához:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Tartsa nyitva a Spark Shell-példányt, és folytassa a következő lépéssel.

## <a name="define-a-catalog-and-query"></a>Katalógus és lekérdezés megadása

Ebben a lépésben olyan katalógus-objektumot határoz meg, amely a sémát Apache Sparkról Apache HBase-re képezi le.  

1. Az Open Spark shellben adja meg a következő `import` utasításokat:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Adja meg az alábbi parancsot a HBase létrehozott Contacts tábla katalógusának definiálásához:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    A kód a következő műveleteket végzi el:  

     a. Adja meg a katalógus sémáját a `Contacts`nevű HBase táblához.  
     b. Azonosítsa a rowkey `key`ként, és képezze le a Sparkban használt oszlopnevek az oszlop családja, az oszlopnév és az oszlop típusát a HBase-ben használt módon.  
     c. A rowkey is részletesen meg kell határozni egy elnevezett oszlopként (`rowkey`), amely a `rowkey`egy adott oszlop családjának `cf`.  

1. Adja meg az alábbi parancsot egy olyan metódus definiálásához, amely DataFrame biztosít a `Contacts` tábla körül a HBase-ben:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Hozza létre a DataFrame egy példányát:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. A DataFrame lekérdezése:

    ```scala
    df.show()
    ```

    Két adatsornak kell megjelennie:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Egy ideiglenes tábla regisztrálása a HBase-tábla a Spark SQL használatával történő lekérdezéséhez:

    ```scala
    df.createTempView("contacts")
    ```

1. SQL-lekérdezés kiadása a `contacts` táblában:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    A következőhöz hasonló eredményeknek kell megjelennie:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Új adathalmaz beszúrása

1. Új kapcsolattartói rekord beszúrásához adjon meg egy `ContactRecord` osztályt:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Hozzon létre egy `ContactRecord` egy példányát, és helyezze egy tömbbe:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Mentse az új HBase a tömbbe:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Vizsgálja meg az eredményeket:

    ```scala  
    df.show()
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. A következő parancs beírásával zárjuk be a Spark shellt:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Következő lépések

* [Apache Spark HBase-összekötő](https://github.com/hortonworks-spark/shc)
