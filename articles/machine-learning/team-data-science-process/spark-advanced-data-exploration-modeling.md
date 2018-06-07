---
title: Az adatok feltárása és Spark modellezés speciális |} Microsoft Docs
description: HDInsight Spark használja az adatok feltárása és kereszt-ellenőrzési és hyperparameter optimalizálással bináris osztályozás és regressziós modell betanításához.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: f90d9a80-4eaf-437b-a914-23514390cd60
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: deguhath
ms.openlocfilehash: 3058678032989d71886311073513a23ac19d18f8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838860"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Speciális adatáttekintés és modellezés a Spark segítségével
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Ez a forgatókönyv HDInsight Spark az adatok feltárása és bináris osztályozás és kereszt-ellenőrzési használatával regressziós modell betanítása és hyperparameter optimalizálása mintán a következőt: út taxiköltség és 2013 dataset díjszabás. Az végigvezeti a lépéseken, a [adatok tudományos folyamat](http://aka.ms/datascienceprocess), végpontok közötti, egy HDInsight Spark-fürt kezelése és az Azure BLOB használatával tárolja az adatokat, majd a modellt. A folyamat felderíti és az Azure Storage-Blobból adatok visualizes, és ezután előkészíti az adatokat a prediktív modellek létrehozásához. A megoldás code, és a megfelelő előkészítésére megjelenítése Python használatban van. Ezek a modellek buildre, a Spark MLlib eszközkészlet bináris osztályozás és regressziós modellezéshez hatékony feladatok elvégzésére. 

* A **bináris osztályozási** feladat-e a út tipp fizetett előrejelzése céljából. 
* A **regressziós** feladata más tip szolgáltatások alapján tipp mennyisége előre jelezni. 

A modellezési lépések a kód bemutatja, hogyan képzése, értékelje ki és mentse a modell típusonkénti is tartalmaznak. A témakör néhány, az azonos alapoktól a [adatok feltárása és Spark modellezés](spark-data-exploration-modeling.md) témakör. De azt a több "kiemelt" abban, hogy a kereszt-ellenőrzési az optimális pontos besorolási és regressziós modell betanítása abszolút hyperparameter is használja. 

**Kereszt-ellenőrzési (KtgE)** egy technika, amely értékeli a milyen mértékben a modellek betanítása adatokat egy ismert csoportján használatúvá történő előrejelzésére részeit, amelyen az még nincs betanítva adatkészletek.  A közös megvalósított, hogy a DataSet adatkészlet felosztani K modellrészt és majd a ciklikus multiplexeléssel egy, a modellrészt a modell betanításához. A modell előrejelzéses pontosan, ha ez nem a modell betanításához használandó modellrészek a független adatkészletét tesztelése képességét megfelelőségét ellenőrizni kell.

**Hyperparameter optimalizálási** a probléma általában azzal a céllal, az algoritmus egy független adatkészlet teljesítményének biztosítása optimalizálása a tanulási algoritmus hiperparamétereket készlete kiválasztása. **Hiperparamétereket** értékeket, amelyeket meg kell adni a modell betanítási eljárás kívül vannak. Ezek az értékek feltételezéseket hatással lehet a rugalmasság és a modell pontosságát. Döntési fák hiperparamétereket, például a kívánt mélysége és a fában levelek például rendelkeznek. Támogatási vektoros gépeknél (SVMs) szükség van a téves besorolás szövegminősítési kifejezés beállítását. 

A közös itt használt hyperparameter optimalizálás végrehajtására módja a rács keresést, vagy egy **paraméter ismétlés**. Ez több végrehajtása keresztül értékek részletes keresést a hyperparameter terület megadott részhalmazának tanulási algoritmus. Keresztellenőrzési megadhatja az optimális eredmények elérése érdekében a rács keresési algoritmus által előállított korlátoznia a teljesítmény metrikát. KtgE használt hyperparameter elvégzésekor mindig segítségével például overfitting egy modell betanítási adatok, hogy a modell megőrzi az általános adatkészletet, amelyből a betanítási adatok kibontotta alkalmazandó kapacitás korlát problémák.

A modellek használjuk logisztikai és lineáris regressziós, véletlenszerű erdők és átmenetes súlyozott fák tartalmazza:

* [A SGD lineáris regressziós](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) egy lineáris regressziós modellt, amely Stochastic átmenetes módszeren (SGD) módszerrel és optimalizálási, valamint a szolgáltatás skálázás tipp összegek előre fizetett. 
* [A LBFGS logisztikai regresszió](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) vagy a "logit" regresszió egy regressziós modell kategorikus adatok besorolása ehhez a függő változó esetén használható. LBFGS egy látszólagos Newton optimalizálási algoritmus, amely megközelíti a Broyden – Fletcher – Goldfarb – Shanno (BFGS) algoritmus csak korlátozott mennyiségű memóriát használ, és a gépi tanulás széles körben használt.
* [Véletlenszerű erdők](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) döntési fák együttes vannak.  Összekapcsolásának sok döntési fa overfitting kockázatának csökkentéséhez. Véletlenszerű erdők regressziós és besorolási használ, és kezelni tud a kategorikus szolgáltatásokat, és annak a multiclass adatbesorolás beállításai. Azok szolgáltatás skálázás nem igényelnek, és képesek nemlinearitás rögzítése és kapcsolati funkció. Véletlenszerű erdők a legtöbb sikeres gépi tanulási modellek besorolás és regressziós egyikét.
* [Színátmenet súlyozott fák](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) együttes döntési fák. GBTs ismételt adatvesztés függvény minimalizálása érdekében a döntési fák betanítása. GBTs regressziós és besorolási használ és kezelni tud a kategorikus szolgáltatásokat, nincs szükség a méretezés szolgáltatás és képesek nemlinearitás rögzítése és kapcsolati funkció. Is is szerepel a multiclass-adatbesorolás beállításai.

Példák KtgE és Hyperparameter modellezési ismétlés láthatók a bináris osztályozási problémához. Egyszerűbb (nélkül paraméter halmokat) be regressziós feladatok fő témakört. De a függelékben használata rugalmas net lineáris regressziós és KtgE az véletlenszerű erdő regressziós használatával ismétlés paraméter érvényesítése is ismertet. A **nettó rugalmas** rendeződik regressziós metódus van, az eljárást, mint az 1. és 2. szintű metrikák lineáris regressziós modellt, amely lineárisan illeszkedő egyesíti a [szabadkézi](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) és [peremmel](https://en.wikipedia.org/wiki/Tikhonov_regularization) módszerek.   

> [!NOTE]
> Bár a Spark MLlib eszközkészlet célja, hogy működik a nagy adatkészleteket, viszonylag kis minta (KB. 30 Mb használatával 170K sorok, az eredeti NYC adatkészlet hamarosan 0,1 %) használható itt kényelmét szolgálja. Az itt megadott gyakorlat 2 munkavégző csomópontokhoz a HDInsight-fürtök a hatékony (KB) a fut. Ugyanazt a kódot, kisebb módosításokkal nagyobb-adatmennyiség megfelelő módosításával az adatokat a memóriában, és a fürt méretének módosítása feldolgozásához használható.
> 
> 

## <a name="setup-spark-clusters-and-notebooks"></a>A telepítő: A Spark-fürtök és notebookok
Beállítási lépéseket és kód okat ebben a forgatókönyvben egy HDInsight Spark 1.6 használatával. De Jupyter notebookok HDInsight Spark 1.6-os és a Spark 2.0 fürtök rendelkeznek. A jegyzetfüzetek és a hozzájuk hivatkozások leírása szerepelnek a [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) az azokat tartalmazó GitHub-tárházban. Ezenkívül a kód itt és a csatolt jegyzetfüzetekben általános és a Spark-fürt kell működnie. Ha nem használja a HDInsight Spark, a fürt beállítása, és lehet, hogy a felügyeleti lépések némileg eltér az itt látható. Kényelmi célokat szolgál az alábbiakban a Jupyter notebookok Spark 1.6-os és 2.0-s verzióját kell futtatni a pyspark kernel a Jupyter Notebook kiszolgáló mutató hivatkozásokat:

### <a name="spark-16-notebooks"></a>Spark 1.6-os notebookok

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): #1 jegyzetfüzet és modell fejlesztési hyperparameter hangolása és kereszt-ellenőrzési témaköröket tartalmazza.

### <a name="spark-20-notebooks"></a>Spark 2.0 notebookok

[Spark2.0-pySpark3-Machine-Learning-Data-Science-Spark-Advanced-Data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl információkat nyújt az adatok feltárása, modellezéséhez, és a Spark 2.0 fürtök pontozási végrehajtásához.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**OUTPUT**

DateTime.DateTime (2016, 4, 18., 17, 36, 27 és 832799)

### <a name="import-libraries"></a>Könyvtárak importálása
Importálás szükséges kódtárak a következő kóddal:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Spark és az PySpark magics az adott néven beállítás
A Jupyter notebookok kapnak PySpark kernelt beállításkészletet kontextusban rendelkezik. Így nem kell beállítani a Spark, vagy Hive-környezeteket elindítása az alkalmazás használata előtt explicit módon fejleszt. Ezek a környezetek érhetők el, alapértelmezés szerint. Ezek a környezetek a következők:

* sc - a Spark 
* az sqlContext - struktúra

A PySpark kernel tartalmaz néhány előre definiált "magics", amelyeket speciális meghívhatja a parancsok %%. Nincsenek két ilyen parancsot a következő kód mintákat használt.

* **%% helyi** határozza meg, amely a kódot az egymás utáni sorok helyileg hajthatnak végre. Kód érvényes Python-kódot kell lennie.
* **%% sql -o <variable name>**  végrehajtja a Hive-lekérdezések a sqlContext ellen. Ha az -o paramétert, a lekérdezés eredménye megőrződjön-e az a %%, egy Pandas DataFrame helyi Python-környezetben.

További információ a Jupyter notebookokból és az előre meghatározott kernelek "magics", amely a biztosítanak, lásd: [HDInsight Spark Linux és a Jupyter notebookok elérhető kernelek a HDInsight-fürtök](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>A nyilvános blob adatfeldolgozást:
Az első lépés az adatok tudományos folyamatban az adatok helyét az adatok feltárása és modellezési környezet forrásokból vizsgálandó. Ebben a környezetben a forgatókönyv külső. Ez a szakasz a kód feladatok sorozatát befejezéséhez:

* betöltési modellezni adatok minta
* olvassa a bemeneti adatkészletet (.tsv fájlként tárolja)
* formázza és az adatok törlése
* Hozzon létre és objektumok (RDDs vagy adatkeretek) a memóriában gyorsítótárazása
* regisztrálja az SQL-környezetben temp-táblázatként.

A kód adatfeldolgozást az itt látható.

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

## <a name="data-exploration--visualization"></a>Az adatok feltárása & képi megjelenítés
Miután az adatok külső üzembe, az tudományos folyamat következő lépése hoz mélyrehatóbb ismereteket szerezhet az adatok feltárása és a képi megjelenítés keresztül. Ebben a részben azt vizsgálja meg az SQL-lekérdezések használatával taxi adatok és a cél változók és a visual hálózatfelügyeleti potenciális funkcióit megrajzolásához. Pontosabban azt megrajzolásához utas számát taxi való adatváltások számát, a gyakoriság tipp díjak és hogyan tippek változhat fizetési mennyiségét, és írja be a gyakoriságát.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>A mintában taxi utak utas száma gyakoriságértékek listáját hisztogram ábrázolása
A kód és a további részletek a minta és az adatok ábrázolására helyi magic lekérdezése SQL magic segítségével.

* **SQL magic (`%%sql`)** a HDInsight PySpark kernel a sqlContext könnyen beágyazott HiveQL lekérdezéseket támogatja. A (-o VARIABLE_NAME) argumentum az SQL-lekérdezés kimenetét a Jupyter kiszolgálón egy Pandas DataFrame, továbbra is fennáll. Ez azt jelenti, hogy a helyi módban érhető el.
* A  **`%%local` magic** kód futtatása helyben a Jupyter kiszolgálón, amely a HDInsight-fürt headnode szolgál. Általában akkor használják `%%local` magic után a `%%sql -o` magic lekérdezés futtatására szolgál. Az -o paraméter szeretné megőrizni a helyileg az SQL-lekérdezés kimenetét. Ezt követően a `%%local` magic elindítja a kódrészleteket helyileg futtatni a helyileg tárolt SQL-lekérdezések eredményének következő készletét. A kimeneti automatikusan történik, a kód futtatása után.

Ez a lekérdezés lekéri a utazgatással utas száma szerint. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Ez a kód létrehoz egy helyi adatok-keret a lekérdezés eredményében, és az adatok tevékenységtérkép. A `%%local` magic létrehoz egy helyi adatok-keret, `sqlResults`, a matplotlib ábrázolásához használható. 

> [!NOTE]
> A PySpark magic ebben a bemutatóban több alkalommal van használva. Nagy adatmennyiség esetén a kell minta lehet adatok-keret létrehozásához a helyi memóriához.
> 
> 

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Útjaira által utas száma ábrázolásához kód

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

![Gyakoriság utak utas száma szerint](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Között számos különböző típusú megjelenítések (táblázat, torta, vonal, terület vagy sáv) segítségével kiválaszthatja a **típus** a notebook menü gombjai. A sáv rajzot itt jelenik meg.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Tipp összegek és hogyan függ a tipp összeg utas számát és a jegy ára összegek hisztogram ábrázolásához.
Használja a mintaadatokat egy SQL-lekérdezést...

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


Ez a kód cella használja az SQL-lekérdezést három előkészítésére az adatokat.

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

![Tipp összeg terjesztési](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tipp összeg utas száma szerint](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipp összeg jegy ára mennyiség szerint](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>A funkció modellezési mérnöki csapathoz, átalakítás és adatok előkészítése
Ez a szakasz ismerteti, és lehetővé teszi a kód készíti elő az adatok ML modellezési használható használt eljárásokat. Azt mutatja be a következő feladatok elvégzéséhez:

* Hozzon létre egy új szolgáltatás üzemideje (óra) a forgalom idő bins particionálás
* Index és a közbeni kódolása kategorikus szolgáltatások
* Gépi tanulás függvényekké bemeneti címkézett pont objektumok létrehozása
* Hozzon létre egy véletlenszerű alárendelt mintavétel az adatok, és ossza képzési és egy tesztelési
* A szolgáltatás skálázás
* A memóriában objektumok

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Hozzon létre egy új szolgáltatás a bins forgalom alkalommal particionálás
A kód bemutatja, hogyan hozhat létre egy új szolgáltatás a bins forgalom alkalommal particionálás és az eredményül kapott adatok keret memóriában gyorsítótárazásának. Gyorsítótárazás vezet továbbfejlesztett végrehajtási idő ahol elosztott rugalmas adatkészleteket (RDDs) és adatkeretek használják ismételten. Igen azt a gyorsítótár RDDs és adatkeretek, a forgatókönyv több lépésből áll.

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

### <a name="index-and-one-hot-encode-categorical-features"></a>Index és egy közbeni kódolása kategorikus szolgáltatások
Ez a szakasz bemutatja, hogyan index, vagy a modellezési függvényekké bemeneti kategorikus szolgáltatások kódolása. A modellezési és MLlib feladatai szükséges szolgáltatások kategorikus bemeneti adatokkal indexelt vagy használata előtt kódolású előrejelzése. 

A modelltől függően szeretnénk index vagy kódolás különböző módon. Például Logistic és lineáris regressziós modell igényelnek egy közbeni kódolását, ahol, például három kategóriába szolgáltatás minden egyes tartalmazó 0 vagy 1 attól függően, hogy egy megfigyelési kategória három funkció oszlopokba bővíthetők. MLlib biztosít [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) függvényét egy közbeni kódolást. A kódoló címke indexek oszlop bináris vektorok értékű legfeljebb egyetlen egy-egy oszlop rendeli hozzá. Ez a kódolás lehetővé teszi, hogy a várt numerikus fontos funkciók, például logisztikai regresszió kategorikus szolgáltatások alkalmazandó algoritmusokat.

Index és kategorikus szolgáltatások kódolja a kód itt látható:

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

Cella fent ideje: 3.14 másodpercben

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Gépi tanulás függvényekké bemeneti címkézett pont objektumok létrehozása
Ez a szakasz a kód bemutatja, hogyan kategorikus szöveges adatok címkézett pont adattípusú értékként index és kódolással azt. Ez felkészíti a beállításhoz képzése és MLlib logisztikai regresszió és egyéb besorolási modell teszteléséhez használandó. Címkézett pont objektum rugalmas elosztott adatkészletek (RDD) formázott oly módon, hogy a bemeneti adatként ML algoritmusok MLlib a legtöbb van szükség. A [pont feliratú](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) egy helyi vektor, sűrű vagy ritka, társítva van egy címke-válasz.

Ez a kód index és a bináris osztályozási funkciói szöveg kódolása.

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
Ez a kód hoz létre egy véletlenszerű mintavételi (25 % itt használt) adatok. Bár nem szükséges ehhez a példához a mérete miatt a DataSet adatkészlet, bemutatjuk, hogyan lehet mintát itt az adatokat. Ezután tudja, hogyan használja, a saját probléma, ha szükséges. Ha minták nagy, ez is tanítási modell jelentős időt takaríthat meg. Ezután azt ossza fel a minta egy képzési (Itt 75 %) és egy tesztelési részét (25 %-os itt) használata a besorolás és regressziós modellezéshez hatékony.

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

### <a name="feature-scaling"></a>A szolgáltatás skálázás
Szolgáltatás skálázást, más néven adatok normalizálási biztosítja, hogy szolgáltatások széles körben folyósított értékekkel van nem a megadott túlzott mérjük a objektív függvényben. A szolgáltatás által használt skálázás kódját a [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) méretezési egység eltérése a szolgáltatásokat. Ez biztosítja MLlib lineáris regressziós rendelkező Stochastic átmenetes módszeren (SGD) használható. SGD egy népszerű más gépi tanulási modellek például rendeződik regresszió vagy támogatási vektoros gépek (SVM) számos különféle képzési algoritmus.   

> [!TIP]
> Találtunk, amelyeknek a LinearRegressionWithSGD algoritmust kell skálázás funkció-és nagybetűket.   
> 
> 

Ez a kód méretezési változók a regularized lineáris SGD algoritmus való használatra.

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
A modell betanítására és tesztelésére ML algoritmusok szükséges idő a bemeneti adatok keret objektumok az osztályozás, regressziós és szolgáltatásokat méretezni gyorsítótárazásával csökkenthető.

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

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Függetlenül attól, tipp bináris osztályozási modellekkel való fizetnek előrejelzése
Ez a szakasz bemutatja, hogyan használható három modelleket használnak becslése a bináris osztályozási feladatát tipp taxi útnak fizetnek-e. A modell jelenik meg a következő:

* Logisztikai regresszió 
* Véletlenszerű erdő
* Átmenet kiemelési fák

Egyes modellek kód szakasz felépítése lépéseket oszlik: 

1. **A modell betanítási** egy paraméterhalmaz adatok
2. **A modell kiértékelése** a metrikák a teszt adatkészlet
3. **Modell mentése** BLOB a jövőbeni felhasználásához

Két módon abszolút paraméterrel megmutatjuk, hogyan ehhez a kereszt-ellenőrzési (KtgE):

1. Használatával **általános** egyéni kód, amely a MLlib bármely algoritmushoz és bármely paraméter alkalmazható algoritmusban állítja be. 
2. Használja a **pySpark CrossValidator csővezeték függvény**. Vegye figyelembe, hogy CrossValidator Spark 1.5.0 néhány korlátozásai: 
   
   * Adatcsatorna modellek nem lehet menteni, megőrzött állapotú későbbi felhasználásra.
   * Nem használható a modellben minden paraméterhez.
   * Nem minden MLlib algoritmus használható.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Érvényesítési és a hyperparameter abszolút bináris osztályozási a logisztikai regresszió algoritmussal használt általános
Ebben a szakaszban a kód bemutatja, hogyan betanítása, értékelje ki és logisztikai regresszió modell mentése [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , amely képes-e tipp fizetnek NYC taxi út és a jegy ára adatkészlet útnak. A modell betanítása érvényesítési (KtgE) és a hyperparameter abszolút megvalósítva, amelyek használhatók a tanulási algoritmusok MLlib az egyik egyéni kód használatával.   

> [!NOTE]
> Az egyéni KtgE kód végrehajtása több percig is eltarthat.
> 
> 

**A KtgE és hyperparameter abszolút logisztikai regresszió modell betanításához**

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

**A szabványos metrikák bináris osztályozási modell kiértékelése**

Ebben a szakaszban a kód bemutatja, hogyan egy test-adatkészlet, beleértve a ROC-görbe rajzot logisztikai regresszió modellt kiértékeléséhez.

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

Törlés a ka területen = 0.985336538462

ROC területen = 0.983383274312

Összesített statisztikák

Pontosság = 0.984174341679

Visszahívása = 0.984174341679

F1 Pontozása = 0.984174341679

Cella fent ideje: 2.67 másodpercben

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

![Logisztikai regresszió: ROC-görbe általános módszer](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**A jövőbeni felhasználásához blob modellt megőrzése**

Ebben a szakaszban a kód bemutatja, hogyan mentse a logisztikai regresszió modellt felhasználásra.

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

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>MLlib tartozó CrossValidator csővezeték függvény használata logisztikai regresszió (rugalmas regresszió) modell
Ebben a szakaszban a kód bemutatja, hogyan betanítása, értékelje ki és logisztikai regresszió modell mentése [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , amely képes-e tipp fizetnek NYC taxi út és a jegy ára adatkészlet útnak. A modell betanítása érvényesítési (KtgE) és hyperparameter abszolút megvalósítva a MLlib CrossValidator csővezeték függvénnyel a KtgE paraméter ismétlés közötti.   

> [!NOTE]
> A MLlib KtgE kód végrehajtása több percig is eltarthat.
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

**A: ROC-görbe ábrázolásához.**

A *predictionAndLabelsDF* táblaként, regisztrálva van *tmp_results*, az előző cella. *tmp_results* segítségével do lekérdezések és a kimeneti eredmények keretbe sqlResults adatok-ábrázolásához. A kód itt látható.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Ez a kód a: ROC-görbe megrajzolásához.

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

![Logisztikai regresszió: ROC-görbe MLlib tartozó CrossValidator használatával](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Véletlenszerű erdő besorolás
Ebben a szakaszban a kód bemutatja, hogyan képzése, értékelje ki és mentse egy véletlenszerű erdő regressziós, amely képes-e tipp fizetnek NYC taxi út és a jegy ára adatkészlet útnak.

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

ROC területen = 0.985336538462

Cella fent ideje: 26.72 másodperc

### <a name="gradient-boosting-trees-classification"></a>Átmenet kiemelési fák besorolás
Ebben a szakaszban a kód bemutatja, hogyan képzése, értékelje ki, és átmenetes kiemelési fák modell, amely képes-e tipp egy út a következőt: taxi út a fizetett mentéséhez és díjszabás adatkészlet.

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

ROC területen = 0.985336538462

Cella fent ideje: 28.13 másodpercben

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Tipp összeg előrejelzése regressziós modellek (nem használ KtgE)
Ez a szakasz bemutatja, hogyan három használja a regressziós tevékenység modellek: előre jelezni az egyéb tip szolgáltatások alapján taxi útnak tipp összeg. A modell jelenik meg a következő:

* Lineáris regressziós rendeződik
* Véletlenszerű erdő
* Átmenet kiemelési fák

Ezek a modellek leírt a Bevezetés. Egyes modellek kód szakasz felépítése lépéseket oszlik: 

1. **A modell betanítási** egy paraméterhalmaz adatok
2. **A modell kiértékelése** a metrikák a teszt adatkészlet
3. **Modell mentése** BLOB a jövőbeni felhasználásához   

> Az AZURE Megjegyzés: Kereszt-ellenőrzési nem használható ebben a szakaszban a három regressziós modell mivel ez a logisztikai regresszió modellek adatai látható volt. Példa bemutatja, hogyan használható az KtgE rugalmas nettó a lineáris regressziós a függelék – az ebben a témakörben találhatók.
> 
> AZURE Megjegyzés: Az a tapasztalat, lehet LinearRegressionWithSGD modellek konvergencia problémákat, és paraméterekkel kell lennie, gondosan az beszerzése egy érvényes modell megváltozott/optimalizált. A változók jelentősen skálázás elősegíti a konvergencia. Rugalmas nettó regressziós, a függelékben ehhez a témakörhöz helyett LinearRegressionWithSGD is használható.
> 
> 

### <a name="linear-regression-with-sgd"></a>A SGD lineáris regressziós
A kódot az itt látható egy lineáris regressziós optimális stochastic átmenetes módszeren (SGD) használó betanítása méretezett szolgáltatások használatának, pontozása, értékelje ki és mentse a modellt az Azure Blob Storage (WASB).

> [!TIP]
> Az a tapasztalat LinearRegressionWithSGD modellek konvergencia problémái lehetnek, és paraméterek kell lennie a megváltozott/optimalizálva gondosan megszerezni egy érvényes modell. A változók jelentősen skálázás elősegíti a konvergencia.
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

### <a name="random-forest-regression"></a>Véletlenszerű erdő regressziós
Ebben a szakaszban a kód bemutatja, hogyan képzése, értékelje ki és mentse egy véletlenszerű erdő modellt, hogy a NYC taxi út adatok tipp összeg előrejelzi.   

> [!NOTE]
> Kereszt-ellenőrzési abszolút egyéni kód használatával paraméterrel a függelékben valósul meg.
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

### <a name="gradient-boosting-trees-regression"></a>Átmenet kiemelési fák regressziós
Ebben a szakaszban a kód bemutatja, hogyan betanítása, kiértékeléséhez és átmenetes kiemelési fák modell, amely képes tipp összeg a következőt: taxi út adatok mentése.

** Betanítása és kiértékelése **

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

**Ábrázolása**

*tmp_results* az előző cella Hive tábla néven van regisztrálva. Kerülnek a kimenetbe az eredményeket a táblából a *sqlResults* adatok-keret ábrázolásához. A kód itt látható

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


A Jupyter kiszolgálóval az adatok ábrázolása a kód itt látható.

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

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>A függelék: További regressziós feladatok keresztellenőrzési használata paraméter el
E függelék rugalmas net használ lineáris regressziós KtgE módjáról és az egyéni kód használatával a véletlenszerű erdő regressziós paraméter ismétlés KtgE módjáról kódját tartalmazza.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Kereszt-nettó rugalmas használatát lineáris regressziós érvényesítése
Ebben a szakaszban a kódot a kereszt-ellenőrzési rugalmas net használ lineáris regressziós és kiértékelheti a modell vizsgálati adatok alapján mutatja.

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

    # CONVERT TO DF AND SAVE REGISER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Cella fent ideje: 161.21 másodperc

**R-SQR metrikájú kiértékelése**

*tmp_results* az előző cella Hive tábla néven van regisztrálva. Kerülnek a kimenetbe az eredményeket a táblából a *sqlResults* adatok-keret ábrázolásához. A kód itt látható

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Ez a kód R-sqr kiszámításához.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**OUTPUT**

R-sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Kereszt-ellenőrzési az egyéni kód használatával a véletlenszerű erdő regressziós paraméter ismétlés
Ebben a szakaszban a kódot a kereszt-ellenőrzési az egyéni kód használatával a véletlenszerű erdő regressziós paraméter törlési és kiértékelheti a modell vizsgálati adatok alapján mutatja.

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

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Memória és a nyomtatási modell helyekről objektumainak eltávolítása
Használjon `unpersist()` jelenleg a memóriában lévő objektumok törlése.

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

[122] PythonRDD RDD PythonRDD.scala a következő: 43

** Nyomtatás fájlok elérési útját modell a fogyasztás notebook használhatók. ** Használnak, és egy független adatkészlet pontozása, meg kell másolja és illessze be a következő fájl neve a "használat jegyzetfüzet".

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
Most, hogy a Spark MlLib regressziós és besorolási modell hozott létre, készen áll útmutató pontozása, és ezek a modellek kiértékeléséhez.

**Modellhez tartozó felhasználás:** pontozása és kiértékelheti a besorolás és regressziós modellek létrehozása ebben a témakörben, lásd: [pontszám és értékelje ki a Spark-beépített machine learning modellek](spark-model-consumption.md).

