---
title: Spark-alapú gépi tanulási modellek működőképessé tétele - Csapatadat-elemzési folyamat
description: Az Azure Blob Storage (WASB) python-nal tárolt tanulási modellek betöltése és pontozása.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3f02690d7c54581ed80b521e8222d1bd5964c878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718548"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>A Spark által készített gépi tanulási modellek működőképessé tétele

Ez a témakör bemutatja, hogyan lehet üzembe egy mentett gépi tanulási modell (ML) a Python hdinsight Spark-fürtök használatával. Ismerteti, hogyan töltheti be a Spark MLlib használatával és az Azure Blob Storage (WASB) tárolt gépi tanulási modelleket, és hogyan pontozhatja őket a WASB-ban is tárolt adatkészletekkel. Bemutatja, hogyan lehet előre feldolgozni a bemeneti adatokat, átalakítani a funkciókat az MLlib eszközkészlet indexelési és kódolási funkcióival, és hogyan hozhat létre egy címkézett pontadat-objektumot, amely a m.m.modellekkel való pontozásbemenetére használható. A pontozási modellek közé tartozik a lineáris regresszió, logisztikai regresszió, véletlenszerű erdőmodellek és színátmenet kiemelési fa modellek.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Spark-fürtök és Jupyter-jegyzetfüzetek
A beállításlépései és a hibakód a forgatókönyv-forgatókönyvben található egy HDInsight Spark 1.6-fürt, valamint egy Spark 2.0-fürt használatával. Ezeknek az eljárásoknak a kódja jupyter-jegyzetfüzetekben is szerepel.

### <a name="notebook-for-spark-16"></a>Notebook Spark 1.6-hoz
A [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter notebook bemutatja, hogyan lehet egy mentett modellt a Python hdinsight-fürtökön használatával működőképessé tenni. 

### <a name="notebook-for-spark-20"></a>Notebook spark 2.0-hoz
A Spark 1.6 Jupyter-jegyzetfüzetének HDInsight Spark 2.0-fürthöz való módosításához cserélje le a Python-kódfájlt erre a [fájlra.](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py) Ez a kód bemutatja, hogyan kell felhasználni a Spark 2.0-ban létrehozott modellek.


## <a name="prerequisites"></a>Előfeltételek

1. A forgatókönyv végrehajtásához egy Azure-fiókra és egy Spark 1.6-os (vagy Spark 2.0-s) HDInsight-fürtre van szükség. Tekintse meg az [adatok tudományának áttekintése a Spark az Azure HDInsight](spark-overview.md) utasításokat, hogyan felel meg ezeknek a követelményeknek. Ez a témakör is tartalmaz egy leírást a NYC 2013 Taxi itt használt adatokat, és utasításokat, hogyan hajtson végre kódot egy Jupyter-jegyzetfüzetből a Spark-fürtön. 
2. Hozza létre a gépi tanulási modelleket, amelyeket itt kell elérni, az [adatok feltárásával és modellezésével](spark-data-exploration-modeling.md) a Spark 1.6-fürthöz vagy a Spark 2.0-s jegyzetfüzetekhez való modellezéssel. 
3. A Spark 2.0-s notebookok egy további adatkészletet használnak a besorolási feladathoz, a jól ismert légitársaság időben indulási adatkészlet2011 és 2012.The Spark 2.0 notebookok egy további adatkészletet a besorolási feladat, a jól ismert légitársaság időben indulási adatkészlet 2011-től és 2012-től. A jegyzetfüzetek és a hozzájuk mutató hivatkozások leírása az azokat tartalmazó GitHub-tárház [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) található. Ezenkívül a kód itt és a csatolt jegyzetfüzetekben általános, és működnie kell minden Spark-fürtön. Ha nem használja a HDInsight Sparkot, a fürt beállítási és felügyeleti lépései némileg eltérhetnek az itt láthatótól. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Beállítás: tárolási helyek, tárak és az előre beállított Spark-környezet
Spark képes olvasni és írni egy Azure Storage Blob (WASB). Így az ott tárolt adatok bármelyike feldolgozható a Spark használatával, és az eredmények et a WASB-ban újra tárolhatja.

A MODELLEK vagy fájlok WASB-be való mentéséhez az elérési utat megfelelően meg kell adni. A Spark-fürthöz csatlakoztatott alapértelmezett tárolóra a következő elérési úttal lehet hivatkozni, amelya következővel kezdődik: *"wasb///"*. A következő kódminta határozza meg az olvasandó adatok helyét és annak a modelltároló könyvtárnak az elérési útját, amelybe a modellkimenetet menti. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Könyvtárelérési utak beállítása a WASB tárolóhelyeinek beállításához
Modellek menti: "wasb:///user/remoteuser/NYCTaxi/Models". Ha ez az elérési út nincs megfelelően beállítva, a modellek nem töltődnek be a pontozáshoz.

A góleredményeka "wasb:///user/remoteuser/NYCTaxi/ScoredResults" helyen lett eltakarítva. Ha a mappa elérési útja helytelen, a rendszer nem menti az eredményeket a mappába.   

> [!NOTE]
> A fájl elérési útja helyek másolhatók és beilleszthetők a helyőrzőkbe ebben a kódban a **gépi tanulási-data-science-spark-data-exploration-modeling.ipynb** notebook utolsó cellájának kimenetéről.   
> 
> 

Itt van a kód beállítani könyvtár elérési utak: 

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

**Kimeneti:**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Könyvtárak importálása
Szikrakörnyezet beállítása és a szükséges könyvtárak importálása a következő kóddal

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Előre beállított Spark-környezet és PySpark-varázslatok
A Jupyter-jegyzetfüzetekhez mellékelt PySpark kernelek előre beállított környezettel rendelkeznek. Ezért nem kell explicit módon beállítania a Spark vagy a Hive-környezeteket, mielőtt elkezdené a fejlődő alkalmazást. Ezek a környezetek alapértelmezés szerint elérhetők:

* sc - a Spark 
* sqlContext - a Hive

A PySpark kernel néhány előre definiált "varázslatot" biztosít, amelyek speciális parancsok, amelyeket a %% segítségével hívhat meg. A kódmintákban két ilyen parancs található.

* **%%helyi** Itt van adva, hogy a következő sorokban lévő kód helyileg kerülvégrehajtásra. A kódnak érvényes Python-kódnak kell lennie.
* **%%sql -o \<változó név>** 
* Hive-lekérdezést hajt végre az sqlContext-en. Ha az -o paraméter átlett adva, a lekérdezés eredménye pandas adatkeretként megmarad a %%local Python környezetben.

A Jupyter-jegyzetfüzetek kerneleiről és az általuk biztosított előre definiált "varázslatokról" a [HDInsight hdInsight-alapú Jupyter-jegyzetfüzetekhez elérhető kernelek](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)című témakörben talál további információt.

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Adatok betöltése és megtisztított adatkeret létrehozása
Ez a szakasz a pontozandó adatok betöltéséhez szükséges feladatok sorozatának kódját tartalmazza. Olvassa el a taxiút és a viteldíjfájl (.tsv fájlként tárolt) egyesített 0,1%-os mintáját, formázza az adatokat, majd létrehoz egy tiszta adatkeretet.

A taxiút és a viteldíjfájlok a következő: [A csapatadat-elemzési folyamat működés közben: hdinsight Hadoop-fürtök témakörben](hive-walkthrough.md) megadott eljárás alapján csatlakoztak egymáshoz.

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

**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 46,37 másodperc

## <a name="prepare-data-for-scoring-in-spark"></a>Adatok előkészítése a Spark ban történő pontozáshoz
Ez a szakasz bemutatja, hogyan indexelheti, kódolhat és skálázhat kategorikus funkciókat, hogy felkészítse őket az MLlib felügyelt tanulási algoritmusok ban történő osztályozásra és a regresszióra.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funkcióátalakítás: index és kódolás kategorikus funkciók a modellekbe történő bevitelhez a pontozáshoz
Ez a szakasz bemutatja, hogyan `StringIndexer` indexelje a `OneHotEncoder` kategorikus adatokat a és a funkciók kódolása a modellekbe történő bevitellel.

A [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) egy címkeoszlopot kódol a címkeindexek oszlopába. Az indexek címkegyakoriság szerint vannak rendezve. 

A [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) egy címkeindex-oszlopot rendel egy bináris vektorokból álló oszlophoz, legbénákkal. Ez a kódolás lehetővé teszi, hogy a folyamatos értékű funkciókat, például a logisztikai regressziót elvárható algoritmusokat alkalmazza a kategorikus funkciókra.

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

**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 5,37 másodperc

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>RDD-objektumok létrehozása jellemzőtömbökkel a modellekbe való bevitelhez
Ez a szakasz olyan kódot tartalmaz, amely bemutatja, hogyan indexelheti a kategorikus szöveges adatokat RDD-objektumként, és egy gyors kódolást, hogy az MLlib logisztikai regressziós és faalapú modellek betanításához és teszteléséhez használható legyen. Az indexelt adatok at [rugalmas elosztott adatkészlet (RDD)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) objektumok tárolja. Az RDD-k a Spark alapvető absztrakciója. Az RDD-objektum egy nem módosítható, particionált elemgyűjteményt jelöl, amely a Sparkkal párhuzamosan működtethető.

Azt is tartalmazza a kódot, amely `StandardScalar` bemutatja, hogyan kell az adatok méretezése a megadott MLlib lineáris regresszió stochastic gradiens (SGD), egy népszerű algoritmus a gépi tanulási modellek széles körének betanításához. A [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) a jellemzők egységvarianciára történő méretezésére szolgál. A funkcióskálázás, más néven az adatok normalizálása biztosítja, hogy a széles körben kifizetett értékekkel rendelkező funkciók ne kapjanak túlzott súlyt az objektív funkcióban. 

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

**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 11,72 másodperc

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Pontszám a logisztikai regressziós modellel, és a kimenet mentése a blobba
Ebben a szakaszban a kód bemutatja, hogyan tölthet be egy logisztikai regressziós modellt, amelyet az Azure blob storage-ba mentett, és arra használhatja, hogy előre jelezze, hogy egy tipp fizetett-e taxiútra, pontozást szabványos besorolási metrikákkal, majd mentse és tervezze meg az eredményeket a blobstorage-ba . A pontozott eredmények RDD-objektumokban tárolódnak. 

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

**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 19,22 másodperc

## <a name="score-a-linear-regression-model"></a>Lineáris regressziós modell pontszáma
A [LinearRegressionWithSGD-t](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) egy lineáris regressziós modell betanítására használtuk sztochastikus gradiens (SGD) használatával az optimalizáláshoz, hogy megjósoljuk a kifizetett borravaló összegét. 

Ebben a szakaszban a kód bemutatja, hogyan tölthet be egy lineáris regressziós modellt az Azure blob storage-ból, a méretezett változók használatával, majd mentse az eredményeket vissza a blobba.

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


**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 16,63 másodperc

## <a name="score-classification-and-regression-random-forest-models"></a>Pontszám besorolásés regressziós véletlenszerű erdőmodellek
Ebben a szakaszban a kód bemutatja, hogyan töltheti be az Azure blob storage-ban mentett véletlenszerű erdőmodellek mentett mentett osztályozási és regressziós modelleket, hogyan szerezheti meg a teljesítményüket a szabványos osztályozó és regressziós mértékek segítségével, majd mentse vissza az eredményeket a blobstorage-ba.

[A véletlenszerű erdők](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) a döntési fák együttesei.  Ezek össze sok döntés fák kockázatának csökkentése érdekében a túlszerelés. A véletlenszerű erdők képesek kezelni a kategorikus jellemzőket, kiterjeszthetők a többosztályos besorolási beállításra, nem igényelnek szolgáltatásméretezést, és képesek nem linearitásokat és funkciók interakcióit rögzíteni. Véletlenszerű erdők az egyik legsikeresebb gépi tanulási modellek osztályozási és regressziós.

[Spark.mllib](https://spark.apache.org/mllib/) támogatja a véletlenszerű erdők bináris és többosztályos osztályozás és a regresszió, mind a folyamatos és kategorikus funkciók. 

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

**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 31,07 másodperc

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Pontszámbesorolás és regressziós színátmenet-kiemelési famodellek
Ebben a szakaszban a kód bemutatja, hogyan töltheti be a besorolási és regressziós gradiens kiemelése famodellek az Azure blob storage, pontszám teljesítményüket a szabványos osztályozó és regressziós mértékek, majd mentse az eredményeket vissza blob storage. 

**A spark.mllib** támogatja a GBTS-t a bináris osztályozáshoz és a regresszióhoz, folyamatos és kategorikus funkciókhasználatával. 

[Gradiens kiemelése fák](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) együttesei döntésfák. A GBTS vonatdöntési fákit iteratív módon a veszteségfunkció minimalizálása érdekében. A GBTS képes kezelni a kategorikus funkciókat, nem igényel szolgáltatásskálázást, és képes a nem linearitás és a funkciók interakcióinak rögzítésére. Ez az algoritmus többosztályos besorolási beállításban is használható.

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

**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 14,6 másodperc

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Objektumok karbantartása a memóriából és a pontozott fájlok nyomtatási helyéből
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


**Kimeneti:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Spark-modellek felhasználása webes felületen keresztül
A Spark egy olyan mechanizmust biztosít, amely távolról küldi el a kötegelt feladatokat vagy az interaktív lekérdezéseket egy LIVy nevű összetevővel rendelkező REST-felületen keresztül. Livy alapértelmezés szerint engedélyezve van a HDInsight Spark-fürt. A Livy-ről további információt a Következő ben [talál: Spark-feladatok küldése távolról a Livy használatával.](../../hdinsight/spark/apache-spark-livy-rest-interface.md) 

Livy segítségével távolról küldhet el egy feladatot, amely kötegelt pontszámok egy Azure blobban tárolt fájlt, majd írja az eredményeket egy másik blobba. Ehhez töltse fel a Python-szkriptet a  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) a Spark-fürt blobját. A parancsfájl tamásolhatja a fürtblobba egy eszközt, például a **Microsoft Azure Storage Explorert** vagy az **AzCopy-t.** A mi esetünkben feltöltöttük a forgatókönyvet ***wasb:///example/python/ConsumeGBNYCReg.py.***   

> [!NOTE]
> A hozzáférési kulcsok, amelyek a Spark-fürthöz társított tárfiók portálján találhatók. 
> 
> 

Miután feltöltötte erre a helyre, ez a parancsfájl a Spark-fürtön belül fut elosztott környezetben. Betölti a modellt, és a modell alapján előrejelzéseket futtat a bemeneti fájlokon.  

Ezt a parancsfájlt távolról is meghívhatja egy egyszerű HTTPS/REST-kérelem végrehajtásával a Livy-n.  Itt van egy curl parancs a PYTHON-parancsfájl távoli meghívásához szükséges HTTP-kérelem létrehozásához. Cserélje le a CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME függvényt a Spark-fürt megfelelő értékeire.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

A távoli rendszer bármely nyelvén meghívhatja a Spark-feladat Livy-n keresztül egy egyszerű https-hívás egyszerű hitelesítéssel.   

> [!NOTE]
> A HTTP-hívás lépésekor célszerű lenne használni a Python-kérelmek könyvtárat, de jelenleg nincs telepítve az Azure Functionsben. Így a régebbi HTTP-könyvtárak at használják helyette.   
> 
> 

Itt van a Python-kód a HTTP-hívás:

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


Ez a Python-kód is hozzáadhatja ezt a [Python-kódot](https://azure.microsoft.com/documentation/services/functions/) az Azure Functions-hez, hogy elindítson egy Spark-feladatbeküldött, amely különböző események, például egy időzítő, létrehozás vagy egy blob frissítése alapján egy blob ot szerez ki. 

Ha egy kódmentes ügyfélélményt szeretne, az [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) használatával hívja meg a Spark kötegelt pontozást egy HTTP-művelet meghatározásával a Logic Apps **Designerben,** és a paraméterek beállítása. 

* Az Azure Portalon hozzon létre egy új logikai alkalmazást az **+Új** -> **web + mobillogikai** -> **logikai alkalmazás**kiválasztásával. 
* A Logic **Apps Designer**felsorasztásához adja meg a Logikai alkalmazás és az App Service-csomag nevét.
* Jelöljön ki egy HTTP-műveletet, és adja meg az alábbi ábrán látható paramétereket:

![Logic Apps Designer](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>A következő lépések
**Kereszt-ellenőrzés és hiperparaméter-söprés**: [Részletes adatfeltárás és -modellezés a Sparkkal,](spark-advanced-data-exploration-modeling.md) hogyan lehet a modelleket betanítani kereszt-ellenőrzés és hiperparaméter-söprés használatával.

