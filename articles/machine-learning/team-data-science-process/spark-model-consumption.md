---
title: A Spark használatával összeállított gépi tanulási modelleket – csoportos adatelemzési folyamat
description: Hogyan tölthet be és tárolja az Azure Blob Storage (WASB) pythonnal tanulási modellek pontozása.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d89886e7cc5fe47013902b281c490b79a07e7641
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888110"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>A Spark használatával összeállított gépi tanulási modellek üzembe helyezése

Ez a témakör bemutatja, hogyan mentett gépi tanulási modell (gépi tanulás) Python használata a HDInsight Spark-fürtök üzembe helyezése. Azt ismerteti, hogyan lehet betölteni a machine learning-modellek, amelyek a Spark MLlib segítségével létrehozott és tárolt Azure Blob Storage (WASB), és hogyan pontszámot rendelni azokat az adatkészleteket, amelyek a WASB is megtalálható. Bemutatja azt, hogyan előzetes feldolgozása a bemeneti adatokat, az indexelés és a kódolási funkcióinak használatát a MLlib eszközkészlet funkciók átalakítása, és hogyan hozhat létre egy címkézett pont objektum, amely bemenetként pontozás a Machine Learning-modellekkel használható. A modellek pontozása használt lineáris regresszió, logisztikai regressziós, véletlenszerű erdő modelleket és átmenetes gyorsított fa modellek tartalmazzák.

## <a name="spark-clusters-and-jupyter-notebooks"></a>A Spark-fürtök és a Jupyter notebookok
Beállítási lépéseket és a kódot, és a egy gépi Tanulási modell üzembe helyezése okat Ez az útmutató egy HDInsight Spark 1.6-os-fürtön, valamint a Spark 2.0-fürt használatával. Ezek az eljárások a kódját a Jupyter notebookok is tartalmaz.

### <a name="notebook-for-spark-16"></a>A Spark 1.6-os notebook
A [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter notebook mutatja be a Python használata a HDInsight-fürtökön mentett modell üzembe helyezése. 

### <a name="notebook-for-spark-20"></a>A Spark 2.0 notebook
Spark 1.6-os használata egy HDInsight Spark 2.0-fürthöz tartozó Jupyter notebookot módosításához cserélje le a Python-kód fájlt a [ezt a fájlt](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Ez a kód bemutatja, hogyan a Spark 2.0-s verziójában létrehozott modellek felhasználása.


## <a name="prerequisites"></a>Előfeltételek

1. Az Azure-fiók és a egy Spark 1.6-os (vagy a Spark 2.0-s) van szüksége a forgatókönyv végrehajtásához HDInsight-fürtön. Tekintse meg a [áttekintése az adatelemzés az Azure HDInsight Spark használatával](spark-overview.md) ezeknek a követelményeknek való létrehozásával kapcsolatos útmutatást. A témakör leírását a NYC 2013-i taxik adatait itt használt, és hajtsa végre a Spark-fürtön lévő Jupyter notebook kód útmutatást is tartalmaz. 
2. Létre kell hozni a gépi tanulási modellek által feldolgozása révén ide pontozunk a [adatáttekintés és modellezés a Spark segítségével](spark-data-exploration-modeling.md) a Spark 1.6-os-fürt vagy a Spark 2.0-jegyzetfüzetek a témakört. 
3. A Spark 2.0 notebookok használata a hálóbesorolási feladat, a jól ismert légitársaság időben indító adatkészlet 2011 és 2012 egy további adatkészlet. A jegyzetfüzetek és a rájuk mutató hivatkozást leírása tartalmazza a [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) az azokat tartalmazó GitHub-adattárban. Továbbá, a kód itt és a társított notebookok az általános és működnie kell bármelyik Spark-fürtön. Ha nem használja a HDInsight Spark, a fürt beállítása és lehet, hogy a felügyeleti lépések kissé eltérhetnek az itt látható. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>A telepítő: tárolási helyek, kódtárak és az előre beállított Spark-környezetben
A Spark is képes olvasni és írni egy Azure Storage Blob (WASB). A meglévő adatokat tárolja, Nincs feldolgozható használata Spark- és WASB újra tárolja az eredményeket.

Szeretné menteni a modellek vagy fájlokat a WASB, az elérési utat meg kell határozni megfelelően. Az alapértelmezett tároló csatlakozik a Spark-fürtöt egy elérési út kezdetű használatával lehet hivatkozni: *"wasb / / /"*. Az alábbi kódmintában kell olvasni az adatokat, és a könyvtár elérési útját modell tárolási, amelyhez a modell kimeneti mentett helyét adja meg. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Állítsa be a tárolási helyek elérési utak a WASB
Modellek lesznek mentve: "wasb: / / / felhasználó/remoteuser/NYCTaxi/modellek". Ha az elérési út nem megfelelően van beállítva, a modellek nem töltődnek pontozó.

Pontozott eredmények lett mentve: "wasb: / / / felhasználó/remoteuser/NYCTaxi/ScoredResults". Ha a mappa elérési útja nem megfelelő, eredmények mentése nem történik abban a mappában.   

> [!NOTE]
> A fájl elérési útjai másolható, és ez a kód az előző cella kimenetében található helyőrzők beillessze a **machine-learning-data-science-spark-data-exploration-modeling.ipynb** notebookot.   
> 
> 

A következő könyvtár elérési útvonalak beállítása a kódot: 

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
Állítsa be a spark környezet, és importálja a következő kódot a szükséges kódtárak

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Előre beállított Spark-környezet és a PySpark magics
A PySpark-kernelek Jupyter notebookok a kell egy előre beállított környezetet. Így nem kell beállítani a Spark vagy a Hive-környezetek az alkalmazás használatának megkezdése előtt explicit módon fejleszti. Ezeket a rendszer alapértelmezés szerint az Ön számára elérhető. Ezek a környezetek a következők:

* sc - és Spark 
* kontext sqlContext – Hive

A PySpark kernel tartalmaz néhány előre meghatározott "magics", amelyek különleges parancsok, amelyek segítségével meghívhatja a %%. Nincsenek két ilyen parancsot a kód a példákon használt.

* **%% helyi** megadott helyileg hajtja végre a következő sorokat a kódot. Kód érvényes a Python-kód kell lennie.
* **%% sql -o <variable name>** 
* Futtatják a Hive-lekérdezést a kontext sqlContext ellen. Az -o paramétert, ha a lekérdezés eredménye a rendszer megőrzi a %% Pandas dataframe helyi Python-környezetben.

További információk a-kernelek Jupyter-notebookok és az előre meghatározott "magics", amely számára biztosítanak, lásd: [notebookokhoz elérhető kernelek Jupyter notebookok a HDInsight Spark Linux-fürtök HDInsight az](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Gyűjthet adatokat, és hozzon létre egy megtisztított adatok keretet
Ez a szakasz betöltheti az adatokat a pontozandó szükséges feladatok egy sorozatát kódját tartalmazza. Csatlakoztatott 0,1 % mintában a taxi utazást és diszkont fájlok (.tsv-fájlként tárolja) formátum az adatok olvasását és majd létrehoz egy tiszta adatkeretbe.

A taxi utazást és diszkont fájlokat is csatlakoztatott alapján az eljárásban ismertetett a: [A csoportos adatelemzési folyamat működés közben: HDInsight Hadoop-fürtöket használó](hive-walkthrough.md) témakör.

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

Cella fent végrehajtásához felhasznált idő: 46.37 másodperc

## <a name="prepare-data-for-scoring-in-spark"></a>A Spark pontozó adatok előkészítése
Ez a szakasz bemutatja, hogyan index, kódolása és készítse elő azokat a besorolási és regressziós felügyelt MLlib tanulási algoritmusok használatra kategorikus szolgáltatások méretezése.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Átalakítás funkció: index és a bemeneti pontozó modellekbe kategorikus funkciók kódolása
Ez a szakasz bemutatja, hogyan kategorikus adatok indexelése egy `StringIndexer` és a funkciókat kódolása `OneHotEncoder` a modellek bemeneteként.

A [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) kódol, egy karakterláncokat tartalmazó oszlopot tartalmazó oszlop, felirat indexek címkék. Az indexek címke gyakoriságok szerint vannak rendezve. 

A [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) címke indexek oszlop képez le egy oszlop bináris vektorok legfeljebb egyetlen one-értékkel. Ehhez a kódoláshoz lehetővé teszi a folyamatos értékelt szolgáltatások, például a logisztikai regressziós kategorikus funkciók alkalmazandó elvárt algoritmusokat.

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

Cella fent végrehajtásához felhasznált idő: 5.37 másodperc

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>A modellek be szolgáltatás-tömböket RDD-objektumok létrehozása
Ez a szakasz tartalmazza a kódot, amely bemutatja, hogyan RDD-objektumként kategorikus szöveges adatok indexelése és a egy gyakori kódolása, így betanítására és MLlib logisztikai regressziós és -fa-alapú modell teszteléséhez használható. Az indexelt adatokat tárolja [rugalmas elosztott adatkészlet (RDD)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) objektumokat. Ezek a Spark az alapszintű absztrakció. Az RDD-objektum egy nem módosítható, particionált elemek gyűjteménye, amelyek a Spark párhuzamosan is üzemeltetett jelöli.

Kód, amely bemutatja, hogyan méretezzünk át az adatokat is tartalmaz a `StandardScalar` MLlib által előírt lineáris regressziós a Sztochasztikus átmenetes Grádiens (SGD), számos machine learning-modellek betanításához egy népszerű algoritmus használatát. A [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) egység eltérése a szolgáltatások méretezése szolgál. A szolgáltatás méretezése adatok normalizálási, más néven adatblokkok, hogy funkciók széles körben folyósított értékekkel van nem adott túlzott mérjük a cél függvényben. 

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
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

Cella fent végrehajtásához felhasznált idő: 11.72 másodperc

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Az a logisztikai regressziós modell pontozása és a kimeneti BLOB mentése
Ebben a szakaszban a kód bemutatja, hogyan betöltése egy logisztikai regressziós modellt, amely az Azure blob storage-ban mentett, és ezzel e tipp taxi utazás fizetős előrejelzése, pontszám, a standard szintű besorolási metrikákkal, majd mentse el és jeleníti meg az eredményeket a blob-stora a GE. A pontozott eredmények tárolása az RDD-objektumokat. 

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

Cella fent végrehajtásához felhasznált idő: 19.22 másodperc

## <a name="score-a-linear-regression-model"></a>Egy lineáris regressziós modell pontozása
Használtuk [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) egy előre fizetett tipp mennyisége Sztochasztikus átmenetes Grádiens (SGD) optimalizálás használatával lineáris regressziós modell betanításához. 

Ebben a szakaszban a kódot egy lineáris regressziós modell betöltése az Azure blob storage-ból, és pontozása méretezett változók használata, majd mentse vissza az eredményeket a blob jeleníti meg.

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

Cella fent végrehajtásához felhasznált idő: 16.63 másodperc

## <a name="score-classification-and-regression-random-forest-models"></a>Besorolási és regressziós véletlenszerű erdő modellek pontozása
Ebben a szakaszban a kód bemutatja, hogyan betölteni a mentett besorolási és regressziós véletlenszerű erdő modellek mentése az Azure blob storage-ban, a standard szintű osztályozó és regressziós mértékek teljesítményük, és mentse az eredményeket a blob storage-ba való.

[Véletlenszerű erdők](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) együttesek döntési fák vannak.  Overfitting kockázatának csökkentése érdekében számos döntési fák kombinálhatja azokat. Véletlenszerű erdők képes kezelni a kategorikus funkciók bővítése a többosztályos osztályozási beállításait, nincs szükség a szolgáltatás méretezése és tudnak nemlinearitás rögzítése és a szolgáltatás kapcsolati. Véletlenszerű erdők a legsikeresebb gépi tanulási besorolási és regressziós modellek tartoznak.

[Spark.mllib](https://spark.apache.org/mllib/) támogatja az erdők véletlenszerű bináris és többosztályos osztályozási regressziós, mind a folyamatos, mind a kategorikus funkciók használatával. 

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

Cella fent végrehajtásához felhasznált idő: 31.07 másodperc

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Besorolási és regressziós átmenetes gyorsított fa modellek pontozása
Ebben a szakaszban a kód bemutatja, hogyan besorolási és regressziós átmenetes gyorsított fa modellek betöltése az Azure blob storage-ból, a standard szintű osztályozó és regressziós mértékek teljesítményük és mentse az eredményeket a blob storage-ba való. 

**Spark.mllib** GBTs támogatja a bináris osztályozási és regressziós, mind a folyamatos, mind a kategorikus funkciók használatával. 

[Színátmenet kiemelési fák](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) együttesek döntési fák. GBTs iteratív, hogy minimalizálják az adatvesztést függvény döntési fák betanítása. GBTs képes kezelni a kategorikus szolgáltatásokat, nincs szükség a szolgáltatás méretezése és tudnak nemlinearitás rögzítése és a szolgáltatás kapcsolati. Ezek is használható osztályú-besorolás beállításban.

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

Cella fent végrehajtásához felhasznált idő: 14.6 másodperc

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Távolítsa el a memória és a nyomtatási objektumait pontozását Alapkönyvtár
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

## <a name="consume-spark-models-through-a-web-interface"></a>Spark-modellek felhasználása webes felületen keresztül
A Spark lehetővé teszi a távolról a Livy összetevőt a elküldeni a kötegelt vagy interaktív lekérdezések egy REST-felületen keresztül. A HDInsight Spark-fürthöz alapértelmezés szerint engedélyezve van a Livy. Livy további információkért lásd: [Távolról a Livy használatával Spark-feladatok elküldése](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Használhatja Livy egy feladatot, amely a batch-pontszámok távolról elküldeni egy fájlt, amely egy Azure-blobból tárolja, és az eredményeket ezután ír egy másik blob. Ehhez a Python-szkript feltöltése  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) a blobba, a Spark-fürt. Egy hasonló eszközzel **Microsoft Azure Storage Explorer** vagy **AzCopy** a szkript a fürt blob másolása. Ebben az esetben azt a parancsfájlt a feltöltött ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> Meg kell találhatók a portálon, a Spark-fürthöz társított storage-fiók hozzáférési kulcsait. 
> 
> 

Miután feltöltötte erre a helyre, ez a szkript egy elosztott környezetben. a Spark-fürtön belül fut. Ez betölti a modell és előrejelzések futtat a bemeneti fájlokat, a modell alapján.  

Ez a szkript távoli hívhat azáltal, hogy egy egyszerű HTTPS, illetve a REST-kérés a Livy.  Íme a curl-parancsot a Python-szkript meghívása távolról a HTTP-kérelem létrehozásához. Cserélje le a megfelelő értékeket a Spark-fürt CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

A távoli rendszeren lévő bármilyen nyelv használatával livy-n keresztül a Spark-feladat indítása azáltal, hogy az egyszerű hitelesítés egy egyszerű HTTPS-hívással.   

> [!NOTE]
> Használható a Python-kérelmek könyvtár, amikor a HTTP-hívás kényelmes lenne, de jelenleg nincs telepítve az Azure Functions alapértelmezés szerint. Így a régebbi HTTP-kódtárak használja.   
> 
> 

Íme a HTTP-hívás a Python-kódban:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
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


Azt is megteheti, a Python-kód [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) egy Spark-feladat küldése, amely egy blobot, például egy időzítő, létrehozás vagy frissítés egy BLOB különféle események alapján pontszámmodell aktiválásához. 

Ha inkább a kód ingyenes ügyféloldali felhasználói élményt, használja a [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) Spark kötegelt pontozási egy HTTP-művelet a definiálásával meghívni a **Logic Apps Designerben** és a paraméterek beállítása. 

* Az Azure Portalról, hozzon létre egy új logikai alkalmazás kiválasztásával **+ új** -> **Web + mobil** -> **logikai alkalmazás**. 
* Csatlakozva a **Logic Apps Designerben**, adja meg a logikai alkalmazás és az App Service-csomag nevét.
* Válasszon ki egy HTTP-műveletet, és adja meg a paramétereket, az alábbi ábrán látható:

![Logic Apps Designer](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>A következő lépések
**Kereszt-ellenőrzési és a hiperparaméter kezdik**: Lásd: [speciális adatáttekintés és modellezés a Spark segítségével](spark-advanced-data-exploration-modeling.md) meg, hogyan lehet a modellek tanítása az kereszt-ellenőrzési és a hyper-paraméter kezdik.

