---
title: 'Oktatóanyag: A Spark machine learning az Azure HDInsight application készítése'
description: Részletes útmutató Apache Spark Machine Learning-alkalmazások HDInsight Spark-fürtökön a Jupyter notebook használatával történő létrehozásához.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 54ef9ac72ffeebbf228a85768f6c2eb4b64862c9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433399"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-hdinsight"></a>Oktatóanyag: Az Apache Spark machine learning-alkalmazás a HDInsight létrehozása 

Ebben az oktatóanyagban elsajátíthatja, hogyan használhatja a [Jupyter Notebook](https://jupyter.org/) hozhat létre egy [Apache Spark](https://spark.apache.org/) machine learning-alkalmazás az Azure HDInsight. 

Az [MLib](https://spark.apache.org/docs/1.1.0/mllib-guide.html) a Spark skálázható Machine Learning-kódtára, amely gyakori tanulási algoritmusokat és segédeszközöket tartalmaz, beleértve a besorolást, regressziót, fürtözést, együttműködési szűrést, dimenziócsökkentést, valamint mögöttes optimalizálási primitíveket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az Apache Spark machine learning-alkalmazás fejlesztése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek:

Az oktatóanyag elkezdéséhez a következőkkel kell rendelkeznie:

* Végezze el az [Apache Spark-fürt az Azure HDInsightban történő létrehozását](apache-spark-jupyter-spark-sql.md) ismertető oktatóanyagot.

## <a name="understand-the-data-set"></a>Az adatkészlet értelmezése

Az alkalmazás a minta HVAC.csv fájl adatait használja, amely alapértelmezés szerint minden fürtön elérhető. A fájl helye: **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Az adatok néhány HVAC-rendszerrel felszerelt épület célhőmérsékletét és jelenlegi hőmérsékletét mutatják. A **System** (Rendszer) oszlop tartalmazza a rendszer-azonosítót, míg a **SystemAge** (Rendszer kora) oszlop azt mutatja, hogy az épületben hány éve működik a HVAC-rendszer. Az adatokból egy rendszer-azonosító és a rendszer kora alapján előrejelezhető, hogy egy épület melegebb vagy hidegebb lesz-e a célhőmérséklet alapján.

![Pillanatkép a Spark Machine Learning-példához használt adatokról](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Pillanatkép a Spark Machine Learning-példához használt adatokról")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Egy Spark Machine Learning-alkalmazás fejlesztése a Spark MLlib segítségével

Ebben az alkalmazásban egy Spark [ML-folyamat](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) által fogja elvégezni egy dokumentum besorolását. A gépi tanulási folyamatok egységes, magas szintű API-kat tartalmaznak. Ezek olyan adathalmazokra épülnek, amelyek segítenek a gépi tanulási folyamatok létrehozásában és finomhangolásában. A folyamat során a dokumentumokat szavakra osztja fel, a szavakat átalakítja egy numerikus vektorrá, majd végül a vektorok és címkék alapján létrehoz egy előrejelzési modellt. Az alkalmazást az alábbi lépések végrehajtásával hozhatja létre.

1. Hozzon létre egy Jupyter notebookot a PySpark-kernellel. Az utasításokért lásd: [Jupyter notebook létrehozása](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).
2. Importálja a forgatókönyvhöz szükséges típusokat. Illessze be a következő kódrészletet egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. 

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
3. Töltse be az adatokat (a hvac.csv fájlból), elemezze őket, és használja fel őket a modellje betanításához. 

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
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    A kódrészletben adjon meg egy függvényt, amely összehasonlítja a jelenlegi hőmérsékletet a célhőmérséklettel. Ha a jelenlegi hőmérséklet a magasabb, akkor az épület meleg, ezt az **1.0** érték jelzi. Ellenkező esetben az épület hideg, amit a **0.0** érték jelez. 

4. Konfigurálja a Spark Machine Learning-folyamatot, amely három lépést tartalmaz: tokenizer, hashingTF és lr. 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    További információ a folyamat és annak működéséről: <a href="https://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Apache Spark machine learning-folyamat</a>.

5. Igazítsa a folyamatot a betanítási dokumentumhoz.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Ellenőrizze a betanítási dokumentumban az alkalmazás előrehaladási állapotát.
   
    ```PySpark
    training.show()
    ```
   
    Az eredmény az alábbihoz hasonlóan fog kinézni:

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

    Hasonlítsa össze a kimenetet a nyers CSV-fájllal. Például a CSV-fájl első sora a következő adatokat tartalmazza:

    ![Pillanatkép a Spark Machine Learning-példa kimeneti adatairól](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Pillanatkép a Spark Machine Learning-példa kimeneti adatairól")

    Figyelje meg, hogy a tényleges hőmérséklet alacsonyabb, mint a célhőmérséklet, ami arra utal, hogy az épület hideg. Ezért a betanítási anyag kimenetének első sorában található **label** (címke) értéke **0.0**, ami azt jelenti, hogy az épület nem meleg.

7. Készítsen elő egy adathalmazt, amelyen lefuttatja a betanított modellt. Ehhez meg kell adnia a rendszer azonosítóját és korát (amit a tanítás kimenetében a **SystemInfo** tulajdonság jelöl), a modell pedig előrejelzi, hogy az adott azonosítójú és korú rendszerrel felszerelt épület melegebb (1.0) vagy hűvösebb (0.0) lesz-e.
   
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
8. Végül végezze el az előrejelzést a tesztadatok alapján. 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Az eredmény az alábbihoz hasonlóan fog kinézni:

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   Az előrejelzés első sorában az látható, hogy 20-as azonosítójú, 25 éves HVAC rendszer esetében az épület meleg (**előrejelzés = 1.0**). A DenseVector első értéke (0.49999) a 0.0 előrejelzésnek, a második értéke (0.5001) az 1.0 előrejelzésnek felel meg. A kimenetben – annak ellenére, hogy a második érték csak kicsivel nagyobb – a modell által mutatott előrejelzés: **prediction = 1.0**.
10. Állítsa le a notebookot az erőforrások felszabadításához. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ez a művelet leállítja és bezárja a notebookot.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Anaconda scikit-learn kódtár használata a Spark Machine Learninghez
A HDInsight-alapú Apache Spark-fürtök Anaconda-kódtárakat tartalmaznak. Ezek közé tartozik a **scikit-learn** gépi tanulási kódtár. A kódtár különböző adathalmazokat tartalmaz, amelyek használatával példaalkalmazások hozhatók létre közvetlenül egy Jupyter notebookból. Példák a scikit-learn kódtár használatára: [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Az Apache Spark machine learning-alkalmazás fejlesztése

A következő oktatóanyag azt mutatja be, hogyan használhatja az IntelliJ IDEA-t Spark-feladatokhoz. 

> [!div class="nextstepaction"]
> [Alkalmazás létrehozása a Scala Maven használatával intellij-vel](./apache-spark-create-standalone-application.md)

