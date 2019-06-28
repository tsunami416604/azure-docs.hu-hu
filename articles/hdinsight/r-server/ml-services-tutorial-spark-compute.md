---
title: 'Oktatóanyag: Használja az R egy Spark számítási környezetet az Azure HDInsight'
description: Oktatóprogram – Ismerkedés az R- és Spark Machine Learning-szolgáltatások.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 244c62467f187417bbb9f0e54173aad5a7d26d0a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450281"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Oktatóanyag: Használja az R egy Spark számítási környezetet az Azure HDInsight

Ebben az oktatóanyagban egy Machine Learning-szolgáltatások Azure HDInsight-fürtön fut egy lépésről lépésre bemutatja az R-függvények használata Apache Spark biztosít.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Mintaadatok letöltése a helyi tároló
> * Adatok másolása az alapértelmezett tároló
> * Adatkészlet beállítása
> * Adatforrás létrehozása
> * A Spark számítási környezet létrehozása
> * Lineáris modellt igazítása
> * Összetett XDF-fájlok használata
> * Átalakítás fürt megosztott Kötetévé XDF

## <a name="prerequisites"></a>Előfeltételek

* Egy Machine Learning-szolgáltatások HDInsight fürt. Lásd: [Apache Hadoop-fürtök létrehozása az Azure portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) válassza **Machine Learning-szolgáltatások** a **fürt típusa**.

## <a name="connect-to-rstudio-server"></a>Csatlakozás az RStudio Serverhez

A fürt határcsomópontjához az RStudio Server fut. Nyissa meg a következő URL-címet, ahol `CLUSTERNAME` a Machine Learning-szolgáltatások létrehozott fürt neve:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Kétszer hitelesíteni kell az első alkalommal jelentkezik be. Az első hitelesítési kérésnél adja meg a fürt rendszergazdai bejelentkezési nevét és jelszavát, az alapértelmezett érték a `admin`. A második hitelesítési kérésnél adja meg a jelszó és SSH-bejelentkezéskor, az alapértelmezett érték a `sshuser`. Későbbi bejelentkezések csak az SSH hitelesítő adatok szükségesek.

## <a name="download-sample-data"></a>Töltse le a mintaadatokat

A *légitársaság 2012 időben adatkészlet* 12 a 2012-es év minden kereskedelmi repülőjáratok belül az Amerikai Egyesült Államok, repülési érkezési és indító részletei vonatkozó információkat tartalmazó vesszővel tagolt fájlból áll. Ez egy olyan big Data típusú adatok a több mint 6 millió megfigyeléseket.

1. Néhány környezeti változók inicializálása. Az RStudio Server konzolon adja meg a következő kódot:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

    A változók alapján a képernyő jobb oldalán jelennek meg a **környezet** fülre.

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

2.  Hozzon létre helyi könyvtárba, és töltse le a mintaadatokat. Az RStudio adja meg a következő kódot:

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

    A letöltés készül 9 és fél percet vesz igénybe.

## <a name="copy-data-to-default-storage"></a>Adatok másolása az alapértelmezett tároló

A HDFS-hely meg van adva a `airDataDir` változó. Az RStudio adja meg a következő kódot:

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

A lépés nagyjából 10 másodpercet vesz igénybe.

## <a name="set-up-data-set"></a>Adatkészlet beállítása

1. Hozzon létre egy fájlt Rendszerobjektum, amely az alapértelmezett értékeket fogja használni. Az RStudio adja meg a következő kódot:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

2. Az eredeti CSV-fájlok helyett nehézkessé változónevek rendelkezik, így a Mi adjuk a `colInfo` lista, így könnyebben kezelhető. Az RStudio adja meg a következő kódot:

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

## <a name="create-data-source"></a>Adatforrás létrehozása

A Spark számítási környezetben a következő függvények használatával adatforrások hozhat létre:

|Függvény | Leírás |
|---------|-------------|
|`RxTextData` | Egy vesszővel tagolt szövegbe adatforrás. |
|`RxXdfData` | Az XDF adatfájlformátumának adatait. A RevoScaleR az XDF fájlformátum hadoop-adatok tárolása egyetlen fájl helyett a fájlok egy összetett készletét módosul. |
|`RxHiveData` | Létrehoz egy Hive-adatforrás-objektum.|
|`RxParquetData` | Egy adatforrás Parquet objektumot állít elő.|
|`RxOrcData` | Az Orc-adatforrás-objektum állít elő.|

Hozzon létre egy [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) objektum HDFS másolt fájlokat használja. Az RStudio adja meg a következő kódot:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-compute-context-for-spark"></a>A Spark számítási környezet létrehozása

Adatok betöltése, és elemzéseket futtathat a feldolgozó csomópontokat, a parancsfájl beállította a számítási környezet [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). Ebben a környezetben R-függvények automatikusan szét a számítási feladat összes a feldolgozó csomópontok, feladatok és a várólista nem beépített igényel. A Spark számítási környezetben keresztül létrejött `RxSpark` vagy `rxSparkConnect()` hozhat létre a Spark számítási környezetben, és használja `rxSparkDisconnect()` térjen vissza a helyi számítási környezettel. Az RStudio adja meg a következő kódot:

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
    
    Ebben a lépésben be kell fejeződnie, 2 és 3 perc között.

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

    Figyelje meg, hogy az eredmények azt jelzik, hogy feldolgozta az összes adatot, 6 millió megfigyelések, a megadott könyvtárban található összes .csv fájl használatával. Figyelje meg, mert a megadott `cube = TRUE`, van egy becsült együttható minden nap, a hét (és nem az intercept).

## <a name="use-composite-xdf-files"></a>Összetett XDF-fájlok használata

Úgy találtuk, elemezheti a hadoop közvetlenül az r nyelv CSV-fájlokat, de az elemzés végezhető gyorsabban ha hatékonyabb formátumban tárolja az adatokat. Az R .xdf formátum rendkívül hatékony, de módosítását valamelyest a HDFS, hogy az egyes fájlok maradjanak HDFS egyetlen blokkot. (A HDFS blokkméret telepítési telepítési változik, de általában akkor 64 MB-os vagy 128 MB.) Használata esetén [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) hadoop, adjon meg egy `RxTextData` adatforrás például `AirDS` a inData, és a egy `RxXdfData` fájlrendszer-adatforrás értékre van állítva egy HDFS-fájlrendszer eredményfájl argumentumaként, létrehozhat egy csoportot összetett .xdf fájlokat. A `RxXdfData` objektumot ezután felhasználhatók a további R-elemzések adatok argumentumaként.

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
    
    Ez a lépés néhány percet vesz igénybe.

1. Új becslés az ugyanazon lineáris modellt, az új és gyorsabb adatforrás használata. Az RStudio adja meg a következő kódot:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    A lépés kevesebb mint egy percet vesz igénybe.

1. Tekintse meg az eredményeket. Az eredményeket a CSV-fájlok viselkedéselemzési azonosnak kell lennie. Az RStudio adja meg a következő kódot:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Átalakítás fürt megosztott Kötetévé XDF

### <a name="in-a-spark-context"></a>Spark-környezetben

Ha a fürt megosztott kötetei szolgáltatás alakítani elemzések futtatása során a hatékonyság kihasználásához, de most szeretné az adatok átalakítás a fürt megosztott Kötetévé XDF használatával teheti [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Hozzon létre egy mappát, vagy CSV-fájlt, először hozzon létre egy `RxTextData` objektumba a könyvtár neve a fájl argumentumként; Ez a mappa, amelyben létrehozza a CSV-fájlokat jelöli. Ez a könyvtár jön létre, amikor futtatja a `rxDataStep`. Ezután mutasson a `RxTextData` objektumának a `outFile` argumentumának a `rxDataStep`. Minden létrehozott CSV kapja a könyvtár neve alapján, és a egy szám követ.

Tegyük fel, hogy szeretnénk írható ki egy mappát a CSV a HDFS-ben a `airDataXdf` összetett XDF azt elvégzése után a logisztikai regressziós és előrejelzési, így az új CSV-fájlok tartalmazzák az előre jelzett értékek és például. Az RStudio adja meg a következő kódot:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Ebben a lépésben nagyjából két és fél percet vesz igénybe.

Figyelje meg, hogy a `rxDataStep` kiírva minden .xdfd fájlt a bemeneti összetett XDF-fájlt egy CSV-t. Ez a fürt megosztott kötetei szolgáltatás összetett XDF az adatraktárba történő írás során HDFS Ha a számítási környezet beállítása alapértelmezett viselkedése `RxSpark`.

### <a name="in-a-local-context"></a>Egy helyi környezetben.

Azt is megteheti, a számítási környezet sikerült váltson vissza a `local` Ha elkészült a elemzések végrehajtása, és kihasználhatja belül két argumentumot `RxTextData` , amelyek lehetővé teszik az Ön valamivel több HDFS-be, a CSV-fájlok írásakor: `createFileSet` és `rowsPerOutFile`. Amikor `createFileSet` értékre van állítva `TRUE`, vagy CSV-fájlt egy mappába íródik a megadott könyvtárban. Amikor `createFileSet` értékre van állítva `FALSE` egy CSV-fájl írása. A második argumentum `rowsPerOutFile`, egy egész számot jelzi, hogy hány sort szeretne írni az egyes CSV-fájl mikor állítható `createFileSet` van `TRUE`.

Az RStudio adja meg a következő kódot:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Ebben a lépésben nagyjából 10 percet vesz igénybe.

Használata esetén egy `RxSpark` számítási környezetet, `createFileSet` alapértelmezés szerint a `TRUE` és `rowsPerOutFile` nem lesz hatása. Így ha hozzon létre egy CSV-t, vagy testre szabhatja a fájlonkénti kell végrehajtania a sorok száma a `rxDataStep` a egy `local` számítási környezet (a adatok továbbra is lehet HDFS-ben).

## <a name="final-steps"></a>Utolsó lépéseit

1. Adatok törlése. Az RStudio adja meg a következő kódot:

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

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Törölje a fürtöt, tekintse meg a [törlése egy HDInsight-fürtön a böngészőben, a PowerShell vagy az Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan az Apache Spark egy Machine Learning-szolgáltatások Azure HDInsight-fürtön futó R-függvények használatához. További információkért tekintse át a következő cikkeket:

* [Számítási környezeti beállítások a HDInsight a Machine Learning-szolgáltatások](r-server-compute-contexts.md)
* [A Spark on Hadoop R függvényekkel](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
