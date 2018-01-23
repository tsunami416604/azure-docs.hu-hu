---
title: "Hozzon létre egy Apache Spark gépi tanulás csővezeték - Azure HDInsight |} Microsoft Docs"
description: "Adatok folyamatok létrehozásához használja az Apache Spark gépi tanulás könyvtár."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: maxluk
ms.openlocfilehash: 238ab5f940fbea836b75e20b015ae16f22eef3e9
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-spark-machine-learning-pipeline"></a>Egy Spark machine learning folyamat létrehozása

Apache Spark méretezhető machine learning könyvtár (MLlib) számos lehetőséget kínál az elosztott környezetben való modellezési képességekkel. A Spark csomag [ `spark.ml` ](http://spark.apache.org/docs/latest/ml-pipeline.html) DataFrames épülő magas szintű API-k olyan készlete. Ezen API-k hogyan hozhat létre, és gyakorlati gépi tanulásra folyamatok hangolását.  *Gépi tanulás Spark* az MLlib DataFrame-alapú API, nem a régebbi RDD-alapú feldolgozási folyamat API hivatkozik.

A gépi tanulási (ML) feldolgozási folyamat különböző gépi tanulási algoritmus együtt összesítik teljes munkafolyamatot. Sok lépést kell feldolgozni, és tanuljanak az adatokból, igénylő algoritmusok sorozata lehet. Folyamatok a szakaszt, és a gépi tanulási folyamat sorrendje határozza meg. A MLlib a folyamat szakaszból jelölik PipelineStages, ahol egy átalakító és egy négyzetgyökének egyes feladatokat adott sorozata.

Átalakítók használva átalakítja a másik egy DataFrame algoritmust a `transform()` metódust. Például egy szolgáltatás átalakító sikerült olvassa el a DataFrame egy oszlopot, leképez egy másik oszlop, és egy új DataFrame kimeneti utótaggal csatlakoztatott oszloppal.

Egy négyzetgyökének absztrakciója tanulási algoritmusok és kitöltési vagy átalakítók létrehozásához a dataset betanítása felelős. Egy négyzetgyökének nevű metódus valósítja meg `fit()`, amely fogad egy DataFrame, és hozza létre a DataFrame, amely egy átalakító.

Minden egyes állapot nélküli példány átalakítók vagy egy négyzetgyökének rendelkezik saját egyedi azonosítója, amely a paramétereinek megadása. Ezek a paraméterek egyaránt használja egy egységes API-t.

## <a name="pipeline-example"></a>Folyamat – példa

Bemutatásához az ML-feldolgozási folyamat gyakorlati használatát, a példában a minta `HVAC.csv` adatfájlt, amely a HDInsight-fürtjéhez, Azure Storage vagy a Data Lake Store az alapértelmezett tároló előre betöltött előre. A fájl tartalmának megtekintéséhez nyissa meg a `/HdiSamples/HdiSamples/SensorSampleData/hvac` könyvtár. `HVAC.csv`HVAC a cél- és tényleges hőmérsékletek idejében tartalmaz (*fűtésrendszerek, a szellőzőrendszerek, a légkondicionáló*) rendszerek különböző épületben. A cél, hogy az adatok a modell betanítását, és az adott épület az előrejelzési hőmérséklet.

A következő kódot:

1. Határozza meg egy `LabeledDocument`, mely tárolja a `BuildingID`, `SystemInfo` (a rendszer azonosítót és életkora), és egy `label` (1.0 az épület túl gyakran használt adatok, ha más 0,0).
2. Létrehoz egy egyéni elemző függvény `parseDocument` , amely fogad egy adatsort (sor), és határozza meg, hogy az épület "Forró" összehasonlítva a tényleges hőmérséklet a célként megadott hőmérsékletet.
3. Az elemző akkor érvényes, ha az adatok kinyeréséhez.
4. Tanítási adatokat hoz létre.

```python
# The data structure (column meanings) of the data array:
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
data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Ez például az adatcsatorna rendelkezik három fázisból áll: `Tokenizer` és `HashingTF` (mindkét transzformátorok), és `Logistic Regression` (egy négyzetgyökének).  A kibontott és elemzett adatokat a `training` DataFrame halad keresztül a feldolgozási folyamat során `pipeline.fit(training)` nevezik.

1. Első lépésként `Tokenizer`, felosztja a `SystemInfo` bemeneti oszlopot (a rendszer azonosítója és a korszűrő értékek álló) egy `words` kimeneti oszlop. Ez új `words` a DataFrame felveszi az oszlopot. 
2. Második lépésként `HashingTF`, konvertálja az új `words` szolgáltatás vektorok oszlopot. Ez új `features` a DataFrame felveszi az oszlopot. A első két szakaszok transzformátorok. 
3. A harmadik szakasz `LogisticRegression`, egy négyzetgyökének, és így a folyamat meghívja a `LogisticRegression.fit()` módszer létrehozásához egy `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Az új megjelenítéséhez `words` és `features` által felvett oszlopok a `Tokenizer` és `HashingTF` transzformátorok és mintát a `LogisticRegression` négyzetgyökének, futtassa egy `PipelineModel.transform()` az eredeti DataFrame metódusa. Az éles kódban a következő lépéssel volna egy teszt DataFrame a képzés érvényesítéséhez.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

A `model` objektum már használható előrejelzéseket készítsen. Az ebben a machine learning alkalmazást, és részletesen is fut a teljes minta, lásd: [Build Apache Spark gépi tanulási a alkalmazások az Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Lásd még

* [Adattudomány Scala és Spark az Azure használatával](../../machine-learning/team-data-science-process/scala-walkthrough.md)
