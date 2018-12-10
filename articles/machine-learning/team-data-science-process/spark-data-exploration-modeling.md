---
title: Adatáttekintés és modellezés a Spark - csoportos adatelemzési folyamat
description: Bemutatja az adatok feltárására és modellezés képességeit a Spark MLlib eszközkészlet az Azure-ban.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ae498cc6aea573e1c610cb50d96552f30be4d75e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140880"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Adatáttekintés és modellezés a Spark segítségével

Ez az útmutató a HDInsight Spark használja az adatok feltárása ehhez és bináris besorolási és regressziós tevékenységek modellezése a NYC mintában taxiköltség utazást és 2013 adatkészlet díjszabás.  Emellett végigvezeti a lépéseken, a [adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), a végpontok közötti, feldolgozási és az Azure-blobok esetében a HDInsight Spark-fürt használata az adatok és a modellek. A folyamatot ismerteti, és egy Azure Storage-blobból adatok elérhetővé, és ezután előkészíti az adatokat, prediktív modelleket hozhat létre. Ezek a modellek olyan bináris besorolási és regressziós modellezéshez feladatokat végezheti el a Spark MLlib eszközkészlet használatával hozhat létre.

* A **bináris osztályozási** tevékenység-e az utazás tipp fizetős előrejelzésére. 
* A **regressziós** feladata előre jelezni a funkciókat a tip alapján tipp mennyisége. 

A modellek használjuk a logisztikai és lineáris regresszió, véletlenszerű erdők és gyorsított fa átmenetes tartalmazza:

* [A SGD lineáris regressziós](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) egy lineáris regressziós modellt, amely egy Sztochasztikus átmenetes Grádiens (SGD) módszert használja, és optimalizálása és a szolgáltatás méretezési lehetőségek érhetők el a tip mennyiségeket előrejelzése kifizetett. 
* [A LBFGS logisztikai regressziós](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) , vagy "logit" regressziós, egy regressziós modellt, amely a kategorikus adatok besorolása ehhez a függő változó esetén használható. LBFGS egy kvázi Newton algoritmus, amely megközelíti a Broyden – Fletcher – Goldfarb – Shanno (BFGS) algoritmus használatával korlátozott mennyiségű memóriát és széles körben használt a machine Learning szolgáltatásban.
* [Véletlenszerű erdők](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) együttesek döntési fák vannak.  Overfitting kockázatának csökkentése érdekében számos döntési fák kombinálhatja azokat. Véletlenszerű erdők regressziós és besorolási használ, és kezelhetik a kategorikus funkciók és többosztályos osztályozási beállítás is kiterjeszthető. Ezek nem igénylik a szolgáltatás méretezése, és képesek nemlinearitás rögzítése és a szolgáltatás kapcsolati. Véletlenszerű erdők a legsikeresebb gépi tanulási besorolási és regressziós modellek tartoznak.
* [Színátmenet súlyozott fák](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) együttesek döntési fák. GBTs iteratív, hogy minimalizálják az adatvesztést függvény döntési fák betanítása. GBTs regressziós és besorolási használ és kezelhetik a kategorikus funkciókat, nincs szükség a szolgáltatás méretezése, és képesek nemlinearitás rögzítése és a szolgáltatás kapcsolati. Ezek is használható osztályú-besorolás beállításban.

A modellezés lépéseket is bemutató betanításához, kiértékelheti és mentse a modell különböző típusú kódot tartalmaznak. Python-kód a megoldás és a megfelelő grafikon megjelenítése használatban van.   

> [!NOTE]
> A Spark MLlib eszközkészlet tervezték Nagy adatkészleteken, bár egy viszonylag kis minta (KB. 30 Mb 170K sorok, körülbelül 0,1 %-át az eredeti NYC adatkészlet használatával) használatos itt kényelmi célokat szolgál. Az itt megadott gyakorlat futtat hatékonyan (körülbelül 10 percet) a 2 feldolgozó csomópontot egy HDInsight-fürtöt. Ugyanazt a kódot, kisebb módosításokkal segítségével adathalmazokba nagyobb –, a megfelelő módosításokat az adatokat a memóriában, és a fürt méretének módosítása.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az Azure-fiók és a egy Spark 1.6-os (vagy a Spark 2.0-s) van szüksége a forgatókönyv végrehajtásához HDInsight-fürtön. Tekintse meg a [áttekintése az adatelemzés az Azure HDInsight Spark használatával](spark-overview.md) ezeknek a követelményeknek való létrehozásával kapcsolatos útmutatást. A témakör leírását a NYC 2013-i taxik adatait itt használt, és hajtsa végre a Spark-fürtön lévő Jupyter notebook kód útmutatást is tartalmaz. 

## <a name="spark-clusters-and-notebooks"></a>A Spark-fürtök és jegyzetfüzetek
Beállítási lépéseket és a kód-okat Ez az útmutató egy HDInsight Spark 1.6-os használatával. De Jupyter notebookok a HDInsight Spark 1.6-os és a Spark 2.0 fürtök biztosított. A jegyzetfüzetek és a rájuk mutató hivatkozást leírása tartalmazza a [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) az azokat tartalmazó GitHub-adattárban. Továbbá, a kód itt és a társított notebookok az általános és működnie kell bármelyik Spark-fürtön. Ha nem használja a HDInsight Spark, a fürt beállítása és lehet, hogy a felügyeleti lépések kissé eltérhetnek az itt látható. Az egyszerűség kedvéért az alábbiakban a Spark 1.6-os (a futtatható a pySpark kernel a Jupyter Notebook Server) és a Spark 2.0-s verzióját (kell futtatni a pySpark3 kernel a Jupyter Notebook Server) a Jupyter notebookok mutató hivatkozásokat:

### <a name="spark-16-notebooks"></a>A Spark 1.6-os notebookok

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): információt nyújt az adatfeltárás, modellezés és kiértékelés a számos különböző algoritmus végrehajtásához.

### <a name="spark-20-notebooks"></a>A Spark 2.0 notebookok
A Spark 2.0-fürt segítségével megvalósított regressziós és besorolási feladatokat külön jegyzetfüzetek és a besorolási notebookot használja egy másik adatkészletet:

- [Spark2.0-pySpark3-Machine-Learning-Data-Science-Spark-Advanced-Data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl információt nyújt az adatfeltárás, modellezés, végrehajtására és pontozás a Spark 2.0-fürtök használatával a NYC Taxi út és diszkont-adatkészlet leírt [Itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Ez a jegyzetfüzet gyorsan megismerheti a kódot, a Spark 2.0 biztosítunk jó kiindulási pont lehet. Részletesebb jegyzetfüzet a NYC i taxik adatait elemzi, lásd a következő notebook ebben a listában. Lásd az alábbi listát, hasonlítsa össze ezeket a notebookokat. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Ez a fájl bemutatja, hogyan hajtsa végre az adatok konvertálását (Spark SQL- és dataframe műveletek), feltárásában, modellezés és a NYC Taxi utazást és a leírt diszkont adatkészlet pontozás [Itt ](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Ez a fájl bemutatja, hogyan hajtsa végre az adatok konvertálását (Spark SQL- és dataframe műveletek), feltárásában, modellezés és pontozás a jól ismert légitársaság időben indító használatával a 2011 és 2012 adatkészlet. Integráltuk az repülőtér időjárási adatok (például szélsebesség, hőmérséklet, tengerszint stb.) a légitársaság adatkészlet előtt modellezés, ezért ezek időjárási szolgáltatások is tartalmazza, a modellben.

<!-- -->

> [!NOTE]
> A légitársaság adatkészlet hozzá lett adva a Spark 2.0 notebookok jobban a besorolási algoritmusok használatát mutatja be. Lásd az alábbi hivatkozásokat légitársaság kapcsolatos információk időben indító adatkészletet és időjárási adatkészletet:

>- Légitársaság időben indító adatok: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Időjárási adatok repülőtér: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
A Spark 2.0 notebookok a NYC taxi és légitársaság repülési késleltetés-adatkészleteken alapuló is igénybe vehet, 10 perc vagy több (függően a HDI-fürt mérete). A fenti listában az első jegyzetfüzet mutatja az adatfeltárás számos aspektusát, megjelenítési és gépi Tanulási modell egy jegyzetfüzetet, futtathat le mintavételezés NYC adatkészlet, amelyben a taxi és diszkont fájlok már előre illesztett kevesebb időt vesz igénybe a képzés: [ Spark2.0-pySpark3-Machine-Learning-Data-Science-Spark-Advanced-Data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Ez a jegyzetfüzet (2-3 perc) befejezéséhez sokkal rövidebb ideig tart, és előfordulhat, hogy lehet egy jó kiindulási pontjaként gyorsan megismerheti a kód adtunk meg a Spark 2.0-s. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
A lenti leírások Spark 1.6-os használatához kapcsolódó. A Spark 2.0-s verziójához használja a notebookok ismertetett és a fentebbiekben hivatkozott. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>A telepítő: tárolási helyek, kódtárak és az előre beállított Spark-környezetben
A Spark is képes olvasni és írni az Azure Storage-Blob (más néven WASB). A meglévő adatokat tárolja, Nincs feldolgozható használata Spark- és WASB újra tárolja az eredményeket.

Szeretné menteni a modellek vagy fájlokat a WASB, az elérési utat meg kell határozni megfelelően. Az alapértelmezett tároló csatlakozik a Spark-fürtöt egy elérési út kezdetű használatával lehet hivatkozni: "wasb: / / /". Más helyeken által hivatkozott "wasb: / /".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Állítsa be a tárolási helyek elérési utak a WASB
Az alábbi kódmintában kell olvasni az adatokat, és a könyvtár elérési útját modell tárolási, amelyhez a modell kimeneti mentett helyét adja meg:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Könyvtárak importálása
Állítsa be a is szükséges a szükséges kódtárak importálása. Állítsa be a spark környezet, és importálja a szükséges kódtárak a következő kóddal:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Előre beállított Spark-környezet és a PySpark magics
A PySpark-kernelek Jupyter notebookok a kell egy előre beállított környezetet. Így nem kell beállítani a Spark vagy a Hive-környezetek az alkalmazás használatának megkezdése előtt explicit módon fejleszti. Ezek a környezetek érhetők el alapértelmezés szerint. Ezek a környezetek a következők:

* sc - és Spark 
* kontext sqlContext – Hive

A PySpark kernel tartalmaz néhány előre meghatározott "magics", amelyek különleges parancsok, amelyek segítségével meghívhatja a %%. Nincsenek két ilyen parancsot a kód a példákon használt.

* **%% helyi** határozza meg, amely a kódot a további sorokban helyileg kell végrehajtani. Kód érvényes a Python-kód kell lennie.
* **%% sql -o <variable name>**  ellen a kontext sqlContext Hive-lekérdezést hajt végre. Az -o paramétert, ha a lekérdezés eredménye a rendszer megőrzi a %% Pandas DataFrame helyi Python-környezetben.

További információk a-kernelek Jupyter-notebookok és az előre meghatározott "magics", amely számára biztosítanak, lásd: [notebookokhoz elérhető kernelek Jupyter notebookok a HDInsight Spark Linux-fürtök HDInsight az](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Adatbetöltés nyilvános blobból
Az adatelemzési folyamat első lépése a forrásból származó elemezni az adatokat, hogy hol van az adatok feltárására és modellezés környezetben található. A környezet ebben az útmutatóban a Spark. Ez a szakasz tartalmazza a kódot, és végezze el a tevékenységeket:

* betöltési modellezni az adatokat minta
* olvassa el a bemeneti adatkészlet (.tsv-fájlként tárolja)
* az adatok formázása és
* Hozzon létre és objektumok (rdd-k vagy adatkeretek) a memóriában gyorsítótárazza
* regisztrálja az SQL-környezetben temp-táblaként.

A kód Adatbetöltési itt látható.

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

## <a name="data-exploration--visualization"></a>Az adatok feltárása és képi megjelenítés
Miután az adatok Spark üzembe, az adatelemzési folyamat következő lépése az az adatokat adatáttekintési és vizualizációs mélyrehatóbb ismereteket szerezhet. Ebben a szakaszban azt az SQL-lekérdezések használatával taxi adatok vizsgálatát és jeleníti meg a cél változók és vizuális ellenőrzése az leendő szolgáltatásai. Pontosabban azt jeleníti meg az utasok számát taxi lelassítja tipp összegeket a gyakoriság és hogyan tippek régiónként eltérő fizetési összeg, és írja be a gyakorisága.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Nyomtatandó hisztogram utas száma gyakoriságok taxi lelassítja-minta
A kód és a következő kódrészletek használata SQL Magic Quadrant a minta és az adatok ábrázolásához helyi Magic Quadrant lekérdezéséhez.

* **SQL Magic Quadrant (`%%sql`)** a HDInsight PySpark kernelt támogatja az egyszerű beágyazott HiveQL-lekérdezéseket futtassanak a kontext sqlContext. A (-o VARIABLE_NAME) argumentum az SQL-lekérdezés kimenetét a Jupyter kiszolgálón egy Pandas DataFrame, továbbra is fennáll. Ez azt jelenti, hogy a helyi módban érhető el.
* A  **`%%local` Magic Quadrant** kód futtatása a Jupyter-kiszolgálón, amelyen a HDInsight-fürt átjárócsomópontjával helyben szolgál. Általában használni `%%local` magic együtt a `%%sql` magic -o paraméterrel. Az -o paraméter helyileg az SQL-lekérdezés kimenete szeretné megőrizni, majd %% helyi Magic Quadrant kódrészletet, amely a helyi rendszer megőrzi az SQL-lekérdezések kimenetének helyi futtatásához a következő készletét lép működésbe.

A kód futtatása után a rendszer automatikusan vizualizálja a kimenetet.

Ez a lekérdezés lekéri a lelassítja utas száma szerint. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Ez a kód létrehoz egy helyi adatkeretbe a lekérdezés kimenetét, és jeleníti meg az adatokat. A `%%local` Magic Quadrant létrehoz egy helyi-adatkeretbe, `sqlResults`, amelyek használhatók a matplotlib ábrázolásához. 

> [!NOTE]
> A PySpark Magic Quadrant Ez az útmutató több alkalommal van használva. Nagy adatmennyiség esetén meg kell minta hozzon létre egy adat-keretet, amelyek illeszkednek a helyi memóriához.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

A nyomtatandó utas száma szerint a lelassítja kódja

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

![Utazás gyakorisága utas száma szerint](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Választhat a különböző típusú Vizualizációk (tábla, a kör, vonal, terület vagy sáv) közül a **típus** menü gombok a jegyzetfüzet. A sáv diagram itt jelenik meg.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Tipp összegek és hogyan tipp összeg eltérő utas száma és diszkont összegek hisztogram ábrázolásához.
Használja a mintaadatok az SQL-lekérdezést.

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


A kódcella az SQL-lekérdezést használ, az adatok három grafikon létrehozásához.

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

![Összeg terjesztési tipp](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tipp összeg utas száma szerint](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipp összeg diszkont értékkel](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>A szolgáltatás modellezési mérnöki csapathoz, átalakítási és az adatok előkészítése
Ez a szakasz ismerteti, és a kódot biztosít az adatok előkészítéséhez használt gépi Tanulási modellezési használható eljárások. Azt mutatja be a következő feladatokat végezheti el:

* Hozzon létre egy új szolgáltatás be forgalom idő gyűjtők dobozolási órák száma alapján
* Index és a kategorikus funkciók kódolása
* A gépi Tanulási funkciók be címkézett pont objektumok létrehozása
* Hozzon létre egy véletlenszerű alárendelt mintavétel az adatok, és azt fel képzési és egy tesztelési
* A szolgáltatás méretezése
* A memóriában objektumok

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Hozzon létre egy új szolgáltatás be forgalom idő gyűjtők dobozolási órák száma alapján
Ez a kód bemutatja, hogyan hozhat létre egy új szolgáltatás dobozolási órák száma alapján forgalom idő gyűjtők be, majd az eredményül kapott adathalmaz a memóriában gyorsítótárazza. Ha ismételten rugalmas elosztott adatkészleteket (rdd-k) és a data-keretek használnak, továbbfejlesztett végrehajtási időpontok gyorsítótárazás vezet. Ennek megfelelően a azt gyorsítótár rdd-k és a data-keretek, a forgatókönyv több lépésből áll. 

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

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Index és a kategorikus funkcióinak be modellezési funkciók kódolása
Ez a szakasz bemutatja, hogyan index, vagy a modellezési funkciók be kategorikus funkciói kódolása. A modellezés és MLlib függvényekben kategorikus indexelve vagy használata előtt kódolású bemeneti adatokat a funkciókat. A modelltől függően kell index vagy más módon kell kódolnia azokat:  

* **Fa-alapú modellezési** kategóriák a numerikus értékek kódolható igényel (például három kategóriába szolgáltatás is kódolhatók 0, 1, 2). Ez a MLlib által biztosított [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) függvény. Ez a függvény egy karakterláncokat tartalmazó oszlopot tartalmazó oszlop, amely címke gyakoriságok szerint vannak rendezve: címke indexek, címkék kódolja. Numerikus értékek a bemenetekhez és adatkezeléssel-indexelt, bár a fa-alapú algoritmusok, megfelelően kezeli őket kategóriák adható meg. 
* **Logisztikai és lineáris regressziós modellek** szükséges egy gyakori kódolási, ahol, például három kategóriába szolgáltatás bővíthet egyes tartalmazó 0 vagy 1 kategóriájától függően egy megfigyelési szolgáltatás három oszlopot. MLlib biztosít [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) egy gyakori kódolási függvényét. A kódoló címke indexek oszlop bináris vektorok értékkel legfeljebb egyetlen egy-egy számoszlop rendeli hozzá. Ehhez a kódoláshoz lehetővé teszi, hogy a numerikus értékelt szolgáltatások, például a logisztikai regressziós kategorikus funkciók alkalmazandó elvárt algoritmusokat.

A következő index és a kategorikus funkciók kódolása a kódot:

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

Cella fent ideje: 1.28 másodperc

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>A gépi Tanulási funkciók be címkézett pont objektumok létrehozása
Ez a szakasz bemutatja, hogyan címkézett pont adattípusú értékként kategorikus szöveges adatok indexelése és kódolja azt, hogy a taníthat vagy tesztelhet MLlib logisztikai regressziós és más képbesorolási modellek használható kódot tartalmazza. Címkézett pont objektumok olyan rugalmas elosztott adatkészleteket (RDD) formátumú úgy, hogy a gépi Tanulási algoritmusokat a MLlib többsége által bemeneti adatként van szükség. A [pont feliratú](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) sűrű vagy ritka, a helyi vektor társítva van egy címke/válasz.  

Ez a szakasz tartalmazza a kódot, amely bemutatja, hogyan kategorikus szöveges adatok indexelése egy [pont feliratú](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) adattípusúra, és kódolása, így betanítására és MLlib logisztikai regressziós és más képbesorolási modellek teszteléséhez használható. Címkézett pont objektumok rugalmas elosztott adatkészleteket (RDD) címkét (cél/válasz változó) és a szolgáltatás vektor. Ezt a formátumot a MLlib számos gépi Tanulási algoritmusokat bemenetként van szükség.

Az index és a bináris osztályozási funkciói szöveg kódolása a kód itt látható.

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


Íme a kódot, amellyel kódolása és lineáris regressziós elemzést funkciói kategorikus szöveges indexelés.

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


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Hozzon létre egy véletlenszerű alárendelt mintavétel az adatok, és azt fel képzési és egy tesztelési
Ez a kód létrehoz egy véletlenszerű mintavételi (25 %-os itt használt) adatok. Bár ez nem szükséges ehhez a példához az adatkészlet méretének miatt, bemutatjuk, hogyan segítségével mintavételi itt, hogy tudja, hogyan használható a saját probléma, ha szükséges. Ha minták nagy, ez is tanítási modell jelentős időt takaríthat meg. Ezután azt ossza fel a minta-képzés (Itt 75 %) és egy tesztelési részét (Itt 25 %) besorolási és regressziós modellezéshez.

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

Cella fent ideje: 0,24 másodperc

### <a name="feature-scaling"></a>A szolgáltatás méretezése
A szolgáltatás méretezése adatok normalizálási, más néven adatblokkok, hogy funkciók széles körben folyósított értékekkel van nem adott túlzott mérjük a cél függvényben. Skálázás használja a funkciónak a [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) egység eltérése a szolgáltatások méretezése. Lineáris regresszió a Sztochasztikus átmenetes Grádiens (SGD), számos más gépi tanulási modelleket például rendeződik regressziót vagy támogatási vektor gépek (SVM) betanításához egy népszerű algoritmus használatra MLlib nyújtja.

> [!NOTE]
> A LinearRegressionWithSGD algoritmust kell lenniük a szolgáltatás méretezése található.
> 
> 

Íme a kódot a skála változók a regularized lineáris SGD algoritmus való használatra.

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
A képzés és a gépi Tanulási algoritmusok tesztelési idő objektumokat az osztályozás, regressziós, és szolgáltatások méretezése a bemeneti adatok keret gyorsítótárazásával csökkenthető.

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

Cella fent ideje: 0,15 másodperc

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>E tipp: a bináris osztályozási modell fizetős előrejelzése
Ez a szakasz bemutatja, hogyan használható három-modellekhez előrejelzésére bináris osztályozási feladatának tipp fizetős taxi útnak-e. A bemutatott modellek a következők:

* Logisztikai regressziós rendeződik 
* Véletlenszerű erdőmodell
* Színátmenet kiemelési fák

Minden modell létrehozásához a kód szakaszban van felosztva, amelyek lépéseket: 

1. **Modell a betanítási** egy paraméterkészlettel adatok
2. **Minta értékelés** egy tesztelési adathalmazon metrikákkal
3. **Modell mentése** későbbi felhasználásra blobba

### <a name="classification-using-logistic-regression"></a>Logisztikai regressziós besorolás
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékelése és a egy logisztikai regressziós modellt a Mentés [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , amely a fedélzeti e tipp NYC taxi utazást és diszkont adatkészlet útnak fizetnek.

**CV és a hiperparaméter kezdik logisztikai regressziós modell betanítása**

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

Coefficients: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

INTERCEPT:-0.0111216486893

Cella fent ideje: 14.43 másodperc

**A standard metrikák bináris osztályozási modell értékelése**

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

Lekéréses kérelem alatti terület = 0.985297691373

Terület alatt ROC = 0.983714670256

Összefoglaló statisztikák

Pontosság = 0.984304060189

Idézze = 0.984304060189

F1 Score = 0.984304060189

Cella fent ideje: 57.61 másodperc

**A ROC-görbe ábrázolásához.**

A *predictionAndLabelsDF* táblázatként, regisztrálva van *tmp_results*, az előző cella. *tmp_results* használható do lekérdezések és a kimeneti eredmények a data-keretben sqlResults küldik az ábrázolást. A kód itt látható.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Itt látható az előrejelzéseket és ROC-görbe megjeleníteni a kódot.

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

![Logistic regression ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Véletlenszerű erdő besorolás
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékelése és a egy véletlenszerű erdőmodell, amely képes-e tipp NYC taxi utazást és diszkont adatkészlet útnak fizetős mentés.

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

Terület alatt ROC = 0.985297691373

Cella fent ideje: 31.09 másodperc

### <a name="gradient-boosting-trees-classification"></a>Színátmenet kiemelési fák besorolás
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékeléséhez, és mentse egy színátmenetes kiemelési fák modellt, az e tipp: a NYC taxi út útnak fizetős és az adatkészlet díjszabás.

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

Terület alatt ROC = 0.985297691373

Cella fent ideje: 19.76 másodperc

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Előrejelzésére regressziós modellek taxi utakat összeget tipp
Ez a szakasz bemutatja, hogyan használja az ötlet mennyisége előrejelzésére regressziós feladatának három modellt fizetős funkciókat a tip alapján taxi útnak. A bemutatott modellek a következők:

* Lineáris regresszió rendeződik
* Véletlenszerű erdő
* Színátmenet kiemelési fák

Ezek a modellek a bevezetésben is ismerteti. Minden modell létrehozásához a kód szakaszban van felosztva, amelyek lépéseket: 

1. **Modell a betanítási** egy paraméterkészlettel adatok
2. **Minta értékelés** egy tesztelési adathalmazon metrikákkal
3. **Modell mentése** későbbi felhasználásra blobba

### <a name="linear-regression-with-sgd"></a>A SGD lineáris regresszió
A kód ebben a szakaszban mutatja be, hogyan használja a méretezhető szolgáltatások egy lineáris regressziós optimalizálás sztochasztikus gradiens módszeres (SGD) használó, és hogyan pontszám, kiértékelheti és mentse a módosítást az Azure Blob Storage (WASB).

> [!TIP]
> Tapasztalataink LinearRegressionWithSGD modellek az átszervezés problémái lehetnek, és paramétereket kell lennie a módosított/optimalizált gondosan beszerzése érvényes modellt. Az átszervezés változók skálázás jelentősen nyújtanak segítséget. 
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

Coefficients: [0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995, -0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

INTERCEPT: 0.853872718283

GYÖKÁTLAGOS = 1.24190115863

R-sqr = 0.608017146081

Cella fent ideje: 58.42 másodperc

### <a name="random-forest-regression"></a>Véletlenszerű erdő regresszió
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékelheti és mentse egy véletlenszerű erdő regressziós, amely előrejelzi a NYC taxi útadatok tipp összege.

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

### <a name="gradient-boosting-trees-regression"></a>Színátmenet kiemelési fák regresszió
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékelése és a egy színátmenetes kiemelési fák modellt, a NYC taxi útadatok tipp összege az mentés.

**Betanítása és kiértékelése**

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

    # CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**A KIMENETRE:**

RMSE = 0.908473148639

R-sqr = 0.753835096681

Cella fent ideje: 34.52 másodperc

**Diagram**

*tmp_results* regisztrálva van az előző cella Hive-táblaként. Kerülnek a kimenetbe az eredményeket a táblából a *sqlResults* adatkeretbe ábrázolásához. A kód itt látható

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Íme a kódot, a Jupyter-kiszolgáló használata az adatok ábrázolásához.

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

![Actual-vs-predicted-tip-amounts](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>A memóriából objektumok törlése
Használat `unpersist()` törli a memóriában gyorsítótárazott objektumokat.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>A modellek a fogyasztás és pontozás rekord tárolási helyek
Fogadni, és a egy független adatkészlet ismertetett pontszám a [pontszám és értékelje a Spark használatával összeállított gépi tanulási modellek](spark-model-consumption.md) másolja és illessze be a következő fájl neve, a mentett, a felhasználás az itt létrehozott modellek tartalmazó kell a témakörben Jupyter notebookot. Íme a kódot, és nyomtassa ki van szüksége a modell fájlok elérési útja.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>A következő lépések
Most, hogy a Spark MlLib a létrehozott regressziós és besorolási modellek, készen áll a pontszám, és ezek a modellek kiértékelése. A speciális adatáttekintés és modellezés jegyzetfüzet, beleértve a kereszt-ellenőrzés, abszolút, a hyper-paraméter mélyebb ismertetőinken és modell kiértékelése. 

**Használati modell:** pontszám és kiértékelheti a besorolási és regressziós modellek, ebben a témakörben létrehozott kapcsolatban lásd: [pontszám és kiértékelheti a Spark használatával összeállított gépi tanulási modellek](spark-model-consumption.md).

**Kereszt-ellenőrzési és a hiperparaméter kezdik**: lásd: [speciális adatáttekintés és modellezés a Spark segítségével](spark-advanced-data-exploration-modeling.md) meg, hogyan lehet a modellek tanítása az kezdik kereszt-ellenőrzési és a hyper-paraméter

