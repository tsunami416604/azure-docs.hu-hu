---
title: "Azok a Spark-beépített machine learning modellek |} Microsoft Docs"
description: "Hogyan tölthetők be, és az Azure Blob Storage (WASB) Python tárolt tanulási modellek pontozása."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 626305a2-0abf-4642-afb0-dad0f6bd24e9
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath;bradsev;gokuma
ms.openlocfilehash: 9ff633b4543fbc537ffdb721756706e8de5e8e88
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Azok a Spark-beépített machine learning modellek
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Ez a témakör azt ismerteti, hogyan azok Python használata a HDInsight Spark-fürtjei mentett gépi tanulási modell (ML). Ismerteti, hogyan lehet betölteni a machine learning modellek használata Spark MLlib épített, és tárolt Azure Blob Storage (WASB), és hogyan pontozása őket adathalmazok esetén WASB is megtalálható. Azt illusztrálja, előre feldolgozzák a bemeneti adatok, az indexelési és kódolási funkciók használatát a MLlib eszközkészlet szolgáltatások átalakító, és a gépi tanulás modellekkel való pontozó használható bemenetként címkézett pont adatok objektum létrehozásához. Pontozó használt minták lineáris regresszió, logisztikai regresszió, véletlenszerű erdő modellek és átmenetes kiemelése fa modellek tartalmazzák.

## <a name="spark-clusters-and-jupyter-notebooks"></a>A Spark-fürtök és a Jupyter notebookok
Beállítási lépéseket és a kódot, és azok az ML-modell találhatók: Ez az útmutató egy HDInsight Spark 1.6-os fürtben, valamint egy Spark 2.0-fürt használatára vonatkozó. Ezeket az eljárásokat a kódot is megtalálható a Jupyter notebookok.

### <a name="notebook-for-spark-16"></a>A Spark 1.6-os notebook
A [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter notebook bemutatja, hogyan azok egy mentett modell Python használata a HDInsight-fürtökön. 

### <a name="notebook-for-spark-20"></a>A Spark 2.0 notebook
A Jupyter notebook egy HDInsight Spark 2.0 fürt használata Spark 1.6 a módosításához cserélje le a Python kódját fájl [ezt a fájlt](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Ez a kód bemutatja, hogyan Spark 2.0 létrehozott modelleket felhasználását.


## <a name="prerequisites"></a>Előfeltételek

1. Az Azure-fiók és a Spark 1.6-os (vagy külső 2.0) HDInsight-fürt forgatókönyv végrehajtásához. Tekintse meg a [áttekintése adatok tudományos Spark on Azure HDInsight használatának](spark-overview.md) útmutatást ezeknek a követelményeknek. Témakör az itt használt NYC 2013 Taxi adatokat és a kód végrehajtása a Spark-fürt Jupyter notebook útmutatást leírását is tartalmazza. 
2. Is létre kell hoznia a gépi tanulási modellek által feldolgozása révén itt pontozni a [adatok feltárása és Spark modellezés](spark-data-exploration-modeling.md) a Spark 1.6-os-fürt vagy a Spark 2.0 notebookok témakör. 
3. A Spark 2.0 notebookok használata további adatok a besorolási feladat esetén a jól ismert légitársaság időben indító dataset 2011 és a 2012. A jegyzetfüzetek és a hozzájuk hivatkozások leírása szerepelnek a [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) az azokat tartalmazó GitHub-tárházban. Ezenkívül a kód itt és a csatolt jegyzetfüzetekben általános és a Spark-fürt kell működnie. Ha nem használja a HDInsight Spark, a fürt beállítása, és lehet, hogy a felügyeleti lépések némileg eltér az itt látható. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Telepítő: tárolóhelyek, könyvtárak, és az előre definiált Spark környezet
Spark el tudja olvasni és írni az Azure tárolási Blob (WASB). A meglévő adatok tárolása nem tudja feldolgozni használata Spark- és az eredmények WASB újra tárolja.

A WASB modellek vagy a fájlok mentéséhez az elérési utat kell megadni megfelelően. Az alapértelmezett tároló, a Spark-fürt csatolva egy elérési utat kezdetű használatával lehet hivatkozni: *"wasb / / /"*. A következő példakód az adatok olvasását és a, amelyhez a modell mentésekor modell tároló könyvtár elérési útja helyét adja meg. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Tárolási helye a WASB set könyvtár elérési útja
Modellek lesznek mentve: "wasb: / / / felhasználó/remoteuser/NYCTaxi/modellek". Ha az elérési út nem megfelelően van beállítva, a modellek nem töltődnek pontozó.

A pontozott eredmények lett mentve: "wasb: / / / felhasználó/remoteuser/NYCTaxi/ScoredResults". Ha a mappa elérési útja helytelen, eredmények mentése nem történik, a mappában.   

> [!NOTE]
> Az elérési út helyét másolható és illeszthetők be ezt a kódot az utolsó celláját kimenetében a helyőrzőket a **machine-learning-data-science-spark-data-exploration-modeling.ipynb** notebookot.   
> 
> 

A könyvtár elérési útvonalak beállítása a kód itt látható: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**A KIMENETRE:**

DateTime.DateTime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Könyvtárak importálása
Spark környezet beállítása, majd importálja a következő kóddal szükséges kódtárak

    #IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>Spark és az PySpark magics az adott néven beállítás
A Jupyter notebookok kapnak PySpark kernelt beállításkészletet kontextusban rendelkezik. Így nem kell beállítani a Spark, vagy Hive-környezeteket elindítása az alkalmazás használata előtt explicit módon fejleszt. Elérhetők az Ön alapértelmezés szerint. Ezek a környezetek a következők:

* sc - a Spark 
* az sqlContext - struktúra

A PySpark kernel tartalmaz néhány előre definiált "magics", amelyeket speciális meghívhatja a parancsok %%. Nincsenek két ilyen parancsot a következő kód mintákat használt.

* **%% helyi** megadott helyileg hajtotta végre az egymás utáni sorok a kódot. Kód érvényes Python-kódot kell lennie.
* **%% sql -o<variable name>** 
* Végrehajtja a Hive-lekérdezések a sqlContext ellen. Ha az -o paramétert, a lekérdezés eredménye megőrződjön-e az a %%, egy Pandas dataframe helyi Python-környezetben.

További információ a Jupyter notebookokból és az előre meghatározott kernelek "magics", amely a biztosítanak, lásd: [HDInsight Spark Linux és a Jupyter notebookok elérhető kernelek a HDInsight-fürtök](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Adatok és megtisztított adatok keret létrehozása
Ez a szakasz az adatok pontozni szükséges lépések egy sorozatát kódját tartalmazza. Olvasása illesztett 0,1 % minta taxi út és a jegy ára fájl (.tsv fájlként tárolja), formátum az adatokat, és létrehoz egy tiszta adatok keret.

Volt a taxi út és a jegy ára fájlok található a megadott eljárás alapján csatlakozott a: [a csapat adatok tudományos folyamat működés közben: HDInsight Hadoop-fürtök használata](hive-walkthrough.md) témakör.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)

    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

Cella fent ideje: 46.37 másodperc

## <a name="prepare-data-for-scoring-in-spark"></a>Készítse elő az adatokat a Spark pontozó
Ez a szakasz bemutatja, hogyan index, kódolása és kategorikus szolgáltatások készítse elő őket a besorolás és regressziós használható felügyelt MLlib tanulási algoritmusok méretezhető.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Átalakítás funkció: index és modellek pontozó a bemenő kategorikus funkciói kódolása
Ez a szakasz bemutatja, hogyan kategorikus adatok index egy `StringIndexer` és a funkcióinak kódolása `OneHotEncoder` a modellek bemeneteként.

A [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) egy karakterlánc-oszlopnak címke indexek oszlophoz címkék kódolja. Az indexek címke gyakoriságot szerint rendezett. 

A [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) leképezi a címke indexek oszlop bináris vektorok értékű legfeljebb egyetlen egy-egy oszlophoz. Ez a kódolás lehetővé teszi, hogy a várt folyamatos fontos funkciók, például logisztikai regresszió kategorikus szolgáltatások alkalmazandó algoritmusokat.

    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()

    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)

    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

Cella fent ideje: 5.37 másodperc

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>A szolgáltatás-tömböket tartalmazó bemeneti be modellek RDD objektumok létrehozása
Ez a szakasz a kódot, amely bemutatja, hogyan kategorikus szöveges adatok RDD objektumként index és egy közbeni kódolása, betanítását és MLlib logisztikai regresszió és -modellek fa-alapú teszteléséhez használható. Az indexelt adatokat tárolja [rugalmas elosztott Dataset (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) objektumok. Ezek a Spark alapvető absztrakciós. RDD objektum egy, a Spark párhuzamosan is üzemeltetnek elemek nem módosítható, particionált gyűjteményét képviseli.

Azt is kódot tartalmaz, amely bemutatja, hogyan adatok méretezése a `StandardScalar` MLlib által előírt lineáris regressziós rendelkező Stochastic átmenetes módszeren (SGD), a számos machine learning modellek betanítása népszerű algoritmust használja. A [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) méretezési egység eltérése a szolgáltatások szolgál. Szolgáltatás skálázást, más néven adatok normalizálási biztosítja, hogy szolgáltatások széles körben folyósított értékekkel van nem a megadott túlzott mérjük a objektív függvényben. 

    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

Cella fent ideje: 11.72 másodperc

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Logisztikai regressziós modell pontozása, és mentse a blob-kimenet
Ebben a szakaszban a kód bemutatja, hogyan betölteni egy logisztikai regressziós modellt az Azure blob storage mentett, és annak segítségével előre jelezni, függetlenül attól, tipp taxi utazás fizetnek, pontozása azt a szabványos besorolás metrika, majd mentse el és a blob storage eredmények ábrázolhatók. A pontozott eredmények RDD objektumok vannak tárolva. 

    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**A KIMENETRE:**

Cella fent ideje: 19.22 másodperc

## <a name="score-a-linear-regression-model"></a>Egy lineáris regressziós modell pontozása
Használtuk [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) egy Stochastic átmenetes módszeren (SGD) optimális használatával megjósolható a fizetős tipp mennyisége lineáris regressziós modell betanításához. 

Ebben a szakaszban a kód bemutatja, hogyan egy lineáris regressziós modell betöltése az Azure blob storage, pontozása méretezett változók használata, és mentse az eredmények vissza a blob.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**A KIMENETRE:**

Cella fent ideje: 16.63 másodperc

## <a name="score-classification-and-regression-random-forest-models"></a>Besorolás és regressziós véletlenszerű erdő modell pontozása
Ebben a szakaszban a kód bemutatja, hogyan betölteni a mentett besorolási és regressziós véletlenszerű erdő modellek mentése az Azure blob Storage tárolóban, a teljesítmény szabványos osztályozó és regressziós intézkedések pontozása, és mentse az eredmények vissza a blob-tároló.

[Véletlenszerű erdők](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) döntési fák együttes vannak.  Összekapcsolásának sok döntési fa overfitting kockázatának csökkentéséhez. Véletlenszerű erdők kezelni tud a kategorikus szolgáltatások terjessze ki a multiclass adatbesorolás beállításai, nincs szükség a méretezés szolgáltatás, és képesek nemlinearitás, majd kapcsolati funkció. Véletlenszerű erdők a legtöbb sikeres gépi tanulási modellek besorolás és regressziós egyikét.

[Spark.mllib](http://spark.apache.org/mllib/) támogatja az erdők véletlenszerű multiclass és bináris besorolási regressziós folyamatos és kategorikus funkciókat használ. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES    
    from pyspark.mllib.tree import RandomForest, RandomForestModel


    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**A KIMENETRE:**

Cella fent ideje: 31.07 másodperc

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Besorolás és regressziós átmenetes kiemelése fa modell pontozása
Ebben a szakaszban a kód bemutatja, hogyan besorolási és regressziós átmenetes kiemelése fa modell betöltése az Azure blob storage, a teljesítmény szabványos osztályozó és regressziós intézkedések pontozása és mentse az eredmények vissza a blob Storage tárolóban. 

**Spark.mllib** támogatja az GBTs bináris osztályozási regressziós folyamatos és kategorikus funkciókat használ. 

[Átmenet kiemelése fák](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) együttes döntési fák. GBTs ismételt adatvesztés függvény minimalizálása érdekében a döntési fák betanítása. GBTs kezelni tud a kategorikus szolgáltatásokat, nincs szükség a méretezés szolgáltatás, és képesek nemlinearitás rögzítése és szolgáltatás kapcsolati. Is is szerepel a multiclass-adatbesorolás beállításai.

    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

Cella fent ideje: 14.6 másodperc

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>A memóriából objektumainak eltávolítása, és nyomtassa ki a pontozott Alapkönyvtár
    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**A KIMENETRE:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Spark modellek felhasználásához webes felületen keresztül
Spark lehetővé teszi a kötegelt feladatok vagy interaktív lekérdezések egy REST-felületen keresztül távolról elküldeni a Livy összetevőt. A HDInsight Spark-fürt alapértelmezés szerint engedélyezve van a Livy. Livy további információkért lásd: [távolról a Livy használatával nyújt Spark feladatok](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Segítségével Livy távolról az, hogy a batch-pontszámok feladat elküldése egy fájlt, amely egy Azure blob tárolja, és az eredményeket ezután ír egy másik blob. Ehhez az szükséges, a Python-parancsfájl feltöltése  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) a Spark-fürt blobba. Egy eszköz, például használhatja **Microsoft Azure Tártallózó** vagy **AzCopy** a parancsfájlt a fürt blob másolása. Abban az esetben, ha azt a parancsfájlt, amellyel feltöltött ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> Meg kell található meg a tárelérési kulcsokat a tárfiók a portálon a Spark-fürt társított. 
> 
> 

Ezen a helyen a feltöltést követően a parancsfájl egy elosztott környezetben a Spark-fürtön belül fut. A modell betöltése, és előrejelzéseket futó bemeneti fájlok minta alapján.  

Ez a parancsfájl távolról meghívni egy egyszerű HTTPS/REST kérelem hajtsanak végre a Livy.  Ez a curl-parancsot a HTTP-kérelem távolról meghívni a Python-parancsfájl összeállításához. Cserélje le a megfelelő értékeket a Spark-fürt CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Egyetlen nyelven használhatja a távoli rendszeren a Spark feladat Livy használatával meghívni egy egyszerű HTTPS-hívással az egyszerű hitelesítéssel.   

> [!NOTE]
> A Python kérelmek könyvtár használata, miközben a HTTP kényelmes lenne, de jelenleg nincs telepítve az Azure Functions alapértelmezés szerint. Így a régebbi HTTP szalagtárak használja helyette.   
> 
> 

A HTTP-hívás a Python kódját a következő:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
    import httplib, urllib, base64

    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'

    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


Azt is megteheti a Python-kódot, hogy [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) egy Spark feladat elküldése, amely egy blob, például egy időzítő, létrehozás vagy frissítés BLOB különböző események alapján pontszámaihoz indításához. 

A kód szabad ügyfélélmény tetszés szerint használja a [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) a Spark kötegelt pontozási meghatározhat egy HTTP-műveletet a meghívni kívánt a **Logic Apps Designer** és a paraméterek beállítása. 

* Azure-portálon hozzon létre egy új logikai alkalmazás kiválasztásával **+ új** -> **Web + mobil** -> **logikai alkalmazás**. 
* Elindítani a **Logic Apps Designer**, adja meg a Logic App és az App Service-csomag nevét.
* Válasszon ki egy HTTP-műveletet, és adja meg a paramétereket, az alábbi ábrán látható:

![Logic Apps-Tervező](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>A következő lépések
**Kereszt-ellenőrzési és hyperparameter abszolút**: lásd: [speciális adatok feltárása és Spark modellezés](spark-advanced-data-exploration-modeling.md) meg, hogyan lehet a modellek betanítása a kereszt-ellenőrzési és a hyper-paraméter abszolút használatával.

