---
title: Gépi tanulási alkalmazás létrehozása az Apache Spark MLlib és az Azure Synapse Analytics segítségével
description: Ismerje meg, hogyan hozhat létre egy gépi tanulási alkalmazást az Apache Spark MLlib használatával, amely logisztikai regressziós osztályozással elemzi az adatkészletet.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430005"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Gépi tanulási alkalmazás létrehozása az Apache Spark MLlib és az Azure Synapse Analytics segítségével

Ebben a cikkben megtudhatja, hogyan használhatja az Apache Spark [MLlib](https://spark.apache.org/mllib/) egy gépi tanulási alkalmazást, amely egyszerű prediktív elemzést végez egy Azure nyílt adatkészleten. A Spark beépített gépi tanulási kódtárakat biztosít. Ez a példa logisztikai regresszión keresztüli *osztályozást* használ.

Az MLlib egy alapvető Spark-könyvtár, amely számos olyan segédprogramot biztosít, amelyek hasznosak a gépi tanulási feladatokhoz, beleértve a következő célokra alkalmas segédprogramokat is:

- Osztályozás
- Regresszió
- Fürtözés
- Téma modellezése
- Egyedi érték-bomlás (SVD) és fő komponenselemzés (PCA)
- Hipotézistesztelés és mintastatisztikák számítása

## <a name="understand-classification-and-logistic-regression"></a>Az osztályozás és a logisztikai regresszió ismertetése

*Besorolás*, egy népszerű gépi tanulási feladat, a folyamat a bemeneti adatok kategóriákba rendezésének folyamata. Egy besorolási algoritmus feladata, hogy kitalálja, hogyan rendelje hozzá *a címkéket* az Ön által megadott bemeneti adatokhoz. Például egy gépi tanulási algoritmus, amely elfogadja a készletadatok beviteli és osztja az állomány két kategóriába sorolhatók: készletek, hogy el kell adni, és a készletek, hogy meg kell tartani.

*A logisztikai regresszió* egy olyan algoritmus, amely osztályozáshoz használható. A Spark logisztikai regressziós API-ja hasznos *a bináris osztályozáshoz,* vagy a bemeneti adatok két csoport egyikébe történő besorolásához. A logisztikai regressziókról a [Wikipédia című témakörben](https://en.wikipedia.org/wiki/Logistic_regression)talál további információt.

Összefoglalva, a logisztikai regressziós folyamat *egy logisztikai függvényt* hoz létre, amely előre jelezheti annak valószínűségét, hogy egy bemeneti vektor az egyik vagy a másik csoportba tartozik.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Prediktív elemzés példa a NYC Taxi adatok

Ebben a példában a Spark használatával végezhet némi prediktív elemzést a New York-i taxiút-tipp adatokon. Az adatok az [Azure Open Datasets szolgáltatáson](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)keresztül érhetők el. Az adatkészlet ezen részhalmaza a sárga taxiutakra vonatkozó információkat tartalmazza, beleértve az egyes utazásokra, a kezdési és befejezési időpontra és helyekre, a költségekre és más érdekes attribútumokra vonatkozó információkat.

> [!IMPORTANT]
> Előfordulhat, hogy további díjakat kell fizetnie az adatok tárolási helyéről történő lekérnie.

A következő lépésekben kifejleszt egy modellt annak előrejelzésére, hogy egy adott utazás tartalmaz-e tippet vagy sem.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Apache Spark MLlib gépi tanulási alkalmazás létrehozása

1. Hozzon létre egy jegyzetfüzetet a PySpark kernel használatával. Az utasításokat a Jegyzetfüzet létrehozása című témakörben [találja.](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook)
2. Importálja az alkalmazáshoz szükséges típusokat. Másolja és illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER**billentyűkombinációt, vagy futtassa a cellát a kódtól balra lévő kék lejátszás ikonnal.

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

    A PySpark kernel miatt nem kell explicit környezeteket létrehoznia. A Spark-környezet automatikusan létrejön az Első kódcella futtatásakor.

## <a name="construct-the-input-dataframe"></a>A bemeneti adatkeret kialakítása

Mivel a nyers adatok parketta formátumúak, a Spark környezetben közvetlenül adatkeretként lehívhatja a fájlt a memóriába. Bár az alábbi kód az alapértelmezett beállításokat használja, szükség esetén kényszerítheti az adattípusok és más sémaattribútumok leképezését.

1. A következő sorok futtatásával hozzon létre egy Spark-adatkeretet a kód új cellába való beillesztésével. Az első szakasz hozzárendeli az Azure storage hozzáférési adatait a változókhoz. A második szakasz lehetővé teszi, hogy a Spark távolról olvassa be a blobstorage-ból. A kód utolsó sora parettát olvas, de ezen a ponton nem töltődik be adat.

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

2. Az összes adat lehúzása körülbelül 1,5 milliárd sort eredményez. A Spark-készlet méretétől függően (előzetes verzió), a nyers adatok túl nagy, vagy túl sok időt vesz igénybe a működéshez. Ezeket az adatokat leszűrheti valami kisebbre. Szükség esetén adja hozzá a következő sorokat, hogy az adatokat körülbelül 2 millió sorra szűrje a válaszkészebb élmény érdekében. Ezekkel a paraméterekkal egy hét adatot szeretne lehívni.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Az egyszerű szűrés hátránya, hogy statisztikai szempontból elfogultságot vezethet be az adatokba. Egy másik megközelítés a Sparkba épített mintavételi. A következő kód az adatkészletet körülbelül 2000 sorra csökkenti, ha a fenti kód után alkalmazza. Ez a mintavételi lépés használható az egyszerű szűrő helyett vagy az egyszerű szűrővel együtt.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Most már lehetséges, hogy nézd meg az adatokat, hogy mi volt olvasni. Általában jobb, ha az adatkészlet méretétől függően a teljes készlet helyett egy részhalmazsal tekinti át az adatokat. A következő kód kétféleképpen tekinthető meg az adatokmegtekintéséhez: az előbbi alapvető, az utóbbi pedig sokkal gazdagabb hálózati élményt nyújt, valamint az adatok grafikus megjelenítésének képességét.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. A létrehozott adatkészletméretétől és a jegyzetfüzet többszöri kísérletének vagy futtatásának szükségességétől függően tanácsos lehet az adatkészlethelyi gyorsítótárazása a munkaterületen. Az explicit gyorsítótárazást háromféleképpen végezheti el:

   - Az adatkeret helyi mentése fájlként
   - Az adatkeret mentése ideiglenes táblaként vagy nézetként
   - Az adatkeret mentése állandó táblaként

E megközelítések közül az első kettő az alábbi kódpéldákban található.

Ideiglenes tábla vagy nézet létrehozása különböző elérési utakat biztosít az adatokhoz, de csak a Spark-példány munkamenetének időtartama alatt tart.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Az adatok megismerése

Normális esetben akkor megy keresztül egy *fázisfeltáró adatok elemzése* (EDA) ezen a ponton, hogy dolgozzon ki egy megértése az adatokat. A következő kód az adatok állapotára és minőségére vonatkozó következtetésekhez vezető tippekhez kapcsolódó adatok három különböző vizualizációját mutatja be.

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
![doboz bajusz](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![telek telek telek telek](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Az adatok előkészítése

Az adatok a nyers formában gyakran nem alkalmas a közvetlenül a modell. Műveletek sorozatát kell végrehajtani az adatokon, hogy olyan állapotba kerüljön, amelyben a modell felhasználhatja.

Az alábbi kódban négy műveletosztályt hajtanak végre:

- A kiugró értékek/helytelen értékek eltávolítása szűrésen keresztül.
- Az oszlopok eltávolítása, amelyek nem szükségesek.
- A nyers adatokból származó új oszlopok létrehozása a modell hatékonyabb működésének, más néven jellemzőknek.
- Címkézés, ahogy vállalkozás bináris besorolás (lesz egy tipp, vagy nem egy adott utazás) van szükség, hogy átalakítsa a hegy összege egy 0 vagy 1 érték.

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

Ezután egy második menet et tesz meg az adatokon, hogy hozzáadja a végleges funkciókat.

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

A végső feladat a címkézett adatok olyan formátummá alakítása, amely logisztikai regresszióval elemezhető. A logisztikai regressziós algoritmus bemenetének *címke-jellemző vektorpárok*készletének kell lennie, ahol a *jellemzővektor* a bemeneti pontot képviselő számok vektora. Tehát a kategorikus oszlopokat számokká kell alakítanunk. A `trafficTimeBins` `weekdayString` és az oszlopokat egész számra kell konvertálni. Az átalakítás végrehajtásának több megközelítése is van, azonban ebben a példában a *OneHotEncoding*, egy közös megközelítés.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Ez egy új adatkeretet eredményez, amely a modell betanításához a megfelelő formátumú összes oszlopot tartalmaz.

## <a name="train-a-logistic-regression-model"></a>Logisztikai regressziós modell betanítása

Az első feladat az adatkészlet felosztása egy betanítási és egy tesztelési vagy érvényesítési készletre. A felosztás itt tetszőleges, és meg kell játszani körül különböző osztott beállításokat, hogy hatással vannak a modell.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Most, hogy két DataFrames, a következő feladat a modell képlet létrehozása és futtatása a betanítási DataFrame, majd érvényesítse a tesztelés dataframe. A modellképlet különböző verzióival kísérletezzen a különböző kombinációk hatásának megtekintéséhez.

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

Ennek a cellának a kimenete

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Az előrejelzés vizuális ábrázolásának létrehozása

Most már létrehozhat egy végső vizualizációt, hogy segítsen a teszt eredményeinek megismerésében. A [ROC-görbe](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) az egyik módja az eredmény áttekintésének.

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

![ROC-görbe a logisztikai regressziós csúcsmodellhez](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "ROC-görbe a logisztikai regressziós csúcsmodellhez")

## <a name="shut-down-the-spark-instance"></a>A Spark-példány leállítása

Miután befejezte az alkalmazás futtatását, állítsa le a jegyzetfüzetet, hogy felszabadítsa az erőforrásokat a lap bezárásával, vagy válassza a **Munkamenet befejezése lehetőséget** a jegyzetfüzet alján található állapotpanelen.

## <a name="see-also"></a>Lásd még

- [Áttekintés: Apache Spark az Azure Synapse Analytics szolgáltatásban](apache-spark-overview.md)

## <a name="next-steps"></a>További lépések

- [Az Apache Spark dokumentációja .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Az Apache Spark hivatalos dokumentációja](https://spark.apache.org/docs/latest/)

>[!NOTE]
> A hivatalos Apache Spark-dokumentáció a Spark-konzol használatára támaszkodik, amely nem érhető el az Azure Synapse Sparkon. Használja inkább a [notebookot](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) vagy [az IntelliJ-élményeket.](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
