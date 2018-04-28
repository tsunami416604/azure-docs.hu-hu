---
title: Az Apache Spark on machine learning-alkalmazások Azure hdinsight létrehozása |} Microsoft Docs
description: Lépésenkénti útmutató Apache Spark gépi tanulás alkalmazás elkészítése a HDInsight Spark-fürtök Jupyter notebook használatával
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: 95daab2bd7bc57d01bc9e3c05404958edd71eecc
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Az Apache Spark on machine learning-alkalmazások Azure hdinsight létrehozása

Ismerje meg, hogyan hozhat létre egy Apache Spark machine learning, hdinsight Spark-fürt használó alkalmazások. Ez a cikk bemutatja, hogyan használható a Jupyter notebook érhető el a fürt és az alkalmazás teszteléséhez. Az alkalmazás alapértelmezés szerint minden fürtön található HVAC.csv mintaadatokat használja.

[MLlib](https://spark.apache.org/docs/1.1.0/mllib-guide.html) a Spark méretezhető machine learning függvénytár közös tanulási algoritmusok és segédprogramok, beleértve a besorolás, regressziós, fürtözés, együttműködést szűrés, granularitása csökkentése érdekében, valamint az alapul szolgáló álló optimalizálás primitívek.

## <a name="prerequisites"></a>Előfeltételek:

A következő elemmel kell rendelkeznie:

* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md). 

## <a name="data"></a>Az adatkészlet ismertetése

A következő adatokat jeleníti meg a cél hőmérséklet és a tényleges hőmérséklet bizonyos épületek, amely HVAC rendszer van telepítve. A **rendszer** oszlop felel meg a rendszer Azonosítót és a **SystemAge** oszlop felel meg a HVAC rendszer korábban már az épület helyén évek száma. Az adatok ebben az oktatóanyagban, előre jelezni, hogy egy épületet hotter vagy lesz colder alapján a cél hőmérséklet, a rendszer azonosítója és a rendszer élettartamát a használhatja.

![Spark machine learning például használt adatok pillanatképe](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Spark machine learning például használt adatok pillanatképe")

Az adatfájl **HVAC.csv**, itt található: **\HdiSamples\HdiSamples\SensorSampleData\hvac** a HDInsight-fürtök esetében.

## <a name="app"></a>Spark machine learning alkalmazás Spark MLlib
Ebben az alkalmazásban használja a Spark [ML adatcsatorna](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) használatával hajtja végre a dokumentum besorolását. Gépi tanulás folyamatok adjon meg egy egységes magas szintű API-k, amelyek segítenek a felhasználók létrehozása és gyakorlati gépi tanulási a folyamatok hangolására DataFrames platformra épül. A sorban a dokumentum felosztása szavak, szavak átalakítása egy numerikus szolgáltatás vektor, és végül a egy előrejelzési modell szolgáltatás vektorok és címkék létrehozása. A következő lépésekkel hozhat létre az alkalmazást.

1. Hozzon létre egy Jupyter notebook PySpark kernel használatával. Az utasításokért lásd: [Jupyter notebook létrehozása](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).
2. Ehhez a forgatókönyvhöz szükséges típusok importálása. Illessze be a következő kódrészletet a cella üres, és nyomja le az **SHIFT + ENTER**. 

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```
3. Betölteni az adatokat (hvac.csv), elemzéséhez és a modell betanításához segítségével. 

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0        

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    A kódrészletben határozza meg, amely összehasonlítja a tényleges hőmérséklet és a cél hőmérséklet függvény. A tényleges hőmérséklet nagyobb, az épület akkor gyakran használt adatok, a értékkel megjelölt **1.0**. Ellenkező esetben az épület cold, a értékkel megjelölt **0,0**. 

4. Konfigurálja a Spark machine learning-feldolgozási folyamat három szakaszból áll: jogkivonatokat létrehozó hashingTF és lr. 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Mi az, hogy egy folyamat és annak működéséről lásd: További információ <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark machine learning adatcsatorna</a>.

5. A folyamat a képzés dokumentumhoz oszlopnál.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Ellenőrizze a képzés dokumentum ellenőrzőpont az előrehaladást, az alkalmazással.
   
    ```PySpark
    training.show()
    ```
   
    Adja meg a kimenet az alábbihoz hasonló:

    ```
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    A kimeneti szemben a nyers CSV-fájl összehasonlítása. Az első sor a CSV-fájl például ezek az adatok rendelkezik:

    ![Kimeneti Spark machine learning például pillanatkép adatai](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "kimeneti adatok pillanatképének elkészítéséhez, Spark machine learning például")

    Figyelje meg, hogyan tényleges hőmérséklete kisebb, mint a cél hőmérséklet javasolhat az épület cold. Ezért a következő kimeneti, képzés **címke** az első sorban van **0,0**, ami azt jelenti, hogy az épület nincs gyakran használt adatok.

7. Készítse el adatok a betanított modell tanítását futtatásához. Ehhez az szükséges, adja meg egy azonosító és a rendszer kora (jelölése **SystemInfo** képzési kimenet), és a modell előrejelzi, hogy az adott rendszer-azonosító és a rendszer kor az épület hotter lesz-e (1.0 jelölik) vagy hűvösebben (0,0 jelölik).
   
    ```PySpark   
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF() 
    ```
8. Végezetül előrejelzéseket készítsen a a tesztadatokat. 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   Az előrejelzés első sorában az látható, hogy 20-as Azonosítójú és 25 évre rendszer korát HVAC rendszer, az épület működés közbeni (**előrejelzés = 1.0**). Az előrejelzés 0,0 DenseVector (0.49999) első értéke megfelel-e, és a második érték (0.5001) 1.0 előrejelzését felel meg. A kimenetben, annak ellenére, hogy a második értéke csak kis mértékben nagyobb, a modell bemutatja **előrejelzés = 1.0**.
10. Állítsa le a notebook az erőforrások kijelölése. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.

## <a name="anaconda"></a>Használjon Anaconda scikit – ismerje meg a könyvtár a Spark gépi tanulás
Az Apache Spark on hdinsight fürtök Anaconda-könyvtárakkal rendelkeznek. Ez magában foglalja a **scikit-további** könyvtár a machine Learning szolgáltatáshoz. A könyvtár is tartalmaz, amelyek közvetlenül a Jupyter notebook minta alkalmazásokat hozhatnak létre különböző adatkészletek. A scikit használatát bemutató példák-könyvtár ismerje meg, lásd: [ http://scikit-learn.org/stable/auto_examples/index.html ](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]:../hadoop/apache-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]:../hadoop/apache-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
