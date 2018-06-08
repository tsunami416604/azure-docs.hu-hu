---
title: Adattudomány Scala és Spark használata az Azure-on |} Microsoft Docs
description: Hogyan használható a Scala a felügyelt gépi tanulási feladatok a Spark méretezhető MLlib és Spark ML-csomagba egy Azure HDInsight Spark-fürtön.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: a7c97153-583e-48fe-b301-365123db3780
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 16e4af4dd7f5c2bd14d70cc28225dfc750ce3bea
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838510"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Adatelemzés a Scala és a Spark használatával az Azure rendszerben
Ez a cikk bemutatja, hogyan Scala felügyelt gépi tanulási feladatok a Spark méretezhető MLlib és Spark ML-csomagba egy Azure HDInsight Spark-fürt használatára. Az végigvezeti a feladatok alkotó a [Adattudomány folyamat](http://aka.ms/datascienceprocess): adatfeldolgozást és a feltárása, a képi megjelenítés, a szolgáltatás mérnöki csapathoz, a modellezési és a model felhasználás. A cikkben szereplő modellek között logisztikai és lineáris regressziós, véletlenszerű erdők és színátmenetes súlyozott fák (GBTs) két általános felügyelt gépi tanulási feladatok mellett:

* Regressziós probléma: előrejelzését taxi útnak tipp összeg (SPN)
* Bináris osztályozás: előrejelzését tipp vagy taxi útnak nincs ötlet (1 vagy 0)

A modellezési folyamatban betanítása és kiértékelése egy teszt adatkészlet és a megfelelő pontossága metrikák igényel. Ebből a cikkből megtudhatja, ezek a modellek tárolása az Azure Blob storage és pontszám és értékelje a prediktív teljesítményét. Ez a cikk is magában foglalja a modellek optimalizálása a kereszt-ellenőrzési és a hyper-paraméter abszolút az összetettebb témákra. A használt adatok látható egy minta 2013 NYC taxi út és a jegy ára adatkészlet elérhető a Githubon.

[Scala](http://www.scala-lang.org/), a Java virtuális gépen alapuló nyelven integrálja az objektumorientált és funkcionális nyelvi fogalmak. Egy méretezhető nyelv, amely kiválóan alkalmas a felhőalapú elosztott feldolgozási, és az Azure Spark-fürtjei fut is.

[Spark](http://spark.apache.org/) nyílt forráskódú párhuzamos feldolgozási keretrendszere, amely támogatja a memórián belüli feldolgozást a big data elemzés-alkalmazások teljesítményének növelése érdekében. A Spark program sebességét, a könnyű, valamint a kifinomult analytics lett tervezve. A Spark memóriában elosztott tárolt számítási képességei jól funkcionálnak a iteratív algoritmusaival a machine learning és a graph számítások. A [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) csomag egységes teszi lehetővé a magas szintű API-k adatokat, melyek segíthetnek keretek létrehozása és gyakorlati gépi tanulási a folyamatok hangolására platformra épül. [MLlib](http://spark.apache.org/mllib/) a Spark méretezhető gépi tanulás függvénytár, amely modellezési képességekkel ez elosztott környezet számára.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) nyílt forráskódú Spark az Azure által üzemeltetett elérhető van. Emellett támogatja a Jupyter Scala notebookok Spark-fürt, és futtathatja a Spark SQL interaktív lekérdezések átalakító, szűrésére és Azure Blob storage-ban tárolt adatok megjelenítése. Scala kódrészletek ebben a cikkben, amely a megoldásokat és megjelenítése a megfelelő előkészítésére adatok megjelenítéséhez futtassa a Jupyter notebookok a Spark-fürtjei telepítve. Ezek a témakörök modellezési lépéseiben kódot, amely azt ismerteti, hogyan betanítása, értékelje ki, mentse és modell különböző típusú felhasználását.

A beállítási lépéseket, illetve a cikkben az Azure HDInsight 3.4 Spark 1.6 vannak. Azonban a cikkben, majd a kódot a [Scala Jupyter Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) általánosak, és a Spark-fürt működnek. A fürt beállítása és felügyelete lépésekre mi is megjelennek ebben a cikkben nem használata a HDInsight Spark kis mértékben eltérő lehet.

> [!NOTE]
> Ez a témakör azt ismerteti, hogyan használja a Scala helyett a Python egy végpontok közötti Adattudomány folyamat feladatok végrehajtásához, lásd: [Spark on Azure HDInsight használatának Adattudomány](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
* Rendelkeznie kell egy Azure-előfizetéssel. Ha még nem rendelkezik egy, [egy Azure ingyenes próbaverzió beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Szüksége van az Azure HDInsight 3.4 Spark 1.6-os-fürt a következő műveletek végrehajtásához. A fürt létrehozásához lásd: utasításait [első lépések: hozzon létre Apache Spark on Azure Hdinsighttal](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). A fürt típusa és verzió beállítása a **fürt típusának kiválasztása** menü.

![A HDInsight fürt típus konfigurálása](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

A NYC taxi út adatok és a kód végrehajtása a Spark-fürt Jupyter notebook módjáról, olvassa el megfelelő [áttekintése adatok tudományos Spark on Azure HDInsight használatának](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Futtassa a Scala-kódot a Spark-fürt Jupyter notebook
Azure-portálról Jupyter notebook indíthatja el. A Spark-fürt az irányítópulton található, majd kattintson a felügyelet lapon adja meg a fürt. Ezután kattintson **fürt irányítópultok**, és kattintson a **Jupyter Notebook** a Spark-fürt társított jegyzetfüzet megnyitása.

![Fürt-irányítópult és a Jupyter notebookok](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Jupyter notebookok: https:// is hozzáférhet&lt;clustername&gt;.azurehdinsight.net/jupyter. Cserélje le *clustername* a fürt nevét. Szüksége van a Jupyter notebookok eléréséhez a rendszergazdai fiók jelszavát.

![Ugrás a Jupyter notebookok a fürt neve](./media/scala-walkthrough/spark-jupyter-notebook.png)

Válassza ki **Scala** néhány példa a PySpark API-t használó előre csomagolt jegyzetfüzetek megegyező nevű könyvtárat megjelenítéséhez. A feltárási modellezéséhez és a kódot tartalmazó Scala.ipynb notebook használatával pontozási minták a Spark témakörök csomagban érhető el a [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

A Jupyter Notebook kiszolgálóhoz közvetlenül a Githubból a notebook feltöltheti a Spark-fürtön. A Jupyter kezdőlapján kattintson a **feltöltése** gombra. A Fájlkezelőben, illessze be a Scala notebook GitHub (nyers tartalom) URL-CÍMÉT, és kattintson a **nyitott**. A Scala notebook a következő URL-címen érhető el:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Telepítő: Előre definiált Spark és Hive-környezetek, Spark magics és Spark-függvénytárak
### <a name="preset-spark-and-hive-contexts"></a>Spark és Hive-környezetek az adott néven beállítás
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


A Spark mag Jupyter notebookok által biztosított rendelkezik előre beállított környezeteket. Nem kell explicit módon állítsa be a Spark, vagy az alkalmazás használata előtt Hive-környezeteket fejleszt. Az előre definiált környezetekben a következők:

* `sc` a SparkContext
* `sqlContext` a HiveContext

### <a name="spark-magics"></a>Spark magics
A Spark kernel tartalmaz néhány előre definiált "magics", amelyeket meghívhatja a különleges parancsok `%%`. Ezek a parancsok közül kettő a következő mintakódok használnak.

* `%%local` Meghatározza, hogy a kód egymás utáni sorok végrehajtja helyileg. A kód érvényes Scala-kódot kell lennie.
* `%%sql -o <variable name>` végrehajtja a Hive-lekérdezések elleni `sqlContext`. Ha a `-o` paramétert, a lekérdezés eredménye őrzi a `%%local` adatok keretként Spark Scala környezetben.

A Jupyter notebookokból és az előre definiált kernelek kapcsolatos további információk "magics", amely meghívja a `%%` (például `%%local`), lásd: [HDInsight Spark Linux és a Jupyter notebookok elérhető kernelek a HDInsight-fürtök](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Könyvtárak importálása
A Spark, MLlib és egyéb szalagtárak, az alábbi kód használatával kell importálni.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Adatfeldolgozás
Az első lépés az adatok tudományos folyamatban az elemezni kívánt adatok. Az adatok feltárása és modellezési környezetbe külső forrásból vagy rendszerek, ahol van hozni az adatokat. Ebben a cikkben az adatokat, akkor a betöltési egy tartományhoz 0,1 % példa a taxi út és a jegy ára fájl (.tsv fájlként tárolja). Az adatok feltárása és modellezési környezet egy Spark. Ez a szakasz a kódot, és végezze el az alábbi lépéseket:

1. Adatok és a modell tárolás a könyvtár elérési útvonalak beállítása.
2. Olvassa el a következő bemeneti adatkészletben (.tsv fájlként tárolja).
3. Definiálhat egy sémát az adatok, és az adatok eltávolításáig.
4. Megtisztított adatok keret létrehozása, és a gyorsítótár, a memória.
5. Az adatok regisztrálható az SQLContext ideiglenes táblájába.
6. A tábla lekérdezése, és importálja az eredményeket az adatok keret.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Az Azure Blob storage tárolási helyek könyvtár elérési útja beállítása
Spark olvashatják és írni az Azure Blob Storage tárolóban. Spark használatával egy olyan meglévő adatait, és ezután az eredmények tárolásához újra a Blob Storage tárolóban.

A Blob Storage tárolóban modellek vagy a fájlok mentéséhez szeretné megfelelően adja meg az elérési útját. Az alapértelmezett tároló, a Spark-fürt csatolva kezdődő elérési úttal hivatkozhat `wasb:///`. Egyéb helyek hivatkozni használatával `wasb://`.

A következő példakód a modell szeretné menteni a bemeneti adatok olvasását és elérési útját, amely csatolva van a Spark-fürt Blob-tároló helyét adja meg.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Adatok importálása, hozzon létre egy RDD és egy, a séma szerint adatok keret
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**A kimenetre:**

A cella futási időnek: 8 másodperc.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>A tábla lekérdezési és adatok keretben eredmények importálása
A következő lekérdezés a tábla a jegy ára, utas és tipp adatok; szűrő sérült, és a külső adatai; és nyomtathatják ki több sort.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**A kimenetre:**

| fare_amount | passenger_count | tip_amount | Formabontó |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Az adatok feltárása és -megjelenítésre
Miután az adatok beolvasása a Spark, a Adattudomány folyamat következő lépése ahhoz, hogy az adatok feltárása és a képi megjelenítés bemutatják. Ebben a szakaszban a taxi adatokat az SQL-lekérdezések használatával ellenőrizze. Ezután importálja az eredményeket a cél változók és a visual hálózatfelügyeleti potenciális funkcióit megrajzolásához Jupyter automatikus-képi megjelenítés funkciójával adatok keretbe.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Használja a helyi és SQL magic adatok ábrázolása
Alapértelmezés szerint minden kódrészletet, amely futtatja a Jupyter notebook eredménye a munkamenet fennállásának a feldolgozó csomópontokon keretén belül érhető el. Ha a munkavégző csomópontokhoz minden számításhoz utazás menti, és a számítási szükséges összes adatot a helyi kiszolgáló-csomóponton a Jupyter (amely az átjárócsomópont) érhető el, ha a `%%local` magic futtatását a kódrészletet a Jupyter kiszolgálón.

* **SQL magic** (`%%sql`). A HDInsight Spark kernel az SQLContext könnyen beágyazott HiveQL lekérdezéseket támogatja. A (`-o VARIABLE_NAME`) argumentum az SQL-lekérdezés kimenetét a Jupyter kiszolgálón Pandas adatok keretként továbbra is fennáll. Ez azt jelenti, hogy a helyi módban érhető el lesz.
* `%%local` **magic**. A `%%local` magic a kód futtatása helyben a Jupyter kiszolgálón, amely a HDInsight-fürt átjárócsomópontjához. Általában akkor használják `%%local` magic együtt a `%%sql` rendelkező magic a `-o` paraméter. A `-o` paraméter szeretné megőrizni a helyileg, az SQL-lekérdezés kimenete, majd `%%local` magic indítsa el a következő készlete futtatásához helyi SQL-lekérdezések eredményének helyileg fennállásának kódrészletet.

### <a name="query-the-data-by-using-sql"></a>A lekérdezést SQL használatával
Ez a lekérdezés lekéri a jegy ára, utas száma, és tipp által taxi való adatváltások számát.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

A következő kódrészlet a `%%local` magic létrehozza a helyi adatok, sqlResults. Használhatja a sqlResults megrajzolásához matplotlib használatával.

> [!TIP]
> Helyi magic ebben a cikkben több alkalommal van használva. Ha az adatkészlet túl nagy, adjon minta lehet adatok keret létrehozásához a helyi memória.
> 
> 

### <a name="plot-the-data"></a>Az adatok ábrázolása
Dolgozunk, Python kód használatával, miután az adatok keret Pandas adatok keretként helyi környezetben is.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 A Spark kernel automatikusan visualizes (HiveQL) az SQL-lekérdezések eredményének, a kód futtatása után. Számos különböző típusú megjelenítések közül választhat:

* Tábla
* Torta
* Vonal
* Terület
* Sáv

Az adatok ábrázolása a kód itt látható:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**A kimenetre:**

![Tipp összeg hisztogram](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tipp összeg utas száma szerint](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tipp jegy ára mennyiséggel összeg](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Funkciók létrehozása és átalakítási szolgáltatások és funkciók modellezési a bemeneti adatok majd előkészítése
Fa-alapú modellezési funkciók Spark ML és MLlib meg kell készítse elő a cél- és a szolgáltatások módszerek, például a dobozolás indexelő, egy közbeni kódolás vagy vectorization használatával. Ebben a szakaszban szereplő eljárások a következők:

1. Hozzon létre egy új szolgáltatás által **dobozolás** üzemideje (óra) a forgalom gyűjtők idő.
2. Alkalmazása **indexelő és egy közbeni kódolás** kategorikus funkcióhoz.
3. **Minta és az adatkészlet vágási** tanítási és tesztelési törtek be.
4. **Adja meg a képzés változó és a szolgáltatások**, és majd létre indexelt vagy egy közbeni kódolású betanítása és rugalmas bemeneti címkézett pont tesztelés elosztott adatkészletek (RDDs) vagy adatkeretek.
5. Automatikusan **kategorizálását és szolgáltatásait és célok vectorize** a machine learning modellek bemeneteként használja.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Hozzon létre egy új szolgáltatás a forgalom idő gyűjtők dobozolás óránként
Ez a kód bemutatja, hogyan hozhat létre egy új szolgáltatás a dobozolás üzemideje (óra) a forgalom idő gyűjtők és az eredményül kapott adatok keret memóriában gyorsítótárazásának. Ha RDDs és az adatok keretek használt ismételten, gyorsítótárazás továbbfejlesztett végrehajtásának lassúságát vezet. Ennek megfelelően RDDs és adatkeretek lesz gyorsítótárazza a következő eljárások több szakaszában.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexelő és egy közbeni kódolás kategorikus funkciók
A modellezési és előrejelzése MLlib feladatai szükséges szolgáltatások kategorikus indexelt vagy használata előtt kódolású bemeneti adatokkal. Ez a szakasz bemutatja, hogyan index, vagy a modellezési függvényekké bemeneti kategorikus szolgáltatások kódolása.

Index, vagy a modellek kódolja a modelltől függően különböző módon kell. Logisztikai és lineáris regressziós modellt megkövetelni például, egy közbeni kódolást. Például három kategóriába szolgáltatás három funkció oszlopokba bővíthetők. Egyes oszlopok tartalmazott volna, 0 vagy 1 attól függően, hogy egy kategóriáját. MLlib biztosít a [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) egy közbeni kódolási funkció. A kódoló címke indexek oszlop bináris vektorok értékű legfeljebb egyetlen egy-egy oszlop rendeli hozzá. A kódolás numerikus fontos funkciók, például logisztikai regresszió várt algoritmusokat kategorikus szolgáltatások alkalmazhatók.

Itt alakítsa át csak négy változókat a példák, amelyek karakterláncok megjelenítése. Más változók, például a hét napja, numerikus érték, mint kategorikus változók által képviselt is indexelheti.

Az indexelő, használja `StringIndexer()`, és egy közbeni kódolását, használja a `OneHotEncoder()` MLlib funkciókat. Index és kategorikus szolgáltatások kódolja a kód itt látható:

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**A kimenetre:**

A cella futási időnek: 4 másodperc.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>A minta és az felosztása tanítási és tesztelési törtek az adatkészlet
Ez a kód hoz létre egy véletlenszerű mintavétel az adatok (25 %, ebben a példában). Mintavételi, de nem szükséges ehhez a példához a készlet mérete miatt a cikk bemutatja, hogyan lehet mintát a megállapításához, hogy szükség esetén a saját problémák használatával. Nagy minták esetén ez jelentős időt takaríthat meg a modellek betanítása közben. Ezután ossza fel a minta egy képzési (ebben a példában a 75 %) és egy tesztelési részét (25 %, ebben a példában) besorolás és regressziós modellezéshez hatékony használata.

Egy véletlenszerű szám (0 és 1) között hozzá minden egyes sorára, amelyek segítségével válassza ki a kereszt-ellenőrzési modellrészt betanítás során (a "VÉL" oszlop).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**A kimenetre:**

A cella futási időnek: 2 másodperc.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Adja meg a képzés változó és a szolgáltatások, és hozza létre indexelt vagy egy közbeni kódolású a modell betanítására és tesztelésére bemeneti pont RDDs vagy adatok keretek címkével
Ez a szakasz azt ismerteti, hogyan index kategorikus szöveges adatok címkézett pont adattípusú értékként, és, hogy használhassa képzése és MLlib logisztikai regresszió és egyéb besorolási modell teszteléséhez kódolása kódját tartalmazza. Címkézett pont objektum RDDs formázott oly módon, hogy a legtöbb gépi tanulási algoritmusok MLlib a bemeneti adatként van szükség. A [pont feliratú](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) egy helyi vektor, sűrű vagy ritka, társítva van egy címke-válasz.

Ezt a kódot kell megadni a célváltozó (függő) és a modell betanításához használandó funkciókat. Ezután létrehoz indexelt vagy egy közbeni kódolású a modell betanítására és tesztelésére bemeneti pont RDDs vagy adatok keretek címkével.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**A kimenetre:**

A cella futási időnek: 4 másodperc.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatikusan kategorizálását és szolgáltatásait és a gépi tanulási modellek bemeneteként használandó célok vectorize
Spark ML segítségével kategorizálása a cél és a szolgáltatások fa-alapú modellezési funkciók használatára. A kód két feladatokat hajtja végre:

* Egy bináris osztályozás cél értéke csak 0 vagy 1 hozzárendelése minden adatpontnál 0 és 1 közötti egy küszöbértéket 0,5 használatával hoz létre.
* Automatikusan kategorizálja szolgáltatásokat. Ha az összes olyan szolgáltatás különböző numerikus értékek száma kisebb, mint 32, adott szolgáltatás kategorizálta.

A két feladatokhoz a kód itt látható.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Bináris osztályozási modell: előre jelezni, hogy kell fordítani tipp:
Ebben a szakaszban háromféle előre jelezni, hogy tipp kell fordítani a bináris osztályozási modellek létrehozása:

* A **logisztikai regresszió modell** a Spark ML használatával `LogisticRegression()` függvény
* A **véletlenszerű erdő besorolási modell** a Spark ML használatával `RandomForestClassifier()` függvény
* A **átmenetes kiemelési fa besorolási modell** a MLlib használatával `GradientBoostedTrees()` függvény

### <a name="create-a-logistic-regression-model"></a>Logisztikai regressziós modell létrehozása
Ezután hozzon létre egy logisztikai regresszió modell használatával a Spark ML `LogisticRegression()` függvény. A lépések egy sorozatát kód létrehozása a modell létrehozása

1. **A modell betanításához** adatok egy paraméter-készlettel.
2. **A modell kiértékelése** a metrikák a teszt adatkészlet.
3. **Mentse a modellt** Blob Storage a jövőbeni felhasználásához.
4. **A modell pontozása** vizsgálati adatok alapján.
5. **Az eredmények ábrázolhatók** a jellemző (ROC) görbék működő fogadó.

Ezeket az eljárásokat a kód itt látható:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Betöltése, pontozása és a-eredményeket menteni.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**A kimenetre:**

A tesztadatokat ROC = 0.9827381497557599

Helyi Pandas adatkeretek: ROC-görbe megrajzolásához Python használatát.

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
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


**A kimenetre:**

![Tipp vagy nincs tipp: ROC-görbe](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Hozzon létre egy véletlenszerű besorolás erdőmodell
Ezután hozzon létre egy véletlenszerű erdő besorolási modell használatával a Spark ML `RandomForestClassifier()` működik, és értékelje ki a modell a tesztadatokat.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**A kimenetre:**

A tesztadatokat ROC = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>GBT besorolási modell létrehozása
Ezután hozzon létre egy GBT besorolási modell MLlib tartozó használatával `GradientBoostedTrees()` működik, és értékelje ki a modell a tesztadatokat.

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**A kimenetre:**

: ROC-görbe alatt: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regressziós modell: tipp összeg előrejelzése
Ebben a szakaszban hozzon létre két típusú regressziós modell előre jelezni a tipp összeg:

* A **rendeződik lineáris regressziós modell** a Spark ML használatával `LinearRegression()` függvény. Mentse a modellt fogjuk, és értékelje ki a modell a tesztadatokat.
* A **színátmenetes kiemelése fa regressziós modell** a Spark ML használatával `GBTRegressor()` függvény.

### <a name="create-a-regularized-linear-regression-model"></a>Rendeződik lineáris regressziós modell létrehozása
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**A kimenetre:**

A cella futási időnek: 13 másodperc.

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**A kimenetre:**

R-sqr a tesztadatokat = 0.5960320470835743

Ezután a vizsgálati eredmények lekérdezési adatok keretként, és megjelenítése az AutoVizWidget és matplotlib használatával.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

A kód létrehozza a helyi adatok a lekérdezés kimenetét, és az adatok tevékenységtérkép. A `%%local` magic létrehoz egy helyi adatok keret `sqlResults`, amelynek megrajzolásához matplotlib a segítségével.

> [!NOTE]
> A Spark magic ebben a cikkben több alkalommal van használva. Nagy adatmennyiség esetén a kell minta lehet adatok keret létrehozásához a helyi memóriához.
> 
> 

Hozzon létre előkészítésére Python matplotlib.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**A kimenetre:**

![Tipp összeg: tényleges és előre jelzett](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>GBT regressziós modell létrehozása
A Spark ML használatával GBT regressziós modell létrehozása `GBTRegressor()` működik, és értékelje ki a modell a tesztadatokat.

[Fák színátmenetes súlyozott](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) együttes döntési fák. GBTs ismételt adatvesztés függvény minimalizálása érdekében a döntési fák betanítása. Használhat GBTs regressziós és besorolás. Azok kezelni tud a kategorikus szolgáltatásokat, nincs szükség a méretezés funkció és nonlinearities és a szolgáltatás kapcsolati. Is használhatja őket a multiclass-besorolás beállításban.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**A kimenetre:**

Teszt R-sqr van: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Speciális modellezési segédprogramok energiaoptimalizálás
Ebben a szakaszban használhatja a machine learning segédprogramok használó fejlesztők gyakran modell optimalizálásra vonatkozóan. Pontosabban optimalizálhatja a machine learning modellek három különböző módon paraméter abszolút és kereszt-ellenőrzési használatával:

* Az adatok felosztása tanítási és érvényesítési beállítása, a modell használatával hyper paraméter abszolút egy gyakorlókészlethez optimalizálása és értékelődik ki az érvényesítési készletének (lineáris regresszió)
* A modell optimalizálhatja a kereszt-ellenőrzési és hyper paraméter abszolút Spark ML CrossValidator függvény (bináris osztályozás) használatával.
* A modell optimalizálhatja a gépi tanulási funkció és paraméter beállítása (lineáris regresszió) használatához egyéni kereszt-ellenőrzési és paraméter abszolút kód használatával

**Kereszt-ellenőrzési** egy technika, amely értékeli az milyen mértékben lesz generalize az egy modell betanítása adatokat egy ismert csoportján részeit, amelyen az még nincs betanítva adatkészletek előre jelezni. Az általános ismereteket, ezzel a technikával mögött, hogy a modell betanítása ismert adatok a megfelelő adatokat, és az előrejelzés pontosságát tesztelik egy független adatkészlet ellen. A közös megvalósítása-felosztása az adatkészlet *k*-modellrészt, és majd a ciklikus multiplexeléssel egy, a modellrészt a modell betanításához.

**Hyper-paraméter optimalizálási** tanulási algoritmus a hyper-paraméterek készletét általában azzal a céllal, az algoritmus egy független adatkészlet teljesítményének biztosítása optimalizálása kiválasztása a problémát. A hyper-paraméter értéke meg kell adnia a modell betanítási eljárás kívül. A hyper-paraméterértékek feltételezéseket hatással lehet a rugalmasság és a modell pontosságát. Döntési fák algoritmus hyper-paraméterek, például a kívánt mélysége és a fában levelek például rendelkezik. Meg kell adni egy téves besorolás szövegminősítési kifejezés támogatási vektoros gépek (SVM).

A közös hyper paraméter optimalizálás végrehajtására módja a rács keresés, más néven a **paraméter ismétlés**. A rács keresés a részletes keresést egy adott részének tanulási algoritmus a hyper-paraméterrel adhatja értékek keresztül történik. Kereszt-ellenőrzési megadhatja az optimális eredmények elérése érdekében a rács keresési algoritmus által előállított korlátoznia a teljesítmény metrikát. Kereszt-ellenőrzési hyper paraméter abszolút használatakor korlátot problémák, például egy modell betanítási adatok overfitting segítségével. Ezzel a módszerrel a modell megőrzi a kapacitás az általános adatkészletet, amelyből a betanítási adatok kibontotta alkalmazandó.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>A hyper-paraméter abszolút egy lineáris regressziós modellt optimalizálása
A következő adatok felosztása tanítási és érvényesítési beállítása, használja a hyper-paraméter abszolút képzési megfelelő optimalizálja a modellt, és értékelődik ki az érvényesítési készletének (lineáris regresszió).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**A kimenetre:**

Teszt R-sqr van: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>A bináris osztályozási modell optimalizálhatja a kereszt-ellenőrzési és a hyper-paraméter abszolút használatával
Ez a szakasz bemutatja, hogyan optimalizálható a bináris osztályozási modell kereszt-ellenőrzési és a hyper-paraméter abszolút használatával. Használja a Spark ML `CrossValidator` függvény.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**A kimenetre:**

A cella futási időnek: 33 másodperc.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>A lineáris regressziós modell optimalizálhatja a kereszt-ellenőrzési és paraméter abszolút egyéni kód használatával
A következő egyéni kód használatával optimalizálhatja a modell, és a legjobb Modellparaméterek azonosíthatja a legmagasabb pontossági feltétel. Ezután hozzon létre a végső modell, a modell a tesztadatokat értékelje ki és mentse a modellt a Blob Storage tárolóban. Végezetül betölteni a modellt, vizsgálati adatok pontozása és értékeléséhez pontosságát.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**A kimenetre:**

A cella futási időnek: 61 másodperc.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Spark-beépített machine learning modellek automatikusan Scala felhasználása
Témakörök, amelyek végigvezetik a feladatokat az Azure-ban a Adattudomány folyamat alkotó áttekintését lásd: [Team adatok tudományos folyamat](http://aka.ms/datascienceprocess).

[Vonja össze az adatokat tudományos folyamat forgatókönyvek](walkthroughs.md) más végpont forgatókönyvek, amelyek bemutatják, meghatározott forgatókönyvek esetén az Team tudományos folyamat lépéseit ismerteti. A forgatókönyvek is bemutatják, hogyan lehet a felhő- és a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása.

[Spark-beépített gépi tanulási modell pontozása](spark-model-consumption.md) bemutatja, hogyan Scala kód segítségével automatikusan betölteni, és új adatkészletek pontozása gépi tanulási modellek Spark a beépített és az Azure Blob storage mentve. Nincs megadott utasításokat, és egyszerűen cserélje le a Python kódját Scala kódra ebben a cikkben a automatizált felhasználásához.

