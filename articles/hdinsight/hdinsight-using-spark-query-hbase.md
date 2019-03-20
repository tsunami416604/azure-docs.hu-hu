---
title: Írási és olvasási HBase-adatok – Azure HDInsight Spark használata
description: A Spark a HBase-összekötő használatával olvasási és írási adatokat egy Spark-fürtöt a HBase-fürtöt.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 0ba61dc266add48577c3a382465ecb2cec9d2a05
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188033"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Az Apache Spark használata Apache HBase-adatok írására és olvasására

Az Apache HBase jellemzően az alacsony szintű API (vizsgálatokat, lekérdezi és helyez) vagy egy SQL-szintaxis használatával az Apache Phoenix lekérdezése követi. Az Apache is biztosít az Apache Spark HBase-összekötőt, ami egy kényelmes és nagy teljesítményű alternatív HBase által tárolt adatok lekérdezéséhez és módosításához.

## <a name="prerequisites"></a>Előfeltételek

* Legalább két különálló a HDInsight-fürtökkel, a egy HBase és a egy Spark-és Spark 2.1-es (HDInsight 3.6) telepítve van.
* A Spark-fürt közvetlenül kommunikálhat a HBase-fürt minimális késéssel, így az ajánlott konfiguráció az azonos virtuális hálózatba mindkét fürt üzembe helyezése kell. További információkért lásd: [a HDInsight az Azure portal használatával Linux-alapú fürtök](hdinsight-hadoop-create-linux-clusters-portal.md).
* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).
* A [URI-séma](/hdinsight-hadoop-linux-information#URI-and-scheme.md) a fürtök elsődleges tárhelyeként. Ez akkor lehet wasb: / / az Azure Blob Storage, abfs: / / az Azure Data Lake Storage Gen2 vagy adl: / / az Azure Data Lake Storage Gen1. Biztonságos átvitel engedélyezve van a Blob Storage vagy a Data Lake Storage Gen2, ha az URI a wasbs lesz: / / vagy abfss: / /, illetve lásd még a [biztonságos átvitelre](../storage/common/storage-require-secure-transfer.md).


## <a name="overall-process"></a>Általános folyamata

A Spark-fürt lekérdezni a HDInsight-fürt engedélyezése magas szintű folyamata a következőképpen történik:

1. Készítse elő a HBase mintaadatokat.
2. A hbase-site.xml fájlt, a HBase-fürt konfigurációs mappa (/ etc/hbase/conf) beszerzése.
3. Hbase-site.xml másolatát helyezze el, a Spark 2 configuration mappában (/ etc/spark2/conf).
4. Futtassa `spark-shell` a hivatkozó a Spark a HBase-összekötő által a Maven koordinálja a `packages` lehetőséget.
5. Adjon meg egy katalógus, amely leképezi a sémát, Spark, HBase.
6. A HBase-adatok az RDD vagy DataFrame API-k használatával kommunikálhat.

## <a name="prepare-sample-data-in-apache-hbase"></a>Adatmintavétel az Apache HBase előkészítése

Ebben a lépésben létrehozásához, és az Apache HBase, amely ezután lekérdezheti a Spark használatával egy egyszerű táblázat feltöltéséhez.

1. Csatlakozzon az SSH-val HBase-fürt főcsomópontjához. További információkért lásd: [HDInsight SSH használatával csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).  Az alábbi parancsot szerkesztése lecserélésével `HBASECLUSTER` a HBase-fürt nevére `sshuser` az az ssh felhasználói fiók neve, és írja be a parancsot.

    ```
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Adja meg az alábbi parancsot a HBase rendszerhéj elindításához:

        hbase shell

3. Adja meg az alábbi parancsot hozhat létre egy `Contacts` tábla a oszlopcsaláddal `Personal` és `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Adja meg a minta néhány sornyi adatot betölteni az alábbi parancsokat:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

5. Adja meg a kilépéshez a HBase rendszerhéj az alábbi parancsot:

        exit 

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Hbase-site.xml átmásolása a Spark-fürt
A hbase-site.xml másolja a helyi tárolóból a Spark-fürt alapértelmezett tárolója gyökerében.  Szerkessze az alábbi parancsot, hogy a konfigurációját tükrözzék.  Ezt követően SSH-munkamenetből a megnyitott, a HBase-fürtnek, adja meg a parancsot:

| Szintaxis-érték | Új érték|
|---|---|
|[URI-séma](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Módosítsa a tároló megfelelően.  Az alábbi szintaxisa a blob Storage-biztonságos átvitel használatára képes.|
|`SPARK_STORAGE_CONTAINER`|Cserélje le a Spark-fürt használt alapértelmezett tároló nevét.|
|`SPARK_STORAGE_ACCOUNT`|Cserélje le a használt Spark-fürt alapértelmezett tárfiókneve.|

```
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Hbase-site.xml helyezi a Spark-fürthöz

1. Csatlakozzon SSH-val a Spark-fürt főcsomópontjához.

2. Adja meg az alábbi parancsot a másolandó `hbase-site.xml` a Spark 2 konfigurációt tartalmazó mappa a helyi tárban a fürtön a Spark-fürt alapértelmezett storage-ból:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Futtassa a Spark-Shell hivatkozik a Spark a HBase-összekötő

1. SSH-munkamenetből a nyissa meg a Spark-fürthöz adja meg az alábbi parancsot egy spark-shell elindításához:

    ```
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Ne zárja be ezt a Spark-Shell-példányt, és folytassa a következő lépéssel.

## <a name="define-a-catalog-and-query"></a>Katalógus- és a lekérdezés definiálása

Ebben a lépésben megadhat egy katalógus objektum, amely leképezi a séma az Apache HBase, Apache Spark rendszerből.  

1. A Spark nyílt rendszerhéjban írja be a következő `import` utasításokat:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Adja meg az alábbi parancsot egy katalógust, az ügyfelek tábla definiálása létrehozott HBase:

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

    A kód elvégzi a következőket:  

     a. A HBase-táblára nevű katalógus sémát `Contacts`.  
     b. Azonosítsa a rowkey tulajdonságok esetén, mint `key`, és a Spark a oszlopcsalád, az oszlop neve, és a típusú oszlop, a HBase használt oszlopnevek leképezése.  
     c. A rowkey tulajdonságok esetén is rendelkezik egy elnevezett oszlopot, részletesen meghatározott (`rowkey`), amely rendelkezik egy adott oszlopcsalád `cf` , `rowkey`.  

3. Adja meg az alábbi parancsot egy metódust, amely körül a DataFrame biztosít meghatározásához a `Contacts` HBase táblájába:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Hozzon létre egy példányt az adathalmaz:

    ```scala
    val df = withCatalog(catalog)
    ```  

5. Az adathalmaz-lekérdezés:

    ```scala
    df.show()
    ```

6. Két sornyi adatot kell megjelennie:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Regisztrálja az ideiglenes táblát, így lekérdezheti a HBase-táblára Spark SQL-lel:

    ```scala
    df.createTempView("contacts")
    ```

8. Kiadása egy SQL-lekérdezést a `contacts` tábla:

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. Az alábbiakhoz hasonló eredményt kell látnia:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Szúrjon be új adatokat

1. Egy új kapcsolattartó rekord beszúrásához definiálni kell egy `ContactRecord` osztály:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Hozzon létre egy példányt `ContactRecord` , és tegye egy tömb:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. A HBase mentése új adatokkal tömbjét:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Vizsgálja meg az eredményeket:

    ```scala  
    df.show()
    ```

5. A következőhöz hasonló kimenetnek kell megjelennie:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>További lépések

* [Az Apache Spark, HBase-összekötő](https://github.com/hortonworks-spark/shc)
