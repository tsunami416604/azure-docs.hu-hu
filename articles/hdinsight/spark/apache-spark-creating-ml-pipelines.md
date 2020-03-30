---
title: Apache Spark-i gépi tanulási folyamat létrehozása - Azure HDInsight
description: Az Apache Spark gépi tanulási könyvtárával adatfolyamatokat hozhat létre az Azure HDInsightban.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: b0de9103fd022dc74e7c75017a602eb6701686fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494669"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Apache Spark Machine Learning-folyamat létrehozása

Az Apache Spark méretezhető gépi tanulási könyvtára (MLlib) modellezési képességeket biztosít egy elosztott környezetben. A Spark-csomag [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) a dataframes-ekre épülő magas szintű API-k készlete. Ezek az API-k segítenek a gyakorlati gépi tanulási folyamatok létrehozásában és hangolásában.  *A Spark machine learning* erre az MLlib DataFrame-alapú API-ra utal, nem a régebbi RDD-alapú folyamatAPI-ra.

A gépi tanulási (ML) folyamat egy teljes munkafolyamat, amely több gépi tanulási algoritmust kombinál. Az adatok feldolgozásához és az adatokból való tanuláshoz számos lépés szükséges, amelyek algoritmusok sorozatát igénylik. A folyamatok határozzák meg a gépi tanulási folyamat szakaszait és sorrendjét. Az MLlib-ben a folyamat szakaszait a PipelineStages egy adott sorozata képviseli, ahol egy Transzformátor és egy becslő hajt végre feladatokat.

A Transformer egy olyan algoritmus, amely átalakítja `transform()` az egyik DataFrame egy másik a módszer rel. Például egy szolgáltatás transzformátor beolvashatja a DataFrame egyik oszlopát, leképezheti egy másik oszlopra, és új DataFrame-et adhat ki a hozzá csatolt oszloppal.

Az Estimator a tanulási algoritmusok absztrakciója, és felelős egy adatkészlet illesztése vagy betanítása egy Transformer létrehozásához. Az Estimator egy DataFrame nevű `fit()`metódust valósít meg, amely elfogadja a DataFrame-et, és létrehoz egy DataFrame-et, amely egy Transzformátor.

A Transformer vagy a Estimator minden állapotnélküli példánya saját egyedi azonosítóval rendelkezik, amely a paraméterek megadásakor használatos. Mindkettő egy egységes API-t használ a paraméterek megadásához.

## <a name="pipeline-example"></a>Példa folyamatra

A gépi tanulási folyamat gyakorlati használatának bemutatásához ez a példa a HDInsight-fürt alapértelmezett tárolójára előre betöltött mintaadatfájlt `HVAC.csv` használja, akár az Azure Storage, akár a Data Lake Storage számára. A fájl tartalmának megtekintéséhez keresse `/HdiSamples/HdiSamples/SensorSampleData/hvac` meg a könyvtárat. `HVAC.csv`tartalmaz egy sor alkalommal mind a cél-és a tényleges hőmérséklet HVAC *(fűtés, szellőztetés, és légkondicionáló)* rendszerek különböző épületekben. A cél az, hogy a modell az adatokbe, és készítsen egy előrejelzési hőmérséklet egy adott épület.

A következő kód:

1. Egy `LabeledDocument`, amely tárolja `BuildingID` `SystemInfo` a , (a rendszer azonosítóját `label` és korát) és egy (1.0, ha az épület túl forró, 0.0 egyébként).
2. Létrehoz egy egyéni elemző `parseDocument` függvényt, amely egy sor (sor) adatot vesz fel, és meghatározza, hogy az épület "forró"-e a célhőmérséklet és a tényleges hőmérséklet összehasonlításával.
3. Az elemzőt alkalmazza a forrásadatok kinyerésekén.
4. Betanítási adatokat hoz létre.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

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
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Ebben a példában `Tokenizer` a `HashingTF` folyamat három szakaszból áll: és (mindkettő Transformers), és `Logistic Regression` (egy estimator).  A kinyert és a DataFrame-ben lévő adatok a `training` folyamaton keresztül folynak, amikor `pipeline.fit(training)` meghívják.

1. Az első `Tokenizer`szakasz a `SystemInfo` bemeneti oszlopot (amely a rendszerazonosítóból `words` és a korértékekből áll) egy kimeneti oszlopra osztja. Ez `words` az új oszlop hozzáadódik a DataFrame-hez. 
2. A második `HashingTF`szakasz az új `words` oszlopot szolgáltatásvektorokká alakítja. Ez `features` az új oszlop hozzáadódik a DataFrame-hez. Ez az első két szakasz a Transformers. 
3. A harmadik `LogisticRegression`szakasz , egy estimator, így `LogisticRegression.fit()` a csővezeték `LogisticRegressionModel`meghívja a metódust, hogy készítsen egy . 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

`words` A `features` `HashingTF` transzformátorok által `LogisticRegression` `PipelineModel.transform()` `Tokenizer` hozzáadott új és oszlopok, valamint a becslés mintamegtekintéséhez futtasson egy módszert az eredeti DataFrame-en. Az éles kódban a következő lépés az lenne, hogy egy teszt DataFrame a betanítás érvényesítéséhez.

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

Az `model` objektum most már használható előrejelzések. A gépi tanulási alkalmazás teljes mintáját és a futtatásához való részletes útmutatót az [Apache Spark gépi tanulási alkalmazások készítése az Azure HDInsightban című témakörben találja.](apache-spark-ipython-notebook-machine-learning.md)

## <a name="see-also"></a>Lásd még

* [Adatelemzés a Scala és az Apache Spark használatával az Azure-ban](../../machine-learning/team-data-science-process/scala-walkthrough.md)
