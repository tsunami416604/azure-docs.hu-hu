---
title: Speciális adatfeltárás és -modellezés a Sparkkal – Csapatadat-elemzési folyamat
description: A HDInsight Spark használatával adatfeltárási és betanítási bináris osztályozási és regressziós modellek kereszt-ellenőrzés és hiperparaméter-optimalizálás használatával.
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
ms.openlocfilehash: 15d9d186ef36ee9181a6ce0386aa9cc5de7838e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718651"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Speciális adatáttekintés és modellezés a Spark segítségével

Ez a forgatókönyv a HDInsight Spark ot használja az adatok feltárásához és a bináris osztályozási és regressziós modellek betanításához keresztérvényesítési és hiperparaméter-optimalizálás használatával a NYC taxiút és a viteldíj 2013-as adatkészlet mintáján. Végigvezeti az [adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)lépésein, teljes körűen, egy HDInsight Spark-fürt feldolgozásra és az Azure-blobok az adatok és a modellek tárolására. A folyamat feltárja és vizualizálja az Azure Storage Blobból bevitt adatokat, majd előkészíti az adatokat a prediktív modellek létrehozásához. A Python a megoldás kódolására és a megfelelő telkek megjelenítésére szolgál. Ezek a modellek a Spark MLlib eszközkészlet használatával épülnek fel a bináris osztályozási és regressziós modellezési feladatok elvégzéséhez. 

* A **bináris osztályozási** feladat annak előrejelzése, hogy egy tipp et fizetnek-e az utazásért. 
* A **regressziós** feladat a tipp mennyiségének előrejelzése más tippfunkciók alapján. 

A modellezési lépések is tartalmaznak kódot, amely bemutatja, hogyan kell betanítása, kiértékelése és mentése az egyes típusú modellek. A témakör a [Spark-témakörrel való adatfeltárással és modellezéssel](spark-data-exploration-modeling.md) azonos típusú terület egy részét ismerteti. De ez több "fejlett", hogy azt is használja kereszt-ellenőrzés hiperparaméter sweeping a vonat optimálisan pontos osztályozási és regressziós modellek. 

**Keresztérvényesítés (CV)** egy olyan technika, amely felméri, hogy egy modell betanított egy ismert adathalmaz általánosítja előre a funkciók az adatkészletek, amelyeken nem van betanítva.  Az itt használt közös implementáció az adatkészlet K-redőkre osztása, majd a modell ciklikus multiplexelési módon történő betanítása az egyik kivételével. Értékelni, hogy a modell képes-e pontosan előrejelzést adni, ha ebben a hajtásban lévő független adatkészlettel szemben tesztelik, és nem használják a modell betanításához.

**A hiperparaméter-optimalizálás** az a probléma, hogy egy tanulási algoritmushoz hiperparamétereket választ, általában azzal a céllal, hogy optimalizálja az algoritmus teljesítményének mérését egy független adatkészleten. **A hiperparaméterek** olyan értékek, amelyeket a modellbetanítási eljáráson kívül kell megadni. Az értékekkel kapcsolatos feltételezések hatással lehetnek a modellek rugalmasságára és pontosságára. Döntés fák hiperparaméterek, például a kívánt mélységet és a levelek száma a fában. A support vector gépek (SVM-ek) esetében helytelen besorolási büntetési feltételt kell beírni. 

Az itt használt hiperparaméter-optimalizálás gyakori módja a rácskeresés vagy a **paraméters söprés.** Ez a keresés egy tanulási algoritmus hiperparaméter-területének egy részhalmazán megy keresztül. A keresztérvényesítés teljesítménymérőt biztosíthat a rácskeresési algoritmus által létrehozott optimális eredmények rendezéséhez. A hyperparameter sweeping használatával használt önéletrajz segít korlátozni a problémákat, például a modell betanítási adatokhoz való túlszabását, hogy a modell megőrizhesse a kapacitást az általános adathalmazra való alkalmazáshoz, amelyből a betanítási adatokat kinyerték.

Az általunk használt modellek közé tartozik a logisztikai és lineáris regresszió, véletlenszerű erdők és gradiens kiemelt fák:

* [Lineáris regresszió sgd](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) egy lineáris regressziós modell, amely egy sztocastikus gradiens (SGD) módszer és optimalizálása és a funkció méretezésmegjósolni a tip összegeket fizetett. 
* [Logisztikai regresszió LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) vagy "logit" regresszió, egy regressziós modell, amely akkor használható, ha a függő változó kategorikus adatbesorolás. Az LBFGS egy kvázi Newton-optimalizálási algoritmus, amely a Broyden–Fletcher–Goldfarb–Shanno (BFGS) algoritmust közelíti korlátozott mennyiségű számítógépes memóriával, és amelyet széles körben használnak a gépi tanulásban.
* [A véletlenszerű erdők](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) a döntési fák együttesei.  Ezek össze sok döntés fák kockázatának csökkentése érdekében a túlszerelés. A véletlenszerű erdők regressziós és besorolási célokra szolgálnak, és képesek kezelni a kategorikus jellemzőket, és kiterjeszthetők a többosztályos besorolási beállításra. Nem igényelnek szolgáltatásméretezést, és képesek a nem linearitások és a szolgáltatásinterakciók rögzítésére. Véletlenszerű erdők az egyik legsikeresebb gépi tanulási modellek osztályozási és regressziós.
* [Gradiens kiemelt fák](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) együttesei döntésfák. A GBTS vonatdöntési fákit iteratív módon a veszteségfunkció minimalizálása érdekében. A GBTS regressziós és besorolási célokra használatos, és képes kezelni a kategorikus funkciókat, nem igényel szolgáltatásskálázást, és képes a nem linearitásés a szolgáltatáskölcsönhatások rögzítésére. Többosztályos besorolási beállításban is használhatók.

A bináris besorolási probléma példái az ÖNÉLETRAJZ és a Hiperparaméter-söprés használatával jelennek meg. Egyszerűbb példák (paraméteres söprések nélkül) a regressziós feladatok fő témakörében jelennek meg. De a függelékben, érvényesítés segítségével rugalmas háló lineáris regresszió és CV paraméter sweep segítségével véletlenszerű erdő regresszió is bemutatásra kerül. A **rugalmas háló** egy szabályosított regressziós módszer lineáris regressziós modellek illesztéséhez, amely lineárisan kombinálja az L1 és L2 metrikákat a [lasszó](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) és gerinc módszerek büntetéseként. [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization)   

<!-- -->

> [!NOTE]
> Bár a Spark MLlib eszközkészlet nagy adatkészleteken való használatra készült, egy viszonylag kis minta (~30 Mb 170K sorok használatával, az eredeti NYC-adatkészlet körülbelül 0,1%-a) itt használatos a kényelem érdekében. Az itt megadott gyakorlat hatékonyan (körülbelül 10 perc alatt) fut egy 2 feldolgozócsomókkal rendelkező HDInsight-fürtön. Ugyanez a kód kisebb módosításokkal nagyobb adatkészletek feldolgozására használható, megfelelő módosításokkal az adatok memóriában való gyorsítótárazásához és a fürt méretének módosításához.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Beállítás: Spark-fürtök és notebookok
A beállításlépései és a kód ebben a forgatókönyvben találhatók a HDInsight Spark 1.6 használatával. A Jupyter-jegyzetfüzetek azonban hdinsight Spark 1.6-hoz és Spark 2.0-fürtökhöz is rendelkezésre állnak. A jegyzetfüzetek és a hozzájuk mutató hivatkozások leírása az azokat tartalmazó GitHub-tárház [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) található. Ezenkívül a kód itt és a csatolt jegyzetfüzetekben általános, és működnie kell minden Spark-fürtön. Ha nem használja a HDInsight Sparkot, a fürt beállítási és felügyeleti lépései némileg eltérhetnek az itt láthatótól. A kényelem érdekében itt találhatók a Spark 1.6 és 2.0 Jupyter notebookjaira mutató hivatkozások, amelyeket a Jupyter Notebook kiszolgáló pyspark kernelében kell futtatni:

### <a name="spark-16-notebooks"></a>Spark 1.6 notebookok

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): témaköröket tartalmaz a notebook #1, és a modell fejlesztés segítségével hyperparameter tuning és kereszt-érvényesítés.

### <a name="spark-20-notebooks"></a>Spark 2.0 notebookok

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)Ez a fájl tájékoztatást nyújt az adatok feltárásáról, modellezéséről és pontozásáról a Spark 2.0-fürtökben.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**Kimeneti**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Könyvtárak importálása
A szükséges könyvtárak importálása a következő kóddal:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Előre beállított Spark-környezet és PySpark-varázslatok
A Jupyter-jegyzetfüzetekhez mellékelt PySpark kernelek előre beállított környezettel rendelkeznek. Így nem kell explicit módon beállítani a Spark vagy a Hive környezeteket, mielőtt elkezdené a fejlődő alkalmazást. Ezek a környezetek alapértelmezés szerint elérhetők. Ezek a kontextusok a következők:

* sc - a Spark 
* sqlContext - a Hive

A PySpark kernel néhány előre definiált "varázslatot" biztosít, amelyek speciális parancsok, amelyeket a %% segítségével hívhat meg. A kódmintákban két ilyen parancs található.

* **%%helyi** Itt adhatja meg, hogy a következő sorokban lévő kódot helyileg kell végrehajtani. A kódnak érvényes Python-kódnak kell lennie.
* **%%sql -o \<változó név>** Hive-lekérdezést hajt végre az sqlContext-en. Ha az -o paraméter átlett adva, a lekérdezés eredménye pandas DataFrame-ként megmarad a %%local Python környezetben.

A Jupyter-jegyzetfüzetek kerneleiről és az általuk biztosított előre definiált "varázslatokról" a [HDInsight hdInsight-alapú Jupyter-jegyzetfüzetekhez elérhető kernelek](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)című témakörben talál további információt.

## <a name="data-ingestion-from-public-blob"></a>Adatok betöltése nyilvános blobból:
Az adatelemzési folyamat első lépése az elemzendő adatok betöltése olyan forrásokból, ahol az adatfeltárási és modellezési környezetbe kerül. Ez a környezet a Spark ebben a forgatókönyvben. Ez a szakasz a feladatok sorozatának elvégzéséhez szükséges kódot tartalmazza:

* a modellezendő adatminta betöltése
* olvasása a bemeneti adatkészletben (.tsv fájlként tárolva)
* formázza és tisztítsa meg az adatokat
* objektumok (RDD-k vagy adatkeretek) létrehozása és gyorsítótárazása a memóriában
* regisztrálja ideiglenes táblaként az SQL-környezetben.

Itt van az adatok betöltésének kódja.

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


**Kimeneti**

A cella feletti végrehajtáshoz szükséges idő: 276,62 másodperc

## <a name="data-exploration--visualization"></a>Adatok feltárása & vizualizáció
Miután az adatok a Sparkba kerültek, az adatelemzési folyamat következő lépése az adatok mélyebb megértése a feltárás és a vizualizáció révén. Ebben a szakaszban sql-lekérdezések segítségével vizsgáljuk meg a taxiadatokat, és ábrázoljuk a célváltozókat és a vizuális ellenőrzés lehetséges jellemzőit. Pontosabban, mi ábrázolják az utasok száma a taxi utak, a gyakorisága tipp összegeket, és hogyan tippek változnak a fizetési összeg és a típus.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Az utasszám-frekvenciák hisztogramjának rajzolása a taxiutak mintájában
Ez a kód és az azt követő kódrészletek az SQL magic használatával lekérdezik a mintát és a helyi mágiát az adatok ábrázolására.

* **SQL magic`%%sql`( )** A HDInsight PySpark kernel támogatja az sqlContext egyszerű inline HiveQL lekérdezéseket. Az (-o VARIABLE_NAME) argumentum a Jupyter-kiszolgálón pandadataframe-ként megőrzi az SQL-lekérdezés kimenetét. Ez azt jelenti, hogy helyi módban érhető el.
* A ** `%%local` mágia** a kód helyi futtatásához használható a Jupyter-kiszolgálón, amely a HDInsight-fürt csomópontja. Általában a mágia `%%local` használata `%%sql -o` után a mágia futtatásához használt lekérdezést. Az -o paraméter helyileg megmaradna az SQL-lekérdezés kimenetén. Ezután `%%local` a mágia elindítja a következő kódrészleteket, hogy helyileg fusson az SQL-lekérdezések helyileg megőrzött kimenetén. A kimenet automatikusan megjelenik a kód futtatása után.

Ez a lekérdezés az utazások utasszám szerint. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Ez a kód létrehoz egy helyi adatkeretet a lekérdezés kimenetéből, és megtervezi az adatokat. A `%%local` varázslat létrehoz egy helyi `sqlResults`adatkeretet, amely a matplotlib-mal való nyomtatáshoz használható. 

<!-- -->

> [!NOTE]
> Ezt a PySpark-varázslatot többször is használják ebben a forgatókönyvben. Ha az adatmennyiség nagy, a helyi memóriába illeszkedő adatkeret létrehozásához mintát kell venni.

<!-- -->

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Itt a kód, hogy ábrázolja az utazások az utasok száma

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

**Kimeneti**

![Az utazások gyakorisága utasszám szerint](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

A jegyzetfüzet **Típus** menügombjaival többféle képi megjelenítés közül választhat (Táblázat, Kör, Vonal, Terület vagy Sáv). A bár telek itt látható.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Ábrázolja a tippösszegek hisztogramját, valamint azt, hogy a tipp összege hogyan változik az utasok számától és a viteldíjaitól.
Sql-lekérdezéssel mintaadatokat..

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


Ez a kódcella az SQL-lekérdezést használja az adatok három nyomtatásának létrehozásához.

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


**Kimeneti:** 

![Tipp összegének eloszlása](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tipp összege utasszám szerint](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipp összege viteldíj szerint Összeg](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Jellemzőtervezés, átalakítás és adatelőkészítés modellezéshez
Ez a szakasz ismerteti és tartalmazza a pénzmosás modellezéséhez használt adatok előkészítéséhez használt eljárások kódját. Bemutatja, hogyan kell elvégezni a következő feladatokat:

* Új szolgáltatás létrehozása az órák forgalmi időtárolókra való particionálásával
* Index és on-hot kódolás kategorikus funkciók
* Címkézett pontobjektumok létrehozása a ml-függvényekbe való bevitelhez
* Az adatok véletlenszerű részmintavételezésének létrehozása és betanítási és tesztelési készletekre való felosztása
* Szolgáltatásskálázás
* Objektumok gyorsítótárazása a memóriában

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Új szolgáltatás létrehozása a forgalmi idők tárolókra történő particionálásával
Ez a kód bemutatja, hogyan hozhat létre egy új funkciót a forgalmi idők tárolókba történő particionálásával, majd az eredményül kapott adatkeret gyorsítótárazásával a memóriában. A gyorsítótárazás jobb végrehajtási időt eredményez, ahol rugalmas elosztott adatkészletek (RDDs) és adatkeretek többször is használatosak. Ezért a forgatókönyv több szakaszában gyorsítótárazjuk az RDD-ket és az adatkereteket.

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

**Kimeneti**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Index és egy forró kódolási kategorikus funkciók
Ez a szakasz bemutatja, hogyan indexelje vagy kódolja a modellezési függvények bevitt kategorikus jellemzőit. Az MLlib modellezési és előrejelzési funkciói megkövetelik, hogy a kategorikus bemeneti adatokkal rendelkező funkciókat használat előtt indexeljék vagy kódolják. 

A modelltől függően különböző módon kell indexelni vagy kódolni őket. Például a logisztikai és lineáris regressziós modellek egy gyors kódolást igényelnek, ahol például egy három kategóriás funkció három jellemzőoszlopra bontható, amelyek mindegyike 0-t vagy 1-et tartalmaz a megfigyelés kategóriájától függően. Az MLlib [onehotencoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkciót biztosít az egy forró kódoláshoz. Ez a kódoló egy címkeindex-oszlopot rendel egy bináris vektorokból álló oszlophoz, legbénákkal. Ez a kódolás lehetővé teszi, hogy a numerikus értékű funkciókat, például a logisztikai regressziót elvárható algoritmusokat alkalmazza a kategorikus funkciókra.

Itt van a kód index és kódolni kategorikus funkciók:

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


**Kimeneti**

A cella feletti végrehajtáshoz szükséges idő: 3,14 másodperc

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Címkézett pontobjektumok létrehozása a ml-függvényekbe való bevitelhez
Ez a szakasz olyan kódot tartalmaz, amely bemutatja, hogyan indexelje a kategorikus szöveges adatokat címkézett pontadattípusként, és hogyan kódolhatja azokat. Ez az átalakítás előkészíti a szöveges adatokat az MLlib logisztikai regresszió és más osztályozási modellek betanításához és teszteléséhez. A címkézett pontobjektumok rugalmas elosztott adatkészletek (RDD), amelyeket az MLlib legtöbb hibairányító algoritmusa bemeneti adatként szükséges módon formáz. A [címkézett pont](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) egy helyi vektor, akár sűrű, akár ritka, amely egy címkéhez/válaszhoz van társítva.

Itt van a kód index és kódolni szöveges funkciók bináris osztályozás.

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Ez a kód véletlenszerű mintavételt hoz létre az adatokból (itt 25% használatos). Bár ez nem szükséges ebben a példában az adatkészlet mérete miatt, bemutatjuk, hogyan lehet mintát az adatokitt. Akkor tudja, hogyan kell használni a saját problémáját, ha szükséges. Ha a minták nagyok, a mintavételezés jelentős időt takaríthat meg a betanítási modellek betanítása közben. Ezután a mintát egy betanítási részre (75% itt) és egy tesztrészre (25% itt) osztjuk, hogy a besorolási és regressziós modellezésben használjuk.

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

**Kimeneti**

A cella feletti végrehajtáshoz szükséges idő: 0,31 másodperc

### <a name="feature-scaling"></a>Szolgáltatásskálázás
A funkcióskálázás, más néven az adatok normalizálása biztosítja, hogy a széles körben kifizetett értékekkel rendelkező funkciók ne kapjanak túlzott súlyt az objektív funkcióban. A szolgáltatásméretezés kódja a [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) segítségével méretezi a funkciókat az egységvarianciájához. Az MLlib biztosítja a sztocaztikus gradiens (SGD) lineáris regresszióhoz való alkalmazásához. Az SGD egy népszerű algoritmus más gépi tanulási modellek széles körének betanításához, például a szabályos regressziók vagy a támogató vektorgépek (SVM) betanításához.   

> [!TIP]
> Úgy találtuk, hogy a LinearRegressionWithSGD algoritmus érzékeny a funkció méretezésére.   
> 
> 

Itt van a kód a változók méretezéséhez a szabályoslineáris SGD algoritmussal való használatra.

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

**Kimeneti**

A cella feletti végrehajtáshoz szükséges idő: 11,67 másodperc

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

**Kimeneti** 

A cella feletti végrehajtáshoz szükséges idő: 0,13 másodperc

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Tippeljen, hogy a tipp kifizetésre kerül-e bináris osztályozási modellekkel
Ez a szakasz azt mutatja be, hogyan használja a három modellt a bináris osztályozási feladat előrejelzésére, hogy egy tipp fizet-e egy taxi út. A bemutatott modellek a következők:

* Logisztikai regresszió 
* Véletlen erdő
* Színátmenetes kiemelési fák

Minden modellépület-kód szakasz lépésekre van osztva: 

1. **Modellbetanítási** adatok egy paraméterkészlettel
2. **Modellkiértékelése** mérőszámokkal rendelkező tesztadatkészleten
3. **Modell mentése** a blobban későbbi felhasználáshoz

Bemutatjuk, hogyan kell kereszt-ellenőrzés (CV) paraméter elsöprő két módon:

1. **Általános** egyéni kód használata, amely az MLlib bármely algoritmusára és az algoritmus bármely paraméterkészletére alkalmazható. 
2. A **pySpark CrossValidator folyamatfüggvény használata.** A CrossValidator néhány korlátozással rendelkezik a Spark 1.5.0-hoz: 
   
   * A folyamatmodellek nem menthetők vagy nem őrizhetők meg későbbi felhasználás esetén.
   * Nem használható a modell minden paraméterénél.
   * Nem használható minden MLlib algoritmushoz.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Általános keresztérvényesítés és hiperparaméter-söprés, amelyet a bináris osztályozási algoritmuslogisztikai regressziós algoritmusával használnak
Ebben a szakaszban a kód bemutatja, hogyan lehet betanítása, kiértékelése és mentése logisztikai regressziós modell [LBFGS,](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) amely előre jelzi, hogy egy tipp fizetik-e az utazás a NYC taxi út és a viteldíj adatkészlet. A modell betanítása keresztérvényesítés (CV) és a hyperparameter sweeping megvalósításával egyéni kód, amely az MLlib bármely tanulási algoritmusa alkalmazható.   

<!-- -->

> [!NOTE]
> Az egyéni önéletrajz-kód végrehajtása több percet is igénybe vehet.

<!-- -->

**A logisztikai regressziós modell betanítása CV és hyperparameter sweeping használatával**

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


**Kimeneti**

Együtthatók: [0,0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.0024764646947233, -0.00165897881503, 0.0675394837328, -0,111823113101, -0,32460912762, -0,204549780032, -1,36499216354, 0,591088507921, - 0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Elfogás: -0.0111216486893

A cella feletti végrehajtáshoz szükséges idő: 14,43 másodperc

**A bináris osztályozási modell kiértékelése szabványos mérőszámokkal**

Ebben a szakaszban a kód bemutatja, hogyan lehet kiértékelni egy logisztikai regressziós modellt egy tesztadatkészlettel szemben, beleértve a ROC-görbe ábrázolását is.

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


**Kimeneti**

A PR alatti terület = 0,985336538462

Roc alatti terület = 0,983383274312

Összefoglaló statisztika

Precizitás = 0,984174341679

Visszahívás = 0,984174341679

F1 pontszám = 0,984174341679

A cella feletti végrehajtáshoz szükséges idő: 2,67 másodperc

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


**Kimeneti**

![Logisztikai regressziós ROC görbe az általános megközelítéshez](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**A modell megőrzése egy blobban későbbi felhasználás esetén**

Ebben a szakaszban a kód bemutatja, hogyan mentheti a logisztikai regressziós modellt fogyasztásra.

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


**Kimeneti**

A cella feletti végrehajtáshoz szükséges idő: 34,57 másodperc

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Az MLlib CrossValidator folyamatfüggvényének használata logisztikai regressziós (rugalmas regressziós) modellel
Ebben a szakaszban a kód bemutatja, hogyan lehet betanítása, kiértékelése és mentése logisztikai regressziós modell [LBFGS,](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) amely előre jelzi, hogy egy tipp fizetik-e az utazás a NYC taxi út és a viteldíj adatkészlet. A modell betanítása keresztérvényesítés (CV) és a hyperparameter sweeping végre az MLlib CrossValidator csővezeték függvény CV paraméter sweep.   

<!-- -->

> [!NOTE]
> Ennek az MLlib CV-kódnak a végrehajtása több percet is igénybe vehet.

<!-- -->

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

**Kimeneti**

A cella feletti végrehajtáshoz szükséges idő: 107,98 másodperc

**Ábrázolja a ROC-görbét.**

Az *predictionAndLabelsDF* tábláként van regisztrálva, *tmp_results*az előző cellában. *tmp_results* lekérdezések és kimeneti eredmények az sqlResults adatkeretnyomtatáshoz. Itt a kód.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Itt van a roc görbe ábrázolásának kódja.

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


**Kimeneti**

![Logisztikai regressziós ROC-görbe az MLlib CrossValidator használatával](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Véletlen erdőosztályozás
Ebben a szakaszban a kód bemutatja, hogyan lehet beadni, kiértékelni és menteni egy véletlenszerű erdő regresszió, amely előre jelzi, hogy egy tipp fizetik-e az utazás a NYC taxi út és a viteldíj adatkészlet.

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


**Kimeneti**

Roc alatti terület = 0,985336538462

A cella feletti végrehajtáshoz szükséges idő: 26,72 másodperc

### <a name="gradient-boosting-trees-classification"></a>Gradiens kiemelési fák osztályozása
A kód ebben a szakaszban bemutatja, hogyan kell betanítása, kiértékelése, és mentse a gradiens kiemelési fák modell, amely előre jelzi, hogy egy tipp fizetik-e egy utazás a NYC taxi út és a viteldíj adatkészlet.

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

**Kimeneti**

Roc alatti terület = 0,985336538462

A cella feletti végrehajtáshoz szükséges idő: 28,13 másodperc

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Tippmennyiség előrejelzése regressziós modellekkel (önéletrajz használata nem)
Ez a szakasz bemutatja, hogyan használja a három modell a regressziós feladat: megjósolni a tipp összege fizetett egy taxi utazás alapján más tipp funkciók. A bemutatott modellek a következők:

* Szabályos lineáris regresszió
* Véletlen erdő
* Színátmenetes kiemelési fák

Ezeket a modelleket a bevezetőben ismertették. Minden modellépület-kód szakasz lépésekre van osztva: 

1. **Modellbetanítási** adatok egy paraméterkészlettel
2. **Modellkiértékelése** mérőszámokkal rendelkező tesztadatkészleten
3. **Modell mentése** a blobban későbbi felhasználáshoz   

<!-- -->

> [!NOTE] 
> A keresztérvényesítés nem használatos a szakaszban szereplő három regressziós modellel, mivel ez részletesen látható volt a logisztikai regressziós modelleknél. A témakör függeléke egy példa bemutatja, hogyan használhatja az önéletrajzot az Elastic Net lineáris regresszióhoz.

<!-- -->

<!-- -->

> [!NOTE] 
> Tapasztalataink szerint problémák merülhetnek fel a LinearRegressionWithSGD modellek konvergenciájával kapcsolatban, és a paramétereket gondosan kell módosítani/ optimalizálni egy érvényes modell megszerzéséhez. A változók skálázása jelentősen segíti a konvergenciát. A jelen témakör függelékében látható rugalmas hálóregresszió a LinearRegressionWithSGD helyett is használható.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Lineáris regresszió sgd-vel
Ebben a szakaszban a kód bemutatja, hogyan használhatja a skálázott funkciókat egy lineáris regresszió betanításához, amely sztochastikus gradiensesést (SGD) használ az optimalizáláshoz, és hogyan pontzthatja, értékelheti és mentheti a modellt az Azure Blob Storage (WASB) rendszerben.

> [!TIP]
> Tapasztalataink szerint problémák merülhetnek fel a LinearRegressionWithSGD modellek konvergenciájával kapcsolatban, és a paramétereket gondosan kell módosítani/ optimalizálni egy érvényes modell megszerzéséhez. A változók skálázása jelentősen segíti a konvergenciát.
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

**Kimeneti**

Együtthatók: [0,0141707753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092, -0.00456498588241, -0,0898228505177, 0,0714046248793, 0,102171263868, 0,100022455632, -0,0028954467449, 0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

Elfogás: 0.854507624459

RMSE = 1,23485131376

R-sqr = 0,597963951127

A cella feletti végrehajtáshoz szükséges idő: 38,62 másodperc

### <a name="random-forest-regression"></a>Véletlen erdő regresszió
Ebben a szakaszban a kód bemutatja, hogyan kell betanítása, kiértékelése és mentése egy véletlenszerű erdőmodell, amely előre jelzi a tipp összege a NYC taxi út adatok.   

<!-- -->

> [!NOTE]
> Az egyéni kód használatával történő keresztérvényesítés a paraméters söpréssel a függelékben található.

<!-- -->

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

**Kimeneti**

RMSE = 0,931981967875

R-sqr = 0,733445485802

A cella feletti végrehajtáshoz szükséges idő: 25,98 másodperc

### <a name="gradient-boosting-trees-regression"></a>Gradiens kiemelésfák regresszió
Ebben a szakaszban a kód bemutatja, hogyan kell beadni, kiértékelni és menteni egy gradiens kiemelési fák modell, amely előre jelzi a tipp összege a NYC taxi út adatok.

**Vonat és értékelés**

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


**Kimeneti**

RMSE = 0,928172197114

R-sqr = 0,732680354389

A cella feletti végrehajtáshoz szükséges idő: 20,9 másodperc

**Telek**

*tmp_results* hive táblaként van regisztrálva az előző cellában. A tábla eredményei kimenetele az *sqlResults* adatkeretnyomtatáshoz. Itt a kód

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Itt van a kód-hoz ábrázol az adatokat a Jupyter szerver.

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

![Tényleges és előre jelzett-tip-összegek](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Függelék: További regressziós feladatok keresztérvényesítéssel a paraméters söprésekkel
Ez a függelék tartalmazza a kódot, amely bemutatja, hogyan kell csinálni CV segítségével rugalmas net lineáris regressziós és hogyan kell csinálni CV paraméter sweep egyéni kód használatával véletlenszerű erdő regresszió.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Keresztérvényesítés rugalmas hálóval lineáris regresszióhoz
Ebben a szakaszban a kód bemutatja, hogyan lehet keresztérvényesítés rugalmas háló lineáris regresszió, és hogyan kell kiértékelni a modell ta- teszt adatok.

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


**Kimeneti**

A cella feletti végrehajtáshoz szükséges idő: 161,21 másodperc

**Értékelés R-SQR metrikával**

*tmp_results* hive táblaként van regisztrálva az előző cellában. A tábla eredményei kimenetele az *sqlResults* adatkeretnyomtatáshoz. Itt a kód

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Itt van az R-sqr kiszámításának kódja.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**Kimeneti**

R-sqr = 0,619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Keresztellenőrzés a paraméteres söpréssel a véletlenszerű erdőregresszió egyéni kódjával
Ebben a szakaszban a kód bemutatja, hogyan lehet keresztellenőrzést végezni a paraméteres söpréssel a véletlenszerű erdőregresszió egyéni kódhasználatával, és hogyan értékelheti ki a modellt a tesztadatok alapján.

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


**Kimeneti**

RMSE = 0,906972198262

R-sqr = 0,740751197012

A cella feletti végrehajtáshoz szükséges idő: 69,17 másodperc

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Objektumok karbantartása a memóriából és a nyomtatási modell helyéről
A `unpersist()` memóriában gyorsítótárazott objektumok törlésére szolgál.

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


**Kimeneti**

PythonRDD[122] az RDD-nél a PythonRDD.scala oldalon: 43

**Kimeneti elérési út a felhasználási jegyzetfüzetben használandó modellfájlokhoz. ** Független adatkészlet felhasználásához és pontozásához másolja és illessze be ezeket a fájlneveket a "Felhasználás jegyzetfüzetbe".

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**Kimeneti**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>A következő lépések
Most, hogy regressziós és besorolási modelleket hozott létre a Spark MlLib-el, készen áll arra, hogy megtanulja, hogyan pontzthatja és értékelheti ezeket a modelleket.

**Modell felhasználása:** A témakörben létrehozott besorolási és regressziós modellek pontozásáról és kiértékeléséről a [Pontszám és a Spark által készített gépi tanulási modellek kiértékelése](spark-model-consumption.md)című témakörben olvashat.

