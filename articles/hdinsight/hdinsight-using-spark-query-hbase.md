---
title: HBase-adatok olvasása és írása a Sparksegítségével – Azure HDInsight
description: A Spark HBase-összekötő használatával adatokat olvashat és írhat egy Spark-fürtből egy HBase-fürtbe.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/24/2020
ms.openlocfilehash: 888f24e13ce67c878592068927383dd8cbfefa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623102"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Az Apache Spark használata Apache HBase-adatok írására és olvasására

Az Apache HBase-t általában alacsony szintű API-jával (beolvasások, beolvasások és putok) vagy az Apache Phoenix használatával egy SQL szintaxissal kérdezik le. Az Apache az Apache Spark HBase connector-t is biztosítja, amely kényelmes és hatékony alternatívája a HBase által tárolt adatok lekérdezésének és módosításának.

## <a name="prerequisites"></a>Előfeltételek

* Két különálló HDInsight-fürt van telepítve ugyanabban a [virtuális hálózatban.](./hdinsight-plan-virtual-network-deployment.md) Egy HBase és egy Spark legalább Spark 2.1 (HDInsight 3.6) telepítve. További információ: [Linux-alapú fürtök létrehozása a HDInsightban az Azure Portalhasználatával.](hdinsight-hadoop-create-linux-clusters-portal.md)

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](hdinsight-hadoop-linux-use-ssh-unix.md)

* A fürtök [elsődleges tárolóURI-séma.](hdinsight-hadoop-linux-information.md#URI-and-scheme) Ez a séma lenne wasb:// az Azure Blob Storage, abfs:// az Azure Data Lake Storage Gen2 vagy adl:// az Azure Data Lake Storage Gen1. Ha a blob storage biztonságos átvitele `wasbs://`engedélyezve van, az URI lesz.  Lásd még: [biztonságos átvitel](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Általános folyamat

A Spark-fürt HDInsight-fürt lekérdezésének engedélyezésére szolgáló magas szintű folyamat a következő:

1. Készítsen elő néhány mintaadatot a HBase-ben.
2. A hbase-site.xml fájl beszerzése a HBase fürt konfigurációs mappájából (/etc/hbase/conf).
3. Helyezzen el egy másolatot a hbase-site.xml a Spark 2 konfigurációs mappában (/etc/spark2/conf).
4. Futtassa `spark-shell` a Spark HBase-összekötőre való hivatkozást `packages` a Maven koordinátái alapján a beállításban.
5. Definiáljon egy katalógust, amely leképezi a sémát a Sparkról a HBase-re.
6. A HBase-adatok használata rdd vagy DataFrame API-k használatával.

## <a name="prepare-sample-data-in-apache-hbase"></a>Mintaadatok előkészítése az Apache HBase-ben

Ebben a lépésben hozzon létre és népesítsen be egy táblát az Apache HBase-ben, amelyet ezután lekérdezhet a Spark használatával.

1. A `ssh` paranccsal csatlakozhat a HBase-fürthöz. Az alábbi parancs szerkesztése a HBase-fürt nevének cseréjével, `HBASECLUSTER` majd írja be a parancsot:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. A `hbase shell` parancs segítségével indítsa el a HBase interaktív rendszerhéjat. Írja be a következő parancsot az SSH-kapcsolatba:

    ```bash
    hbase shell
    ```

3. A `create` paranccsal hozzon létre egy HBase táblát kétoszlopos családokkal. Írja be a következő parancsot:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. A `put` paranccsal értékeket szúrhat be egy adott tábla megadott sorának megadott oszlopába. Írja be a következő parancsot:

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

5. A `exit` parancs segítségével állítsa le a HBase interaktív rendszerhéjat. Írja be a következő parancsot:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Hbase-site.xml másolása a Spark-fürtbe

Másolja a hbase-site.xml fájlt a helyi tárolóból a Spark-fürt alapértelmezett tárolójának gyökerébe.  Az alábbi parancs szerkesztése a konfigurációnak megfelelően.  Ezután a megnyitott SSH-munkamenetből a HBase-fürtbe írja be a következő parancsot:

| Szintaktikai érték | Új érték|
|---|---|
|[URI-séma](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Módosítsa a tárhelynek megfelelően.  Az alábbi szintaxis a blob storage biztonságos átvitel engedélyezve van.|
|`SPARK_STORAGE_CONTAINER`|Cserélje le a Spark-fürthöz használt alapértelmezett tárolótároló nevére.|
|`SPARK_STORAGE_ACCOUNT`|Cserélje le a Spark-fürthöz használt alapértelmezett tárfióknévre.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Ezután lépjen ki az ssh-kapcsolatból a HBase-fürthöz.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Hbase-site.xml fájl beállítása a Spark-fürtre

1. Csatlakozzon a Spark-fürt főcsomópontjához az SSH használatával. Az alábbi parancs szerkesztése a Spark-fürt nevének cseréjével, `SPARKCLUSTER` majd írja be a parancsot:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Írja be az `hbase-site.xml` alábbi parancsot a Spark-fürt alapértelmezett tárolójáról a fürt helyi tárolójának Spark 2 konfigurációs mappájába való másolásához:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spark Shell futtatása a Spark HBase-összekötőre

1. A megnyitott SSH-munkamenetből a Spark-fürtbe írja be az alábbi parancsot egy szikrahéj elindításához:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Tartsa nyitva ezt a Spark Shell-példányt, és folytassa a következő lépéssel.

## <a name="define-a-catalog-and-query"></a>Katalógus és lekérdezés definiálása

Ebben a lépésben definiálegy katalógusobjektumot, amely leképezi a sémát az Apache Sparkról az Apache HBase-re.  

1. A nyitott Spark Shellben `import` adja meg a következő állításokat:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Írja be az alábbi parancsot a HBase-ben létrehozott Névjegyalbum tábla katalógusának meghatározásához:

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

    A kód a következőket teszi:  

     a. Katalógusséma definiálása a HBase nevű `Contacts`táblához.  
     b. Azonosítsa `key`a sorkulcsot , és rendelje hozzá a Sparkban használt oszlopneveket az oszlopcsaládhoz, az oszlopnévhez és az oszloptípushoz a HBase-ben használt módon.  
     c. A sorkulcsot részletesen meg kell határozni elnevezett`rowkey`oszlopként ( ), `cf` `rowkey`amelynek egy adott oszlopcsaládja van.  

1. Írja be az alábbi parancsot egy olyan `Contacts` metódus definiálásához, amely dataframe-et biztosít a táblázat körül a HBase-ben:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. A DataFrame egy példányának létrehozása:

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

1. Ideiglenes tábla regisztrálása, így lekérdezheti a HBase táblát a Spark SQL használatával:

    ```scala
    df.createTempView("contacts")
    ```

1. SQL-lekérdezés kiadása `contacts` a táblához:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Az alábbihoz hasonló eredményeket kell látnia:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Új adatok beszúrása

1. Új kapcsolattartó-rekord beszúrásához `ContactRecord` definiáljon egy osztályt:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Hozzon létre `ContactRecord` egy példányt, és tegye egy tömbbe:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Mentse az új adatok tömbjét a HBase-be:

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

1. Zárja be a szikrahéjat a következő parancs megadásával:

    ```scala
    :q
    ```

## <a name="next-steps"></a>További lépések

* [Apache Spark HBase csatlakozó](https://github.com/hortonworks-spark/shc)
