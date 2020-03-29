---
title: Adatelemzés a Scala és a Spark használatával az Azure-ban – Csapatadat-elemzési folyamat
description: A Scala használata felügyelt gépi tanulási feladatokhoz a Spark méretezhető MLlib és Spark ML-csomagokkal egy Azure HDInsight Spark-fürtön.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76716761"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Adatelemzés a Scala és a Spark használatával az Azure rendszerben
Ez a cikk bemutatja, hogyan használhatja a Scala felügyelt gépi tanulási feladatok a Spark méretezhető MLlib és Spark ML-csomagok egy Azure HDInsight Spark-fürtön. Végigvezeti az [adatelemzési folyamatot](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)alkotó feladatokon: adatbetöltés és feltárás, vizualizáció, szolgáltatástervezés, modellezés és modellfelhasználás. A cikkben a modellek közé tartozik a logisztikai és lineáris regresszió, véletlenszerű erdők és gradiens-boosted fák (GBTs), amellett, hogy két közös felügyelt gépi tanulási feladatok:

* Regressziós probléma: A borravaló összegének előrejelzése ($) egy taxiútra
* Bináris osztályozás: Tipp vagy tipp (1/0) előrejelzése taxikiránduláshoz

A modellezési folyamat betanítást és értékelést igényel egy tesztadatkészleten és a releváns pontossági mutatókon. Ebben a cikkben megtudhatja, hogyan tárolhatja ezeket a modelleket az Azure Blob storage-ban, és hogyan pontozhatja és értékelheti a prediktív teljesítményüket. Ez a cikk a modellek keresztérvényesítési és hiperparaméter-söprés használatával történő optimalizálásának speciális témaköreit is ismerteti. A felhasznált adatok egy példa a 2013 NYC taxi út és a viteldíj adatkészlet elérhető GitHub.

[Scala](https://www.scala-lang.org/), a java virtuális gépen alapuló nyelv, integrálja az objektum-orientált és funkcionális nyelvi fogalmakat. Ez egy skálázható nyelv, amely kiválóan alkalmas a felhőben elosztott feldolgozáshoz, és az Azure Spark-fürtökön fut.

[A Spark](https://spark.apache.org/) egy nyílt forráskódú párhuzamos feldolgozási keretrendszer, amely támogatja a memórián belüli feldolgozást a big data-elemzési alkalmazások teljesítményének növelése érdekében. A Spark feldolgozómotor a sebesség, a könnyű használat és a kifinomult analitika érdekében készült. A Spark memórián belüli elosztott számítási képességei jó választássá teszik a gépi tanulás és a gráfszámítások iteratív algoritmusai számára. A [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) csomag az adatkeretekre épülő magas szintű API-k egységes készletét biztosítja, amelyek segítségével gyakorlati gépi tanulási folyamatokat hozhat létre és hangolhat be. [Az MLlib](https://spark.apache.org/mllib/) a Spark méretezhető gépi tanulási könyvtára, amely modellezési képességeket hoz létre ebben az elosztott környezetben.

[A HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) a nyílt forráskódú Spark Azure-üzemeltetett ajánlata. Támogatja a Jupyter Scala-jegyzetfüzeteket a Spark-fürtön, és a Spark SQL interaktív lekérdezések futtatásával átalakíthatja, szűrheti és vizualizálhatja az Azure Blob storage-ban tárolt adatokat. A scala kódrészletek ebben a cikkben, amely a megoldásokat, és a megfelelő telkek megjelenítéséhez a Spark-fürtökre telepített Jupyter-jegyzetfüzetekben futó adatok megjelenítéséhez. Az ezekben a témakörökben leírt modellezési lépések kóddal rendelkeznek, amely bemutatja, hogyan lehet betanítása, kiértékelése, mentése és felhasználása az egyes típusú modellek.

Ebben a cikkben a beállítási lépések és a kód az Azure HDInsight 3.4 Spark 1.6-hoz. Azonban a kódot ebben a cikkben és a [Scala Jupyter notebook](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) általános, és működnie kell minden Spark-fürtön. A fürt beállítási és felügyeleti lépései némileg eltérhetnek a cikkben láthatótól, ha nem a HDInsight Sparkot használja.

> [!NOTE]
> Ha egy témakört, amely bemutatja, hogyan használhatja a Python helyett a Scala feladatok teljes körű adatelemzési folyamat, olvassa el [az Adatelemzés a Spark használatával az Azure HDInsight.](spark-overview.md)
> 
> 

## <a name="prerequisites"></a>Előfeltételek
* Rendelkeznie kell egy Azure-előfizetéssel. Ha még nem rendelkezik ilyen, [kap egy Ingyenes Azure-próbaverziót.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)
* Az alábbi eljárások végrehajtásához egy Azure HDInsight 3.4 Spark 1.6-fürtre van szükség. Fürt létrehozásához olvassa el az Első lépések című témakörutasításait: [Az Apache Spark létrehozása az Azure HDInsight-on](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)című témakörben. Állítsa be a fürt típusát és verzióját a **Fürttípus kiválasztása** menüben.

![HDInsight-fürttípus-konfiguráció](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

A nyc-i taxiút-adatok leírását és a Spark-fürt Jupyter-jegyzetfüzetéből származó kód végrehajtására vonatkozó utasításokat az [Azure HDInsight Spark használatával az adatelemzés áttekintése](spark-overview.md)című témakörmegfelelő szakaszaiban találja.  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Scala-kód végrehajtása egy Jupyter-jegyzetfüzetből a Spark-fürtön
Jupyter-jegyzetfüzetet az Azure Portalról indíthat el. Keresse meg a Spark-fürtaz irányítópulton, és kattintson rá a fürt felügyeleti lapjának megadásához. Ezután kattintson **a Fürtirányítópultok**elemre , majd a **Jupyter-jegyzetfüzet** elemre a Spark-fürthöz társított jegyzetfüzet megnyitásához.

![Fürtirányítópult és Jupyter-jegyzetfüzetek](./media/scala-walkthrough/spark-jupyter-on-portal.png)

A Jupyter-jegyzetfüzetek https://&lt;fürtnév&gt;.azurehdinsight.net/jupyter. Cserélje le a *fürtnevét* a fürt nevére. A Jupyter-jegyzetfüzetek eléréséhez a rendszergazdai fiók jelszavára van szükség.

![Ugrás a Jupyter-jegyzetfüzetek rekedése a fürtnevének használatával](./media/scala-walkthrough/spark-jupyter-notebook.png)

Válassza a **Scala** lehetőséget egy olyan könyvtár megtekintéséhez, amely néhány példát tartalmaz a PySpark API-t használó előre csomagolt jegyzetfüzetekre. A Scala.ipynb notebook használatával történő feltárási modellezés és pontozás, amely a Spark-témakörök ezen csomagjának kódmintáit tartalmazza, elérhető a [GitHubon.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)

A jegyzetfüzetet közvetlenül a GitHubról töltheti fel a Jupyter notebook-kiszolgálóra a Spark-fürtön. A Jupyter kezdőlapján kattintson a **Feltöltés gombra.** A fájlkezelőben illessze be a Scala-jegyzetfüzet GitHub-URL-címét, majd kattintson a **Megnyitás**gombra. A Scala-jegyzetfüzet a következő URL-címen érhető el:

[Feltárás-modellezés-és pontozási-használat-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Beállítás: Előre beállított Spark- és Hive-környezetek, Spark-varázslatok és Spark-könyvtárak
### <a name="preset-spark-and-hive-contexts"></a>Előre beállított Spark- és Hive-környezetek
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


A Jupyter-jegyzetfüzetekhez mellékelt Spark kernelek előre beállított környezetekkel rendelkeznek. Nem kell explicit módon beállítani a Spark vagy a Hive környezetben, mielőtt elkezdené a munkát a fejlődő alkalmazással. Az előre beállított környezetek a következők:

* `sc`SparkContext-hez
* `sqlContext`HiveContext esetén

### <a name="spark-magics"></a>Szikravarázslatok
A Spark kernel néhány előre definiált "varázslatot" biztosít, `%%`amelyek speciális parancsok, amelyeket a segítségével hívhat meg. Ezek közül kettő a következő kódmintákban használatos.

* `%%local`megadja, hogy a következő sorokban lévő kód helyileg kerülvégrehajtásra. A kódnak érvényes Scala-kódnak kell lennie.
* `%%sql -o <variable name>`Hive-lekérdezést hajt `sqlContext`végre. Ha `-o` a paraméter átlett adva, a lekérdezés `%%local` eredménye megmarad a Scala környezetben Spark-adatkeretként.

A Jupyter-jegyzetfüzetek kerneleiről és azok előre definiált `%%` "varázslatairól", `%%local`amelyekkel (például ) rendelkezik , a [HDInsight-alapú Jupyter-jegyzetfüzetek számára elérhető Kernelek](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)című témakörben talál további információt.

### <a name="import-libraries"></a>Könyvtárak importálása
Importálja a Sparkot, az MLlib-et és más tárakat, amelyekre szüksége lesz a következő kód használatával.

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
Az adatelemzési folyamat első lépése az elemezni kívánt adatok betöltése. Az adatokat külső forrásokból vagy rendszerekből hozza létre, ahol azok az adatok feltárási és modellezési környezetében találhatók. Ebben a cikkben a betöltési adatok a taxiút és a viteldíjfájl (.tsv fájlként tárolt) 0,1%-os mintája. Az adatok feltárása és modellezési környezet Spark. Ez a szakasz a következő feladatsorozat elvégzéséhez szükséges kódot tartalmazza:

1. Címtárelérési utak beállítása az adatok és a modelltárolás hoz.
2. Olvasás a bemeneti adatkészletben (.tsv fájlként tárolva).
3. Adjon meg egy sémát az adatokhoz, és tisztítsa meg az adatokat.
4. Hozzon létre egy megtisztított adatkeretet, és gyorsítótárazza a memóriában.
5. Regisztrálja az adatokat ideiglenes táblaként az SQLContext környezetben.
6. A tábla lekérdezése és az eredmények importálása adatkeretbe.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Könyvtárelérési utak beállítása a tárolási helyekhez az Azure Blob storage-ban
A Spark képes olvasni és írni az Azure Blob storage-ba. A Spark segítségével feldolgozhatja a meglévő adatokat, majd az eredményeket újra tárolhatja a Blob storage-ban.

A Blob storage-ba történő modellek vagy fájlok mentéséhez megfelelően meg kell adnia az elérési utat. Hivatkozzon a Spark-fürthöz csatlakoztatott alapértelmezett tárolóra `wasb:///`a programmal kezdődő elérési út használatával. Hivatkozzon más `wasb://`helyekre a használatával.

A következő kódminta határozza meg az olvasandó bemeneti adatok helyét, és a Spark-fürthöz csatolt Blob storage elérési útját, ahol a modell mentésre kerül.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Adatok importálása, RDD létrehozása és adatkeret definiálása a séma szerint
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


**Kimeneti:**

8 másodperc a cella futtatására.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>A tábla lekérdezése és az eredmények importálása adatkeretben
Ezután kérdezze meg a táblát a viteldíj, az utas és a tipp adatairól; kiszűrni a sérült és a külső adatokat; és nyomtasson több sort.

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

**Kimeneti:**

| fare_amount | passenger_count | tip_amount | Megbillent |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10,5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Adatok feltárása és vizualizációja
Miután az adatokat a Sparkba hozta, az adatelemzési folyamat következő lépése az adatok mélyebb megértése a feltárás és a vizualizáció révén. Ebben a szakaszban sql-lekérdezések segítségével vizsgálhatja meg a taxi adatokat. Ezután importálja az eredményeket egy adatkeretbe a célváltozók és a vizuális ellenőrzés lehetséges funkcióinak ábrázolásához az automatikus vizualizációs Jupyter funkció használatával.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Adatok nyomtatása helyi és SQL-mágiával
Alapértelmezés szerint a Jupyter-jegyzetfüzetből futtatott kódrészlet kimenete elérhető a munkavégző csomópontokon megőrzött munkamenet környezetében. Ha menteni szeretne egy utat a munkavégző csomópontokhoz minden számításhoz, és ha a számításhoz szükséges összes adat elérhető helyileg a Jupyter kiszolgáló csomóponton `%%local` (amely a fő csomópont), a varázsoló segítségével futtathatja a kódrészletet a Jupyter-kiszolgálón.

* **SQL** magic`%%sql`( ). A HDInsight Spark kernel támogatja az SQLContext egyszerű, inline HiveQL lekérdezéseit. A`-o VARIABLE_NAME`( ) argumentum a Jupyter-kiszolgálón pandaadatkeretként megőrzi az SQL-lekérdezés kimenetét. Ez a beállítás azt jelenti, hogy a kimenet helyi módban lesz elérhető.
* `%%local`**varázslat**. A `%%local` varázslat helyileg futtatja a kódot a Jupyter-kiszolgálón, amely a HDInsight-fürt fő csomópontja. Jellemzően, akkor `%%local` használja a `%%sql` mágia együtt `-o` a mágia a paraméter. A `-o` paraméter helyileg megmaradna az SQL-lekérdezés kimenetén, majd `%%local` a magic elindítja a következő kódrészletet, amely helyileg fut az SQL-lekérdezések helyileg megőrzött kimenetén.

### <a name="query-the-data-by-using-sql"></a>Adatok lekérdezése sql használatával
Ez a lekérdezés lekéri a taxi utak a viteldíj összege, az utasok száma, és a tipp összege.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

A következő kódban `%%local` a mágia létrehoz egy helyi adatkeretet, az sqlResults-et. Az sqlResults segítségével matplotlib használatával is megrajzolhatja.

> [!TIP]
> A helyi varázslatot többször is használják ebben a cikkben. Ha az adatkészlet nagy, például hozzon létre egy olyan adatkeretet, amely elfér a helyi memóriában.
> 
> 

### <a name="plot-the-data"></a>Az adatok nyomtatása
A Python-kód használatával is ábrázolhatja, miután az adatkeret a helyi környezetben, mint a Pandas adatkeret.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 A Spark kernel automatikusan vizualizálja az SQL (HiveQL) lekérdezések kimenetét a kód futtatása után. Többféle vizualizáció közül választhat:

* Tábla
* Torta
* Vonal
* Terület
* Sáv

Itt a kód, hogy ábrázolja az adatokat:

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


**Kimeneti:**

![Tipp összege hisztogram](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tipp összege utasszám szerint](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tipp összege viteldíj összeg szerint](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Funkciók létrehozása és a funkciók átalakítása, majd az adatok előkészítése a modellezési függvényekbe történő bevitelhez
A Spark ML és Az MLlib faalapú modellezési függvényeihez különböző technikák, például binning, indexelés, egy forró kódolás és vektorizálás használatával kell előkészítenie a cél- és funkciókat. Itt vannak az eljárások, hogy kövesse ebben a szakaszban:

1. Hozzon létre egy új funkciót **úgy, hogy** az órákat forgalmi időgyűjtőkbe binningeli.
2. **Indexelés és egy gyors kódolás** alkalmazása kategorikus funkciókra.
3. **Minta és felosztás a adathalmazt** betanítási és tesztelési törtekre.
4. **Adja meg a képzési változót és a funkciókat,** majd hozzon létre indexelt vagy egy gyorsan kódolt betanítási és tesztelési bemeneti címkével ellátott pont rugalmas elosztott adatkészletek (RDDs) vagy adatkeretek.
5. Automatikusan **kategorizálhatja és vektorizálja** a gépi tanulási modellek bemenetként használható jellemzőit és céljait.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Új funkció létrehozása az órák forgalmi időgyűjtőkbe való berakásával
Ez a kód bemutatja, hogyan hozhat létre egy új funkciót az órák forgalmi időgyűjtőkbe való beolvasásával és az eredményül kapott adatkeret gyorsítótárazásával a memóriában. Ha az RDD-ket és az adatkereteket ismételten használják, a gyorsítótárazás jobb végrehajtási időket eredményez. Ennek megfelelően az RDD-ket és az adatkereteket a következő eljárások több szakaszában gyorsítótárazza.

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


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>A kategorikus funkciók indexelése és egy forró kódolása
Az MLlib modellezési és előrejelzési funkcióinak használatához a kategorikus bemeneti adatokkal rendelkező funkciókat a használat előtt indexelni vagy kódolni kell. Ez a szakasz bemutatja, hogyan indexelheti vagy kódolhatja a modellezési függvények kategorikus funkcióit.

A modelltől függően különböző módon kell indexelni vagy kódolni a modelleket. Például a logisztikai és lineáris regressziós modellek egy forró kódolást igényelnek. Egy három kategóriás funkció például három jellemzőoszlopra bontható ki. Minden oszlop 0-t vagy 1-et tartalmaz a megfigyelés kategóriájától függően. Az MLlib biztosítja a [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkciót az egy forró kódoláshoz. Ez a kódoló egy címkeindex-oszlopot rendel hozzá a bináris vektorok oszlopához, legbazis egyetlen értékkel. Ezzel a kódolással a numerikus értékű funkciókat, például a logisztikai regressziót elszenvedő algoritmusok alkalmazhatók a kategorikus funkciókra.

Itt csak négy változót alakíthat át, hogy példákat mutasson be, amelyek karaktersorozatok. Más változókat is indexelhet, például a hétköznapokat, amelyeket numerikus értékek jelölnek kategorikus változókként.

Az indexeléshez, a használathoz `StringIndexer()`és `OneHotEncoder()` az egy forró kódoláshoz használja az MLlib függvényeit. Itt van a kód index és kódolni kategorikus funkciók:

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


**Kimeneti:**

Ideje futtatni a cellát: 4 másodperc.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Minta és felosztása az adatkészlet képzési és vizsgálati frakciók
Ez a kód véletlenszerű mintavételt hoz létre az adatokból (25%, ebben a példában). Bár ebben a példában az adatkészlet mérete miatt nincs szükség mintavételezésre, a cikk bemutatja, hogyan minta, így tudja, hogyan kell használni a saját problémáit, ha szükséges. Ha a minták nagyok, ez jelentős időt takaríthat meg a modellek betanítása közben. Ezután ossza fel a mintát egy betanítási részre (ebben a példában 75%) és egy tesztelési részre (ebben a példában) a besorolási és regressziós modellezéshez.

Adjon hozzá egy véletlen számot (0 és 1 között) minden sorhoz (egy "rand" oszlopban), amely a keresztérvényesítési redők kiválasztására használható az edzés során.

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


**Kimeneti:**

Ideje futtatni a cellát: 2 másodperc.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Adja meg a betanítási változót és a funkciókat, majd hozzon létre indexelt vagy egy gyorsan kódolt betanítási és tesztelési bemeneti pont RDD-k vagy adatkeretek
Ez a szakasz olyan kódot tartalmaz, amely bemutatja, hogyan indexelheti a kategorikus szöveges adatokat címkézett pontadattípusként, és kódolhatja őket, így az MLlib logisztikai regresszióés más besorolási modellek betanításához és teszteléséhez használható. A címkézett pontobjektumok olyan RDD-k, amelyek az MLlib legtöbb gépi tanulási algoritmusa számára szükséges bemeneti adatként vannak formázva. A [címkézett pont](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) egy helyi vektor, akár sűrű, akár ritka, amely egy címkéhez/válaszhoz van társítva.

Ebben a kódban adja meg a cél (függő) változót és a modellek betanításához használandó funkciókat. Ezután hozzon létre indexelt vagy egy gyorsan kódolt betanítási és tesztelési bemeneti pont RDD-k vagy adatkeretek.

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


**Kimeneti:**

Ideje futtatni a cellát: 4 másodperc.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Gépi tanulási modellek bemenetként használt funkciók és célok automatikus kategorizálása és vektorizálása
A Spark ML használatával kategorizálhatja a cél és a faalapú modellezési függvényekben használható funkciókat. A kód két feladatot hajt végre:

* Bináris célként hoz létre besorolást úgy, hogy 0 vagy 1 értéket rendel minden 0 és 1 közötti adatponthoz 0 és 1 közötti értékkel 0,5 küszöbérték használatával.
* Automatikusan kategorizálja a funkciókat. Ha bármely jellemző különböző numerikus értékeinek száma 32-nél kisebb, a szolgáltatás kategorizálva lesz.

Itt a kód a két feladathoz.

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



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Bináris osztályozási modell: Tipp elvárható, hogy egy tippet ki kell-e fizetni
Ebben a szakaszban háromféle bináris osztályozási modellt hoz létre annak előrejelzéséhez, hogy egy tippet meg kell-e fizetni:

* **Logisztikai regressziós modell** a `LogisticRegression()` Spark ML függvény használatával
* **Véletlen erdőosztályozási modell** a `RandomForestClassifier()` Spark ML függvény használatával
* **Színátmenetes fokozó faosztályozási modell** `GradientBoostedTrees()` az MLlib függvény használatával

### <a name="create-a-logistic-regression-model"></a>Logisztikai regressziós modell létrehozása
Ezután hozzon létre egy logisztikai regressziós modellt a Spark ML-függvény `LogisticRegression()` használatával. A modellépítési kódot lépések sorozatában hozza létre:

1. **A modelladatok betanítása** egy paraméterkészlettel.
2. **Értékelje ki a modellt** egy mérőszámokkal rendelkező tesztadatkészleten.
3. **Mentse a modellt a** Blob storage későbbi felhasználáshoz.
4. **Pontszám a modell** a vizsgálati adatok.
5. **Az eredményeket a** vevő működési jellemző (ROC) görbéivel ábrázolja.

Itt van a kód részére ezek eljárásmód:

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

Töltse be, pontszám, és mentse az eredményeket.

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


**Kimeneti:**

ROC a vizsgálati adatokon = 0,9827381497557599

Használja a Python helyi Pandas adatkeretek a ROC-görbe ábrázolásához.

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


**Kimeneti:**

![Tipp vagy nem tip ROC görbe](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Véletlen erdőosztályozási modell létrehozása
Ezután hozzon létre egy véletlenszerű erdőbesorolási modellt a Spark ML `RandomForestClassifier()` függvény használatával, majd értékelje ki a modellt a tesztadatokon.

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


**Kimeneti:**

ROC a vizsgálati adatokon = 0,9847103571552683

### <a name="create-a-gbt-classification-model"></a>GBT besorolási modell létrehozása
Ezután hozzon létre egy GBT besorolási `GradientBoostedTrees()` modellt az MLlib függvény használatával, majd értékelje ki a modellt a tesztadatokon.

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


**Kimeneti:**

Roc görbe alatti terület: 0,9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regressziós modell: Tipp összegének előrejelzése
Ebben a szakaszban kétféle regressziós modellt hozhat létre a tipp összegének előrejelzéséhez:

* A spark ML `LinearRegression()` függvény használatával **szabályosanizált lineáris regressziós modell.** Mentse a modellt, és értékelje ki a modellt a tesztadatokon.
* **Színátmenet-kiemelésfa regressziós** modell `GBTRegressor()` a Spark ML függvény használatával.

### <a name="create-a-regularized-linear-regression-model"></a>Szabályos lineáris regressziós modell létrehozása
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


**Kimeneti:**

Ideje futtatni a cellát: 13 másodperc.

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


**Kimeneti:**

R-sqr a vizsgálati adatokon = 0,5960320470835743

Ezután tesztelje le a teszteredményeket adatkeretként, és használja az AutoVizWidget és a matplotlib segítségével a megjelenítést.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

A kód létrehoz egy helyi adatkeretet a lekérdezés kimenetéből, és megtervezi az adatokat. A `%%local` varázslat létrehoz egy `sqlResults`helyi adatkeretet, amelyet a matplotlib-mal ábrázolhat.

> [!NOTE]
> Ezt a Spark-varázslatot ebben a cikkben többször is használják. Ha az adatmennyiség nagy, a helyi memóriába illeszkedő adatkeret létrehozásához mintát kell venni.
> 
> 

Hozzon létre telkeket python matplotlib használatával.

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

**Kimeneti:**

![Tipp összege: Tényleges és előre jelzett](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>GBT regressziós modell létrehozása
Hozzon létre egy GBT regressziós modellt a Spark ML `GBTRegressor()` függvény használatával, majd értékelje ki a modellt a tesztadatokon.

[A gradienssel felturbózott fák](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) döntési fák együttesei. A GBTS a veszteségfunkció minimalizálása érdekében iteratív módon kiképezi a döntési fákat. A GBTS-t regressziós és besorolási célokra használhatja. Képesek kezelni a kategorikus funkciókat, nem igényelnek szolgáltatásskálázást, és rögzíthetik a nemlinearitásokat és a szolgáltatásinterakciókat. Ezeket többosztályos besorolási beállításban is használhatja.

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


**Kimeneti:**

Teszt R-sqr: 0,7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Speciális modellezési segédprogramok optimalizáláshoz
Ebben a szakaszban gépi tanulási segédprogramokat használ, amelyeket a fejlesztők gyakran használnak a modelloptimalizáláshoz. Pontosabban a gépi tanulási modelleket három különböző módon optimalizálhatja a paraméters söpréssel és keresztellenőrzéssel:

* Az adatok felosztása betanítási és érvényesítési készletekre, a modell optimalizálása egy betanítási készlet hiperparaméteres söprésével, és egy érvényesítési készlet (lineáris regresszió) kiértékelése.
* A modell optimalizálása keresztérvényesítési és hiperparaméter-söprés használatával a Spark ML CrossValidator függvényének használatával (bináris besorolás)
* A modell optimalizálása egyéni keresztérvényesítési és paraméter-seprési kód használatával bármely gépi tanulási függvény és paraméterkészlet (lineáris regresszió) használatához

**Keresztérvényesítés** egy olyan technika, amely felméri, hogy egy modell betanított egy ismert adathalmaz általánosítani, hogy előre jelezze az adatkészletek azon jellemzőit, amelyeken nem van betanítva. A technika mögött rejlő általános elképzelés az, hogy a modell ismert adatok adatkészletén van betanítva, majd az előrejelzések pontosságát egy független adatkészlettel tesztelik. A közös megvalósítás az, *k*hogy az adatkészletet k-hajtásokra osztja fel, majd ciklikus multiplexelési módon betanítja a modellt az egyik kivételével.

**A hyper-parameter optimization** a probléma a tanulási algoritmus hiperparamétereinek kiválasztásával, általában azzal a céllal, hogy optimalizálja az algoritmus teljesítményének mérését egy független adatkészleten. A hiper-paraméter olyan érték, amelyet meg kell adnia a modell betanítási eljáráson kívül. A hiperparaméter-értékekkel kapcsolatos feltételezések befolyásolhatják a modell rugalmasságát és pontosságát. Döntés fák hiper-paraméterek, például, mint például a kívánt mélység és a levelek száma a fában. Be kell állítania egy téves besorolás büntetés idot egy támogatási vektorgép (SVM).

A hiperparaméterek optimalizálásának gyakori módja a rácskeresés, más néven **paraméteres söprés**használata. A rácskereséssorán a teljes körű keresés a tanulási algoritmus hiperparaméter-területének egy megadott részhalmazának értékein keresztül történik. A keresztérvényesítés teljesítménymérőt biztosíthat a rácskeresési algoritmus által létrehozott optimális eredmények rendezéséhez. Ha kereszt-érvényesítési hiperparaméter-söprést használ, csökkentheti az olyan problémák korlátozását, mint például a modell betanítási adatokhoz való túlszerelése. Így a modell megtartja a kapacitást, hogy alkalmazza az általános adathalmazt, amelyből a betanítási adatok kinyerték.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Lineáris regressziós modell optimalizálása hiperparaméter-söpréssel
Ezután ossza fel az adatokat betanítási és érvényesítési készletekre, használjon hyper-parameter sweeping-t egy betanítási készleten a modell optimalizálásához, és értékelje ki az érvényesítési készletet (lineáris regresszió).

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


**Kimeneti:**

Teszt R-sqr: 0,626484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimalizálja a bináris osztályozási modellt kereszt-érvényesítés és hiper-paraméter söprés használatával
Ez a szakasz bemutatja, hogyan optimalizálhatja a bináris osztályozási modell segítségével kereszt-érvényesítés i és hyper-paraméter sweeping használatával. Ez a Spark `CrossValidator` ML függvényt használja.

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


**Kimeneti:**

33 másodperc a cella futtatására.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>A lineáris regressziós modell optimalizálása egyéni keresztérvényesítési és paraméter-seprési kód használatával
Ezután optimalizálja a modellt egyéni kód használatával, és azonosítsa a legjobb modellparamétereket a legnagyobb pontosságú kritérium használatával. Ezután hozza létre a végleges modellt, értékelje ki a modellt a tesztadatokon, és mentse a modellt a Blob storage-ban. Végül töltse be a modellt, pontozási tesztadatokat, és értékelje ki a pontosságot.

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


**Kimeneti:**

61 másodperc a cella futtatására.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Spark-alapú gépi tanulási modellek automatikus felhasználása a Scala segítségével
Az Azure-beli adatelemzési folyamatot alkotó feladatokat ismertető témakörök áttekintését a [Csapatadat-elemzési folyamat című](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)témakörben találja.

[A csapatadat-elemzési folyamat forgatókönyvei](walkthroughs.md) más végpontok között forgatókönyveket is leírnak, amelyek bemutatják a csapatadat-elemzési folyamat lépéseit az adott forgatókönyvekhez. A forgatókönyvek azt is bemutatják, hogyan kombinálhatja a felhőbeli és a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához.

[A Score Spark által készített gépi tanulási modellek](spark-model-consumption.md) bemutatják, hogyan használhatja a Scala-kódot az új adatkészletek automatikus betöltéséhez és pontozásához a Sparkbeépített és az Azure Blob storage-ban mentett gépi tanulási modellekkel. Kövesse az ott megadott utasításokat, és egyszerűen cserélje le a Python-kódot a Scala-kódra ebben a cikkben az automatikus felhasználás érdekében.

