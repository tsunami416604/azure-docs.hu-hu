---
title: Application Insights naplóinak elemzése a Spark - Azure HDInsight |} Microsoft Docs
description: Megtudhatja, hogyan exportálják a blob-tároló, és a naplóinak majd elemzése a Spark on HDInsight az Application Insights-naplókat.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 883beae6-9839-45b5-94f7-7eb0f4534ad5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: larryfr
ms.openlocfilehash: 31068376e20b240a440432319e65f4e479163ee0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>A Spark on HDInsight az Application Insights telemetria naplóinak elemzése

Megtudhatja, hogyan válassza a Spark on HDInsight Application Insights telemetriai adatok elemzését.

[A Visual Studio Application Insights](../../application-insights/app-insights-overview.md) analytics szolgáltatás, amely figyeli a webes alkalmazások. Az Application Insights által létrehozott telemetriai adatok Azure Storage exportálhatja. Ha az adatokat az Azure Storage, HDInsight segítségével elemezze.

## <a name="prerequisites"></a>Előfeltételek

* Egy alkalmazás, amely az Application Insights használatára van konfigurálva.

* Linux-alapú HDInsight-fürt létrehozása ismeretét. További információkért lásd: [hozzon létre a Spark on HDInsight](apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]
  > A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Linux igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy webböngészőben.

A következő források a fejlesztés és tesztelés Ez a dokumentum használták:

* Application Insights telemetria adatok generálta egy [Application Insights használatára konfigurált Node.js webalkalmazás](../../application-insights/app-insights-nodejs.md).

* A Linux-alapú a Spark on HDInsight-fürt verziószáma 3.5 használatával elemezheti az adatokat.

## <a name="architecture-and-planning"></a>Architektúra és tervezése

A következő diagram azt ábrázolja, ebben a példában a service-architektúra:

![a blob storage az Application Insights áramló adatok jelennek meg, majd a Spark on HDInsight által feldolgozott diagramja](./media/apache-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Azure Storage tárterület

Az Application Insights beállítható úgy, hogy folyamatosan telemetriai adatainak exportálása blobokat. HDInsight majd elolvashatják a blobok tárolt adatokat. Vannak azonban olyan követelményekkel, amelyeket kell követnie:

* **Hely**: Ha a Tárfiók és a HDInsight különböző helyeken vannak, megnövelheti késés. Kimenő forgalom díjak adatok régiók közötti áthelyezése is vonatkozik, költség, is növeli.

    > [!WARNING]
    > A Storage-fiók egy másik helyen, mint a HDInsight használata nem támogatott.

* **BLOB-típusú**: HDInsight csak blokk blobokat támogat. Alkalmazás Insights alapértelmezés szerint használja a blokkblobok, úgy kell működnie alapértelmezés szerint a HDInsight.

A tárhely hozzáadása egy meglévő fürthöz információkért lásd: a [adja hozzá a további tárfiókok](../hdinsight-hadoop-add-storage.md) dokumentum.

### <a name="data-schema"></a>Adatséma

Az Application Insights biztosít [exportálja az adatokat az adatmodellbe](../../application-insights/app-insights-export-data-model.md) blobok exportálják a telemetriai adatok formátuma. A jelen dokumentumban leírt lépések Spark SQL használatával az adatokat. Spark SQL automatikusan hozhat létre a JSON-adatszerkezet az Application Insights által naplózott sémát.

## <a name="export-telemetry-data"></a>Telemetriai adatok exportálása

Kövesse a [konfigurálása a folyamatos exportálás](../../application-insights/app-insights-export-telemetry.md) konfigurálása az Application Insights telemetria adatok exportálása egy Azure storage-blobba.

## <a name="configure-hdinsight-to-access-the-data"></a>Az adatok eléréséhez HDInsight konfigurálása

HDInsight-fürtöt hoz létre, ha a tárfiók hozzáadása a fürt létrehozása során.

Az Azure Storage-fiók hozzáadása egy meglévő fürthöz, olvassa el a a [adja hozzá a további Tárfiókok](../hdinsight-hadoop-add-storage.md) dokumentum.

## <a name="analyze-the-data-pyspark"></a>Az adatok elemzése: PySpark

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki a Spark on HDInsight-fürt. Az a **Gyorshivatkozások** szakaszban jelölje be **fürt irányítópultok**, majd válassza ki **Jupyter Notebook** a fürt Dashboard__ szakaszából.

    ![A fürt irányítópultok](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)

2. Válassza ki a Jupyter oldal jobb felső sarkában **új**, majd **PySpark**. A Python-alapú Jupyter Notebook tartalmazó új böngészőlapon nyílik meg.

3. Az első mezőben (nevű egy **cella**) lapon adja meg a következő szöveget:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Ez a kód Spark rekurzív módon hozzáférését a könyvtárstruktúra a bemeneti adatok konfigurálja. Application Insights telemetria kerül a hasonló könyvtárszerkezete a `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Használjon **SHIFT + ENTER** futtatja a kódot. A cella bal oldalán egy "\*" annak jelzésére, hogy ezt a cellát a kód végrehajtott zárójelek között jelenik meg. Miután befejeződött, a "\*" módosítását egy számot, és a kimenet az alábbihoz hasonló a cella alább látható:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Egy új cella alatt az elsőt jön létre. Adja meg a következő szöveget a új cellára. Cserélje le `CONTAINER` és `STORAGEACCOUNT` az Azure storage-fiók nevét és az Application Insights-adatokat tartalmazó blob-tároló neve.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Használjon **SHIFT + ENTER** végrehajtani ezt a cellát. Az alábbi hasonló eredményt látja:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    A wasb visszaadott elérési út az Application Insights telemetria adatok helyét. Módosítsa a `hdfs dfs -ls` visszaadott wasb elérési utat használja a cellában. sor, és ezután **SHIFT + ENTER** újra futtatni a cella. Ebben az esetben az eredmények megjelenjen-e a telemetriai adatokat tartalmazó könyvtárak.

   > [!NOTE]
   > Az ebben a szakaszban a többi a `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` directory lett megadva. Lehet, hogy a könyvtárstruktúra különböző.

6. A következő cellában, írja be a következő kódot: cserélje le `WASB_PATH` az előző lépésben elérési úttal.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ez a kód egy dataframe a folyamatos exportálás folyamat által exportált JSON-fájlokat hoz létre. Használjon **SHIFT + ENTER** ezt a cellát futtatásához.
7. A következő cellában adja meg, és futtassa a következő, a séma, Spark hozott létre a JSON-fájlok megtekintéséhez:

   ```python
   jsonData.printSchema()
   ```

    Az egyes telemetriai adatokat a séma nem egyezik. A következő példa egy a webes kérelmek az létrehozott séma (tárolt adatok a `Requests` alkönyvtár):

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
8. Használja a következő ideiglenes táblából a dataframe regisztrálásához és a lekérdezés futtatása az adatok alapján:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Ez a lekérdezés város olyan információkat ad vissza a felső 20 rekordok ahol context.location.city értéke nem null.

   > [!NOTE]
   > A környezet struktúra megtalálható-e az Application Insights által naplózott összes telemetriai adat. A város elem nem lehet megadni a naplókban. A séma segítségével azonosíthatók a más elemeket lekérdezhető a naplók adatok szerepelhetnek.

    A lekérdezés által visszaadott adatokat az alábbihoz hasonló:

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

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki a Spark on HDInsight-fürt. Az a **Gyorshivatkozások** szakaszban jelölje be **fürt irányítópultok**, majd válassza ki **Jupyter Notebook** a fürt Dashboard__ szakaszából.

    ![A fürt irányítópultok](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)
2. Válassza ki a Jupyter oldal jobb felső sarkában **új**, majd **Scala**. Scala-alapú Jupyter Notebook tartalmazó új böngészőlapon jelenik meg.
3. Az első mezőben (nevű egy **cella**) lapon adja meg a következő szöveget:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Ez a kód Spark rekurzív módon hozzáférését a könyvtárstruktúra a bemeneti adatok konfigurálja. Application Insights telemetria kerül a hasonló könyvtárstruktúra `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Használjon **SHIFT + ENTER** futtatja a kódot. A cella bal oldalán egy "\*" annak jelzésére, hogy ezt a cellát a kód végrehajtott zárójelek között jelenik meg. Miután befejeződött, a "\*" módosítását egy számot, és a kimenet az alábbihoz hasonló a cella alább látható:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Egy új cella alatt az elsőt jön létre. Adja meg a következő szöveget a új cellára. Cserélje le `CONTAINER` és `STORAGEACCOUNT` naplózza az Azure storage-fiók nevét és az Application Insights tartalmazó blob-tároló neve.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Használjon **SHIFT + ENTER** végrehajtani ezt a cellát. Az alábbi hasonló eredményt látja:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    A wasb visszaadott elérési út az Application Insights telemetria adatok helyét. Módosítsa a `hdfs dfs -ls` visszaadott wasb elérési utat használja a cellában. sor, és ezután **SHIFT + ENTER** újra futtatni a cella. Ebben az esetben az eredmények megjelenjen-e a telemetriai adatokat tartalmazó könyvtárak.

   > [!NOTE]
   > Az ebben a szakaszban a többi a `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` directory lett megadva. Ez a könyvtár nem létezik, kivéve, ha a telemetriai adatok egy webalkalmazás.

6. A következő cellában, írja be a következő kódot: cserélje le `WASB\_PATH` az előző lépésben elérési úttal.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ez a kód egy dataframe a folyamatos exportálás folyamat által exportált JSON-fájlokat hoz létre. Használjon **SHIFT + ENTER** ezt a cellát futtatásához.

7. A következő cellában adja meg, és futtassa a következő, a séma, Spark hozott létre a JSON-fájlok megtekintéséhez:

   ```scala
   jsonData.printSchema
   ```

    Az egyes telemetriai adatokat a séma nem egyezik. A következő példa egy a webes kérelmek az létrehozott séma (tárolt adatok a `Requests` alkönyvtár):

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

8. Használja a következő ideiglenes táblából a dataframe regisztrálásához és a lekérdezés futtatása az adatok alapján:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Ez a lekérdezés város olyan információkat ad vissza a felső 20 rekordok ahol context.location.city értéke nem null.

   > [!NOTE]
   > A környezet struktúra megtalálható-e az Application Insights által naplózott összes telemetriai adat. A város elem nem lehet megadni a naplókban. A séma segítségével azonosíthatók a más elemeket lekérdezhető a naplók adatok szerepelhetnek.
   >
   >

    A lekérdezés által visszaadott adatokat az alábbihoz hasonló:

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

További példák a Spark használata adatokhoz és szolgáltatásokhoz az Azure-ban tekintse meg a következő dokumentumokat:

* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

Létrehozása és alkalmazások futtatása Spark kapcsolatos tudnivalókat lásd: a következő dokumentumokat:

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)
