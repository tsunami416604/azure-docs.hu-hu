---
title: 'Oktatóanyag: Használja az R egy Spark számítási környezetet az Azure HDInsight'
description: Oktatóanyag – első lépések az R- és Spark az Azure HDInsight, Machine Learning-szolgáltatások fürtön.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: f072b6905881da7b7854b0e51d690dbbd40dffb5
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227443"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Oktatóanyag: Használja az R egy Spark számítási környezetet az Azure HDInsight

Ez az oktatóanyag ismerteti a lépésről lépésre bemutatja a R funkciókkal, az Apache Spark, amely egy Azure HDInsight, Machine Learning szolgáltatások a fürtön futnak.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Töltse le a mintaadatokat a helyi tároló
> * Az adatok másolása az alapértelmezett tároló
> * Egy adatkészlet beállítása
> * Adatforrások létrehozása
> * A Spark számítási környezet létrehozása
> * Lineáris modellt igazítása
> * Összetett XDF-fájlok használata
> * Átalakítás fürt megosztott Kötetévé XDF

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure HDInsight, Machine Learning-szolgáltatások fürt. Lépjen a [Apache Hadoop-fürtök létrehozása az Azure portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) és a **fürt típusa**válassza **ML szolgáltatásokkal**.

## <a name="connect-to-rstudio-server"></a>Csatlakozás az RStudio Serverhez

A fürt határcsomópontjához az RStudio Server fut. Nyissa meg a következő hely (ahol *CLUSTERNAME* az URL-cím van a HDInsight, Machine Learning-szolgáltatások létrehozott fürt neve):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Az első alkalommal jelentkezik be, hitelesítést kétszer. : Az első hitelesítési kérésnél adja meg a fürt rendszergazdai felhasználónevét és jelszavát (az alapértelmezett érték *rendszergazdai*). A hitelesítés második adatkéréskor adja meg az SSH-felhasználónevet és jelszót (az alapértelmezett érték *sshuser*). Későbbi bejelentkezések csak az SSH hitelesítő adatok szükségesek.

## <a name="download-the-sample-data-to-local-storage"></a>Töltse le a mintaadatokat a helyi tároló

A *légitársaság 2012 időben adatkészlet* 12 a 2012-es év repülési érkezési és indító részletei az Egyesült Államok belül minden kereskedelmi repülőjáratok tartalmazó vesszővel tagolt fájlok áll. Ez az adatkészlet túl nagy, a több mint 6 millió megfigyeléseket.

1. Néhány környezeti változók inicializálása. Az RStudio Server konzolon adja meg a következő kódot:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. A jobb oldali ablaktáblában jelölje ki a **környezet** fülre. A változók szakaszban látható **értékek**.

    ![Az RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

1.  Hozzon létre egy helyi könyvtárba, és töltse le a mintaadatokat. Az RStudio adja meg a következő kódot:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    A letöltés teljes készül 9,5 percek alatt kell lennie.

## <a name="copy-the-data-to-default-storage"></a>Az adatok másolása az alapértelmezett tároló

A Hadoop elosztott fájlrendszer (HDFS) hely meg van adva a `airDataDir` változó. Az RStudio adja meg a következő kódot:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

A lépés el kell végezni a nagyjából 10 másodpercet.

## <a name="set-up-a-dataset"></a>Egy adatkészlet beállítása

1. Hozzon létre egy fájlt Rendszerobjektum, amely az alapértelmezett értékeket fogja használni. Az RStudio adja meg a következő kódot:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Mivel az eredeti CSV-fájlok helyett nehézkessé változónevek rendelkeznek, Önnek kell letöltenie a *colInfo* lista, így könnyebben kezelhető. Az RStudio adja meg a következő kódot:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Adatforrások létrehozása

A Spark számítási környezetben a következő függvények használatával hozhat létre adatforrások:

|Függvény | Leírás |
|---------|-------------|
|`RxTextData` | Egy vesszővel tagolt szövegbe adatforrás. |
|`RxXdfData` | Az XDF adatfájlformátumának adatait. A RevoScaleR az XDF fájlformátum hadoop-adatok tárolása egyetlen fájl helyett a fájlok egy összetett készletét módosul. |
|`RxHiveData` | Létrehoz egy Hive-adatforrás-objektum.|
|`RxParquetData` | Egy adatforrás Parquet objektumot állít elő.|
|`RxOrcData` | Az Orc-adatforrás-objektum állít elő.|

Hozzon létre egy [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) objektum a HDFS másolt fájlok használatával. Az RStudio adja meg a következő kódot:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>A Spark számítási környezet létrehozása

Adatok betöltése, és elemzéseket futtathat a feldolgozó csomópontokat, a parancsfájl beállította a számítási környezet [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). Ebben a környezetben R-függvények automatikusan szét a számítási feladat összes a feldolgozó csomópontok, feladatok és a várólista nem beépített igényel. A Spark számítási környezetben keresztül létrejött `RxSpark` vagy `rxSparkConnect()` hozhat létre a Spark számítási környezetben, és használ `rxSparkDisconnect()` térjen vissza a helyi számítási környezettel. Az RStudio adja meg a következő kódot:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Lineáris modellt igazítása

1. Használja a [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) egy lineáris modellt az megfelelően működik az `airDS` adatforrás. Az RStudio adja meg a következő kódot:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Ezt a lépést el kell végezni a 2-3 percig.

1. Tekintse meg az eredményeket. Az RStudio adja meg a következő kódot:

    ```R
    summary(delayArr)
    ```

    A következő eredményeknek kell megjelennie:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Az eredmények jelzik, feldolgozta-e már az összes adatot, 6 millió megfigyelések, a megadott könyvtárban CSV-fájlok használatával. Mivel a megadott `cube = TRUE`, minden nap, a hét (és nem az intercept) egy becsült együttható rendelkezik.

## <a name="use-composite-xdf-files"></a>Összetett XDF-fájlok használata

Hogy megismerte, elemezhetők a hadoop közvetlenül az r nyelv CSV-fájlok. Azonban lehetőség van az elemzés gyorsabban ha hatékonyabb formátumban tárolja az adatokat. Az R XDF fájlformátum hatékony, de azt módosította valamelyest a HDFS, hogy az egyes fájlok maradjanak HDFS egyetlen blokkot. (A HDFS blokkméret telepítési telepítési változik, de általában akkor 64 MB-os vagy 128 MB.) 

Használata esetén [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) a Hadoop-hozhat létre olyan összetett XDF-fájlok készlete, adja meg egy `RxTextData` adatforrás például `AirDS` a inData, és a egy `RxXdfData` fájlrendszer-adatforrás beállítása egy HDFS-fájlrendszer, a Eredményfájl argumentum. Ezután a `RxXdfData` objektumot használ az adatok argumentum, a további R-elemzések.

1. Adja meg egy `RxXdfData` objektum. Az RStudio adja meg a következő kódot:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Állítsa be a blokkméret 250000 sort, és adja meg, hogy beolvasnia az összes adatot. Az RStudio adja meg a következő kódot:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importálja az adatokat az `rxImport`. Az RStudio adja meg a következő kódot:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Ezt a lépést el kell végezni néhány perc múlva.

1. Új becslés az ugyanazon lineáris modellt, az új és gyorsabb adatforrás használata. Az RStudio adja meg a következő kódot:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    A lépés el kell végezni, kevesebb mint egy perc alatt.

1. Tekintse meg az eredményeket. Az eredményeket a CSV-fájlok viselkedéselemzési azonosnak kell lennie. Az RStudio adja meg a következő kódot:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Átalakítás fürt megosztott Kötetévé XDF

### <a name="in-a-spark-context"></a>Spark-környezetben

Ha az elemzések, de az adatok átalakítás a fürt megosztott Kötetévé most szeretne futtatása során a nagyobb hatékonyság XDF fájl formátumra alakítja át a CSV-fájlokat, megteheti a [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Hozzon létre egy mappát, vagy CSV-fájlt, először hozzon létre egy `RxTextData` objektum könyvtár nevét használják, mint a fájl argumentum. Ez az objektum a mappát, amelybe a CSV-fájlok létrehozása jelöli. Ez a könyvtár jön létre, amikor futtatja a `rxDataStep`. Ezután mutasson a `RxTextData` objektumának a `outFile` argumentumának a `rxDataStep`. Minden létrehozott CSV neve a könyvtár neve alapján, és a egy szám követ.

Tegyük fel, hogy szeretné-e írható ki egy mappába vagy CSV-fájlt a HDFS-ben a `airDataXdf` összetett XDF végrehajtása a logisztikai regressziós és előrejelzési, így az új CSV-fájlok tartalmazzák az előre jelzett értékek és például után. Az RStudio adja meg a következő kódot:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Ezt a lépést el kell végezni, körülbelül 2,5 percek alatt.

A `rxDataStep` kiírva minden XDFD fájlt a bemeneti összetett XDF-fájlt egy CSV-fájl. Ez a CSV-fájlok összetett XDF-fájlok az adatraktárba történő írás során HDFS Ha a számítási környezet értékre van állítva az alapértelmezett viselkedése `RxSpark`.

### <a name="in-a-local-context"></a>Egy helyi környezetben.

Azt is megteheti, ha befejezte az elemzést végez, a számítási környezet sikerült váltson vissza a `local` kihasználásához belül két argumentumot `RxTextData` , amely biztosítanak valamivel nagyobb mértékű HDFS-be CSV-fájlok kiírni: `createFileSet` és `rowsPerOutFile`. Ha `createFileSet` való `TRUE`, vagy CSV-fájlt egy mappába íródik a megadott könyvtárban. Ha `createFileSet` való `FALSE`, egy CSV-fájl írása. Beállíthatja, hogy a második argumentum `rowsPerOutFile`, annak jelzésére, hogy hány sort szeretne írni az egyes CSV-fájl mikor egész `createFileSet` van `TRUE`.

Az RStudio adja meg a következő kódot:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Ezt a lépést el kell végezni, körülbelül 10 perc múlva.

Ha egy `RxSpark` számítási környezetet, `createFileSet` alapértelmezés szerint a `TRUE` és `rowsPerOutFile` nem lesz hatása. Ezért, ha azt szeretné, hozzon létre egy CSV-t, vagy testre szabhatja a fájlonkénti sorok száma, hajtsa végre `rxDataStep` a egy `local` számítási környezet (a adatok továbbra is lehetnek HDFS-ben).

## <a name="final-steps"></a>Utolsó lépéseit

1. Az adatok törlése. Az RStudio adja meg a következő kódot:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Állítsa le a távoli Spark-alkalmazás. Az RStudio adja meg a következő kódot:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Lépjen ki az R-munkamenet. Az RStudio adja meg a következő kódot:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes törölni a fürtöt. HDInsight az adatok az Azure Storage tárolja, akkor nyugodtan törölheti az fürt, ha nem használja. Akkor is díjkötelesek egy HDInsight-fürtöt, még ha nem használja. Mivel a fürt díjai sokszor több, mint a storage díjai, logikus gazdasági fürtök törlése, ha azok még nem használja.

Törölje a fürtöt, tekintse meg a [egy HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan használhatja az R a functions az Apache Spark, amely egy HDInsight, Machine Learning-szolgáltatások fürtön futnak. További információkért tekintse át a következő cikkeket:

* [Számítási környezeti beállítások egy Azure HDInsight, Machine Learning-szolgáltatások fürt esetében](r-server-compute-contexts.md)
* [A Spark on Hadoop R függvényekkel](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
