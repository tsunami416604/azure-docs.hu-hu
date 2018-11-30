---
title: Speciális adatáttekintés és modellezés a Spark segítségével |} A Microsoft Docs
description: HDInsight Spark használatával hajtsa végre az adatok feltárása és kereszt-ellenőrzési és a hiperparaméter optimalizálás használata bináris besorolási és regressziós modelleket taníthat be.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 02/15/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: e0fa3d481e18cdb15095968e791bd9eee630f8af
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446330"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Speciális adatáttekintés és modellezés a Spark segítségével

Ez az útmutató a HDInsight Spark használ az adatok feltárása és bináris besorolási és regressziós modellek használatával kereszt-ellenőrzési és hiperparaméter optimalizálása a NYC mintán taxiköltség utazást és 2013 adatkészlet díjszabás. Emellett végigvezeti a lépéseken, a [adatelemzési folyamat](https://aka.ms/datascienceprocess), a végpontok közötti, feldolgozási és az Azure-blobok esetében a HDInsight Spark-fürt használata az adatok és a modellek. A folyamatot ismerteti, és egy Azure Storage-blobból adatok elérhetővé, és ezután előkészíti az adatokat, prediktív modelleket hozhat létre. Python-kód a megoldás és a megfelelő grafikon megjelenítése használatban van. Ezek a modellek olyan bináris besorolási és regressziós modellezéshez feladatokat végezheti el a Spark MLlib eszközkészlet használatával hozhat létre. 

* A **bináris osztályozási** tevékenység-e az utazás tipp fizetős előrejelzésére. 
* A **regressziós** feladata előre jelezni a funkciókat a tip alapján tipp mennyisége. 

A modellezés lépéseket is bemutató betanításához, kiértékelheti és mentse a modell különböző típusú kódot tartalmaznak. A témakör néhány, az azonos alapoktól a [adatáttekintés és modellezés a Spark segítségével](spark-data-exploration-modeling.md) témakör. De azt a "speciális" abban, hogy a kereszt-ellenőrzés az optimális pontos besorolási és regressziós modelleket taníthat be abszolút hiperparaméter is használja. 

**Kereszt-ellenőrzés (CV)** olyan módszer, amely felméri arról, hogy egy ismert adatkészletet a betanított modell általánosítja jelezni, amelyre azt még nincs betanítva adatkészletek funkcióját.  Itt egy közös végrehajtási, hogy egy adatkészlet felosztása K modellrész, és majd a egy kivételével az modellrész a Ciklikus időszeleteléses módon a modell betanításához. Azon képessége, pontosan akkor, amikor tesztelve lett a Microsoft nem használja fel a modell betanítását modellrészek független az adatkészlet előrejelzési modell adatokon.

**Hiperparaméter optimalizálási** hiperparaméterek a tanulási algoritmus, készletét általában a cél az optimalizálás, az algoritmus teljesítmény független adathalmazon mérték kiválasztása a probléma merült fel. **Hiperparaméterek** olyan értékek, amelyek a modell betanítási eljárás kívül meg kell adni. Ezek az értékek feltételezéseket hatással lehet a rugalmasság és a modellek pontossága. Döntési fák hiperparaméterek, például a kívánt mélységének és hagyja, a fában száma például rendelkeznek. Támogatási vektor gépek (SVMs) van szükség, egy téves besorolás napján belül pótdíj időszak beállítása. 

Egy közös hajtsa végre az itt használt hiperparaméter optimalizálási módja a rács keresést, vagy egy **paraméteres**. Ez a tanulási algoritmus a hiperparaméter terület egy adott részét keresztül az értékeket egy teljes körű keresés végrehajtása áll. A teljesítmény-mérőszám ki a rács keresési algoritmus által előállított az optimális eredmények rendezéséhez keresztellenőrzési adhat meg. CV használt hiperparaméter elvégzésekor mindig segít korlát kapcsolatos, például egy modell a betanítási adatok overfitting úgy, hogy a modell őrzi meg a kapacitást, az általános az adatkészlethez tartozó, amelyről a betanítási adatok kibontotta a alkalmazni.

A modellek használjuk a logisztikai és lineáris regresszió, véletlenszerű erdők és gyorsított fa átmenetes tartalmazza:

* [A SGD lineáris regressziós](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) egy lineáris regressziós modellt, amely egy Sztochasztikus átmenetes Grádiens (SGD) módszert használja, és optimalizálása és a szolgáltatás méretezési lehetőségek érhetők el a tip mennyiségeket előrejelzése kifizetett. 
* [A LBFGS logisztikai regressziós](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) , vagy "logit" regressziós, egy regressziós modellt, amely a kategorikus adatok besorolása ehhez a függő változó esetén használható. LBFGS egy kvázi Newton algoritmus, amely megközelíti a Broyden – Fletcher – Goldfarb – Shanno (BFGS) algoritmus használatával korlátozott mennyiségű memóriát és széles körben használt a machine Learning szolgáltatásban.
* [Véletlenszerű erdők](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) együttesek döntési fák vannak.  Overfitting kockázatának csökkentése érdekében számos döntési fák kombinálhatja azokat. Véletlenszerű erdők regressziós és besorolási használ, és kezelhetik a kategorikus funkciók és többosztályos osztályozási beállítás is kiterjeszthető. Ezek nem igénylik a szolgáltatás méretezése, és képesek nemlinearitás rögzítése és a szolgáltatás kapcsolati. Véletlenszerű erdők a legsikeresebb gépi tanulási besorolási és regressziós modellek tartoznak.
* [Színátmenet súlyozott fák](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) együttesek döntési fák. GBTs iteratív, hogy minimalizálják az adatvesztést függvény döntési fák betanítása. GBTs regressziós és besorolási használ és kezelhetik a kategorikus funkciókat, nincs szükség a szolgáltatás méretezése, és képesek nemlinearitás rögzítése és a szolgáltatás kapcsolati. Ezek is használható osztályú-besorolás beállításban.

Példák CV és a Hiperparaméter modellezési ismétlés jelennek meg a bináris osztályozási probléma. Egyszerűbb példák (nélkül halmokat paraméter) jelennek meg a fő témakörben regressziós feladatokhoz. De a függelékben lineáris regressziós és CV paramétert a véletlenszerű erdő regressziós használatával ismétlés rugalmas net-érvényesítési is ismertet. A **nettó rugalmas** van egy rendeződik regressziós módszert L1 és L2 metrikákat lineáris regressziós modelleket, hogy a költségráfordításokkal egyenes arányban igyekeznek egyesíti az eljárást, mint a [szabadkézi](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) és [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization)módszereket.   

> [!NOTE]
> A Spark MLlib eszközkészlet tervezték Nagy adatkészleteken, bár egy viszonylag kis minta (KB. 30 Mb 170K sorok, körülbelül 0,1 %-át az eredeti NYC adatkészlet használatával) használatos itt kényelmi célokat szolgál. Az itt megadott gyakorlat futtat hatékonyan (körülbelül 10 percet) a 2 feldolgozó csomópontot egy HDInsight-fürtöt. Ugyanazt a kódot, kisebb módosításokkal segítségével adathalmazokba nagyobb –, a megfelelő módosításokat az adatokat a memóriában, és a fürt méretének módosítása.
> 
> 

## <a name="setup-spark-clusters-and-notebooks"></a>A telepítő: Spark-fürtök és jegyzetfüzetek
Beállítási lépéseket és a kód-okat Ez az útmutató egy HDInsight Spark 1.6-os használatával. De Jupyter notebookok a HDInsight Spark 1.6-os és a Spark 2.0 fürtök biztosított. A jegyzetfüzetek és a rájuk mutató hivatkozást leírása tartalmazza a [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) az azokat tartalmazó GitHub-adattárban. Továbbá, a kód itt és a társított notebookok az általános és működnie kell bármelyik Spark-fürtön. Ha nem használja a HDInsight Spark, a fürt beállítása és lehet, hogy a felügyeleti lépések kissé eltérhetnek az itt látható. Az egyszerűség kedvéért az alábbiakban a, a Jupyter notebookok a Spark 1.6-os és a 2.0-s verzióját kell futtatni a pyspark kernel a Jupyter Notebook Server mutató hivatkozásokat:

### <a name="spark-16-notebooks"></a>A Spark 1.6-os notebookok

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): a notebook #1 és a hiperparaméter finomhangolása és kereszt-ellenőrzés, modell fejlesztési témaköröket tartalmazza.

### <a name="spark-20-notebooks"></a>A Spark 2.0 notebookok

[Spark2.0-pySpark3-Machine-Learning-Data-Science-Spark-Advanced-Data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl információt nyújt az adatfeltárás, modellezés és a Spark 2.0 fürtök pontozási végrehajtásához.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**OUTPUT**

DateTime.DateTime (2016. 4., 18., 17., 36., 27., 832799)

### <a name="import-libraries"></a>Könyvtárak importálása
Importálja a szükséges kódtárak a következő kóddal:

    # LOAD PYSPARK LIBRARIES
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

## <a name="data-ingestion-from-public-blob"></a>Nyilvános blob adatbetöltést:
Az adatelemzési folyamat első lépése, hogy hol vannak a adatáttekintés és modellezés környezetben történő forrásból származó elemezni az adatokat. Ebben a környezetben a Spark ebben az útmutatóban. Ez a szakasz tartalmazza a kódot, és végezze el a tevékenységeket:

* betöltési modellezni az adatokat minta
* olvassa el a bemeneti adatkészlet (.tsv-fájlként tárolja)
* az adatok formázása és
* Hozzon létre és objektumok (rdd-k vagy adatkeretek) a memóriában gyorsítótárazza
* regisztrálja az SQL-környezetben temp-táblaként.

A kód Adatbetöltési itt látható.

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

    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Cella fent ideje: 276.62 másodperc

## <a name="data-exploration--visualization"></a>Az adatok feltárása és képi megjelenítés
Miután az adatok Spark üzembe, az adatelemzési folyamat következő lépése az az adatokat adatáttekintési és vizualizációs mélyrehatóbb ismereteket szerezhet. Ebben a szakaszban azt az SQL-lekérdezések használatával taxi adatok vizsgálatát és jeleníti meg a cél változók és vizuális ellenőrzése az leendő szolgáltatásai. Pontosabban azt jeleníti meg az utasok számát taxi lelassítja tipp összegeket a gyakoriság és hogyan tippek régiónként eltérő fizetési összeg, és írja be a gyakorisága.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Nyomtatandó hisztogram utas száma gyakoriságok taxi lelassítja-minta
A kód és a következő kódrészletek használata SQL Magic Quadrant a minta és az adatok ábrázolásához helyi Magic Quadrant lekérdezéséhez.

* **SQL Magic Quadrant (`%%sql`)** a HDInsight PySpark kernelt támogatja az egyszerű beágyazott HiveQL-lekérdezéseket futtassanak a kontext sqlContext. A (-o VARIABLE_NAME) argumentum az SQL-lekérdezés kimenetét a Jupyter kiszolgálón egy Pandas DataFrame, továbbra is fennáll. Ez azt jelenti, hogy a helyi módban érhető el.
* A  **`%%local` Magic Quadrant** kód futtatása a Jupyter-kiszolgálón, amelyen a HDInsight-fürt átjárócsomópontjával helyben szolgál. Általában használni `%%local` magic után a `%%sql -o` Magic Quadrant a lekérdezés futtatására szolgál. Az -o paraméter szeretné megőrizni a helyileg az SQL-lekérdezés kimenete. Ezután a `%%local` Magic Quadrant elindítja a következő készletét kódrészletek, amelyek helyileg tárolja az SQL-lekérdezések kimenetének helyi futtatásához. A kód futtatása után a rendszer automatikusan vizualizálja a kimenetet.

Ez a lekérdezés lekéri a lelassítja utas száma szerint. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Ez a kód létrehoz egy helyi adatkeretbe a lekérdezés kimenetét, és jeleníti meg az adatokat. A `%%local` Magic Quadrant létrehoz egy helyi-adatkeretbe, `sqlResults`, amelyek használhatók a matplotlib ábrázolásához. 

> [!NOTE]
> A PySpark Magic Quadrant Ez az útmutató több alkalommal van használva. Nagy adatmennyiség esetén meg kell minta hozzon létre egy adat-keretet, amelyek illeszkednek a helyi memóriához.
> 
> 

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

A nyomtatandó utas száma szerint a lelassítja kódja

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**OUTPUT**

![Az utak utas száma szerint gyakorisága](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Választhat a különböző típusú Vizualizációk (tábla, a kör, vonal, terület vagy sáv) közül a **típus** menü gombok a jegyzetfüzet. A sáv diagram itt jelenik meg.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Tipp összegek és hogyan tipp összeg eltérő utas száma és diszkont összegek hisztogram ábrázolásához.
Mintaadatok az SQL-lekérdezés használata...

    # SQL SQUERY
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

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline

    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()


**A KIMENETRE:** 

![Összeg terjesztési tipp](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tipp összeg utas száma szerint](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Összeg tipp diszkont összeg szerint](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>A szolgáltatás modellezési mérnöki csapathoz, átalakítási és az adatok előkészítése
Ez a szakasz ismerteti, és a kódot biztosít az adatok előkészítéséhez használt gépi Tanulási modellezési használható eljárások. Azt mutatja be a következő feladatokat végezheti el:

* Hozzon létre egy új szolgáltatás óra particionálásával be forgalom idő bins
* Index és a gyakori kódolása kategorikus funkciók
* A gépi Tanulási funkciók be címkézett pont objektumok létrehozása
* Hozzon létre egy véletlenszerű alárendelt mintavétel az adatok, és azt fel képzési és egy tesztelési
* A szolgáltatás méretezése
* A memóriában objektumok

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Forgalom alkalommal particionálásával be őket egy új szolgáltatás létrehozása
A kód bemutatja, hogyan hozhat létre egy új szolgáltatás forgalom alkalommal particionálásával be őket, majd az eredményül kapott adathalmaz a memóriában gyorsítótárazza. Gyorsítótárazás vezet javított végrehajtási idő, rugalmas elosztott adatkészleteket (rdd-k) és adatkeretek használják ismételten. Tehát azt gyorsítótár rdd-k és adatkeretek, a forgatókönyv több lépésből áll.

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

**OUTPUT**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Index és a egy gyakori kódolása kategorikus funkciók
Ez a szakasz bemutatja, hogyan index, vagy a modellezési funkciók be kategorikus funkciói kódolása. A modellezés és MLlib függvényekben kategorikus bemeneti adatokat a funkciókat indexelve vagy használata előtt kódolva. 

A modelltől függően kell index vagy más módon kell kódolnia azokat. Ha például Logistic és lineáris regressziós modellek igényelnek egy gyakori kódolási, ahol, például három kategóriába szolgáltatás bővíthet oszlopok szolgáltatás három, minden egyes tartalmazó 0 vagy 1 kategóriájától függően egy megfigyelési. MLlib biztosít [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) egy gyakori kódolási függvényét. A kódoló címke indexek oszlop bináris vektorok értékkel legfeljebb egyetlen egy-egy számoszlop rendeli hozzá. Ehhez a kódoláshoz lehetővé teszi, hogy a numerikus értékelt szolgáltatások, például a logisztikai regressziós kategorikus funkciók alkalmazandó elvárt algoritmusokat.

A következő index és a kategorikus funkciók kódolása a kódot:

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

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


**OUTPUT**

Cella fent ideje: 3.14 másodperc

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>A gépi Tanulási funkciók be címkézett pont objektumok létrehozása
Ez a szakasz tartalmazza a kódot, címkézett pont adattípusú értékként kategorikus szöveges adatok indexelése és kódolása azt jeleníti meg. Ez előkészíti taníthat vagy tesztelhet MLlib logisztikai regressziós és más képbesorolási modellek használható. Címkézett pont objektumok olyan rugalmas elosztott adatkészleteket (RDD) formátumú úgy, hogy a gépi Tanulási algoritmusokat a MLlib többsége által bemeneti adatként van szükség. A [pont feliratú](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) sűrű vagy ritka, a helyi vektor társítva van egy címke/válasz.

Az index és a bináris osztályozási funkciói szöveg kódolása a kód itt látható.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Ez a kód létrehoz egy véletlenszerű mintavételi (25 %-os itt használt) adatok. Bár ez nem szükséges ehhez a példához az adatkészlet méretének miatt, bemutatjuk, hogyan lehet mintát itt az adatokat. Majd, tudja, hogyan használhatja a saját problémára vonatkozó, szükség esetén. Ha minták nagy, ez is tanítási modell jelentős időt takaríthat meg. Ezután azt ossza fel a minta-képzés (Itt 75 %) és egy tesztelési részét (Itt 25 %) besorolási és regressziós modellezéshez.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand

    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()

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

**OUTPUT**

Cella fent ideje: 0.31 másodperc

### <a name="feature-scaling"></a>A szolgáltatás méretezése
A szolgáltatás méretezése adatok normalizálási, más néven adatblokkok, hogy funkciók széles körben folyósított értékekkel van nem adott túlzott mérjük a cél függvényben. Skálázás használja a funkciónak a [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) egység eltérése a szolgáltatások méretezése. Lineáris regresszió a Sztochasztikus átmenetes Grádiens (SGD) használható MLlib nyújtja. SGD számos más gépi tanulási modelleket például rendeződik regressziót vagy támogatási vektor gépek (SVM) betanításához egy népszerű algoritmus.   

> [!TIP]
> A LinearRegressionWithSGD algoritmust kell lenniük a szolgáltatás méretezése található.   
> 
> 

Íme a kódot a skála változók a regularized lineáris SGD algoritmus való használatra.

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

**OUTPUT**

Cella fent ideje: 11.67 másodperc

### <a name="cache-objects-in-memory"></a>A memóriában objektumok
A képzés és a gépi Tanulási algoritmusok tesztelési idő objektumokat az osztályozás, regressziós és szolgáltatások méretezése a bemeneti adatok keret gyorsítótárazásával csökkenthető.

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

**OUTPUT** 

Cella fent ideje: 0.13 másodperc

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>E tipp: a bináris osztályozási modell fizetős előrejelzése
Ez a szakasz bemutatja, hogyan használható három-modellekhez előrejelzésére bináris osztályozási feladatának tipp fizetős taxi útnak-e. A bemutatott modellek a következők:

* Logisztikai regresszió 
* Véletlenszerű erdő
* Színátmenet kiemelési fák

Minden modell létrehozásához a kód szakaszban van felosztva, amelyek lépéseket: 

1. **Modell a betanítási** egy paraméterkészlettel adatok
2. **Minta értékelés** egy tesztelési adathalmazon metrikákkal
3. **Modell mentése** későbbi felhasználásra blobba

Bemutatjuk, hogyan kereszt-ellenőrzés (CV) ehhez a két módon abszolút paraméterrel:

1. Használatával **általános** egyéni kódot, amely MLlib az algoritmus, és bármely paraméter alkalmazható állít be egy olyan algoritmust. 
2. Használatával a **pySpark CrossValidator folyamat függvény**. Ne feledje, hogy CrossValidator Spark 1.5.0 vonatkozik néhány korlátozás: 
   
   * Folyamat modellek nem lehet menteni, perzisztens későbbi felhasználásra.
   * A modellben minden paraméter nem használható.
   * Minden MLlib algoritmus nem használható.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Általános érvényesítési és a hiperparaméter kezdik a bináris osztályozási logisztikai regressziós algoritmusával alkalmazott
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékelése és a egy logisztikai regressziós modellt a Mentés [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , amely a fedélzeti e tipp NYC taxi utazást és diszkont adatkészlet útnak fizetnek. A modell tanítása az érvényesítési (CV) és a hiperparaméter kezdik a tanulási algoritmusok MLlib a valamelyik alkalmazható egyéni kód implementálása.   

> [!NOTE]
> Egyéni CV programkód végrehajtása több percig is eltarthat.
> 
> 

**CV és a hiperparaméter kezdik logisztikai regressziós modell betanítása**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics

    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)

    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric

    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)


    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Coefficients: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

INTERCEPT:-0.0111216486893

Cella fent ideje: 14.43 másodperc

**A standard metrikák bináris osztályozási modell értékelése**

Ebben a szakaszban a kód bemutatja, hogyan értékelheti ki egy logisztikai regressziós modell egy test-adatkészlet, többek között a ROC-görbe rajz ellen.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

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

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Lekéréses kérelem alatti terület = 0.985336538462

Terület alatt ROC = 0.983383274312

Összefoglaló statisztikák

Pontosság = 0.984174341679

Idézze = 0.984174341679

F1 Pontozása = 0.984174341679

Cella fent ideje: 2.67 másodperc

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

    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVES
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


**OUTPUT**

![Logisztikai regressziós ROC-görbét az általános megközelítés](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Egy blobot a jövőbeni felhasználásához a modell megőrzése**

Ebben a szakaszban a kód bemutatja, hogyan menteni a logisztikai regressziós modellt a felhasználásához.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;

    logitBest.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**OUTPUT**

Cella fent ideje: 34.57 másodperc

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>(Rugalmas regressziós) logisztikai regressziós modell MLlib a CrossValidator folyamat függvény használata
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékelése és a egy logisztikai regressziós modellt a Mentés [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , amely a fedélzeti e tipp NYC taxi utazást és diszkont adatkészlet útnak fizetnek. A modell tanítása az érvényesítési (CV) és a hiperparaméter kezdik az valósítja meg az MLlib CrossValidator folyamat funkcióval a CV paraméteres.   

> [!NOTE]
> Ez a kód MLlib CV végrehajtása több percig is eltarthat.
> 
> 

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc

    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT**

Cella fent ideje: 107.98 másodperc

**A ROC-görbe ábrázolásához.**

A *predictionAndLabelsDF* táblázatként, regisztrálva van *tmp_results*, az előző cella. *tmp_results* használható do lekérdezések és a kimeneti eredmények a data-keretben sqlResults küldik az ábrázolást. A kód itt látható.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Íme a kódot a ROC-görbe ábrázolásához.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc

    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    #PLOT
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


**OUTPUT**

![Logisztikai regressziós ROC-görbe MLlib a CrossValidator használatával](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Véletlenszerű erdő besorolás
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékelése és a egy véletlenszerű erdő regressziós, amely képes-e tipp fizetős útnak NYC taxi utazást és diszkont adatkészlet mentése.

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
    ## UN-COMMENT IF YOU WANT TO PRING TREES
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


**OUTPUT**

Terület alatt ROC = 0.985336538462

Cella fent ideje: 26.72 másodperc

### <a name="gradient-boosting-trees-classification"></a>Színátmenet kiemelési fák besorolás
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékeléséhez, és mentse egy színátmenetes kiemelési fák modellt, az e tipp: a NYC taxi út útnak fizetős és az adatkészlet díjszabás.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # Area under ROC curve
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

**OUTPUT**

Terület alatt ROC = 0.985336538462

Cella fent ideje: 28.13 másodperc

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Tipp összeg előrejelzésére regressziós modellek (nem használ CV)
Ez a szakasz bemutatja, hogyan használható három modellekhez a regressziós feladat: a fizetős funkciókat a tip alapján taxi útnak tipp összeg előrejelzésére. A bemutatott modellek a következők:

* Lineáris regresszió rendeződik
* Véletlenszerű erdő
* Színátmenet kiemelési fák

Ezek a modellek a bevezetésben is ismerteti. Minden modell létrehozásához a kód szakaszban van felosztva, amelyek lépéseket: 

1. **Modell a betanítási** egy paraméterkészlettel adatok
2. **Minta értékelés** egy tesztelési adathalmazon metrikákkal
3. **Modell mentése** későbbi felhasználásra blobba   

> AZURE Megjegyzés: Kereszt-ellenőrzés nem használható ebben a szakaszban a három regressziós modellek, mivel ez a részletek a logisztikai regressziós modellek látható volt. Egy példa a nettó rugalmas CV lineáris regresszió használata biztosítja a függelék – az ebben a témakörben.
> 
> AZURE Megjegyzés: Tapasztalataink lehet LinearRegressionWithSGD modellek convergence problémái, és paramétereket kell lennie a módosított/optimalizált gondosan beszerzése érvényes modellt. Az átszervezés változók skálázás jelentősen nyújtanak segítséget. A függelék: az ebben a témakörben látható rugalmas nettó regressziós is használható LinearRegressionWithSGD helyett.
> 
> 

### <a name="linear-regression-with-sgd"></a>A SGD lineáris regresszió
A kód ebben a szakaszban mutatja be, hogyan használja a méretezhető szolgáltatások egy lineáris regressziós optimalizálás sztochasztikus gradiens módszeres (SGD) használó, és hogyan pontszám, kiértékelheti és mentse a módosítást az Azure Blob Storage (WASB).

> [!TIP]
> Tapasztalataink LinearRegressionWithSGD modellek az átszervezés problémái lehetnek, és paramétereket kell lennie a módosított/optimalizált gondosan beszerzése érvényes modellt. Az átszervezés változók skálázás jelentősen nyújtanak segítséget.
> 
> 

    # LINEAR REGRESSION WITH SGD 

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

    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Coefficients: [0.0141707753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092, -0.00456498588241, -0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, -0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

INTERCEPT: 0.854507624459

RMSE = 1.23485131376

R-sqr = 0.597963951127

Cella fent ideje: 38.62 másodperc

### <a name="random-forest-regression"></a>Véletlenszerű erdő regresszió
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékelheti és mentse egy véletlenszerű erdőmodell, amely előrejelzi a NYC taxi útadatok tipp összege.   

> [!NOTE]
> Kereszt-ellenőrzési abszolút egyéni kódot használó paraméterrel megtalálható a függelékben.
> 
> 

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

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

**OUTPUT**

GYÖKÁTLAGOS = 0.931981967875

R-sqr = 0.733445485802

Cella fent ideje: 25.98 másodperc

### <a name="gradient-boosting-trees-regression"></a>Színátmenet kiemelési fák regresszió
Ebben a szakaszban a kód bemutatja, hogyan betanításához, kiértékelése és a egy színátmenetes kiemelési fák modellt, a NYC taxi útadatok tipp összege az mentés.

**Betanítása és kiértékelése**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

RMSE = 0.928172197114

R-sqr = 0.732680354389

Cella fent ideje: 20.9 másodperc

**Diagram**

*tmp_results* regisztrálva van az előző cella Hive-táblaként. Kerülnek a kimenetbe az eredményeket a táblából a *sqlResults* adatkeretbe ábrázolásához. A kód itt látható

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Íme a kódot, a Jupyter-kiszolgáló használata az adatok ábrázolásához.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np

    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Actual-vs-predicted-tip-amounts](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>A függelék: További regressziós feladatok paraméterelemzést keresztellenőrzési használata
Ez a függelék rugalmas net lineáris regresszió használata CV módjáról és az egyéni kód használata véletlenszerű erdő regressziós paraméteres CV módjáról kódját tartalmazza.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Keresztellenőrzés nettó rugalmas lineáris regresszió használata
A kód ebben a szakaszban mutatja be, hogyan rugalmas net lineáris regresszió használata keresztellenőrzés és a teszt adatai alapján a modell kiértékelése.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 

    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)

    # CONVERT TO DF AND SAVE REGISTER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Cella fent ideje: 161.21 másodperc

**Az R-SQR metrika kiértékelése**

*tmp_results* regisztrálva van az előző cella Hive-táblaként. Kerülnek a kimenetbe az eredményeket a táblából a *sqlResults* adatkeretbe ábrázolásához. A kód itt látható

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Ez a kód az R-sqr kiszámításához.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**OUTPUT**

R-sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Az egyéni kód használata véletlenszerű erdő regressziós paraméteres keresztellenőrzés
Ebben a szakaszban a kódot az egyéni kód használata véletlenszerű erdő regressziós paraméteres keresztellenőrzés és a modell teszt adatai alapján kiértékelheti, hogy jeleníti meg.

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid

    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))

    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric

    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

GYÖKÁTLAGOS = 0.906972198262

R-sqr = 0.740751197012

Cella fent ideje: 69.17 másodperc

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Memória és a nyomtatási modell helyekről származó objektumok törlése
Használat `unpersist()` törli a memóriában gyorsítótárazott objektumokat.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()

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


**OUTPUT**

[122] PythonRDD RDD PythonRDD.scala címen található: 43

** Használható a felhasználás jegyzetfüzet modellfájlokat nyomtatás elérési útja. ** Felhasználását, és a egy független adatkészlet pontszám, meg kell másolja és illessze be a következő fájl neve a "használat"jegyzetfüzetben.

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>A következő lépések
Most, hogy a Spark MlLib a létrehozott regressziós és besorolási modellek, készen áll a pontszám, és ezek a modellek kiértékelése.

**Használati modell:** pontszám és kiértékelheti a besorolási és regressziós modellek, ebben a témakörben létrehozott kapcsolatban lásd: [pontszám és kiértékelheti a Spark használatával összeállított gépi tanulási modellek](spark-model-consumption.md).

