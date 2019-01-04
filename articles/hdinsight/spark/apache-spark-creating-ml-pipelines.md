---
title: Hozzon létre egy Apache Spark machine learning-folyamat – Azure HDInsight
description: Az Apache Spark machine learning-kódtár használatával adatfolyamatok létrehozására.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: dbda20554b119bfb72b939cbeb7f19e0b9093b31
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597477"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Apache Spark Machine Learning-folyamat létrehozása

Az Apache Spark skálázható gépi tanulási kódtár (MLlib) egy elosztott környezetben modellező képességeket biztosít. A Spark-csomag [ `spark.ml` ](https://spark.apache.org/docs/latest/ml-pipeline.html) DataFrames épülő magas szintű API-k készlete. Ezen API-k segítségével hozhat létre, és gyakorlati gépi tanulási folyamatok finomhangolása.  *A Spark machine learning* az MLlib DataFrame-alapú API, nem a régebbi RDD-alapú folyamat API hivatkozik.

Machine learning (gépi tanulás) folyamat kombinálva különböző gépi tanulási algoritmus együtt teljes munkafolyamatot. Számos, dolgozza fel, és ismerje meg az adatokat, egy feladatütemezési algoritmusok igénylő szükséges lépéseket is lehet. A folyamatok a szinteket és a egy gépi tanulási folyamat sorrendje határozza meg. MLlib, a folyamat szakaszai képviseli PipelineStages, ahol egy átalakító és a egy Estimator egyes feladatokat konkrét sorrendje.

Átalakítón alakítja át az egyik DataFrame egy másikra algoritmust a `transform()` metódust. Például funkció átalakítón sikerült olvassa el a DataFrame egy számoszlop leképez egy másik oszlopot és egy új adathalmaz kimeneti rá hozzáfűzve leképezett oszloppal.

Egy Estimator tanulási algoritmus absztrakciója, és megfelel, és egy adatkészleten átalakítón előállításához képzésre felelős. Egy Estimator valósít meg egy metódust `fit()`, amely DataFrame fogadja, és egy adathalmaz, amely átalakítón eredményez.

Minden állapot nélküli példány átalakítón vagy egy Estimator rendelkezik a saját egyedi azonosítóját, amely paramétereinek megadása. Ezek a paraméterek megadása is használja egy egységes API-t.

## <a name="pipeline-example"></a>Folyamat – példa

Egy gépi Tanulási folyamat gyakorlati használatának bemutatása érdekében ez a példa a minta `HVAC.csv` adatfájlt, amely az alapértelmezett tároló a HDInsight-fürthöz, vagy az Azure Storage, vagy a Data Lake Storage előre betöltött származik. A fájl tartalmának megtekintéséhez lépjen a `/HdiSamples/HdiSamples/SensorSampleData/hvac` könyvtár. `HVAC.csv` a HVAC, ahányszor a cél- és tényleges hőmérsékletek tartalmaz (*fűtésrendszerek, a szellőzőrendszerek, a légkondicionáló*) rendszerek különböző épületben. A célja, hogy az adatokat a modell betanítását, és az előrejelzési hőmérséklet egy megadott létrehozásához.

A következő kódot:

1. Meghatározza egy `LabeledDocument`, melyik üzletek a `BuildingID`, `SystemInfo` (a rendszer azonosítót és a korszűrő), és a egy `label` (1.0-t, ha a az épület túl gyakori és egyéb 0.0).
2. Létrehoz egy egyéni elemző függvényt `parseDocument` , amely egy adatsort (sor) vesz igénybe, és határozza meg, hogy az épület "Forró" összehasonlítja a tényleges hőmérséklet a célként megadott hőmérsékletet.
3. Az elemző vonatkozik, az adatok kinyerésére.
4. Betanítási adatok hoz létre.

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
data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Ez a példa a folyamat három fázisa van: `Tokenizer` és `HashingTF` (mindkét transzformátorok), és `Logistic Regression` (egy Estimator).  A kivont és elemzett adatokat a `training` DataFrame végig a folyamat során `pipeline.fit(training)` nevezzük.

1. Első lépésként `Tokenizer`, felosztja a `SystemInfo` bemeneti oszlopot (amely a rendszer azonosítót és a korszűrő értékek) egy `words` kimeneti oszlop. Az új `words` az adathalmaz felveszi az oszlopot. 
2. A második szakaszban `HashingTF`, átalakítja az új `words` funkció vektorok oszlop. Az új `features` az adathalmaz felveszi az oszlopot. Ezen első két szakasza a következő transzformátorok. 
3. A harmadik szakasz `LogisticRegression`, egy Estimator, és így a folyamat meghívja a `LogisticRegression.fit()` előállításához metódus egy `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Megtekintheti az új `words` és `features` által felvett oszlopok a `Tokenizer` és `HashingTF` transzformátorok, és a egy mintát a `LogisticRegression` estimator, futtassa a `PipelineModel.transform()` metódus az eredeti adathalmaz. Az éles kódban a következő lépés lehet a teszt ellenőrzése a képzés DataFrame adja át.

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

A `model` objektum már használható, hogy előrejelzéseket végezzen. A machine learning-alkalmazás részletes ismertetését, valamint az azt futtató, a teljes minta: [hozhat létre Apache Spark machine learning-alkalmazások az Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Lásd még

* [Adatelemzés a Scala és az Apache Spark használatával az Azure-ban](../../machine-learning/team-data-science-process/scala-walkthrough.md)
