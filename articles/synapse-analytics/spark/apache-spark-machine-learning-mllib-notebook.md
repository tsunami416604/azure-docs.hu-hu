---
title: 'Oktatóanyag: gépi tanulási alkalmazás létrehozása Apache Spark MLlib'
description: Útmutató arról, hogyan használható a Apache Spark MLlib egy olyan Machine learning-alkalmazás létrehozásához, amely a logisztikai regresszió használatával elemzi az adatkészleteket.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 50429696c4cbe10c4723f6d4bb9c9499d9b775c5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450428"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Oktatóanyag: gépi tanulási alkalmazás létrehozása Apache Spark MLlib és az Azure szinapszis Analytics használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre Apache Spark [MLlib](https://spark.apache.org/mllib/) olyan gépi tanulási alkalmazás létrehozásához, amely egyszerű prediktív elemzést végez egy Azure Open-adatkészleten. A Spark beépített gépi tanulási kódtárakat biztosít. Ez a példa a *besorolást* a logisztikai regresszión keresztül használja.

A SparkML és a MLlib olyan alapvető Spark-kódtárak, amelyek számos, a gépi tanulási feladatokhoz hasznos segédprogramot biztosítanak, beleértve a következőkre alkalmas segédprogramokat:

- Osztályozás
- Regresszió
- Fürtözés
- Témakör modellezése
- Egyrészes értékek elbomlása (SVD) és a fő összetevők elemzése (PEM)
- A hipotézis tesztelése és a minta statisztikáinak kiszámítása

## <a name="understand-classification-and-logistic-regression"></a>A besorolás és a logisztikai regresszió ismertetése

Az *osztályozás*, amely egy népszerű gépi tanulási feladat, a bemeneti adatok kategóriákba rendezésének folyamata. A besorolási algoritmus feladata, hogy kiderítse, hogyan rendeljen hozzá *címkéket* a megadott bemeneti adatokhoz. Tegyük fel például, hogy egy gépi tanulási algoritmus, amely adatokat fogad el bemenetként, és két kategóriába osztja el az állományt: a készleteket, amelyeket érdemes értékesíteni és készleteket tárolni.

A *logisztikai regresszió* egy algoritmus, amelyet besoroláshoz használhat. A Spark logisztikai regressziós API-ját *bináris besoroláshoz* vagy a bemeneti adatok két csoportba való besorolásához lehet hasznos. További információ a logisztikai regressziókkal kapcsolatban: [wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Összefoglalva, a logisztikai regressziós folyamat egy *logisztikai függvényt* hoz létre, amellyel előre megjósolható, hogy egy bemeneti vektor egy csoportba vagy a másikba tartozik-e.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Prediktív elemzés – példa a New York-i taxi adataira

Ebben a példában a Spark használatával némi prediktív elemzést végezünk a New York-i taxi TRIPS-adatokról. Az adatokat az [Azure Open-adatkészletek](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)segítségével érheti el. Az adatkészlet ezen részhalmaza a Yellow taxi-utakról tartalmaz információkat, beleértve az egyes utazásokkal, a kezdési és befejezési időponttal és a hellyel, a díjszabással és egyéb érdekes attribútumokkal kapcsolatos információkat.

> [!IMPORTANT]
> Az adatok tárolási helyétől való kihúzása további költségekkel járhat.

A következő lépésekben olyan modellt fejlesztünk ki, amely azt jelzi, hogy egy adott utazáshoz tartozik-e tipp vagy sem.

## <a name="create-an-apache-spark--machine-learning-model"></a>Apache Spark Machine learning-modell létrehozása

1. Hozzon létre egy jegyzetfüzetet a PySpark kernel használatával. Az utasításokért lásd: [Jegyzetfüzet létrehozása](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importálja az alkalmazáshoz szükséges típusokat. Másolja és illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt, vagy futtassa a cellát a kód bal oldalán található kék lejátszás ikon használatával.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    A PySpark kernel miatt nem kell explicit módon létrehoznia a környezeteket. A Spark-környezet automatikusan létrejön az első kód cellájának futtatásakor.

## <a name="construct-the-input-dataframe"></a>A bemeneti dataframe felépítése

Mivel a nyers adatmennyiség parkettás formátumú, a Spark-környezettel közvetlenül is lehívhatja a fájlt a memóriába dataframe. Míg az alábbi kód az alapértelmezett beállításokat használja, az adattípusok és egyéb sémák attribútumainak leképezése is kényszeríthető, ha szükséges.

1. A következő sorok futtatásával hozzon létre egy Spark-dataframe a kód egy új cellába való beillesztésével. Ezzel lekéri az adatokat a megnyitott adatkészletek API-n keresztül. Az összes ilyen adatmennyiség körülbelül 1 500 000 000 sort hoz létre. A kiszolgáló nélküli Apache Spark készlet méretétől függően előfordulhat, hogy a nyers adatmennyiség túl nagy, vagy túl sok időt vesz igénybe. Ezt az adatmennyiséget lejjebb is szűrheti. A következő kódrészlet a start_date és a end_date használatával alkalmaz egy olyan szűrőt, amely egyetlen hónapot ad vissza.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Az egyszerű szűrés hátránya, hogy statisztikai szempontból az adatokra vonatkozó elfogultság is bevezethető. Egy másik módszer a Spark-ba épített mintavétel használata. A következő kód csökkenti az adatkészletet körülbelül 2000 sorra, ha a fenti kód után alkalmazza őket. Ezt a mintavételi lépést az egyszerű szűrő helyett, vagy az egyszerű szűrővel együtt lehet használni.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Most már megtekintheti az itt olvasható információt. Általában jobb, ha az adatokat egy részhalmazsal szeretné áttekinteni, és nem a teljes készletet az adatkészlet méretétől függően. A következő kód két módszert kínál az adatok megtekintésére: az előző, hogy az alapszintű és az utóbbi egy sokkal gazdagabb Grid-élményt biztosít, valamint az adatok grafikus megjelenítésének képességét.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. A generált adatkészlet méretétől függően, és a jegyzetfüzetet többször kell kísérletezni vagy futtatni, célszerű lehet a munkaterületen helyileg gyorsítótárazni az adatkészletet. Az explicit gyorsítótárazás három módon hajtható végre:

   - A dataframe helyi mentése fájlként
   - A dataframe mentése ideiglenes táblába vagy nézetbe
   - A dataframe mentése állandó táblázatként

A módszerek első két példája az alábbi példákban szerepel.

A temp tábla vagy nézet létrehozása különböző hozzáférési útvonalakat biztosít az adatelérési utakhoz, de csak a Spark-példány munkamenetének időtartamára tart.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Az adatok előkészítése

A nyers formában lévő adatforrások gyakran nem alkalmasak arra, hogy közvetlenül a modellbe továbbítsák őket. Egy sor műveletet kell végrehajtani az adatmennyiségen ahhoz, hogy egy olyan állapotba kerüljön, amelyben a modell fel tudja használni azt.

A négy műveleti osztályt a következő kódban hajtja végre:

- Kiugró/helytelen értékek eltávolítása szűréssel.
- Az oszlopok eltávolítása nem szükséges.
- A nyers adatokból származtatott új oszlopok létrehozása, hogy a modell hatékonyabban működjön, más néven featurization.
- Címkézés – mivel a rendszer bináris besorolást végez (ez lesz a tipp, vagy nem egy adott útvonalon), a tipp összegét 0 vagy 1 értékre kell konvertálnia.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Ekkor a rendszer a végső funkciók hozzáadásához egy második Pass-t továbbít az adaton.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Logisztikai regressziós modell létrehozása

A végső feladat a címkézett adatok átalakítása olyan formátumra, amelyet a logisztikai regresszió alapján lehet elemezni. A logisztikai regressziós algoritmus bemenetének a *label-Feature Vector pár* készletének kell lennie, ahol a *funkció vektor* a bemeneti pontot jelképező számok vektora. Ezért a kategorikus oszlopokat számmá kell alakítani. A `trafficTimeBins` és `weekdayString` oszlopoknak egész szám típusú ábrázolásra kell konvertálniuk. Az átalakítás végrehajtásához több módszer is rendelkezésre áll, azonban az ebben a példában szereplő megközelítés *OneHotEncoding*, közös megközelítés.

```python
# Since the sample uses an algorithm that only works with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Ez a művelet egy új dataframe eredményez, amely a megfelelő formátumban lévő összes oszloppal betanítja a modellt.

## <a name="train-a-logistic-regression-model"></a>Logisztikai regressziós modell betanítása

Az első feladata, hogy az adatkészletet egy betanítási csoportba, egy tesztelési vagy érvényesítési csoportba ossza fel. Az itt megjelenő felosztás tetszőleges, és különböző felosztott beállításokkal kell játszania, hogy látható legyen-e a modell hatása.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Most, hogy két DataFrames van, a következő feladat a modell képletének létrehozása és futtatása a betanítási DataFrame, majd a tesztelési DataFrame érvényesítve. A különböző kombinációk hatásának megtekintéséhez a modell képletének különböző verzióival kell kísérletezni.

> [!Note]
> A modell mentéséhez szüksége lesz a Storage blob adatközreműködői Azure-szerepkörre. A Storage-fiók területen navigáljon a Access Control (IAM) elemre, és válassza a **szerepkör-hozzárendelés hozzáadása** elemet. Rendeljen Storage blob-adatközreműködő Azure-szerepkört a SQL Database-kiszolgálóhoz. Ezt a lépést csak a tulajdonosi jogosultsággal rendelkező tagok hajthatják végre. A különböző Azure-beli beépített szerepkörökhöz tekintse meg ezt az [útmutatót](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

A cella kimenete:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Az előrejelzés vizuális ábrázolásának létrehozása

Most létrehozhat egy végső vizualizációt, amely segít a teszt eredményeinek indoklásában. A [Roc görbe](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) egy módszer az eredmény áttekintésére.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![ROC-görbe logisztikai regressziós tipp modellhez](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>A Spark-példány leállítása

Miután befejezte az alkalmazás futtatását, állítsa le a notebookot az erőforrások felszabadításához. ehhez zárja be a fület, vagy válassza a **munkamenet befejezése** elemet a jegyzetfüzet alján található állapot panelen.

## <a name="see-also"></a>Lásd még

- [Áttekintés: Apache Spark az Azure szinapszis Analytics szolgáltatásban](apache-spark-overview.md)

## <a name="next-steps"></a>További lépések

- [.NET Apache Spark dokumentációhoz](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark hivatalos dokumentáció](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> A hivatalos Apache Spark dokumentációja a Spark-konzol használatával működik, amely az Azure szinapszis Sparkon nem érhető el. Ehelyett használjon [jegyzetfüzetet](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) vagy [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) -élményt.
