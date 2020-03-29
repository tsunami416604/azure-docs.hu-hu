---
title: Adatok feltárása és modellezése a Sparkkal – Csapatadatelemzési folyamat
description: Bemutatja a Spark MLlib eszközkészlet adatfeltárási és modellezési képességeit az Azure-ban.
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
ms.openlocfilehash: 208f176ca942fb382ff2ed81d872602f7229b0a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718633"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Adatáttekintés és modellezés a Spark segítségével

Ez a forgatókönyv a HDInsight Spark ot használja az adatok feltárásához és a bináris osztályozási és regressziós modellezési feladatokhoz a NYC taxiút és a viteldíj 2013-as adatkészlet mintáján.  Végigvezeti az [adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)lépésein, teljes körűen, egy HDInsight Spark-fürt feldolgozásra és az Azure-blobok az adatok és a modellek tárolására. A folyamat feltárja és vizualizálja az Azure Storage Blobból bevitt adatokat, majd előkészíti az adatokat a prediktív modellek létrehozásához. Ezek a modellek a Spark MLlib eszközkészlet használatával épülnek fel a bináris osztályozási és regressziós modellezési feladatok elvégzéséhez.

* A **bináris osztályozási** feladat annak előrejelzése, hogy egy tipp et fizetnek-e az utazásért. 
* A **regressziós** feladat a tipp mennyiségének előrejelzése más tippfunkciók alapján. 

Az általunk használt modellek közé tartozik a logisztikai és lineáris regresszió, véletlenszerű erdők és gradiens kiemelt fák:

* [Lineáris regresszió sgd](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) egy lineáris regressziós modell, amely egy sztocastikus gradiens (SGD) módszer és optimalizálása és a funkció méretezésmegjósolni a tip összegeket fizetett. 
* [Logisztikai regresszió LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) vagy "logit" regresszió, egy regressziós modell, amely akkor használható, ha a függő változó kategorikus adatbesorolás. Az LBFGS egy kvázi Newton-optimalizálási algoritmus, amely a Broyden–Fletcher–Goldfarb–Shanno (BFGS) algoritmust közelíti korlátozott mennyiségű számítógépes memóriával, és amelyet széles körben használnak a gépi tanulásban.
* [A véletlenszerű erdők](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) a döntési fák együttesei.  Ezek össze sok döntés fák kockázatának csökkentése érdekében a túlszerelés. A véletlenszerű erdők regressziós és besorolási célokra szolgálnak, és képesek kezelni a kategorikus jellemzőket, és kiterjeszthetők a többosztályos besorolási beállításra. Nem igényelnek szolgáltatásméretezést, és képesek a nem linearitások és a szolgáltatásinterakciók rögzítésére. Véletlenszerű erdők az egyik legsikeresebb gépi tanulási modellek osztályozási és regressziós.
* [Gradiens kiemelt fák](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) együttesei döntésfák. A GBTS vonatdöntési fákit iteratív módon a veszteségfunkció minimalizálása érdekében. A GBTS regressziós és besorolási célokra használatos, és képes kezelni a kategorikus funkciókat, nem igényel szolgáltatásskálázást, és képes a nem linearitásés a szolgáltatáskölcsönhatások rögzítésére. Többosztályos besorolási beállításban is használhatók.

A modellezési lépések is tartalmaznak kódot, amely bemutatja, hogyan kell betanítása, kiértékelése és mentése az egyes típusú modellek. A Python a megoldás kódolására és a megfelelő telkek megjelenítésére szolgál.   

> [!NOTE]
> Bár a Spark MLlib eszközkészlet nagy adatkészleteken való használatra készült, egy viszonylag kis minta (~30 Mb 170K sorok használatával, az eredeti NYC-adatkészlet körülbelül 0,1%-a) itt használatos a kényelem érdekében. Az itt megadott gyakorlat hatékonyan (körülbelül 10 perc alatt) fut egy 2 feldolgozócsomókkal rendelkező HDInsight-fürtön. Ugyanez a kód kisebb módosításokkal nagyobb adatkészletek feldolgozására használható, megfelelő módosításokkal az adatok memóriában való gyorsítótárazásához és a fürt méretének módosításához.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
A forgatókönyv végrehajtásához egy Azure-fiókra és egy Spark 1.6-os (vagy Spark 2.0-s) HDInsight-fürtre van szükség. Tekintse meg az [adatok tudományának áttekintése a Spark az Azure HDInsight](spark-overview.md) utasításokat, hogyan felel meg ezeknek a követelményeknek. Ez a témakör is tartalmaz egy leírást a NYC 2013 Taxi itt használt adatokat, és utasításokat, hogyan hajtson végre kódot egy Jupyter-jegyzetfüzetből a Spark-fürtön. 

## <a name="spark-clusters-and-notebooks"></a>Spark-fürtök és jegyzetfüzetek
A beállításlépései és a kód ebben a forgatókönyvben találhatók a HDInsight Spark 1.6 használatával. A Jupyter-jegyzetfüzetek azonban hdinsight Spark 1.6-hoz és Spark 2.0-fürtökhöz is rendelkezésre állnak. A jegyzetfüzetek és a hozzájuk mutató hivatkozások leírása az azokat tartalmazó GitHub-tárház [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) található. Ezenkívül a kód itt és a csatolt jegyzetfüzetekben általános, és működnie kell minden Spark-fürtön. Ha nem használja a HDInsight Sparkot, a fürt beállítási és felügyeleti lépései némileg eltérhetnek az itt láthatótól. Az egyszerűség kedvéért, itt vannak a linkeket a Jupyter notebookok Spark 1.6 (futtatni kell a pySpark kernel a Jupyter Notebook szerver) és a Spark 2.0 (futtatni kell a pySpark3 kernel a Jupyter Notebook szerver):

### <a name="spark-16-notebooks"></a>Spark 1.6 notebookok

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Az adatok feltárásával, modellezésével és pontozásával kapcsolatos információkat több különböző algoritmussal ismerteti.

### <a name="spark-20-notebooks"></a>Spark 2.0 notebookok
A Spark 2.0-fürt használatával megvalósított regressziós és besorolási feladatok külön jegyzetfüzetekben vannak, és a besorolási jegyzetfüzet egy másik adatkészletet használ:

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl tájékoztatást nyújt arról, hogyan lehet elvégezni az adatok feltárását, modellezését és pontozását a Spark 2.0 fürtökben a NYC Taxi utazás és a viteldíj-adatkészlet [használatával, amelyet itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)ismertetett . Ez a notebook jó kiindulási pont lehet a Spark 2.0-hoz megadott kód gyors feltárásához. A nyc taxi adatok részletesebb jegyzetfüzetelemzéséhez tekintse meg a következő jegyzetfüzetet ebben a listában. Tekintse meg az alábbi megjegyzéseket, amelyek összehasonlítják ezeket a jegyzetfüzeteket. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Ez a fájl bemutatja, hogyan kell elvégezni az adatok huzavona (Spark SQL és dataframe műveletek), feltárása, modellezése és pontozási segítségével NYC Taxi utazás és viteldíj adatkészlet [itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)leírt .
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Ez a fájl bemutatja, hogyan végezhetadatokat wrangling (Spark SQL és dataframe műveletek), feltárása, modellezése és pontozási a jól ismert légitársaság időben indulási adatkészlet 2011 és 2012.Spark.the pySpark3_Airline_Departure_Delay_Classification.ipynb: This file shows how to perform data wrangling (Spark SQL and dataframe operations), exploration, modeling and scoring using the well-known Airline On-time departure dataset from 2011 and 2012. A modellezés előtt integráltuk a légitársaság imázsának adatait a repülőtéri időjárási adatokkal (például szélsebesség, hőmérséklet, magasság stb.), hogy ezek az időjárási jellemzők bekerüljenek a modellbe.

<!-- -->

> [!NOTE]
> A légitársaság adatkészlete hozzá lett adva a Spark 2.0-s jegyzetfüzetekhez, hogy jobban illusztrálja a besorolási algoritmusok használatát. Az alábbi hivatkozásokat a légitársaság időben indulási és időjárási adatkészletéről talál:
> 
> - A légitársaság időben idoben megadott adatai:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Repülőtéri időjárási adatok:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 

<!-- -->

<!-- -->

> [!NOTE]
> A Spark 2.0 notebookok a NYC taxi és a légitársaság járat késési adatkészletek is eltarthat 10 mins vagy több futtatásához (méretétől függően a HDI-fürt). A fenti lista első jegyzetfüzete az adatfeltárás, a vizualizáció és a gépi tanulási modell betanításának számos aspektusát mutatja be egy olyan jegyzetfüzetben, amely kevesebb időt vesz igénybe a lemintavételezett NYC-adatkészlettel való futtatáshoz, amelyben a taxi- és viteldíjfájlok előre csatlakoztatva vannak: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Ez a notebook sokkal rövidebb időt vesz igénybe (2-3 mins), és jó kiindulási pont lehet a Spark 2.0-hoz megadott kód gyors felfedezéséhez. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
> Az alábbi leírások a Spark 1.6 használatával kapcsolatosak. A Spark 2.0-s verzióihoz használja a fent leírt és hivatkozott jegyzetfüzeteket. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Beállítás: tárolási helyek, tárak és az előre beállított Spark-környezet
Spark képes olvasni és írni az Azure Storage Blob (más néven WASB). Így az ott tárolt adatok bármelyike feldolgozható a Spark használatával, és az eredmények et a WASB-ban újra tárolhatja.

A MODELLEK vagy fájlok WASB-be való mentéséhez az elérési utat megfelelően meg kell adni. A Spark-fürthöz csatlakoztatott alapértelmezett tárolóra a következő elérési út használatával lehet hivatkozni, amely a következővel kezdődik: "wasb:///". Más helyekre a "wasb://" hivatkozik.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Könyvtárelérési utak beállítása a WASB tárolóhelyeinek beállításához
A következő kódminta megadja az olvasandó adatok helyét és annak a modelltároló könyvtárnak az elérési útját, amelybe a modellkimenetet menti:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Könyvtárak importálása
A beállításhoz szükség van a szükséges könyvtárak importálására is. Állítsa be a szikrakörnyezetet, és importálja a szükséges könyvtárakat a következő kóddal:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Előre beállított Spark-környezet és PySpark-varázslatok
A Jupyter-jegyzetfüzetekhez mellékelt PySpark kernelek előre beállított környezettel rendelkeznek. Így nem kell explicit módon beállítani a Spark vagy a Hive környezeteket, mielőtt elkezdené a fejlődő alkalmazást. Ezek a környezetek alapértelmezés szerint elérhetők. Ezek a kontextusok a következők:

* sc - a Spark 
* sqlContext - a Hive

A PySpark kernel néhány előre definiált "varázslatot" biztosít, amelyek speciális parancsok, amelyeket a %% segítségével hívhat meg. A kódmintákban két ilyen parancs található.

* **%%helyi** Itt adhatja meg, hogy a következő sorokban lévő kódot helyileg kell végrehajtani. A kódnak érvényes Python-kódnak kell lennie.
* **%%sql -o \<változó név>** Hive-lekérdezést hajt végre az sqlContext-en. Ha az -o paraméter átlett adva, a lekérdezés eredménye pandas DataFrame-ként megmarad a %%local Python környezetben.

A Jupyter notebook kerneleiről és az előre definiált "varázslatokról" a [HdInsight HDInsight-fürtökkel rendelkező Jupyter-jegyzetfüzetekhez elérhető Kernelek](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)című témakörben talál további információt.

## <a name="data-ingestion-from-public-blob"></a>Adatok betöltése nyilvános blobból
Az adatelemzési folyamat első lépése az elemzendő adatok betöltése olyan forrásokból, ahol az adatok feltárási és modellezési környezetében található. Ebben a forgatókönyvben a környezet a Spark. Ez a szakasz a feladatok sorozatának elvégzéséhez szükséges kódot tartalmazza:

* a modellezendő adatminta betöltése
* olvasása a bemeneti adatkészletben (.tsv fájlként tárolva)
* formázza és tisztítsa meg az adatokat
* objektumok (RDD-k vagy adatkeretek) létrehozása és gyorsítótárazása a memóriában
* regisztrálja ideiglenes táblaként az SQL-környezetben.

Itt van az adatok betöltésének kódja.

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

**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 51,72 másodperc

## <a name="data-exploration--visualization"></a>Adatok feltárása & vizualizáció
Miután az adatok a Sparkba kerültek, az adatelemzési folyamat következő lépése az adatok mélyebb megértése a feltárás és a vizualizáció révén. Ebben a szakaszban sql-lekérdezések segítségével vizsgáljuk meg a taxiadatokat, és ábrázoljuk a célváltozókat és a vizuális ellenőrzés lehetséges jellemzőit. Pontosabban, mi ábrázolják az utasok száma a taxi utak, a gyakorisága tipp összegeket, és hogyan tippek változnak a fizetési összeg és a típus.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Az utasszám-frekvenciák hisztogramjának rajzolása a taxiutak mintájában
Ez a kód és az azt követő kódrészletek az SQL magic használatával lekérdezik a mintát és a helyi mágiát az adatok ábrázolására.

* **SQL magic`%%sql`( )** A HDInsight PySpark kernel támogatja az sqlContext egyszerű inline HiveQL lekérdezéseket. Az (-o VARIABLE_NAME) argumentum a Jupyter-kiszolgálón pandadataframe-ként megőrzi az SQL-lekérdezés kimenetét. Ez a beállítás helyi módban teszi elérhetővé a kimenetet.
* A ** `%%local` mágia** a kód helyi futtatásához használható a Jupyter-kiszolgálón, amely a HDInsight-fürt csomópontja. Jellemzően, akkor `%%local` használja a `%%sql` mágia együtt a mágia -o paraméter. Az -o paraméter helyileg megmaradna az SQL-lekérdezés kimenetén, majd a %%local magic elindítja a következő kódrészlet-készletet, amely helyileg fut az SQL-lekérdezések kimenetén.

A kimenet automatikusan megjelenik a kód futtatása után.

Ez a lekérdezés az utazások utasszám szerint. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Ez a kód létrehoz egy helyi adatkeretet a lekérdezés kimenetéből, és megtervezi az adatokat. A `%%local` varázslat létrehoz egy helyi `sqlResults`adatkeretet, amely a matplotlib-mal való nyomtatáshoz használható. 

> [!NOTE]
> Ezt a PySpark-varázslatot többször is használják ebben a forgatókönyvben. Ha az adatmennyiség nagy, a helyi memóriába illeszkedő adatkeret létrehozásához mintát kell venni.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Itt a kód, hogy ábrázolja az utazások az utasok száma

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

**Kimeneti:**

![Menetgyakoriság utasszám szerint](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

A jegyzetfüzet **Típus** menügombjaival többféle képi megjelenítés közül választhat (Táblázat, Kör, Vonal, Terület vagy Sáv). A bár telek itt látható.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Ábrázolja a tippösszegek hisztogramját, valamint azt, hogy a tipp összege hogyan változik az utasok számától és a viteldíjaitól.
Sql-lekérdezés használatával minta adatokat.

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


Ez a kódcella az SQL-lekérdezést használja az adatok három nyomtatásának létrehozásához.

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


**Kimeneti:** 

![Tipp összegének eloszlása](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tipp összege utasszám szerint](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipp összege viteldíj összeg szerint](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Jellemzőtervezés, átalakítás és adatelőkészítés modellezéshez
Ez a szakasz ismerteti és tartalmazza a pénzmosás modellezéséhez használt adatok előkészítéséhez használt eljárások kódját. Bemutatja, hogyan kell elvégezni a következő feladatokat:

* Új funkció létrehozása az órák forgalmi időgyűjtőkbe való berakásával
* Kategorikus funkciók indexelése és kódolása
* Címkézett pontobjektumok létrehozása a ml-függvényekbe való bevitelhez
* Az adatok véletlenszerű részmintavételezésének létrehozása és betanítási és tesztelési készletekre való felosztása
* Szolgáltatásskálázás
* Objektumok gyorsítótárazása a memóriában

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Új funkció létrehozása az órák forgalmi időgyűjtőkbe való berakásával
Ez a kód bemutatja, hogyan hozhat létre egy új funkciót az órák forgalmi időgyűjtőkbe való beolvasásával, majd az eredményül kapott adatkeret gyorsítótárazásával a memóriában. Ha rugalmas elosztott adatkészleteket (RDD- k) és adatkereteket használ, a gyorsítótárazás jobb végrehajtási időket eredményez. Ennek megfelelően a forgatókönyv több szakaszában gyorsítótárazjuk az RDD-ket és az adatkereteket. 

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

**Kimeneti:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Kategorikus funkciók indexelése és kódolása a modellezési függvényekbe való bevitelhez
Ez a szakasz bemutatja, hogyan indexelje vagy kódolja a modellezési függvények bevitt kategorikus jellemzőit. Az MLlib modellezési és előrejelzési funkcióinak használatához a kategorikus bemeneti adatokkal rendelkező funkciókat a használat előtt indexelni vagy kódolni kell. A modelltől függően különböző módokon kell indexelni vagy kódolni őket:  

* **A faalapú modellezéshez** a kategóriákat numerikus értékként kell kódolni (például egy három kategóriás jellemző 0, 1, 2-vel kódolható). Ezt az algoritmust az MLlib [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) függvénye biztosítja. Ez a függvény egy címkeoszlopot kódol a címkegyakoriság szerint rendezett címkeindexek oszlopába. Bár numerikus értékekkel indexelhető a bemeneti és adatkezelési, a fa alapú algoritmusok megadhatók, hogy megfelelően kezeljék őket kategóriákként. 
* **A logisztikai és lineáris regressziós modellek** egy gyors kódolást igényelnek, ahol például egy három kategóriás funkció három jellemzőoszlopra bontható, amelyek mindegyike 0-t vagy 1-et tartalmaz a megfigyelés kategóriájától függően. Az MLlib [onehotencoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkciót biztosít az egy forró kódoláshoz. Ez a kódoló egy címkeindex-oszlopot rendel egy bináris vektorokból álló oszlophoz, legbénákkal. Ez a kódolás lehetővé teszi, hogy a numerikus értékű funkciókat, például a logisztikai regressziót elvárható algoritmusokat alkalmazza a kategorikus funkciókra.

Itt van a kód index és kódolni kategorikus funkciók:

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

**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 1,28 másodperc

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Címkézett pontobjektumok létrehozása a ml-függvényekbe való bevitelhez
Ez a szakasz olyan kódot tartalmaz, amely bemutatja, hogyan indexelheti a kategorikus szöveges adatokat címkézett pontadattípusként, és hogyan kódolhatja őket úgy, hogy az MLlib logisztikai regresszió és más besorolási modellek betanításához és teszteléséhez használható legyen. A címkézett pontobjektumok rugalmas elosztott adatkészletek (RDD), amelyeket az MLlib legtöbb hibairányító algoritmusa bemeneti adatként szükséges módon formáz. A [címkézett pont](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) egy helyi vektor, akár sűrű, akár ritka, amely egy címkéhez/válaszhoz van társítva.  

Ez a szakasz olyan kódot tartalmaz, amely bemutatja, hogyan indexelheti a kategorikus szöveges adatokat [címkézett](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) pontadattípusként, és hogyan kódolhatja őket úgy, hogy az MLlib logisztikai regresszió és más besorolási modellek betanításához és teszteléséhez használható legyen. A címkézett pontobjektumok rugalmas elosztott adatkészletek (RDD), amelyek egy címkésből (cél/válasz változó) és szolgáltatásvektorból állnak. Erre a formátumra számos ML-algoritmus bevitele szükséges az MLlib-ben.

Itt van a kód index és kódolni szöveges funkciók bináris osztályozás.

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Itt van a kód kódolni és index kategorikus szöveg jellemzői lineáris regressziós elemzés.

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


### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Az adatok véletlenszerű részmintavételezésének létrehozása és betanítási és tesztelési készletekre való felosztása
Ez a kód véletlenszerű mintavételt hoz létre az adatokból (itt 25% használatos). Bár ez nem szükséges ebben a példában az adatkészlet mérete miatt, bemutatjuk, hogyan minta itt, így tudja, hogyan kell használni a saját problémáját, ha szükséges. Ha a minták nagyok, a mintavételezés jelentős időt takaríthat meg a betanítási modellek betanítása közben. Ezután a mintát egy betanítási részre (75% itt) és egy tesztrészre (25% itt) osztjuk, hogy a besorolási és regressziós modellezésben használjuk.

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

**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 0,24 másodperc

### <a name="feature-scaling"></a>Szolgáltatásskálázás
A funkcióskálázás, más néven az adatok normalizálása biztosítja, hogy a széles körben kifizetett értékekkel rendelkező funkciók ne kapjanak túlzott súlyt az objektív funkcióban. A szolgáltatásméretezés kódja a [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) segítségével méretezi a funkciókat az egységvarianciájához. Az MLlib a sztochastikus gradiens (SGD) lineáris regresszióhoz való használatát biztosítja, amely egy népszerű algoritmus más gépi tanulási modellek széles körének betanítására, mint például a szabályosregressziók vagy a támogató vektorgépek (SVM).

> [!NOTE]
> Úgy találtuk, hogy a LinearRegressionWithSGD algoritmus érzékeny a funkció méretezésére.
> 
> 

Itt van a kód a változók méretezéséhez a szabályoslineáris SGD algoritmussal való használatra.

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

**Kimeneti:**

A cella feletti végrehajtáshoz szükséges idő: 13,17 másodperc

### <a name="cache-objects-in-memory"></a>Objektumok gyorsítótárazása a memóriában
A gépi tanulási algoritmusok betanításához és teszteléséhez szükséges idő csökkenthető a besoroláshoz, regresszióhoz és méretezett funkciókhoz használt bemeneti adatkeret-objektumok gyorsítótárazásával.

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

**Kimeneti:** 

A cella feletti végrehajtáshoz szükséges idő: 0,15 másodperc

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Tippeljen, hogy a tipp kifizetésre kerül-e bináris osztályozási modellekkel
Ez a szakasz azt mutatja be, hogyan használja a három modellt a bináris osztályozási feladat előrejelzésére, hogy egy tipp fizet-e egy taxi út. A bemutatott modellek a következők:

* Szabályos logisztikai regresszió 
* Véletlen erdőmodell
* Színátmenetes kiemelési fák

Minden modellépület-kód szakasz lépésekre van osztva: 

1. **Modellbetanítási** adatok egy paraméterkészlettel
2. **Modellkiértékelése** mérőszámokkal rendelkező tesztadatkészleten
3. **Modell mentése** a blobban későbbi felhasználáshoz

### <a name="classification-using-logistic-regression"></a>Osztályozás logisztikai regresszióval
Ebben a szakaszban a kód bemutatja, hogyan lehet betanítása, kiértékelése és mentése logisztikai regressziós modell [LBFGS,](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) amely előre jelzi, hogy egy tipp fizetik-e az utazás a NYC taxi út és a viteldíj adatkészlet.

**A logisztikai regressziós modell betanítása CV és hyperparameter sweeping használatával**

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


**Kimeneti:** 

Együtthatók: [0,0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.0024764646947233, -0.00165897881503, 0.0675394837328, -0,111823113101, -0,32460912762, -0,204549780032, -1,36499216354, 0,591088507921, - 0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Elfogás: -0.0111216486893

A cella feletti végrehajtáshoz szükséges idő: 14,43 másodperc

**A bináris osztályozási modell kiértékelése szabványos mérőszámokkal**

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

**Kimeneti:** 

A PR alá tartozott terület = 0,985297691373

Roc alatti terület = 0,983714670256

Összefoglaló statisztika

Pontosság = 0,984304060189

Visszahívás = 0,984304060189

F1 pontszám = 0,984304060189

A cella feletti végrehajtáshoz szükséges idő: 57,61 másodperc

**Ábrázolja a ROC-görbét.**

Az *predictionAndLabelsDF* tábláként van regisztrálva, *tmp_results*az előző cellában. *tmp_results* lekérdezések és kimeneti eredmények az sqlResults adatkeretnyomtatáshoz. Itt a kód.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Itt van a kódot, hogy előrejelzéseket, és ábrázolja a ROC-görbe.

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


**Kimeneti:**

![Logisztikai regressziós ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Véletlen erdőosztályozás
Ebben a szakaszban a kód bemutatja, hogyan lehet beadni, kiértékelni és menteni egy véletlenszerű erdőmodellt, amely előre jelzi, hogy a new york-i taxiút és a viteldíj-adatkészlet ben egy-egy tipp et fizetnek-e.

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

**Kimeneti:**

Roc alatti terület = 0,985297691373

A cella feletti végrehajtáshoz szükséges idő: 31.09 másodperc

### <a name="gradient-boosting-trees-classification"></a>Gradiens kiemelési fák osztályozása
A kód ebben a szakaszban bemutatja, hogyan kell betanítása, kiértékelése, és mentse a gradiens kiemelési fák modell, amely előre jelzi, hogy egy tipp fizetik-e egy utazás a NYC taxi út és a viteldíj adatkészlet.

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


**Kimeneti:**

Roc alatti terület = 0,985297691373

A cella feletti végrehajtáshoz szükséges idő: 19,76 másodperc

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Tippösszegek előrejelzése a regressziós modellekkel rendelkező taxiutakhoz
Ez a szakasz bemutatja, hogyan használja a három modell a regressziós feladat előre jelzi a borravaló fizetett egy taxi utazás más tip funkciók alapján fizetett. A bemutatott modellek a következők:

* Szabályos lineáris regresszió
* Véletlen erdő
* Színátmenetes kiemelési fák

Ezeket a modelleket a bevezetőben ismertették. Minden modellépület-kód szakasz lépésekre van osztva: 

1. **Modellbetanítási** adatok egy paraméterkészlettel
2. **Modellkiértékelése** mérőszámokkal rendelkező tesztadatkészleten
3. **Modell mentése** a blobban későbbi felhasználáshoz

### <a name="linear-regression-with-sgd"></a>Lineáris regresszió sgd-vel
Ebben a szakaszban a kód bemutatja, hogyan használhatja a skálázott funkciókat egy lineáris regresszió betanításához, amely sztochastikus gradiensesést (SGD) használ az optimalizáláshoz, és hogyan pontzthatja, értékelheti és mentheti a modellt az Azure Blob Storage (WASB) rendszerben.

> [!TIP]
> Tapasztalataink szerint problémák merülhetnek fel a LinearRegressionWithSGD modellek konvergenciájával kapcsolatban, és a paramétereket gondosan kell módosítani/ optimalizálni egy érvényes modell megszerzéséhez. A változók skálázása jelentősen segíti a konvergenciát. 
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

**Kimeneti:**

Együtthatók: [0,00457675809917, -0,0226314167349, -0,01919191910355236, 0,246793409578, 0,31204789999, 0,359634405999, 0,359634405999, 0,319634405999, 0,359634405999, 0,31204705999, 0,359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.11551951711, 0.149250164995, 0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Elfogás: 0.853872718283

RMSE = 1,24190115863

R-sqr = 0,608017146081

A cella feletti végrehajtáshoz szükséges idő: 58,42 másodperc

### <a name="random-forest-regression"></a>Véletlen erdő regresszió
Ebben a szakaszban a kód bemutatja, hogyan kell beadni, kiértékelni és menteni egy véletlenszerű erdő regresszió, amely előre jelzi a tipp összege a NYC taxi út adatok.

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

**Kimeneti:**

RMSE = 0,891209218139

R-sqr = 0,759661334921

A cella feletti végrehajtáshoz szükséges idő: 49,21 másodperc

### <a name="gradient-boosting-trees-regression"></a>Gradiens kiemelésfák regresszió
Ebben a szakaszban a kód bemutatja, hogyan kell beadni, kiértékelni és menteni egy gradiens kiemelési fák modell, amely előre jelzi a tipp összege a NYC taxi út adatok.

**Vonat és értékelés**

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

**Kimeneti:**

RMSE = 0,908473148639

R-sqr = 0,753835096681

A cella feletti végrehajtáshoz szükséges idő: 34,52 másodperc

**Telek**

*tmp_results* hive táblaként van regisztrálva az előző cellában. A tábla eredményei kimenetele az *sqlResults* adatkeretnyomtatáshoz. Itt a kód

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Itt van a kód-hoz ábrázol az adatokat a Jupyter szerver.

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


**Kimeneti:**

![Tényleges és előre jelzett-tip-összegek](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Objektumok karbantartása a memóriából
A `unpersist()` memóriában gyorsítótárazott objektumok törlésére szolgál.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>A modellek tárolási helyeinek rögzítése fogyasztásra és pontozásra
A Pontszám ban leírt független adatkészlet felhasználásához [és pontozásához, és kiértékelheti](spark-model-consumption.md) a Spark által készített gépi tanulási modellek témakörét, másolja és illessze be ezeket a fájlneveket, amelyek tartalmazzák az itt létrehozott mentett modelleket a Fogyasztás jupyter-jegyzetfüzetbe. Itt van a kód, hogy nyomtassa ki az elérési utakat a modell fájlokat van szüksége van.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**Kimeneti**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>A következő lépések
Most, hogy regressziós és besorolási modelleket hozott létre a Spark MlLib-el, készen áll arra, hogy megtanulja, hogyan pontzthatja és értékelheti ezeket a modelleket. A speciális adatfeltárás i és modellezési notebook merül mélyebben, beleértve a kereszt-ellenőrzés, hyper-paraméter sweeping, és a modell értékelése. 

**Modell felhasználása:** A témakörben létrehozott besorolási és regressziós modellek pontozásáról és kiértékeléséről a [Pontszám és a Spark által készített gépi tanulási modellek kiértékelése](spark-model-consumption.md)című témakörben olvashat.

**Keresztellenőrzés és hiperparaméter-söprés**: [Részletes adatfeltárás és -modellezés](spark-advanced-data-exploration-modeling.md) a Sparkkal kapcsolatban, hogyan képezhetők be a modellek keresztellenőrzéssel és hiperparaméter-söpréssel

