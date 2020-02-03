---
title: Adatelemzés a Scala és Spark használata az Azure-on – a csoportos adatelemzési folyamat
description: Hogyan Scala használható a Spark méretezhető MLlib és a Spark ML-csomagokat az Azure HDInsight Spark-fürtön a felügyelt gépi tanulási feladatok.
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
ms.openlocfilehash: b36a3faab49ee8d51c25aa18879e6f5d1db8c2fb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716761"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Adatelemzés a Scala és a Spark használatával az Azure rendszerben
Ez a cikk bemutatja, hogyan Scala használata a Spark méretezhető MLlib és a Spark ML-csomagokat az Azure HDInsight Spark-fürtön a felügyelt gépi tanulási feladatok. Végigvezeti az [adatelemzési folyamatot](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)alkotó feladatokon: az adatok betöltését és feltárását, a vizualizációt, a szolgáltatások mérnöki használatát, a modellezést és a modell felhasználását. A cikk a modellek között logisztikai és lineáris regresszió, véletlenszerű erdők és színátmenet súlyozott fákat (GBTs), két általános felügyelt gépi tanulási feladatok mellett:

* Regressziós problémaként: előrejelzés taxi útnak tipp összeg ($)
* Bináris osztályozás: előrejelzés tipp vagy taxi útnak nincs tip (1/0)

A modellezési folyamat szükséges betanítása és kiértékelése egy teszt adatkészlet, és pontossága vonatkozó metrikákat. Ebből a cikkből tudhat meg ezek a modellek tárolása az Azure Blob storage-ban és a pontszám, és a prediktív teljesítmény kiértékelése. Ez a cikk emellett ismerteti, hogyan optimalizálható a modellek kereszt-ellenőrzési és a hyper-paraméter kezdik használatával összetettebb témákra. Az adatok, használja a 2013 NYC taxi utazást és diszkont adatkészlet a Githubon elérhető mintát.

A [Scala](https://www.scala-lang.org/), a Java virtuális gépen alapuló nyelv, integrálja az objektumorientált és funkcionális nyelvi fogalmakat. Egy méretezhető nyelv, amely kiválóan alkalmas elosztott feldolgozás a felhőben, és futtat az Azure-alapú Spark-fürtök.

A [Spark](https://spark.apache.org/) egy nyílt forráskódú, párhuzamos feldolgozást végző keretrendszer, amely támogatja a memórián belüli feldolgozást Big Data elemzési alkalmazások teljesítményének növelése érdekében. A Spark feldolgozási motorjára a nagy sebesség, a könnyű használat és a kifinomult elemzési. Spark memóriabeli elosztott számítási képességeket adja meg a megfelelő választás az iteratív algoritmusaival együtt a machine learning és a graph számítások. A [Spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) csomag olyan, az adatkockákra épülő magas szintű API-k egységes készletét biztosítja, amelyek segítségével gyakorlati gépi tanulási folyamatokat hozhat létre és hangolhat össze. A [MLlib](https://spark.apache.org/mllib/) a Spark skálázható gépi tanulási könyvtára, amely modellezési képességeket biztosít az elosztott környezet számára.

A [HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) a nyílt forráskódú Spark Azure által üzemeltetett ajánlata. Emellett támogatja a Jupyter Scala notebookok a Spark-fürtön, és futtathatja a Spark SQL interaktív lekérdezések átalakítása, szűrését és az Azure Blob storage szolgáltatásban tárolt adatok megjelenítése. Scala kódrészletek ebben a cikkben, amelyek a megoldásokat, és megjelenítheti az adatokat a megfelelő grafikon megjelenítése futtatása a Jupyter notebooks, a Spark-fürtökön telepített. Ezek a témakörök modellezési lépéseiben kódot, amely bemutatja, hogyan betanításához, kiértékelése, mentése és felhasználását a modell különböző típusú.

A beállítási lépéseket, és ebben a cikkben kód kapacitások Azure HDInsight 3.4-es Spark 1.6-os. Azonban a jelen cikkben és a [Scala Jupyter Notebookban](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) szereplő kód általános, és minden Spark-fürtön működnie kell. Lehet, hogy a fürt beállítása és kezelése a lépések kissé eltérő, az alábbiakhoz képest ez a cikk a HDInsight Spark nem használata.

> [!NOTE]
> Egy olyan témakörben, amely bemutatja, hogyan használhatja a Pythont a Scala helyett a teljes adatelemzési folyamat feladatainak elvégzéséhez, lásd: [adatelemzés a Spark on Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
* Rendelkeznie kell egy Azure-előfizetéssel. Ha még nem rendelkezik ilyennel, szerezze be [az ingyenes Azure-próbaverziót](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Szüksége lesz egy Azure HDInsight 3.4-es Spark 1.6-os-fürtön az alábbi eljárások. Fürt létrehozásához tekintse [meg az első lépések: Apache Spark létrehozása az Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)című témakör utasításait. Állítsa be a fürt típusát és verzióját a **fürt típusának kiválasztása** menüben.

![HDInsight fürt konfigurálása](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

A New York-i utazási adatokról és a Spark-fürtön található Jupyter-jegyzetfüzetből származó kódok végrehajtásával kapcsolatos útmutatásért tekintse meg a megfelelő részeket az [adatelemzés az Azure HDInsight-on való használatának áttekintése](spark-overview.md)című témakörben.  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Hajtsa végre a Spark-fürtön lévő Jupyter notebook Scala-kód
Jupyter notebook az Azure portálról indíthatja el. A Spark-fürtöt az irányítópulton található, és kattintson rá a felügyelet lapon adja meg a fürt. Ezután kattintson a **fürt irányítópultok**elemre, majd kattintson a **Jupyter notebook** elemre a Spark-fürthöz társított jegyzetfüzet megnyitásához.

![Fürt irányítópultja és Jupyter-notebookok](./media/scala-walkthrough/spark-jupyter-on-portal.png)

A Jupyter-jegyzetfüzeteket a https://&lt;clustername&gt;. azurehdinsight.net/jupyter címen is elérheti. Cserélje le a *clustername* nevet a fürt nevére. A jelszót a rendszergazdai fiók, a Jupyter notebookok elérésére van szüksége.

![Nyissa meg a fürt nevét a Jupyter notebookok](./media/scala-walkthrough/spark-jupyter-notebook.png)

A **Scala** lehetőség kiválasztásával megtekintheti a PySpark API-t használó előre csomagolt jegyzetfüzetek néhány példáját tartalmazó könyvtárat. A "a" ipynb notebook, amely tartalmazza a következő Spark-csomagokhoz tartozó kódrészleteket, a a [githubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)érhető el: a felderítési modellezés és pontozás.

A Jupyter Notebook server közvetlenül a githubból a notebook tölthet fel a Spark-fürtön. A Jupyter kezdőlapján kattintson a **feltöltés** gombra. A Fájlkezelőben illessze be a Scala notebookhoz tartozó GitHub (nyers tartalom) URL-címét, majd kattintson a **Megnyitás**gombra. A Scala-jegyzetfüzetek a következő URL-címen érhető el:

[Feltárás – modellezés és pontozás – a-Scala. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Telepítő: Spark-kódtárak, előre a Spark és a Hive-környezetek és a Spark magics
### <a name="preset-spark-and-hive-contexts"></a>Előre beállított Spark- és Hive-környezetek
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


A Spark kernelekkel, a Jupyter notebookok rendelkezik előre beállított környezeteket. Nem kell explicit módon állítsa be a Spark vagy a Hive-környezetek az alkalmazás használatának megkezdése előtt fejleszt. Az előre beállított környezetekről a következők:

* SparkContext `sc`
* HiveContext `sqlContext`

### <a name="spark-magics"></a>A Spark magics
A Spark kernel néhány előre definiált "varázslatot" biztosít, amelyek a `%%`hívható speciális parancsok. A következő Kódminták két ezeket a parancsokat kell használni.

* `%%local` megadja, hogy a következő sorokban lévő kód helyileg lesz végrehajtva. A kód érvényes Scala-kódot kell lennie.
* `%%sql -o <variable name>` egy kaptár-lekérdezést hajt végre `sqlContext`on. Ha a `-o` paraméter át lett adva, a lekérdezés eredménye a `%%local` Scala-környezetben, Spark-adatkeretként marad.

A Jupyter-jegyzetfüzetek és az előre definiált "Magics" kernelekkel kapcsolatos további információkért, amelyeket a `%%` hív meg (például `%%local`), tekintse [meg a Jupyter notebookokhoz elérhető kerneleket HDInsight Spark Linux-fürtökkel a HDInsight-on](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Könyvtárak importálása
A Spark MLlib és más könyvtárat a következő kód használatával kell importálni.

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
Az adatelemzési folyamat első lépése az elemzési adatok betöltését. Az adatok külső vagy származó rendszerek helyét, az adatok feltárására és modellezés környezetbe tenné. Ez a cikk az adatok betöltését, egy csatlakoztatott 0,1 % példa a taxi utazást és diszkont fájl (.tsv-fájlként tárolja). Az adatok feltárására és modellezési környezet Spark. Ez a szakasz tartalmazza a kódot, és hajtsa végre az alábbi lépéseket:

1. Állítsa be az adatokat és a modell storage elérési utak.
2. Olvassa el a bemeneti adatkészletben (.tsv-fájlként tárolja).
3. Az adatok sémát, és tisztítsa meg az adatokat.
4. Megtisztított adatkeretek létrehozása, és a memóriában gyorsítótárazza azt.
5. Az adatok regisztrálása kontext SQLContext ideiglenes táblaként.
6. A tábla lekérdezése, és az eredmények importálhat adatkeretek.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>A tárolási helyek elérési utak beállítása az Azure Blob storage-ban
A Spark olvashat és írhat az Azure Blob storage. A Spark használata a meglévő adatokat feldolgozni, és ezután az eredmények tárolásához újra a Blob storage-ban.

Szeretné menteni a modellek vagy a fájlok Blob storage-ban, szüksége megfelelően adja meg az elérési útját. A Spark-fürthöz csatolt alapértelmezett tárolóra a `wasb:///`kezdetű útvonal használatával hivatkozhat. A `wasb://`használatával más helyszínekre hivatkozhat.

Az alábbi kódmintában szeretné menteni fogja a modellt kell olvasni a bemeneti adatokat és az elérési út a Blob storage, amely csatlakozik a Spark-fürt helyét adja meg.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Adatok importálása, hozzon létre egy RDD és -adatok, keret a séma szerint
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


**Kimeneti**

A cella futási idejét: 8 másodperc.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Lekérdezése a tábla és a egy adatkeretben eredmények importálása
Ezután lekérdezése a tábla diszkont, az utasok és a tipp adatokat; sérült és lakatlan adatai; szűrése és nyomtathatják ki több sort.

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

**Kimeneti**

| fare_amount | passenger_count | tip_amount | Formabontó |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Az adatok feltárása és képi megjelenítés
A Spark lekérte az adatokat, miután az adatelemzési folyamat következő lépése az adatáttekintési és vizualizációs keresztül az adatok mélyebb betekintést nyerni. Ebben a szakaszban a taxik adatait az SQL-lekérdezések vizsgálata. Ezt követően importálja az eredményeket egy adatkeretbe, hogy az automatikus vizualizáció Jupyter funkció használatával kirajzolja a célzott változókat és a vizuális ellenőrzéshez szükséges jövőbeli funkciókat.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Használja a helyi és SQL Magic Quadrant adatok ábrázolásához.
Alapértelmezés szerint minden futtatása a Jupyter notebook Fragment kódu kimenete érhető el, amelyek a feldolgozó csomópontok a rendszer megőrzi a munkamenet környezetében. Ha a munkavégző csomópontok számára minden számításhoz szeretne utat menteni, és ha a számításhoz szükséges összes adat helyileg elérhető a Jupyter-kiszolgáló csomópontján (amely a fő csomópont), a `%%local` Magic használatával futtathatja a kódrészletet a Jupyter-kiszolgálón.

* **SQL Magic** (`%%sql`). A HDInsight Spark-kernel támogatja az egyszerű beágyazott HiveQL-lekérdezéseket az kontext SQLContext. A (`-o VARIABLE_NAME`) argumentum megőrzi az SQL-lekérdezés kimenetét a Jupyter-kiszolgálón található pandák adatkeretként. Ez a beállítás azt jelenti, hogy a kimenet a helyi módban lesz elérhető.
* `%%local` **Magic**. A `%%local` Magic helyileg futtatja a kódot a Jupyter-kiszolgálón, amely a HDInsight-fürt fő csomópontja. A `%%local` Magic szolgáltatás általában a `%%sql` Magic és a `-o` paraméter együttes használatával használható. A `-o` paraméter helyileg megőrizheti az SQL-lekérdezés kimenetét, majd `%%local` a Magic a következő kódrészletet aktiválja, hogy helyileg fusson a helyileg megőrzött SQL-lekérdezések kimenetén.

### <a name="query-the-data-by-using-sql"></a>Az adatok lekérdezése SQL használatával
Ez a lekérdezés lekéri a taxi lelassítja diszkont összeg, utas száma és tipp összege.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

A következő kódban a `%%local` Magic létrehoz egy helyi adatkeretet, a sqlResults. SqlResults segítségével matplotlib használatával jeleníti meg.

> [!TIP]
> Helyi Magic Quadrant ebben a cikkben több alkalommal van használva. Ha az adatkészlet túl nagy, adjon a memóriában való létrehozásához, amelyek illeszkednek adatok keret helyi minta.
> 
> 

### <a name="plot-the-data"></a>Az adatok ábrázolása
Dolgozunk a Python-kód után az adathalmaz Pandas adatok keretet másként a helyi környezetben is.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 A Spark-kernel automatikusan elérhetővé (HiveQL) SQL-lekérdezések kimenetének, a kód futtatása után. Többféle típusú vizualizációt is választhat:

* Tábla
* Torta
* Sor
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


**Kimeneti**

![Összeg hisztogram tipp](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tipp összeg utas száma szerint](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tipp összeg diszkont értékkel](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Funkciók létrehozása és funkciók átalakítása, és ezután előkészítheti az adatokat a modellezési funkciók be
Fa-alapú modellezési függvények a Spark ML és MLlib akkor készítse elő a cél- és a funkciók különböző technikák, például a dobozolás, indexelő, egy gyakori kódolási és vektorizációt használatával. Az alábbiakban az ebben a szakaszban szereplő eljárásokat:

1. Hozzon létre egy új szolgáltatást **dobozolási** órával a forgalmi idő gyűjtőbe.
2. Alkalmazzon **indexelést és egy gyors kódolást** a kategorikus funkciókra.
3. **Kóstolja meg és ossza szét az adathalmazt** képzésbe és tesztelési frakcióba.
4. **Adja meg a betanítási változót és a funkciókat**, majd hozzon létre indexelt vagy egy gyors kódolású betanítást, és tesztelje a bemeneti feliratú pont rugalmas elosztott adatkészleteit (RDD) vagy adatkereteket.
5. A gépi tanulási modellekhez bemenetként használandó **funkciók és célok automatikus kategorizálása és vektorizálhatja** .

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Hozzon létre egy új szolgáltatás be forgalom idő gyűjtők dobozolási órák száma alapján
Ez a kód bemutatja, hogyan hozhat létre egy új szolgáltatás dobozolási órák száma alapján forgalom idő gyűjtőket, és hogyan gyorsítótárazza a memóriában az eredményül kapott adathalmaz. Ahol rdd-k és az adatok keretek használt ismételten, gyorsítótárazás javított végrehajtási időpontok vezet. Ennek megfelelően a rdd-k és adatkeretek fogja gyorsítótárazni, több fázisból áll, a következő eljárások.

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


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexelés és a egy gyakori kódolási kategorikus funkciók
A modellezés és MLlib függvényekben kategorikus indexelve vagy használata előtt kódolású bemeneti adatokat a funkciókat. Ez a szakasz bemutatja, hogyan index, vagy a modellezési funkciók be kategorikus funkciói kódolása.

Index, vagy a modellek kódolása a modelltől függően más-más módon kell. Például logisztikai és lineáris regressziós modellek van szükség, egy gyakori kódolást. Például három kategóriába szolgáltatás három funkció oszlop bővíthet. 0 vagy 1 kategóriájától függően egy megfigyelési mindegyik oszlop tartalmaz. Az MLlib biztosítja az [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) függvényt egy gyors kódoláshoz. A kódoló címke indexek oszlop bináris vektorok értékkel legfeljebb egyetlen egy-egy számoszlop rendeli hozzá. A kódolással elvárt numerikus értékelt szolgáltatások, például a logisztikai regressziós algoritmus kategorikus funkciókat is alkalmazható.

Itt alakítsa át csak négy változókat, példák, amelyek karakterláncok megjelenítése. Más változók, például a hét napja, numerikus értékek kategorikus változókként által képviselt is tudja indexelni.

Indexeléshez használja a `StringIndexer()`t, és egy gyors kódoláshoz használja a MLlib `OneHotEncoder()` függvényeit. A következő index és a kategorikus funkciók kódolása a kódot:

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


**Kimeneti**

A cella futási idejét: 4 másodperc.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>A minta és felosztása tanítási és tesztelési percenkénti egységeinek törtrészeként az adatkészlet
Ez a kód létrehoz egy véletlenszerű mintavétel az adatok (ebben a példában a 25 %). Bár mintavételi nem szükséges ehhez a példához az adatkészlet méretének miatt, a cikk bemutatja, hogyan, hogy tudja, hogyan használhatja a saját problémákat, amikor szükség mintát is. Ha nagy a mintákat, ez jelentős időt takaríthat közben-modellek betanításához. Ezután ossza fel a minta-képzés (ebben a példában a 75 %) és egy tesztelési részét (ebben a példában a 25 %) besorolási és regressziós modellezéshez.

Minden egyes sorban (a "rand" oszlop), amelyek segítségével válassza ki a kereszt-ellenőrzési modellrész betanítás során adja hozzá egy véletlenszerű szám (0 és 1) között.

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


**Kimeneti**

A cella futási idejét: 2 másodperc.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Képzési változót és a szolgáltatásokat, és hozza létre indexelt vagy egy gyakori kódolású betanítására és tesztelésére bemeneti pont rdd-k vagy adatok keretek címkével
Ez a szakasz tartalmazza a kódot, amely bemutatja, hogyan címkézett pont adattípusú értékként kategorikus szöveges adatok indexelése és kódolása, így azt használhatja taníthat vagy tesztelhet MLlib logisztikai regressziós és más képbesorolási modellek. Címkézett pont objektumok úgy, hogy a bemeneti adatként van szükség a gépi tanulási algoritmusok MLlib a többsége által formázott rdd-kként. A [címkével ellátott pont](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) egy olyan helyi vektor, amely egy címkéhez vagy válaszhoz van társítva, vagy sűrű vagy ritka.

Ezt a kódot kell megadni a cél (függő) változót és a funkciók használatához modelleket taníthat be. Ezután létrehozhat indexelt vagy egy gyakori betanítására és tesztelésére pont rdd-k vagy adatok keretek feliratú bemeneti kódolású.

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


**Kimeneti**

A cella futási idejét: 4 másodperc.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatikusan kategorizálása és a funkciók és a gépi tanulási modelleket bemenetként használandó célok vectorize
A Spark ML használatával kategorizálása a cél és a szolgáltatásokat a fa-alapú modellezési funkciók használatára. A kód két feladatokat hajtja végre:

* 0 vagy 1 értéket rendel az egyes adatpontok 0 és 1 közötti 0.5-ös küszöbérték érték használatával hoz létre egy bináris osztályozási célja.
* Automatikusan kategorizálja a funkciókat. Ha az összes olyan szolgáltatás különböző numerikus értékek száma kisebb, mint 32, kategorizált ezt a funkciót.

Ez a kód a két feladatokhoz.

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



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Bináris osztályozási modell: előre jelezni, hogy tipp ki kell fizetni.
Ebben a szakaszban három típusú bináris osztályozási modell előrejelzési e tipp kell fizetni hoz létre:

* Egy **logisztikai regressziós modell** a Spark ml `LogisticRegression()` függvény használatával
* **Véletlenszerű erdő besorolási modell** a Spark ml `RandomForestClassifier()` függvény használatával
* **Színátmenetet növelő faosztályozási modell** a MLlib `GradientBoostedTrees()` függvény használatával

### <a name="create-a-logistic-regression-model"></a>Egy logisztikai regressziós modell létrehozása
Ezután hozzon létre egy logisztikai regressziós modellt a Spark ML `LogisticRegression()` függvény használatával. A modell létrehozásához a kód a lépések egy sorozatát hoz létre:

1. **A modellre** vonatkozó adatkészletek betanítása egyetlen paraméterrel.
2. **Értékelje ki a modellt** egy olyan tesztelési adatkészleten, amely metrikákkal rendelkezik.
3. **Mentse a modellt** a blob Storage-ban a későbbi felhasználás érdekében.
4. **A modell kiértékelése a** tesztelési adatként.
5. **Az eredményeket** a fogadó működési jellemző (Roc) görbékkel ábrázolhatja.

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

Betöltése, pontszám és a-eredményeket menteni.

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


**Kimeneti**

A teszt adatai ROC = 0.9827381497557599

Python használata a helyi Pandas adatkeretek ROC-görbe ábrázolásához.

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


**Kimeneti**

![Tipp vagy nincs tipp ROC-görbe](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Hozzon létre egy véletlenszerű besorolási erdőmodell
Ezután hozzon létre egy véletlenszerű erdő besorolási modellt a Spark ML `RandomForestClassifier()` függvény használatával, majd értékelje ki a modellt a tesztelési adaton.

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


**Kimeneti**

A teszt adatai ROC = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Hozzon létre egy GBT osztályozási modell
Ezután hozzon létre egy GBT besorolási modellt a MLlib `GradientBoostedTrees()` függvényének használatával, majd értékelje ki a modellt a tesztelési adatain.

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


**Kimeneti**

ROC-görbe alatti terület: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regressziós modell: tipp összeg előrejelzése
Ebben a szakaszban két típusú tipp összeg előrejelzésére regressziós modelleket hoz létre:

* Egy **szabályos lineáris regressziós modell** a Spark ml `LinearRegression()` függvény használatával. A modell mentése fog, és a teszt adatai a modell kiértékelésére.
* **Színátmenet-növelő fa regressziós modell** a Spark ml `GBTRegressor()` függvény használatával.

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


**Kimeneti**

A cella futási idejét: 13 másodperc.

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


**Kimeneti**

R-sqr a Tesztadatok = 0.5960320470835743

Ezután a vizsgálati eredmények lekérdezése adatkeretek, és AutoVizWidget és matplotlib a Vizualizáció.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

A kód helyi adatkeretek hoz létre a lekérdezés kimenetét, és jeleníti meg az adatokat. A `%%local` Magic létrehoz egy helyi adatkeretet, `sqlResults`, amelyet a matplotlib való ábrázoláshoz használhat.

> [!NOTE]
> A Spark Magic Quadrant ebben a cikkben több alkalommal van használva. Nagy adatmennyiség esetén a kell mintát hozhat létre egy adatkeretbe, amelyek illeszkednek a helyi memóriához.
> 
> 

Grafikon létrehozása Python matplotlib használatával.

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

**Kimeneti**

![Összeg tipp: tényleges és becsült](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>GBT regressziós modell létrehozása
Hozzon létre egy GBT regressziós modellt a Spark ML `GBTRegressor()` függvény használatával, majd értékelje ki a modellt a tesztelési adaton.

A [színátmenet által növelt fák](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS-EK) a döntési fák együttesei. A GBTS vonatok döntési feladatait iteratív a veszteségek csökkentése érdekében. A GBTS a regresszió és a besorolás használatára használhatja. Azok képes kezelni a kategorikus szolgáltatásokat, nincs szükség a szolgáltatás méretezése és rögzíthetők a hétköznapi nonlinearities és a szolgáltatás kapcsolati. Is használhatja őket egy osztályú-besorolás beállításban.

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


**Kimeneti**

Teszt R-sqr van: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Az optimalizálás speciális modellekre segédprogramok
Ebben a szakaszban használhatja a machine learning segédprogramok, amely a fejlesztőknek gyakran használt modell optimalizálása. Pontosabban a machine learning-modellek három különböző módon is optimalizálhatja paraméter kezdik és kereszt-ellenőrzés:

* Az adatok felosztása tanítási és érvényesítési csoportok, a modell optimalizálása a gyakorlókészlethez hyper paraméter kezdik használatával és értékeléséhez érvényesítési konfigurálásában (lineáris regresszió)
* A modell optimalizálása a kereszt-ellenőrzési és a hyper-abszolút Spark ML CrossValidator függvény (bináris osztályozás) használatával.
* A modell optimalizálása bármely machine learning-függvény, és paraméter beállítása (lineáris regresszió) használatához egyéni kereszt-ellenőrzési és a paraméter-kezdik kód használatával

A többszörös **Érvényesítés** egy olyan technika, amely kiértékeli, hogy az ismert adathalmazon alapuló modellek mennyire jól általánosítják az olyan adatkészletek funkcióinak előrejelzését, amelyeken még nincs betanítva. Ezzel a technikával általános működési, hogy a modell tanítása az ismert adatok adathalmazon, és az előrejelzések pontossága tesztelik egy független adatkészlet alapján. A közös megvalósítás egy adatkészlet felosztása a *k*-redők közé, majd a modell betanítása egy ciklikus multiplexelés, de az egyik hajtogatási módszer.

A **Hyper-paraméter optimalizálása** olyan Hyper-paraméterek kiválasztásának a problémája, amely egy tanulási algoritmushoz szükséges, általában azzal a céllal, hogy az algoritmus teljesítményének mértékét egy független adathalmazon optimalizálja. A hyper-paraméter értéke a modell betanítási eljárás kívül is meg kell adnia. A hyper-paraméterértékek feltételezéseket hatással lehet a rugalmasság és a modell pontosságát. Döntési fák algoritmus a hyper-paraméterek, például a kívánt mélységének és hagyja, a fában száma például rendelkezik. Be kell állítani egy téves besorolás napján belül pótdíj kifejezés egy tartóvektor-gép (SVM).

A Hyper-paraméterek optimalizálásának gyakori módja, ha egy rácsos keresést használ, más néven a **paramétert**is. Rács keresésnél egy teljes körű keresés az értékeket egy adott részének a tanulási algoritmus hyper paraméterrel adhatja át történik meg. Kereszt-ellenőrzési adhat meg a teljesítmény-mérőszám ki a rács keresési algoritmus által előállított az optimális eredmények rendezéséhez. Kereszt-ellenőrzési hyper paraméter kezdik használatakor korlát kapcsolatos, például egy modell a betanítási adatok overfitting segíthet. Ezzel a módszerrel a modell megőrzi a alkalmazni szeretné az általános, amelyről a betanítási adatok kinyert adatok kapacitást.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>A hyper-paraméter kezdik lineáris regressziós modell optimalizálása
Ezután adatok felosztása tanítási és érvényesítés csoportok, használja a hyper-paraméter abszolút képzési konfigurálásában a modell optimalizálása és értékeléséhez érvényesítési konfigurálásában (lineáris regresszió).

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


**Kimeneti**

Teszt R-sqr van: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>A bináris osztályozási modell optimalizálása a kereszt-ellenőrzési és a hyper-paraméter kezdik használatával
Ez a szakasz bemutatja, hogyan kereszt-ellenőrzési és a hyper-paraméter kezdik használatával egy bináris osztályozási modell optimalizálása érdekében. Ez a Spark ML `CrossValidator` függvényt használja.

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


**Kimeneti**

A cella futási idejét: 33 másodperc.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Egyéni kereszt-ellenőrzési és a paraméter-kezdik kód használatával a lineáris regressziós modell optimalizálása
Ezután a modell optimalizálása egyéni kód használatával, és a legjobb modellt paraméterek azonosításához a legnagyobb pontosságú feltétel használatával. Ezután hozzon létre a kész modell, a Tesztadatok a modell kiértékelésére, és mentse a Blob storage-ban. Végül a modell betöltése, tesztadatok pontszám és értékeléséhez pontosságát.

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


**Kimeneti**

A cella futási idejét: 61 másodperc.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>A Spark használatával összeállított gépi tanulási modelleket automatikusan Scala felhasználása
Az Azure adatelemzési folyamatát alkotó feladatokkal kapcsolatos témakörök áttekintését itt találja: [csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

A [csoportos adatelemzési folyamat](walkthroughs.md) bemutatói ismertetik azokat a végpontok közötti bemutatókat, amelyek bemutatják a csoportos adatelemzési folyamat lépéseit adott forgatókönyvek esetén. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőalapú és helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására is.

A [Spark-alapú gépi tanulási modellek](spark-model-consumption.md) azt mutatják be, hogyan használható a Scala Code az új adatkészletek automatikus betöltéséhez és az Azure Blob Storage-ban mentett gépi tanulási modellekkel való összegyűjtéséhez. A megjelenő utasításokat követve van, és egyszerűen cserélje le a Python-kód Scala kódra ebben a cikkben automatizált felhasználásra.

