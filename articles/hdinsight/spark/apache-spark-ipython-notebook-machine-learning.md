---
title: "Az Apache Spark on machine learning-alkalmazások Azure hdinsight létrehozása |} Microsoft Docs"
description: "Lépésenkénti útmutató Apache Spark gépi tanulás alkalmazás elkészítése a HDInsight Spark-fürtök Jupyter notebook használatával"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 8d4f29cab5e548c6ba4780e08c7c2a5b0c9fcefe
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Az Apache Spark on machine learning-alkalmazások Azure hdinsight létrehozása

Ismerje meg, hogyan hozhat létre egy Apache Spark machine learning, hdinsight Spark-fürt használó alkalmazások. Ez a cikk bemutatja, hogyan használható a Jupyter notebook érhető el a fürt és az alkalmazás teszteléséhez. Az alkalmazás alapértelmezés szerint minden fürtön található HVAC.csv mintaadatokat használja.

**Előfeltételek:**

Az alábbiakkal kell rendelkeznie:

* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md). 

## <a name="data"></a>Az adatkészlet ismertetése
Először az alkalmazás összeállítása, mielőtt ossza meg velünk, ismerje meg, amelynek azt létrehozása az alkalmazás és az elemzés végezzük el az adatokat az adatok szerkezete. 

Ebben a cikkben a mintát használjuk **HVAC.csv** adatfájlt, amely a HDInsight-fürthöz társított Azure Storage-fiók érhető el. A tárfiókon belül a fájl jelenleg **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Töltse le és nyissa meg a CSV-fájl lekérése az adatok pillanatképe.  

![Spark machine learning például használt adatok pillanatképe](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Spark machine learning például használt adatok pillanatképe")

Az adatok a cél hőmérséklet és a tényleges hőmérséklet HVAC-rendszerek telepítése épület jeleníti meg. Tételezzük fel, hogy a **rendszer** oszlop felel meg a rendszer Azonosítót és a **SystemAge** oszlop felel meg a HVAC rendszer korábban már az épület helyén évek száma.

Ezek az adatok előre jelezni, hogy egy épületet hotter vagy lesz colder alapján a cél hőmérséklet, a rendszer azonosítója és a rendszer élettartamát a használjuk.

## <a name="app"></a>Spark machine learning alkalmazás Spark MLlib
Ebben az alkalmazásban a Spark ML folyamat használatával hajtja végre a dokumentum besorolását használjuk. A folyamat azt a dokumentum felosztása szavakat, a szavakat átalakítása egy numerikus szolgáltatás vektoros és végül a egy előrejelzési modell szolgáltatás vektorok és címkék létrehozása. A következő lépésekkel hozhat létre az alkalmazást.

1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).   
2. A Spark-fürt panelén kattintson a **Fürt irányítópultja Dashboard** lehetőségre, majd a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.
   
   > [!NOTE]
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 
3. Hozzon létre új notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.
   
    ![Spark machine learning például Jupyter notebook létrehozása](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-create-notebook.png "Spark machine learning például Jupyter notebook létrehozása")
4. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. A felső részen kattintson a notebook nevére, és adjon meg egy könnyen megjegyezhető nevet.
   
    ![Adja meg a notebook neve Spark machine learning például](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-notebook-name.png "notebook nevezze el a Spark machine learning – példa")
5. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Ehhez a forgatókönyvhöz szükséges típusok importálása megkezdése. Illessze be a következő kódrészletet a cella üres, és nyomja le az **SHIFT + ENTER**. 
   
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
6. Meg kell most betölteni az adatokat (hvac.csv), elemzéséhez és a modell betanításához segítségével. Ehhez adja meg egy függvényt, amely ellenőrzi, hogy a tényleges hőmérséklet az épület nagyobb, mint a célként megadott hőmérsékletet. A tényleges hőmérséklet nagyobb, az épület akkor gyakran használt adatok, a értékkel megjelölt **1.0**. A tényleges hőmérséklet kevesebb, az épület akkor cold, a értékkel megjelölt **0,0**. 
   
    Illessze be az alábbi részlet egy cella üres, és nyomja le az **SHIFT + ENTER**.

        # List the structure of data for better understanding. Because the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

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


1. Konfigurálja a Spark machine learning-feldolgozási folyamat három szakaszból áll: jogkivonatokat létrehozó hashingTF és lr. Mi az, hogy egy folyamat és annak működéséről lásd: További információ <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark machine learning adatcsatorna</a>.
   
    Illessze be az alábbi részlet egy cella üres, és nyomja le az **SHIFT + ENTER**.
   
        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
2. A folyamat a képzés dokumentumhoz oszlopnál. Illessze be az alábbi részlet egy cella üres, és nyomja le az **SHIFT + ENTER**.
   
        model = pipeline.fit(training)
3. Ellenőrizze a képzés dokumentum ellenőrzőpont az előrehaladást, az alkalmazással. Illessze be az alábbi részlet egy cella üres, és nyomja le az **SHIFT + ENTER**.
   
        training.show()
   
    Adja meg a kimenet az alábbihoz hasonló:
   
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

    Lépjen vissza, és ellenőrizze a kimeneti szemben a nyers CSV-fájlt. Az első sor a CSV-fájl például ezek az adatok rendelkezik:

    ![Kimeneti Spark machine learning például pillanatkép adatai](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "kimeneti adatok pillanatképének elkészítéséhez, Spark machine learning például")

    Figyelje meg, hogyan tényleges hőmérséklete kisebb, mint a cél hőmérséklet javasolhat az épület cold. Ezért a következő kimeneti, képzés **címke** az első sorban van **0,0**, ami azt jelenti, hogy az épület nincs gyakran használt adatok.

1. Készítse el adatok a betanított modell tanítását futtatásához. Ehhez az szükséges, azt szeretné továbbítani egy rendszer-Azonosítót és a rendszer kora (jelölése **SystemInfo** képzési kimenet), és a modell volna előre jelezni, hogy az adott rendszer-azonosító és a rendszer kor az épület hotter lenne (1.0 jelölik) vagy (kimaradásával hűvösebben 0,0).
   
   Illessze be az alábbi részlet egy cella üres, és nyomja le az **SHIFT + ENTER**.
   
       # SystemInfo here is a combination of system ID followed by system age
       Document = Row("id", "SystemInfo")
       test = sc.parallelize([(1L, "20 25"),
                     (2L, "4 15"),
                     (3L, "16 9"),
                     (4L, "9 22"),
                     (5L, "17 10"),
                     (6L, "7 22")]) \
           .map(lambda x: Document(*x)).toDF() 
2. Végezetül előrejelzéseket készítsen a a tesztadatokat. Illessze be az alábbi részlet egy cella üres, és nyomja le az **SHIFT + ENTER**.
   
        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row
3. A következőhöz hasonló kimenetnek kell megjelennie:
   
       Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
       Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
       Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
       Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
       Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
       Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
   
   Az első sorból a előrejelzését, láthatja, hogy a 20-as Azonosítójú és 25 évre rendszer korát HVAC rendszer esetében az épület lesz működés közbeni (**előrejelzés = 1.0**). Az előrejelzés 0,0 DenseVector (0.49999) első értéke megfelel-e, és a második érték (0.5001) 1.0 előrejelzését felel meg. A kimenetben, annak ellenére, hogy a második értéke csak kis mértékben nagyobb, a modell bemutatja **előrejelzés = 1.0**.
4. Az alkalmazás futtatását követően állítsa le a notebookot az erőforrások felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.

## <a name="anaconda"></a>Használjon Anaconda scikit – ismerje meg a könyvtár a Spark gépi tanulás
Az Apache Spark on hdinsight fürtök Anaconda-könyvtárakkal rendelkeznek. Ez magában foglalja a **scikit-további** könyvtár a machine Learning szolgáltatáshoz. A könyvtár is tartalmaz, amelyek közvetlenül a Jupyter notebook minta alkalmazásokat hozhatnak létre különböző adatkészletek. A scikit használatát bemutató példák-könyvtár ismerje meg, lásd: [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: A Spark on HDInsight használata valós idejű streamelési alkalmazások összeállítására](apache-spark-eventhub-streaming.md)
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
