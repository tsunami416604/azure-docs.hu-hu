---
title: Application Insights-naplók elemzése a Spark – Azure HDInsight segítségével
description: Ismerje meg, hogyan exportálhatja a blob storage-ban, és ezután a-naplók elemzése a Spark on HDInsight az Application Insights-naplókat.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.openlocfilehash: 951292a34f59fd143a7997571513a3c852bbce81
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497987"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Az Apache Spark on HDInsight az Application Insights-telemetrianaplók elemzése

Ismerje meg, hogyan használható [Apache Spark](https://spark.apache.org/) a HDInsight Application Insights telemetriai adatok elemzéséhez.

[A Visual Studio Application Insights](../../application-insights/app-insights-overview.md) egy elemzési szolgáltatás, amely a webes alkalmazások figyelésére. Az Application Insights által létrehozott telemetriaadatok exportálhatók az Azure Storage. Amint az adatok Azure Storage-ban, HDInsight segítségével elemezhetők.

## <a name="prerequisites"></a>Előfeltételek

* Egy alkalmazás, amely az Application Insights használatára van konfigurálva.

* Linux-alapú HDInsight-fürt létrehozásának ismerete. További információkért lásd: [HDInsight az Apache Spark létrehozása](apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]
  > A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy webböngészőben.

A következő erőforrások fejleszthetik és tesztelhetik az ebben a dokumentumban is használja:

* Application Insights telemetriai adatokat generálta egy [Node.js-webalkalmazások az Application Insights használatára konfigurált](../../application-insights/app-insights-nodejs.md).

* Egy Linux-alapú Spark on HDInsight fürt 3.5-ös verziója lett megadva az adatok elemzéséhez.

## <a name="architecture-and-planning"></a>Architektúra és tervezés

Az alábbi ábra az ebben a példában a service-architektúra:

![ábra, amely az Application Insights szolgáltatásból a blob storage, adatátvitel, és a Spark on HDInsight által feldolgozott azt](./media/apache-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Azure Storage tárterület

Az Application Insights beállítható úgy, hogy folyamatosan exportálhatja a blobokhoz telemetrikus adatokat. HDInsight el tudja olvasni a blobokban tárolt adatokat. Vannak azonban bizonyos követelményeknek, figyelembe kell venni:

* **Hely**: Ha a Tárfiók és a HDInsight különböző helyeken vannak, előfordulhat, hogy növelheti a késést. Kimenő adatforgalmi díjat akkor számítunk fel az adatok régiók közötti áthelyezése, költség, is nő.

    > [!WARNING]
    > HDInsight, mint egy másik helyen lévő Tárfiókok használata nem támogatott.

* **BLOB típusát**: HDInsight csak blokkblobokat támogat. Application Insights, az alapértelmezett érték blokkblobokat, így működnek a HDInsight alapértelmezés szerint.

A tárolóeszközök hozzáadása egy meglévő fürthöz további információkért lásd: a [további tárfiókok hozzáadása](../hdinsight-hadoop-add-storage.md) dokumentumot.

### <a name="data-schema"></a>Adatséma

Az Application Insights nyújt [adatmodell exportálása](../../application-insights/app-insights-export-data-model.md) blobok exportálják a telemetriai adatok formátuma. A jelen dokumentumban leírt lépések a Spark SQL használatával az adatok. Spark SQL automatikusan hozhat létre egy sémát a JSON-adatstruktúra Application Insights által naplózott számára.

## <a name="export-telemetry-data"></a>Telemetriai adatok exportálása

Kövesse a [folyamatos exportálást konfigurálni](../../application-insights/app-insights-export-telemetry.md) konfigurálása az Application Insights telemetriai adatok exportálása az Azure storage-blobba.

## <a name="configure-hdinsight-to-access-the-data"></a>Az adatok HDInsight konfigurálása

Egy HDInsight-fürtöt hoz létre, ha a tárfiók hozzáadása a fürt létrehozása során.

Az Azure Storage-fiók hozzáadásához egy meglévő fürthöz használja található információk a [további Tárfiókok hozzáadása](../hdinsight-hadoop-add-storage.md) dokumentumot.

## <a name="analyze-the-data-pyspark"></a>Az adatok elemzését: PySpark

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a Spark on HDInsight-fürt. Az a **Gyorshivatkozások** szakaszban jelölje be **fürt irányítópultjai**, majd válassza ki **Jupyter Notebook** fürt Dashboard__ szakaszából.

    ![A fürt irányítópultjai](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)

2. A Jupyter lap jobb felső sarkában válassza **új**, majd **PySpark**. Egy Python-alapú Jupyter Notebook tartalmazó új böngészőlapon nyílik meg.

3. Az első mező (nevű egy **cella**) lapon adja meg a következő szöveget:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Ez a kód a bemeneti adatok rekurzív módon elérési a könyvtárstruktúra Spark konfigurálja. Application Insights telemetria a rendszer egy hasonló könyvtárstruktúrát naplózza a `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Használat **SHIFT + ENTER** a kód futtatásához. A cella bal oldalán egy "\*" jelzi, hogy a kód a cellában lévő folyamatban van a zárójelek között jelenik meg. Ha kész, a "\*" cella alatti módosítások számát, illetve az alábbi szöveghez hasonló kimenet jelenik meg:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Egy új cella jön létre alább az elsőt. Adja meg a következő szöveget az új cellára. Cserélje le `CONTAINER` és `STORAGEACCOUNT` az Azure storage-fiók nevét és az Application Insights-adatokat tartalmazó blobtároló neve.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Használat **SHIFT + ENTER** végrehajtani ezt a cellát. Az alábbi szöveghez hasonló eredmény jelenik meg:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Vissza a wasb-elérési út a helye az Application Insights telemetriai adatokat. Módosítsa a `hdfs dfs -ls` adott vissza a wasb-elérési út használata a cellában. sor, és használja **SHIFT + ENTER** a cella ismét futtatni. Ezúttal az eredményeket kell megjelenítenie a telemetriai adatokat tartalmazó könyvtárakat.

   > [!NOTE]
   > Ebben a szakaszban a lépéseket további részében a `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` directory lett megadva. A könyvtárstruktúra eltérő lehet.

6. A következő cellába, írja be a következő kódot: csere `WASB_PATH` az előző lépésben az elérési útját.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ez a kód létrehozza a dataframe a folyamatos exportálás folyamat által exportált JSON-fájlokból. Használat **SHIFT + ENTER** futtatásához ezt a cellát.
7. A következő cellába adja meg, és futtassa a következő, a sémát, amely a Spark létre a JSON-fájlok megtekintéséhez:

   ```python
   jsonData.printSchema()
   ```

    A séma az egyes telemetriai az eltérő. Az alábbi példában a rendszer által létrehozott webes kéréseket séma (a tárolt adatok a `Requests` alkönyvtár):

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
8. Az adathalmaz regisztrálása ideiglenes táblaként, és az adatokra irányuló lekérdezések futtatása a következő használatával:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Ez a lekérdezés visszaadja az első 20 bejegyzést a város adatai ahol context.location.city értéke nem null.

   > [!NOTE]
   > A környezet struktúra Application Insights által naplózott összes telemetriai adat szerepel. Az városa elem nem lehet a naplók feltöltve. A séma segítségével azonosíthatja a más elemeket, lekérdezheti, ha a naplók adatokat is tartalmazhat.

    Ez a lekérdezés visszaadja az információkat az alábbi szöveghez hasonló:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>Az adatok elemzését: Scala

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a Spark on HDInsight-fürt. Az a **Gyorshivatkozások** szakaszban jelölje be **fürt irányítópultjai**, majd válassza ki **Jupyter Notebook** fürt Dashboard__ szakaszából.

    ![A fürt irányítópultjai](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)
2. A Jupyter lap jobb felső sarkában válassza **új**, majd **Scala**. Megjelenik egy új böngészőlap, amely tartalmazza a Jupyter Notebook Scala-alapú.
3. Az első mező (nevű egy **cella**) lapon adja meg a következő szöveget:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Ez a kód a bemeneti adatok rekurzív módon elérési a könyvtárstruktúra Spark konfigurálja. Application Insights telemetria a rendszer naplózza a hasonló könyvtárstruktúrát `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Használat **SHIFT + ENTER** a kód futtatásához. A cella bal oldalán egy "\*" jelzi, hogy a kód a cellában lévő folyamatban van a zárójelek között jelenik meg. Ha kész, a "\*" cella alatti módosítások számát, illetve az alábbi szöveghez hasonló kimenet jelenik meg:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Egy új cella jön létre alább az elsőt. Adja meg a következő szöveget az új cellára. Cserélje le `CONTAINER` és `STORAGEACCOUNT` blobtároló neve, amely tartalmazza az Application Insights és az Azure storage-fiók nevét naplózza.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Használat **SHIFT + ENTER** végrehajtani ezt a cellát. Az alábbi szöveghez hasonló eredmény jelenik meg:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Vissza a wasb-elérési út a helye az Application Insights telemetriai adatokat. Módosítsa a `hdfs dfs -ls` adott vissza a wasb-elérési út használata a cellában. sor, és használja **SHIFT + ENTER** a cella ismét futtatni. Ezúttal az eredményeket kell megjelenítenie a telemetriai adatokat tartalmazó könyvtárakat.

   > [!NOTE]
   > Ebben a szakaszban a lépéseket további részében a `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` directory lett megadva. Ez a könyvtár nem létezik, kivéve, ha a telemetriai adatok webes alkalmazásokhoz.

6. A következő cellába, írja be a következő kódot: csere `WASB\_PATH` az előző lépésben az elérési útját.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ez a kód létrehozza a dataframe a folyamatos exportálás folyamat által exportált JSON-fájlokból. Használat **SHIFT + ENTER** futtatásához ezt a cellát.

7. A következő cellába adja meg, és futtassa a következő, a sémát, amely a Spark létre a JSON-fájlok megtekintéséhez:

   ```scala
   jsonData.printSchema
   ```

    A séma az egyes telemetriai az eltérő. Az alábbi példában a rendszer által létrehozott webes kéréseket séma (a tárolt adatok a `Requests` alkönyvtár):

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

8. Az adathalmaz regisztrálása ideiglenes táblaként, és az adatokra irányuló lekérdezések futtatása a következő használatával:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Ez a lekérdezés visszaadja az első 20 bejegyzést a város adatai ahol context.location.city értéke nem null.

   > [!NOTE]
   > A környezet struktúra Application Insights által naplózott összes telemetriai adat szerepel. Az városa elem nem lehet a naplók feltöltve. A séma segítségével azonosíthatja a más elemeket, lekérdezheti, ha a naplók adatokat is tartalmazhat.
   >
   >

    Ez a lekérdezés visszaadja az információkat az alábbi szöveghez hasonló:

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

Az Apache Spark-adatok és az Azure-szolgáltatások használatával további példákért lásd az alábbi dokumentumokat:

* [Az Apache Spark és BI: interaktív adatelemzés a Spark on HDInsight használatával, BI-eszközökkel végrehajtása](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: a Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére a használata](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: használja a Spark on HDInsight az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](apache-spark-custom-library-website-log-analysis.md)

Információ létrehozása és alkalmazások futtatására a Spark az alábbi dokumentumokban talál:

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark fürtön a Livy használatával](apache-spark-livy-rest-interface.md)
