---
title: Alkalmazásinsight-naplók elemzése a Sparkkal – Azure HDInsight
description: Megtudhatja, hogyan exportálhatja az Application Insight-naplókat a blobstorage-ba, és hogyan elemezheti a naplókat a Spark segítségével a HDInsight-on.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 6fd7682f56fbe446904a4acdb39e78525f2523a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435240"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Az Application Insights telemetriai naplóinak elemzése az Apache Spark segítségével a HDInsighton

Ismerje meg, hogyan elemezheti az [Alkalmazásinsight](https://spark.apache.org/) telemetriai adatait az Apache Spark a HDInsighton.

[A Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) egy elemzési szolgáltatás, amely figyeli a webalkalmazásokat. Az Application Insights által létrehozott telemetriai adatok exportálhatók az Azure Storage-ba. Miután az adatok az Azure Storage-ban, HDInsight lehet elemezni.

## <a name="prerequisites"></a>Előfeltételek

* Az Application Insights használatára konfigurált alkalmazás.

* Linux alapú HDInsight-fürt létrehozásának ismerete. További információ: [Apache Spark létrehozása a HDInsighton.](apache-spark-jupyter-spark-sql.md)

* Egy webböngésző.

A dokumentum kifejlesztéséhez és teszteléséhez a következő erőforrásokat használták fel:

* Az Application Insights telemetriai adatai az [Application Insights használatára konfigurált Node.js webalkalmazás](../../azure-monitor/app/nodejs.md)használatával jönnek létre.

* Linux-alapú Spark a HDInsight fürt 3.5-ös verzióján az adatok elemzéséhez.

## <a name="architecture-and-planning"></a>Építészet és tervezés

Az alábbi ábra a példa szolgáltatásarchitektúráját mutatja be:

![Az Application Insightsból a blobstorage-ba, majd a Spark](./media/apache-spark-analyze-application-insight-logs/application-insights.png)

### <a name="azure-storage"></a>Azure Storage

Az Application Insights konfigurálható úgy, hogy folyamatosan exportálja a telemetriai adatokat a blobokba. A HDInsight ezután olvashatja a blobokban tárolt adatokat. Vannak azonban olyan követelmények, amelyeket be kell tartania:

* **Hely:** Ha a tárfiók és a HDInsight különböző helyeken vannak, növelheti a késést. Ez is növeli a költségeket, mivel a kimenő forgalom díjakat alkalmaznak a régiók közötti adatok ra.

    > [!WARNING]  
    > A HDInsight-tól eltérő helyen lévő tárfiók használata nem támogatott.

* **Blob típusa:** A HDInsight csak a blokkblobokat támogatja. Az Application Insights alapértelmezés szerint blokkblobok használatával, így alapértelmezés szerint a HDInsight.

A meglévő fürthöz való tároláshozzáadásáról a [További tárfiókok hozzáadása](../hdinsight-hadoop-add-storage.md) című dokumentumban talál további tárhelyfiókok hozzáadása című témakört.

### <a name="data-schema"></a>Adatséma

Az Application Insights [exportálási adatmodell-információkat](../../azure-monitor/app/export-data-model.md) biztosít a blobokba exportált telemetriai adatformátumhoz. A jelen dokumentum lépései a Spark SQL használatával dolgoznak az adatokkal. A Spark SQL automatikusan létrehozhat egy sémát az Application Insights által naplózott JSON-adatstruktúra számára.

## <a name="export-telemetry-data"></a>Telemetriai adatok exportálása

Kövesse a [Folyamatos exportálás konfigurálása](../../azure-monitor/app/export-telemetry.md) című szakaszlépéseit, hogy konfigurálja az Application Insights telemetriai adatokat egy Azure Storage-blobba.

## <a name="configure-hdinsight-to-access-the-data"></a>A HDInsight konfigurálása az adatok eléréséhez

Ha HDInsight-fürtöt hoz létre, adja hozzá a tárfiókot a fürt létrehozása során.

Az Azure Storage-fiók hozzáadása egy meglévő fürthöz, használja a [további tárfiókok hozzáadása](../hdinsight-hadoop-add-storage.md) dokumentumban található információkat.

## <a name="analyze-the-data-pyspark"></a>Az adatok elemzése: PySpark

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/jupyter` meg a CLUSTERNAME helyhez a fürt nevét.

2. A Jupyter lap jobb felső sarkában válassza az **Új**lehetőséget, majd a **PySpark**lehetőséget. Megnyílik egy python-alapú Jupyter notebookot tartalmazó új böngészőlap.

3. Az oldal első mezőjébe **(cella)** írja be a következő szöveget:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Ez a kód úgy konfigurálja a Sparkot, hogy rekurzívan férjen hozzá a bemeneti adatok könyvtárstruktúrájához. Az Application Insights telemetriai adatait a `/{telemetry type}/YYYY-MM-DD/{##}/`rendszer a címéhez hasonló könyvtárstruktúrába naplózza.

4. A kód futtatásához használja a **SHIFT+ENTER billentyűkombinációt.** A cella bal oldalán egy\*' ' jelenik meg a zárójelek között, jelezve, hogy a cellában lévő kód végrehajtása folyamatban van. Miután befejeződött,\*a ' ' számra változik, és a következő szöveghez hasonló kimenet jelenik meg a cella alatt:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Az első alatt új cella jön létre. Írja be a következő szöveget az új cellába. Cserélje `CONTAINER` `STORAGEACCOUNT` le, és az Azure Storage-fiók nevét és blob tároló nevét, amely tartalmazza az Application Insights-adatokat.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    A cella végrehajtásához használja a **SHIFT+ENTER** billentyűkombinációt. A következő höz hasonló eredmény jelenik meg:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    A visszaadott wasbs elérési út az Application Insights telemetriai adatok helye. Módosítsa `hdfs dfs -ls` a cella vonalát úgy, hogy a visszaadott wasbs elérési út legyen, majd a **SHIFT+ENTER billentyűkombinációval** futtassa újra a cellát. Ezúttal az eredmények nek meg kell jeleníteniük a telemetriai adatokat tartalmazó könyvtárakat.

   > [!NOTE]  
   > A szakasz ban leírt lépések további `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` részében a könyvtárat használták. A könyvtárszerkezet eltérő lehet.

6. A következő cellába írja be `WASB_PATH` a következő kódot: Cserélje le az előző lépés elérési útját.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ez a kód a folyamatos exportálási folyamat által exportált JSON-fájlokból hoz létre egy adatkeretet. A cella futtatásához használja a **SHIFT+ENTER** billentyűkombinációt.
7. A következő cellában írja be és futtassa a következőket a Spark által a JSON-fájlokhoz létrehozott séma megtekintéséhez:

   ```python
   jsonData.printSchema()
   ```

    A telemetriai adatok egyes típusainak sémája eltérő. A következő példa a webes kérelmekhez (az `Requests` alkönyvtárban tárolt adatokhoz) létrehozott séma:

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Az alábbi módon regisztrálhatja az adatkeretet ideiglenes táblaként, és lekérdezést futtathat az adatokon:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Ez a lekérdezés a 20 legfontosabb rekord városadatait adja vissza, ahol a context.location.city értéke nem null.

   > [!NOTE]  
   > A környezeti struktúra jelen van az Application Insights által naplózott összes telemetriai adatokban. Előfordulhat, hogy a városelem nincs feltöltve a naplókban. A séma segítségével azonosíthatja azokat az egyéb elemeket, amelyek lekérdezheti a naplók adatait tartalmazó adatokat.

    Ez a lekérdezés a következő szöveghez hasonló információkat ad vissza:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>Az adatok elemzése: Scala

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/jupyter` meg a CLUSTERNAME helyhez a fürt nevét.

2. A Jupyter lap jobb felső sarkában válassza az **Új**lehetőséget, majd a **Scala**lehetőséget. Megjelenik egy új böngészőlap, amely egy Scala-alapú Jupyter notebookot tartalmaz.

3. Az oldal első mezőjébe **(cella)** írja be a következő szöveget:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Ez a kód úgy konfigurálja a Sparkot, hogy rekurzívan férjen hozzá a bemeneti adatok könyvtárstruktúrájához. Az Application Insights telemetriai adatait `/{telemetry type}/YYYY-MM-DD/{##}/`a rendszer a rendszerhez hasonló könyvtárstruktúrába naplózza.

4. A kód futtatásához használja a **SHIFT+ENTER billentyűkombinációt.** A cella bal oldalán egy\*' ' jelenik meg a zárójelek között, jelezve, hogy a cellában lévő kód végrehajtása folyamatban van. Miután befejeződött,\*a ' ' számra változik, és a következő szöveghez hasonló kimenet jelenik meg a cella alatt:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Az első alatt új cella jön létre. Írja be a következő szöveget az új cellába. Cserélje `CONTAINER` `STORAGEACCOUNT` le, és az Azure Storage-fiók nevét és blob tároló nevét, amely tartalmazza az Application Insights-naplók.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    A cella végrehajtásához használja a **SHIFT+ENTER** billentyűkombinációt. A következő höz hasonló eredmény jelenik meg:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    A visszaadott wasbs elérési út az Application Insights telemetriai adatok helye. Módosítsa `hdfs dfs -ls` a cella vonalát úgy, hogy a visszaadott wasbs elérési út legyen, majd a **SHIFT+ENTER billentyűkombinációval** futtassa újra a cellát. Ezúttal az eredmények nek meg kell jeleníteniük a telemetriai adatokat tartalmazó könyvtárakat.

   > [!NOTE]  
   > A szakasz ban leírt lépések további `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` részében a könyvtárat használták. Előfordulhat, hogy ez a könyvtár csak akkor létezik, ha a telemetriai adatok egy webalkalmazáshoz.

6. A következő cellába írja be `WASB\_PATH` a következő kódot: Cserélje le az előző lépés elérési útját.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ez a kód a folyamatos exportálási folyamat által exportált JSON-fájlokból hoz létre egy adatkeretet. A cella futtatásához használja a **SHIFT+ENTER** billentyűkombinációt.

7. A következő cellában írja be és futtassa a következőket a Spark által a JSON-fájlokhoz létrehozott séma megtekintéséhez:

   ```scala
   jsonData.printSchema
   ```

    A telemetriai adatok egyes típusainak sémája eltérő. A következő példa a webes kérelmekhez (az `Requests` alkönyvtárban tárolt adatokhoz) létrehozott séma:

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Az alábbi módon regisztrálhatja az adatkeretet ideiglenes táblaként, és lekérdezést futtathat az adatokon:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Ez a lekérdezés a 20 legfontosabb rekord városadatait adja vissza, ahol a context.location.city értéke nem null.

   > [!NOTE]  
   > A környezeti struktúra jelen van az Application Insights által naplózott összes telemetriai adatokban. Előfordulhat, hogy a városelem nincs feltöltve a naplókban. A séma segítségével azonosíthatja azokat az egyéb elemeket, amelyek lekérdezheti a naplók adatait tartalmazó adatokat.

    Ez a lekérdezés a következő szöveghez hasonló információkat ad vissza:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>További lépések

További példák az Apache Spark használatával az Azure-beli adatok kal és szolgáltatásokkal való együttműködésre:

* [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](apache-spark-custom-library-website-log-analysis.md)

A Spark-alkalmazások létrehozásáról és futtatásáról az alábbi dokumentumokban talál további információt:

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)
