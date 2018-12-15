---
title: Írási és olvasási HBase-adatok – Azure HDInsight Spark használata
description: A Spark a HBase-összekötő használatával olvasási és írási adatokat egy Spark-fürtöt a HBase-fürtöt.
services: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: f76dfc8499d294c2c682c4d9c4cf2cd2f858432f
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408864"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Az Apache Spark használata Apache HBase-adatok írására és olvasására

Az Apache HBase jellemzően az alacsony szintű API (vizsgálatokat, lekérdezi és helyez) vagy egy SQL-szintaxis használatával az Apache Phoenix lekérdezése követi. Az Apache is biztosít az Apache Spark HBase-összekötőt, ami egy kényelmes és nagy teljesítményű alternatív HBase által tárolt adatok lekérdezéséhez és módosításához.

## <a name="prerequisites"></a>Előfeltételek

* Egy Spark, a HDInsight fürtök és a egy HBase két különálló Spark 2.1 (HDInsight 3.6) telepítve van.
* A Spark-fürt közvetlenül kommunikálhat a HBase-fürt minimális késéssel, így az ajánlott konfiguráció az azonos virtuális hálózatba mindkét fürt üzembe helyezése kell. További információkért lásd: [a HDInsight az Azure portal használatával Linux-alapú fürtök](hdinsight-hadoop-create-linux-clusters-portal.md).
* Minden egyes fürt SSH-hozzáférést.
* Minden egyes fürt alapértelmezett tárolója való hozzáférést.

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

1. Csatlakozzon az SSH-val HBase-fürt főcsomópontjához. További információkért lásd: [HDInsight SSH használatával csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).
2. A HBase rendszerhéj futtassa:

        hbase shell

3. Hozzon létre egy `Contacts` tábla a oszlopcsaláddal `Personal` és `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Minta néhány sornyi adatot betöltése:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>A HBase-fürtöt a hbase-site.xml beszerzése

1. Csatlakozzon az SSH-val HBase-fürt főcsomópontjához.
2. A hbase-site.xml másolja a helyi tárolóból a HBase-fürt alapértelmezett tárolója gyökérmappájában:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. A HBase fürt használatával keresse meg a [az Azure portal](https://portal.azure.com).
4. Válassza ki a Storage-fiókok. 

    ![Tárfiókok](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Válassza ki a tárfiókot, amelynek az alapértelmezett oszlop alatt jelölje be a listában.

    ![Alapértelmezett tárfiók](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. A tárolási fiók panelen válassza ki a Blobok csempét.

    ![Blobok csempe](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. A tárolók listájában válassza ki a tárolót, a HBase-fürt által használt.
8. Jelölje ki a fájl listában `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. A Blob tulajdonságai panelen válassza ki és mentse `hbase-site.xml` egy olyan helyre, a helyi gépen.

    ![Letöltés](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Hbase-site.xml helyezi a Spark-fürthöz

1. Keresse meg a Spark-fürtöt hozhat az [az Azure portal](https://portal.azure.com).
2. Válassza ki a Storage-fiókok.

    ![Tárfiókok](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Válassza ki a tárfiókot, amelynek az alapértelmezett oszlop alatt jelölje be a listában.

    ![Alapértelmezett tárfiók](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. A tárolási fiók panelen válassza ki a Blobok csempét.

    ![Blobok csempe](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. A tárolók listájában válassza ki a tárolót, a Spark-fürt által használt.
6. Feltöltés kiválasztása.

    ![Feltöltés](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Válassza ki a `hbase-site.xml` a helyi gépen korábban letöltött fájl.

    ![Hbase-site.xml feltöltése](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Feltöltés kiválasztása.
9. Csatlakozzon SSH-val a Spark-fürt főcsomópontjához.
10. Másolás `hbase-site.xml` a Spark 2 konfigurációt tartalmazó mappa a helyi tárban a fürtön a Spark-fürt alapértelmezett storage-ból:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Futtassa a Spark-Shell hivatkozik a Spark a HBase-összekötő

1. Csatlakozzon SSH-val a Spark-fürt főcsomópontjához.
2. Indítsa el a spark shell, a Spark-összekötő HBase csomag megadása:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11 --repositories http://repo.hortonworks.com/content/groups/public/

3. Ne zárja be ezt a Spark-Shell-példányt, és folytassa a következő lépéssel.

## <a name="define-a-catalog-and-query"></a>Katalógus- és a lekérdezés definiálása

Ebben a lépésben megadhat egy katalógus objektum, amely leképezi a séma az Apache HBase, Apache Spark rendszerből. 

1. A Spark nyílt rendszerhéjban futtassa a következő `import` utasításokat:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Adja meg a HBase létrehozott kapcsolatok táblához tartozó katalógus:
    1. A HBase-táblára nevű katalógus sémát `Contacts`.
    2. Azonosítsa a rowkey tulajdonságok esetén, mint `key`, és a Spark a oszlopcsalád, az oszlop neve, és a típusú oszlop, a HBase használt oszlopnevek leképezése.
    3. A rowkey tulajdonságok esetén is rendelkezik egy elnevezett oszlopot, részletesen meghatározott (`rowkey`), amely rendelkezik egy adott oszlopcsalád `cf` , `rowkey`.

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

3. Adja meg egy metódust, amely körül a DataFrame biztosít a `Contacts` HBase táblájába:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Hozzon létre egy példányt az adathalmaz:

        val df = withCatalog(catalog)

5. Az adathalmaz-lekérdezés:

        df.show()

6. Két sornyi adatot kell megjelennie:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Regisztrálja az ideiglenes táblát, így lekérdezheti a HBase-táblára Spark SQL-lel:

        df.registerTempTable("contacts")

8. Kiadása egy SQL-lekérdezést a `contacts` tábla:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Az alábbiakhoz hasonló eredményt kell látnia:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Szúrjon be új adatokat

1. Egy új kapcsolattartó rekord beszúrásához definiálni kell egy `ContactRecord` osztály:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Hozzon létre egy példányt `ContactRecord` , és tegye egy tömb:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. A HBase mentése új adatokkal tömbjét:

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. Vizsgálja meg az eredményeket:
    
        df.show()

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
