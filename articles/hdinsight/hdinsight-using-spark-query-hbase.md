---
title: "Olvasási és írási adatok a HBase - Azure HDInsight Spark használatával |} Microsoft Docs"
description: "A Spark HBase-összekötő segítségével olvashatja és Spark-fürt adatokat írhat HBase-fürtöt."
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: ccbcd1d9cb45da7076d73f71a2ed692e71816650
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Válassza a Spark írási és olvasási HBase adatok

Apache HBase jellemzően az alacsony szintű API (vizsgálatokat, lekérdezi és visszahelyezi) vagy egy SQL-szintaxis használatával Phoenix lekérik. Apache Spark HBase összekötő, amely kényelmes és performant alternatív lekérdezése, és módosítsa a HBase tárolt adatokat is mutatja.

## <a name="prerequisites"></a>Előfeltételek

* Egy Spark, a HDInsight fürtök és a HBase egy két különálló telepítve (HDInsight 3.6) Spark 2.1-es verzióját.
* A Spark-fürt kommunikálnia kell közvetlenül a HBase-fürtöt és minimális késéssel, ezért az ajánlott konfiguráció telepít, az azonos virtuális hálózatban lévő mindkét fürt. További információkért lásd: [az Azure portál használatával hdinsight létrehozása Linux-alapú fürtökön](hdinsight-hadoop-create-linux-clusters-portal.md).
* SSH hozzáférés az egyes csoportokban.
* Az egyes fürtökön alapértelmezett tároló elérésére.

## <a name="overall-process"></a>Teljes folyamata

A Spark-fürt a HDInsight-fürt lekérdezni engedélyezése folyamata a következőképpen történik:

1. Készítse elő a HBase néhány adatot.
2. Szerezzen be a hbase-site.xml fájl módosítása a HBase fürt konfigurációs mappából (/ etc/hbase/conf).
3. A hbase-site.xml másolatát helyezze el, a Spark 2 konfigurációs mappában (/ etc/spark2/conf).
4. Futtassa `spark-shell` a hivatkozik a Spark HBase-összekötő által a Maven koordinálja a `packages` lehetőséget.
5. Adja meg egy katalógust, amely leképezhető a HBase a Spark sémáját.
6. A HBase adatok RDD vagy DataFrame API-k segítségével kommunikál.

## <a name="prepare-sample-data-in-hbase"></a>A HBase mintaadatok előkészítése

Ebben a lépésben hozzon létre, és a HBase, majd lekérheti a Spark használatával egy egyszerű táblázat feltöltéséhez.

1. Az SSH használatával a HBase-fürt átjárócsomópontjához csatlakozik. További információkért lásd: [HDInsight SSH használatával csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Indítsa el a HBase:

        hbase shell

3. Hozzon létre egy `Contacts` a oszlopcsaláddal tábla `Personal` és `Office`:

        create 'Contacts', 'Personal', 'Office'

4. A minta néhány sornyi adatot betöltése:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Szerezzen be a hbase-site.xml a HBase fürtök

1. Az SSH használatával a HBase-fürt átjárócsomópontjához csatlakozik.
2. Másolja a hbase-site.xml helyi tárolóról a HBase-fürtöt alapértelmezett tároló:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. A HBase fürt használatával keresse meg a [Azure-portálon](https://portal.azure.com).
4. Válassza ki a Storage-fiókok. 

    ![Tárfiókok](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Válassza ki a tárfiókot, amely rendelkezik az alapértelmezett oszlopban jelölje a listában.

    ![Alapértelmezett tárfiók](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. A tárolási fiók panelen válassza ki a Blobok csempén.

    ![Blobok csempe](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. A tárolók, jelölje ki a tárolóhoz, amelybe a HBase fürt által használt.
8. A fájl listában válassza ki a `hbase-site.xml`.

    ![A HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. A Blob tulajdonságai panelen válassza ki töltse le és mentse `hbase-site.xml` a helyi számítógépen lévő helyre.

    ![Letöltés](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>A hbase-site.xml elhelyezése a Spark-fürt

1. A Spark fürt használatával keresse meg a [Azure-portálon](https://portal.azure.com).
2. Válassza ki a Storage-fiókok.

    ![Tárfiókok](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Válassza ki a tárfiókot, amely rendelkezik az alapértelmezett oszlopban jelölje a listában.

    ![Alapértelmezett tárfiók](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. A tárolási fiók panelen válassza ki a Blobok csempén.

    ![Blobok csempe](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. A tárolók, jelölje ki a tárolóhoz, amelybe a Spark-fürt által használt.
6. Válassza ki a feltöltés.

    ![Feltöltés](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Válassza ki a `hbase-site.xml` fájl korábban letöltötte a helyi számítógépre.

    ![A hbase-site.xml feltöltése](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Válassza ki a feltöltés.
9. Az SSH használatával a Spark-fürt átjárócsomópontjához csatlakozik.
10. Másolás `hbase-site.xml` helyi fürttároló Spark 2 konfigurációs mappájába a Spark-fürt alapértelmezett tárolóból:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Indítsa el a Spark hivatkozik a Spark HBase-összekötő

1. Az SSH használatával a Spark-fürt átjárócsomópontjához csatlakozik.
2. Indítsa el a spark felületet, adja meg a Spark HBase összekötő csomag:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11

3. A Spark rendszerhéj példány nyitva hagyja, és folytassa a következő lépéssel.

## <a name="define-a-catalog-and-query"></a>A katalógus és a lekérdezés megadása

Ebben a lépésben adja meg a katalógus objektum, amely leképezhető a HBase a Spark sémáját. 

1. A nyitott Spark rendszerhéj, futtassa a következő `import` utasításokat:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Adja meg a HBase-ben létrehozott kapcsolatok táblához tartozó katalógus:
    1. Adja meg a HBase táblában katalógus sémát `Contacts`.
    2. Azonosítsa a rowkey, `key`, és képezze le az oszlopok neveit, a Spark segítségével a oszlop termékcsalád, az oszlop neve, valamint a HBase-ben használt oszlop típusa.
    3. A rowkey is rendelkezik egy elnevezett oszlopot, részletesen meghatározva (`rowkey`), amely tartalmaz egy adott oszlopban termékcsalád `cf` a `rowkey`.

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

3. Adja meg egy módszer, amely körül a DataFrame a `Contacts` tábla HBase-ben:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. A DataFrame példányának létrehozása:

        val df = withCatalog(catalog)

5. A lekérdezés a DataFrame:

        df.show()

6. Két sornyi adatot kell megjelennie:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Regisztráljon az ideiglenes táblából, a HBase táblában Spark SQL használatával kérdezheti:

        df.registerTempTable("contacts")

8. Egy SQL-lekérdezést, szemben a `contacts` tábla:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Hasonló eredményeket kell megjelennie:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Új adatok beszúrása

1. Ügyfél új rekord beszúrásához definiálni kell egy `ContactRecord` osztály:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Hozzon létre egy példánya `ContactRecord` , és egy tömb:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Mentse az új adatokat tartalmazó tömb HBase:

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

* [Spark HBase összekötő](https://github.com/hortonworks-spark/shc)
