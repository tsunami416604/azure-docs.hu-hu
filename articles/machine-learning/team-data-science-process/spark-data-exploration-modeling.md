---
title: "Az adatok feltárása és Spark modellezés |} Microsoft Docs"
description: "Az feltárására és modellezési képességek az Azure-on a Spark MLlib eszközkészlet bővíthető."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b989b918-5ba5-4696-b8d0-76ae510a23f4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath;bradsev;gokuma
ms.openlocfilehash: 59f1e4c5bf1c1a3ea4af42872246b5230d4c6d24
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="data-exploration-and-modeling-with-spark"></a>Adatáttekintés és modellezés a Spark segítségével
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Ez az útmutató az adatok feltárása ehhez használja a HDInsight Spark és bináris osztályozás és feladatok modellezési a következőt: a minta regressziós út taxiköltség és 2013 dataset díjszabás.  Az végigvezeti a lépéseken, a [adatok tudományos folyamat](http://aka.ms/datascienceprocess), végpontok közötti, egy HDInsight Spark-fürt kezelése és az Azure BLOB használatával tárolja az adatokat, majd a modellt. A folyamat felderíti és az Azure Storage-Blobból adatok visualizes, és ezután előkészíti az adatokat a prediktív modellek létrehozásához. Ezek a modellek buildre, a Spark MLlib eszközkészlet bináris osztályozás és regressziós modellezéshez hatékony feladatok elvégzésére.

* A **bináris osztályozási** feladat-e a út tipp fizetett előrejelzése céljából. 
* A **regressziós** feladata más tip szolgáltatások alapján tipp mennyisége előre jelezni. 

A modellek használjuk logisztikai és lineáris regressziós, véletlenszerű erdők és átmenetes súlyozott fák tartalmazza:

* [A SGD lineáris regressziós](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) egy lineáris regressziós modellt, amely Stochastic átmenetes módszeren (SGD) módszerrel és optimalizálási, valamint a szolgáltatás skálázás tipp összegek előre fizetett. 
* [A LBFGS logisztikai regresszió](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) vagy a "logit" regresszió egy regressziós modell kategorikus adatok besorolása ehhez a függő változó esetén használható. LBFGS egy látszólagos Newton optimalizálási algoritmus, amely megközelíti a Broyden – Fletcher – Goldfarb – Shanno (BFGS) algoritmus csak korlátozott mennyiségű memóriát használ, és a gépi tanulás széles körben használt.
* [Véletlenszerű erdők](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) döntési fák együttes vannak.  Összekapcsolásának sok döntési fa overfitting kockázatának csökkentéséhez. Véletlenszerű erdők regressziós és besorolási használ, és kezelni tud a kategorikus szolgáltatásokat, és annak a multiclass adatbesorolás beállításai. Azok szolgáltatás skálázás nem igényelnek, és képesek nemlinearitás rögzítése és kapcsolati funkció. Véletlenszerű erdők a legtöbb sikeres gépi tanulási modellek besorolás és regressziós egyikét.
* [Színátmenet súlyozott fák](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) együttes döntési fák. GBTs ismételt adatvesztés függvény minimalizálása érdekében a döntési fák betanítása. GBTs regressziós és besorolási használ és kezelni tud a kategorikus szolgáltatásokat, nincs szükség a méretezés szolgáltatás és képesek nemlinearitás rögzítése és kapcsolati funkció. Is is szerepel a multiclass-adatbesorolás beállításai.

A modellezési lépések a kód bemutatja, hogyan képzése, értékelje ki és mentse a modell típusonkénti is tartalmaznak. A megoldás code, és a megfelelő előkészítésére megjelenítése Python használatban van.   

> [!NOTE]
> Bár a Spark MLlib eszközkészlet célja, hogy működik a nagy adatkészleteket, viszonylag kis minta (KB. 30 Mb használatával 170K sorok, az eredeti NYC adatkészlet hamarosan 0,1 %) használható itt kényelmét szolgálja. Az itt megadott gyakorlat 2 munkavégző csomópontokhoz a HDInsight-fürtök a hatékony (KB) a fut. Ugyanazt a kódot, kisebb módosításokkal nagyobb-adatmennyiség megfelelő módosításával az adatokat a memóriában, és a fürt méretének módosítása feldolgozásához használható.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az Azure-fiók és a Spark 1.6-os (vagy külső 2.0) HDInsight-fürt forgatókönyv végrehajtásához. Tekintse meg a [áttekintése adatok tudományos Spark on Azure HDInsight használatának](spark-overview.md) útmutatást ezeknek a követelményeknek. Témakör az itt használt NYC 2013 Taxi adatokat és a kód végrehajtása a Spark-fürt Jupyter notebook útmutatást leírását is tartalmazza. 

## <a name="spark-clusters-and-notebooks"></a>A Spark-fürtök és notebookok
Beállítási lépéseket és kód okat ebben a forgatókönyvben egy HDInsight Spark 1.6 használatával. De Jupyter notebookok HDInsight Spark 1.6-os és a Spark 2.0 fürtök rendelkeznek. A jegyzetfüzetek és a hozzájuk hivatkozások leírása szerepelnek a [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) az azokat tartalmazó GitHub-tárházban. Ezenkívül a kód itt és a csatolt jegyzetfüzetekben általános és a Spark-fürt kell működnie. Ha nem használja a HDInsight Spark, a fürt beállítása, és lehet, hogy a felügyeleti lépések némileg eltér az itt látható. Kényelmi célokat szolgál az alábbiakban a hivatkozásokat a Jupyter notebookok Spark 1.6 (futtatásához a pySpark kernel a Jupyter Notebook kiszolgáló) és a Spark 2.0-s verzióját (a pySpark3 kernel a Jupyter Notebook kiszolgáló kell futtatni):

### <a name="spark-16-notebooks"></a>Spark 1.6-os notebookok

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): információt biztosít az adatok feltárása, modellezéséhez, és számos különböző algoritmusok pontozási végrehajtásához.

### <a name="spark-20-notebooks"></a>Spark 2.0 notebookok
A regresszió és besorolási feladatokat, amelyeket a rendszer egy külső 2.0 fürt használatával külön jegyzetfüzetek és a besorolási notebook használja egy másik adatkészletet:

- [Spark2.0-pySpark3-Machine-Learning-Data-Science-Spark-Advanced-Data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl hogyan hajthat végre az adatok feltárása, modellezési, és a Spark 2.0 pontozási fürtök NYC Taxi út és jegy ára adatkészlet leírt [Itt](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). A notebook gyorsan felfedezése a Spark 2.0 adtunk kódot az jó kiindulási pont lehet. További részletes notebook elemzi az NYC Taxi adatokat, lásd: a következő notebook ezen a listán. Tekintse meg a megjegyzéseket, a lista a következő, hasonlítsa össze ezeket a jegyzetfüzetek. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Ez a fájl bemutatja, hogyan végezhet wrangling (Spark SQL és dataframe műveletek), feltárása, modellezéséhez és a NYC Taxi út és a jegy ára adatkészlet leírt pontozási [Itt](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Ez a fájl bemutatja, hogyan végezhet wrangling (Spark SQL és dataframe műveletek), feltárása, modellezéséhez és pontozási a jól ismert légitársaság időben indító adatkészlet 2011 és a 2012. Azt integrálva az adatokkal repülőtéri időjárási (pl. szélsebesség, hőmérséklet, magasság stb.) a légitársaság dataset előtt modellezési, így időjárási felhasználásokhoz felvehetők a modell.

<!-- -->

> [!NOTE]
> A légitársaság dataset lett hozzáadva, a Spark 2.0 jegyzetfüzeteihez jobban a besorolási algoritmusok használatát mutatja be. Tekintse meg a következőket légitársaság kapcsolatos információk időben indító dataset és időjárási adatkészlet:

>- Légitársaság időben indító adatok: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Repülőtéri időjárási adatok: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
A Spark 2.0 jegyzetfüzeteket az NYC taxi és légitársaság repülési késleltetés-készleteket is igénybe vehet, 10 perc vagy több (attól függően, hogy a HDI-fürtnek a mérete). A fenti listában első notebook mutatja az adatok feltárása sok területén, a képi megjelenítés és ML minta egy jegyzetfüzetet le mintát NYC adatkészlet, amelyben a taxi és jegy ára fájlok voltak előre illesztett futtatásához kevesebb időt vesz igénybe, a képzési: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) jegyzetfüzet (2-3 perc) befejezéséhez sokkal rövidebb ideig tart, és előfordulhat, hogy lehet Jó kiindulópont gyorsan felfedezése a Spark 2.0 adtunk kódot. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
Az alábbi leírásokat Spark 1.6 használatához kapcsolódó. Spark 2.0-s verziói a notebookok ismertetett és a fentebbiekben hivatkozott használjon. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Telepítő: tárolóhelyek, könyvtárak, és az előre definiált Spark környezet
Spark el tudja olvasni és írni az Azure Storage-blobba (más néven WASB). A meglévő adatok tárolása nem tudja feldolgozni használata Spark- és az eredmények WASB újra tárolja.

A WASB modellek vagy a fájlok mentéséhez az elérési utat kell megadni megfelelően. Az alapértelmezett tároló, a Spark-fürt csatolva egy elérési utat kezdetű használatával lehet hivatkozni: "wasb: / / /". Más helyeken által hivatkozott "wasb: / /".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Tárolási helye a WASB set könyvtár elérési útja
A következő példakód határozza meg a helyét, olvassa el az adatokat, valamint a, amelyhez a modell mentésekor modell tároló könyvtár elérési útja:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Könyvtárak importálása
Beállítása is szükséges a szükséges kódtárak importálása. Spark környezet beállítása, majd importálja a szükséges kódtárak az alábbi kódra:

    # IMPORT LIBRARIES
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
A Jupyter notebookok kapnak PySpark kernelt beállításkészletet kontextusban rendelkezik. Így nem kell beállítani a Spark, vagy Hive-környezeteket elindítása az alkalmazás használata előtt explicit módon fejleszt. Ezek a környezetek érhetők el, alapértelmezés szerint. Ezek a környezetek a következők:

* sc - a Spark 
* az sqlContext - struktúra

A PySpark kernel tartalmaz néhány előre definiált "magics", amelyeket speciális meghívhatja a parancsok %%. Nincsenek két ilyen parancsot a következő kód mintákat használt.

* **%% helyi** határozza meg, amely a kódot az egymás utáni sorok helyileg hajthatnak végre. Kód érvényes Python-kódot kell lennie.
* **%% sql -o <variable name>**  végrehajtja a Hive-lekérdezések a sqlContext ellen. Ha az -o paramétert, a lekérdezés eredménye megőrződjön-e az a %%, egy Pandas DataFrame helyi Python-környezetben.

További információ a Jupyter notebookokból és az előre meghatározott kernelek "magics", amely a biztosítanak, lásd: [HDInsight Spark Linux és a Jupyter notebookok elérhető kernelek a HDInsight-fürtök](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>A nyilvános blob adatfeldolgozást
Az első lépés az adatok tudományos folyamatban elemzendő forrásokból származó adatok hol van az adatok feltárása és modellezési környezetbe helyezkedik el. A környezet Spark ebben a forgatókönyvben. Ez a szakasz a kód feladatok sorozatát befejezéséhez:

* betöltési modellezni adatok minta
* olvassa a bemeneti adatkészletet (.tsv fájlként tárolja)
* formázza és az adatok törlése
* Hozzon létre és objektumok (RDDs vagy adatkeretek) a memóriában gyorsítótárazása
* regisztrálja az SQL-környezetben temp-táblázatként.

A kód adatfeldolgozást az itt látható.

    # INGEST DATA

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
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

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**A KIMENETRE:**

Cella fent ideje: 51.72 másodperc

## <a name="data-exploration--visualization"></a>Az adatok feltárása & képi megjelenítés
Miután az adatok külső üzembe, az tudományos folyamat következő lépése hoz mélyrehatóbb ismereteket szerezhet az adatok feltárása és a képi megjelenítés keresztül. Ebben a részben azt vizsgálja meg az SQL-lekérdezések használatával taxi adatok és a cél változók és a visual hálózatfelügyeleti potenciális funkcióit megrajzolásához. Pontosabban azt megrajzolásához utas számát taxi való adatváltások számát, a gyakoriság tipp díjak és hogyan tippek változhat fizetési mennyiségét, és írja be a gyakoriságát.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>A mintában taxi utak utas száma gyakoriságértékek listáját hisztogram ábrázolása
A kód és a további részletek a minta és az adatok ábrázolására helyi magic lekérdezése SQL magic segítségével.

* **SQL magic (`%%sql`)** a HDInsight PySpark kernel a sqlContext könnyen beágyazott HiveQL lekérdezéseket támogatja. A (-o VARIABLE_NAME) argumentum az SQL-lekérdezés kimenetét a Jupyter kiszolgálón egy Pandas DataFrame, továbbra is fennáll. Ez azt jelenti, hogy a helyi módban érhető el.
* A  **`%%local` magic** kód futtatása helyben a Jupyter kiszolgálón, amely a HDInsight-fürt headnode szolgál. Általában akkor használják `%%local` magic együtt a `%%sql` magic -o paraméterrel. Az -o paraméter szeretné megőrizni a helyileg az SQL-lekérdezés kimenete, majd %% helyi magic kiváltották futtatásához helyi SQL-lekérdezések eredményének helyileg fennállásának kódrészletet a következő készlete

A kimeneti automatikusan történik, a kód futtatása után.

Ez a lekérdezés lekéri a utazgatással utas száma szerint. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Ez a kód létrehoz egy helyi adatok-keret a lekérdezés eredményében, és az adatok tevékenységtérkép. A `%%local` magic létrehoz egy helyi adatok-keret, `sqlResults`, a matplotlib ábrázolásához használható. 

> [!NOTE]
> A PySpark magic ebben a bemutatóban több alkalommal van használva. Nagy adatmennyiség esetén a kell minta lehet adatok-keret létrehozásához a helyi memóriához.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Útjaira által utas száma ábrázolásához kód

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**A KIMENETRE:**

![Út gyakoriság utas száma szerint](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Között számos különböző típusú megjelenítések (táblázat, torta, vonal, terület vagy sáv) segítségével kiválaszthatja a **típus** a notebook menü gombjai. A sáv rajzot itt jelenik meg.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Tipp összegek és hogyan függ a tipp összeg utas számát és a jegy ára összegek hisztogram ábrázolásához.
Mintaadatokat egy SQL-lekérdezést használja.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped 
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7 
    AND fare_amount > 0 
    AND fare_amount < 200 
    AND payment_type in ('CSH', 'CRD') 
    AND tip_amount > 0 
    AND tip_amount < 25


Ez a kód cella használja az SQL-lekérdezést három előkészítésére az adatokat.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**A KIMENETRE:** 

![Tipp összeg terjesztési](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tipp összeg utas száma szerint](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipp jegy ára mennyiséggel összeg](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>A funkció modellezési mérnöki csapathoz, átalakítása és az adatok előkészítése
Ez a szakasz ismerteti, és lehetővé teszi a kód készíti elő az adatok ML modellezési használható használt eljárásokat. Azt mutatja be a következő feladatok elvégzéséhez:

* Hozzon létre egy új szolgáltatás a forgalom idő gyűjtők dobozolás óránként
* Index és kategorikus szolgáltatások kódolása
* Gépi tanulás függvényekké bemeneti címkézett pont objektumok létrehozása
* Hozzon létre egy véletlenszerű alárendelt mintavétel az adatok, és ossza képzési és egy tesztelési
* A szolgáltatás skálázás
* A memóriában objektumok

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Hozzon létre egy új szolgáltatás a forgalom idő gyűjtők dobozolás óránként
Ez a kód egy új szolgáltatás létrehozása a dobozolás órával a forgalom idő gyűjtők és az eredményül kapott adatok keret memóriában gyorsítótárazásának jeleníti meg. Ha ismételten elosztott rugalmas adatkészleteket (RDDs) és adatkeretek használnak, továbbfejlesztett végrehajtásának lassúságát gyorsítótárazás vezet. Ennek megfelelően azt a gyorsítótár RDDs és adatkeretek, a forgatókönyv több lépésből áll. 

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**A KIMENETRE:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Index és a bemenő funkciók modellezési kategorikus funkciói kódolása
Ez a szakasz bemutatja, hogyan index, vagy a modellezési függvényekké bemeneti kategorikus szolgáltatások kódolása. A modellezési és előrejelzése MLlib feladatai szükséges szolgáltatások kategorikus indexelt vagy használata előtt kódolású bemeneti adatokkal. Attól függően, hogy a modell szeretnénk index vagy kódolás különböző módon:  

* **Fa-alapú modellezési** numerikus értékként kódolni kategóriák igényel (például három kategóriába szolgáltatás előfordulhat, hogy kódolhatók 0, 1, 2). Ez biztosítja a MLlib [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) függvény. Ez a függvény egy karakterlánc-oszlopnak címke indexek címke gyakoriságot szerint rendezett oszlophoz címkék kódolja. Bár az indexelt numerikus értékeket kell megadni a bemeneti és a kezelése, a fa-alapú algoritmusok megfelelően kezeli őket kategóriák adható meg. 
* **Logisztikai és lineáris regressziós modell** egy közbeni kódolási funkciójához szükséges, ha például három kategóriába szolgáltatás bővíthetők minden tartalmazó 0 vagy 1 attól függően, hogy egy megfigyelési kategória három funkció oszlopokba. MLlib biztosít [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) függvényét egy közbeni kódolást. A kódoló címke indexek oszlop bináris vektorok értékű legfeljebb egyetlen egy-egy oszlop rendeli hozzá. Ez a kódolás lehetővé teszi, hogy a várt numerikus fontos funkciók, például logisztikai regresszió kategorikus szolgáltatások alkalmazandó algoritmusokat.

Index és kategorikus szolgáltatások kódolja a kód itt látható:

    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
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

    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

Cella fent ideje: 1.28 másodpercben

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Gépi tanulás függvényekké bemeneti címkézett pont objektumok létrehozása
Ez a szakasz bemutatja, hogyan kategorikus szöveges adatok címkézett pont adattípusú értékként index és, hogy képzése és MLlib logisztikai regresszió és egyéb besorolási modell teszteléséhez használható kódolása kódját tartalmazza. Címkézett pont objektum rugalmas elosztott adatkészletek (RDD) formázott oly módon, hogy a bemeneti adatként ML algoritmusok MLlib a legtöbb van szükség. A [pont feliratú](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) egy helyi vektor, sűrű vagy ritka, társítva van egy címke-válasz.  

Ez a szakasz kódot tartalmaz, amely bemutatja, hogyan kategorikus szöveg adatait, mivel az index egy [pont feliratú](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) adattípusúra, és így képzése és MLlib logisztikai regresszió és egyéb besorolási modell teszteléséhez használható kódolása. Címkézett pont objektum rugalmas elosztott adatkészletek (RDD) címke (cél-válasz változó) és a szolgáltatás vektoros. Ezt a formátumot a MLlib számos ML algoritmus bemeneti van szükség.

Ez a kód index és a bináris osztályozási funkciói szöveg kódolása.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Ez a kód kódolásához és lineáris regressziós elemzési szolgáltatásainak kategorikus szöveg index.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])

        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Hozzon létre egy véletlenszerű alárendelt mintavétel az adatok, és ossza képzési és egy tesztelési
Ez a kód hoz létre egy véletlenszerű mintavételi (25 % itt használt) adatok. Bár nem szükséges ehhez a példához a mérete miatt a DataSet adatkészlet, bemutatjuk, hogyan segítségével mintavételi itt így megtudhatja, hogy miképpen lehet vele a saját probléma, ha szükséges. Ha minták nagy, ez is tanítási modell jelentős időt takaríthat meg. Ezután azt ossza fel a minta egy képzési (Itt 75 %) és egy tesztelési részét (25 %-os itt) használata a besorolás és regressziós modellezéshez hatékony.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

Cella fent ideje: 0,24 másodpercben

### <a name="feature-scaling"></a>A szolgáltatás skálázás
Szolgáltatás skálázást, más néven adatok normalizálási biztosítja, hogy szolgáltatások széles körben folyósított értékekkel van nem a megadott túlzott mérjük a objektív függvényben. A szolgáltatás által használt skálázás kódját a [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) méretezési egység eltérése a szolgáltatásokat. Ez biztosítja MLlib lineáris regressziós rendelkező Stochastic átmenetes módszeren (SGD), egy népszerű más gépi tanulási modellek például rendeződik regresszió vagy támogatási vektoros gépek (SVM) számos különféle képzési algoritmus használható.

> [!NOTE]
> Találtunk, amelyeknek a LinearRegressionWithSGD algoritmust kell skálázás funkció-és nagybetűket.
> 
> 

Ez a kód méretezési változók a regularized lineáris SGD algoritmus való használatra.

    # FEATURE SCALING

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils

    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

Cella fent ideje: 13.17 másodperc

### <a name="cache-objects-in-memory"></a>A memóriában objektumok
A modell betanítására és tesztelésére ML algoritmusok szükséges idő a bemeneti adatok keret objektumok az osztályozás, regressziós, és a szolgáltatások méretezhető gyorsítótárazásával csökkenthető.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()

    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:** 

Cella fent ideje: 0,15 másodpercben

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Függetlenül attól, tipp bináris osztályozási modellekkel való fizetnek előrejelzése
Ez a szakasz bemutatja, hogyan használható három modelleket használnak becslése a bináris osztályozási feladatát tipp taxi útnak fizetnek-e. A modell jelenik meg a következő:

* Logisztikai regresszió rendeződik 
* Véletlenszerű erdőmodell
* Átmenet kiemelési fák

Egyes modellek kód szakasz felépítése lépéseket oszlik: 

1. **A modell betanítási** egy paraméterhalmaz adatok
2. **A modell kiértékelése** a metrikák a teszt adatkészlet
3. **Modell mentése** BLOB a jövőbeni felhasználásához

### <a name="classification-using-logistic-regression"></a>A besorolási logisztikai regresszió segítségével
Ebben a szakaszban a kód bemutatja, hogyan betanítása, értékelje ki és logisztikai regresszió modell mentése [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , amely képes-e tipp fizetnek NYC taxi út és a jegy ára adatkészlet útnak.

**A KtgE és hyperparameter abszolút logisztikai regresszió modell betanításához**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics


    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**A KIMENETRE:** 

Együttható: [0.0082065285375,-0.0223675576104,-0.0183812028036, - 3.48124578069e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

INTERCEPT:-0.0111216486893

Cella fent ideje: 14.43 másodperc

**A szabványos metrikák bináris osztályozási modell kiértékelése**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**A KIMENETRE:** 

Törlés a ka területen = 0.985297691373

ROC területen = 0.983714670256

Összesített statisztikák

Pontosság = 0.984304060189

Visszahívása = 0.984304060189

F1 Pontozása = 0.984304060189

Cella fent ideje: 57.61 másodperc

**A: ROC-görbe ábrázolásához.**

A *predictionAndLabelsDF* táblaként, regisztrálva van *tmp_results*, az előző cella. *tmp_results* segítségével do lekérdezések és a kimeneti eredmények keretbe sqlResults adatok-ábrázolásához. A kód itt látható.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Ez a kód előrejelzéseket készítsen, és a ROC-görbe megrajzolásához.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**A KIMENETRE:**

![Logisztikai regresszió ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Véletlenszerű erdő besorolás
Ebben a szakaszban a kód bemutatja, hogyan betanítása, értékelje ki és mentése egy véletlenszerű erdő modell, amely képes-e tipp NYC taxi út és a jegy ára adatkészlet útnak fizetnek.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

ROC területen = 0.985297691373

Cella fent ideje: 31.09 másodperc

### <a name="gradient-boosting-trees-classification"></a>Átmenet kiemelési fák besorolás
Ebben a szakaszban a kód bemutatja, hogyan képzése, értékelje ki, és átmenetes kiemelési fák modell, amely képes-e tipp egy út a következőt: taxi út a fizetett mentéséhez és díjszabás adatkészlet.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;

    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**A KIMENETRE:**

ROC területen = 0.985297691373

Cella fent ideje: 19.76 másodperc

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Tipp összegek taxi utakat regressziós modell előrejelzése
Ez a szakasz bemutatja, hogyan használja a tipp mennyisége előrejelzésére regressziós feladatának három licencmodellek fizetett taxi útnak más tip szolgáltatások alapján. A modell jelenik meg a következő:

* Lineáris regressziós rendeződik
* Véletlenszerű erdő
* Átmenet kiemelési fák

Ezek a modellek leírt a Bevezetés. Egyes modellek kód szakasz felépítése lépéseket oszlik: 

1. **A modell betanítási** egy paraméterhalmaz adatok
2. **A modell kiértékelése** a metrikák a teszt adatkészlet
3. **Modell mentése** BLOB a jövőbeni felhasználásához

### <a name="linear-regression-with-sgd"></a>A SGD lineáris regressziós
A kódot az itt látható egy lineáris regressziós optimális stochastic átmenetes módszeren (SGD) használó betanítása méretezett szolgáltatások használatának, pontozása, értékelje ki és mentse a modellt az Azure Blob Storage (WASB).

> [!TIP]
> Az a tapasztalat LinearRegressionWithSGD modellek konvergencia problémái lehetnek, és paraméterek kell lennie a megváltozott/optimalizálva gondosan megszerezni egy érvényes modell. A változók jelentősen skálázás elősegíti a konvergencia. 
> 
> 

    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats

    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))

    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)

    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

Együttható: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

INTERCEPT: 0.853872718283

GYÖKÁTLAGOS = 1.24190115863

R-sqr = 0.608017146081

Cella fent ideje: 58.42 másodperc

### <a name="random-forest-regression"></a>Véletlenszerű erdő regressziós
Ebben a szakaszban a kód bemutatja, hogyan képzése, értékelje ki és mentse egy véletlenszerű erdő regressziós, hogy a NYC taxi út adatok tipp összeg előrejelzi.

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

GYÖKÁTLAGOS = 0.891209218139

R-sqr = 0.759661334921

Cella fent ideje: 49.21 másodperc

### <a name="gradient-boosting-trees-regression"></a>Átmenet kiemelési fák regressziós
Ebben a szakaszban a kód bemutatja, hogyan betanítása, kiértékeléséhez és átmenetes kiemelési fák modell, amely képes tipp összeg a következőt: taxi út adatok mentése.

** Betanítása és kiértékelése **

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

GYÖKÁTLAGOS = 0.908473148639

R-sqr = 0.753835096681

Cella fent ideje: 34.52 másodperc

**Ábrázolása**

*tmp_results* az előző cella Hive tábla néven van regisztrálva. Kerülnek a kimenetbe az eredményeket a táblából a *sqlResults* adatok-keret ábrázolásához. A kód itt látható

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

A Jupyter kiszolgálóval az adatok ábrázolása a kód itt látható.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)


**A KIMENETRE:**

![Tényleges-vs-előre jelezni-tipp-díjak](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>A memóriából objektumainak eltávolítása
Használjon `unpersist()` jelenleg a memóriában lévő objektumok törlése.

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()

    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>A modellek felhasználása és pontozási rekord tárolóhelyek
Fogadni, és egy független adatkészlet ismertetett pontozása a [pontszám és értékelje ki a Spark-beépített gépi tanulási modellek](spark-model-consumption.md) másolja és illessze be a következő fájl neve, a mentett a fogyasztás Jupyter notebook az itt létrehozott modelleket tartalmazó kell a témakörben. Ez a kód nyomtassa ki a van szüksége a modell fájlok elérési útjait.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**KIMENETI**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>A következő lépések
Most, hogy a Spark MlLib regressziós és besorolási modell hozott létre, készen áll útmutató pontozása, és ezek a modellek kiértékeléséhez. A speciális adatok feltárása és a notebook modellezési dives mélyebb kereszt-ellenőrzési, hyper paraméter abszolút, beleértve azokat, és a modell kiértékelése. 

**Modellhez tartozó felhasználás:** pontozása és kiértékelheti a besorolás és regressziós modellek létrehozása ebben a témakörben, lásd: [pontszám és értékelje ki a Spark-beépített machine learning modellek](spark-model-consumption.md).

**Kereszt-ellenőrzési és hyperparameter abszolút**: lásd: [speciális adatok feltárása és Spark modellezés](spark-advanced-data-exploration-modeling.md) meg, hogyan lehet a modellek betanítása a kereszt-ellenőrzési és a hyper-paraméter abszolút használatával

