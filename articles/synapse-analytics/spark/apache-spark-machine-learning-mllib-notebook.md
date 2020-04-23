---
title: Gépi tanulási alkalmazás létrehozása Apache Spark MLlib és az Azure szinapszis Analytics használatával
description: Megtudhatja, hogyan használhatja a Apache Spark MLlib olyan gépi tanulási alkalmazások létrehozásához, amelyek a besorolást a logisztikai regresszió használatával elemzik.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 25d11d2cf41f8653c5a54007f121c1251bb24b1f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096299"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Gépi tanulási alkalmazás létrehozása Apache Spark MLlib és az Azure szinapszis Analytics használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre Apache Spark [MLlib](https://spark.apache.org/mllib/) olyan gépi tanulási alkalmazás létrehozásához, amely egyszerű prediktív elemzést végez egy Azure Open-adatkészleten. A Spark beépített gépi tanulási kódtárakat biztosít. Ez a példa a *besorolást* a logisztikai regresszión keresztül használja.

A MLlib egy alapvető Spark-könyvtár, amely számos segédprogramot biztosít a gépi tanulási feladatokhoz, beleértve a következőkre alkalmas segédprogramokat:

- Osztályozás
- Regresszió
- Fürtözés
- Témakör modellezése
- Egyrészes értékek elbomlása (SVD) és a fő összetevők elemzése (PEM)
- A hipotézis tesztelése és a minta statisztikáinak kiszámítása

## <a name="understand-classification-and-logistic-regression"></a>A besorolás és a logisztikai regresszió ismertetése

Az *osztályozás*, amely egy népszerű gépi tanulási feladat, a bemeneti adatok kategóriákba rendezésének folyamata. A besorolási algoritmus feladata, hogy kiderítse, hogyan rendeljen hozzá *címkéket* a megadott bemeneti adatokhoz. Tegyük fel például, hogy egy gépi tanulási algoritmus, amely adatokat fogad el bemenetként, és két kategóriába osztja el az állományt: a készleteket, amelyeket érdemes értékesíteni és készleteket tárolni.

A *logisztikai regresszió* egy algoritmus, amelyet besoroláshoz használhat. A Spark logisztikai regressziós API-ját *bináris besoroláshoz*vagy a bemeneti adatok két csoportba való besorolásához lehet hasznos. További információ a logisztikai regressziókkal kapcsolatban: [wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Összefoglalva, a logisztikai regressziós folyamat egy *logisztikai függvényt* hoz létre, amellyel előre megjósolható, hogy egy bemeneti vektor egy csoportba vagy a másikba tartozik-e.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Prediktív elemzés – példa a New York-i taxi adataira

Ebben a példában a Spark használatával némi prediktív elemzést végezünk a New York-i taxi TRIPS-adatokról. Az adatokat az [Azure Open-adatkészletek](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)segítségével érheti el. Az adatkészlet ezen részhalmaza a Yellow taxi-utakról tartalmaz információkat, beleértve az egyes utazásokkal, a kezdési és befejezési időponttal és a hellyel, a díjszabással és egyéb érdekes attribútumokkal kapcsolatos információkat.

> [!IMPORTANT]
> Az adatok tárolási helyétől való kihúzása további költségekkel járhat.

A következő lépésekben olyan modellt fejlesztünk ki, amely azt jelzi, hogy egy adott utazáshoz tartozik-e tipp vagy sem.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Apache Spark MLlib Machine learning-alkalmazás létrehozása

1. Hozzon létre egy jegyzetfüzetet a PySpark kernel használatával. Az utasításokért lásd: [Jegyzetfüzet létrehozása](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importálja az alkalmazáshoz szükséges típusokat. Másolja és illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER**billentyűkombinációt, vagy futtassa a cellát a kód bal oldalán található kék lejátszás ikon használatával.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
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

1. A következő sorok futtatásával hozzon létre egy Spark-dataframe a kód egy új cellába való beillesztésével. Az első szakasz az Azure Storage elérési adatait rendeli hozzá a változókhoz. A második szakasz lehetővé teszi, hogy a Spark távolról is beolvassa a blob Storage-ból. A kód utolsó sora a parkettát olvassa, de ezen a ponton nem töltődik be az adatmennyiség.

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. Az összes ilyen adatmennyiség körülbelül 1 500 000 000 sort hoz létre. A Spark-készlet (előzetes verzió) méretétől függően előfordulhat, hogy a nyers adatmennyiség túl nagy, vagy túl sok időt vesz igénybe. Ezt az adatmennyiséget lejjebb is szűrheti. Ha szükséges, adja hozzá a következő sorokat az adatszűréshez a körülbelül 2 000 000 sorra a rugalmasabb felhasználói élmény érdekében. Ezekkel a paraméterekkel egy hetet lehet lekérni.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Az egyszerű szűrés hátránya, hogy statisztikai szempontból az adatokra vonatkozó elfogultság is bevezethető. Egy másik módszer a Spark-ba épített mintavétel használata. A következő kód csökkenti az adatkészletet körülbelül 2000 sorra, ha a fenti kód után alkalmazza őket. Ezt a mintavételi lépést az egyszerű szűrő helyett, vagy az egyszerű szűrővel együtt lehet használni.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Most már megtekintheti az itt olvasható információt. Általában jobb, ha az adatokat egy részhalmazsal szeretné áttekinteni, és nem a teljes készletet az adatkészlet méretétől függően. A következő kód két módszert kínál az adatok megtekintésére: az előző, hogy az alapszintű és az utóbbi egy sokkal gazdagabb Grid-élményt biztosít, valamint az adatok grafikus megjelenítésének képességét.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. A generált adatkészlet méretétől és a jegyzetfüzet többszöri kipróbálásának vagy futtatásának szükségessége alapján célszerű lehet az adatkészlet helyi gyorsítótárazása a munkaterületen. Az explicit gyorsítótárazás három módon végezhető el:

   - A dataframe helyi mentése fájlként
   - A dataframe mentése ideiglenes táblába vagy nézetbe
   - A dataframe mentése állandó táblázatként

A módszerek első két példája az alábbi példákban szerepel.

A temp tábla vagy nézet létrehozása különböző hozzáférési útvonalakat biztosít az adatelérési utakhoz, de csak a Spark-példány munkamenetének időtartamára tart.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Az adatgyűjtés ismertetése

Általában a *feltáró adatelemzés* (EDA) egy fázisán haladhat át, hogy megismerje az adatgyűjtést. A következő kód három különböző vizualizációt mutat be a tippekkel kapcsolatos információkhoz, amelyek az állapottal és a minőséggel kapcsolatos következtetésekhez vezetnek.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![Hisztogram](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![Box whisky-mintaterület](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![– pontdiagram](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Az adatok előkészítése

A nyers formában lévő adatforrások gyakran nem alkalmasak arra, hogy közvetlenül a modellbe továbbítsák őket. Egy sor műveletet kell végrehajtani az adatmennyiségen ahhoz, hogy egy olyan állapotba kerüljön, amelyben a modell fel tudja használni azt.

A négy műveleti osztályt a következő kódban hajtja végre:

- Kiugró/helytelen értékek eltávolítása szűréssel.
- Az oszlopok eltávolítása nem szükséges.
- A nyers adatokból származtatott új oszlopok létrehozása, hogy a modell hatékonyabban működjön, más néven featurization.
- A címkézéssel, ahogy a bináris besorolást (ha lesz tipp, vagy nem egy adott útvonalon), a tip összegét 0 vagy 1 értékre kell konvertálni.

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

A végső feladat a címkézett adatok átalakítása olyan formátumra, amelyet a logisztikai regresszió alapján lehet elemezni. A logisztikai regressziós algoritmus bemenetének a *label-Feature Vector pár*készletének kell lennie, ahol a *funkció vektor* a bemeneti pontot jelképező számok vektora. Ezért a kategorikus oszlopokat számmá kell alakítani. Az `trafficTimeBins` és `weekdayString` az oszlopokat egész szám típusú ábrázolásra kell átalakítani. Az átalakítás végrehajtásához több módszer is rendelkezésre áll, azonban az ebben a példában szereplő megközelítés *OneHotEncoding*, közös megközelítés.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Ez egy új dataframe eredményez, amely a megfelelő formátumban lévő összes oszlopból betanít egy modellt.

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

A cella kimenete

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

![ROC-görbe logisztikai regressziós tipp modellhez](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "ROC-görbe logisztikai regressziós tipp modellhez")

## <a name="shut-down-the-spark-instance"></a>A Spark-példány leállítása

Miután befejezte az alkalmazás futtatását, állítsa le a notebookot az erőforrások felszabadításához. ehhez zárja be a fület, vagy válassza a **munkamenet befejezése** elemet a jegyzetfüzet alján található állapot panelen.

## <a name="see-also"></a>Lásd még

- [Áttekintés: Apache Spark az Azure szinapszis Analytics szolgáltatásban](apache-spark-overview.md)

## <a name="next-steps"></a>További lépések

- [.NET Apache Spark dokumentációhoz](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark hivatalos dokumentáció](https://spark.apache.org/docs/latest/)

>[!NOTE]
> A hivatalos Apache Spark dokumentációja a Spark-konzol használatával működik, amely az Azure szinapszis Sparkon nem érhető el. Ehelyett használjon [jegyzetfüzetet](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) vagy [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) -élményt.
