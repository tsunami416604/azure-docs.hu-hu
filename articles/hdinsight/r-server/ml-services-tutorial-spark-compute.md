---
title: 'Oktatóanyag: Az R használata spark-számítási környezetben az Azure HDInsightban'
description: Oktatóanyag – Az R és a Spark első lépései egy Azure HDInsight Machine Learning-szolgáltatásfürtön.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121940"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Oktatóanyag: Az R használata spark-számítási környezetben az Azure HDInsightban

Ez az oktatóanyag részletesen bemutatja az Azure HDInsight Machine Learning-szolgáltatások fürtjein futó Apache Spark R-függvényeinek használatát.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A mintaadatok letöltése a helyi tárolóba
> * Adatok másolása az alapértelmezett tárolóba
> * Adatkészlet beállítása
> * Adatforrások létrehozása
> * Számítási környezet létrehozása a Spark számára
> * Lineáris modell illesztése
> * Összetett XDF-fájlok használata
> * XDF konvertálása CSV-vé

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure HDInsight Machine Learning-szolgáltatások fürtje. Nyissa meg [az Apache Hadoop-fürtök létrehozása az Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) című lehetőséget, és **fürttípus**esetén válassza az **ML-szolgáltatások**lehetőséget.

## <a name="connect-to-rstudio-server"></a>Csatlakozás az RStudio Serverhez

Az RStudio-kiszolgáló a fürt peremhálózati csomópontján fut. Nyissa meg a következő *CLUSTERNAME* helyet (ahol az URL-címfürtneve a létrehozott HDInsight Machine Learning-szolgáltatások fürtje):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Amikor először jelentkezik be, kétszer hitelesíti magát. Az első hitelesítési kérdésnél adja meg a fürt felügyeleti felhasználónevét és jelszavát (az alapértelmezett a *rendszergazda*). A második hitelesítési kérdésnél adja meg az SSH felhasználónevét és jelszavát (az alapértelmezett érték az *sshuser).* A későbbi bejelentkezések csak az SSH hitelesítő adatokat igényelnek.

## <a name="download-the-sample-data-to-local-storage"></a>A mintaadatok letöltése a helyi tárolóba

A *Airline 2012 On-Time Data Set* 12 vesszővel elválasztott fájlból áll, amelyek tartalmazzák a 2012-es évre vonatkozó, az Egyesült Államokon belüli összes kereskedelmi járat érkezési és indulási adatait. Ez az adatkészlet nagy, több mint 6 millió megfigyeléssel.

1. Néhány környezeti változó inicializálása. Az RStudio Server konzolon adja meg a következő kódot:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. A jobb oldali ablaktáblában válassza a **Környezet** lapot. A változók az **Értékek**területen jelennek meg.

    ![HDInsight R stúdió webkonzol](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Hozzon létre egy helyi könyvtárat, és töltse le a mintaadatokat. Az RStudio mezőbe írja be a következő kódot:

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

    A letöltés nek körülbelül 9,5 perc múlva kell befejeződnie.

## <a name="copy-the-data-to-default-storage"></a>Adatok másolása az alapértelmezett tárolóba

A Hadoop elosztott fájlrendszer (HDFS) helye `airDataDir` a változóval van megadva. Az RStudio mezőbe írja be a következő kódot:

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

A lépésnek körülbelül 10 másodpercen belül be kell fejeződnie.

## <a name="set-up-a-dataset"></a>Adatkészlet beállítása

1. Hozzon létre egy olyan fájlrendszer-objektumot, amely az alapértelmezett értékeket használja. Az RStudio mezőbe írja be a következő kódot:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Mivel az eredeti CSV-fájlok meglehetősen nehézkes változónevekkel rendelkeznek, egy *colInfo* listát kell megadnia, hogy könnyebben kezelhetők legyenek. Az RStudio mezőbe írja be a következő kódot:

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

A Spark számítási környezetben adatforrásokat hozhat létre a következő függvények használatával:

|Függvény | Leírás |
|---------|-------------|
|`RxTextData` | Vesszővel tagolt szöveges adatforrás. |
|`RxXdfData` | Adatok XDF adatfájlformátumban. A RevoScaleR-ben az XDF fájlformátum módosul, hogy a Hadoop az adatokat egyetlen fájl helyett összetett fájlkészletben tárolja. |
|`RxHiveData` | Hive adatforrás-objektumot hoz létre.|
|`RxParquetData` | Parketta adatforrás-objektumot hoz létre.|
|`RxOrcData` | Ork adatforrás-objektumot hoz létre.|

Hozzon létre egy [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) objektumot a HDFS fájlba másolt fájlok használatával. Az RStudio mezőbe írja be a következő kódot:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Számítási környezet létrehozása a Spark számára

Adatok betöltéséhez és a feldolgozói csomópontok elemzésének futtatásához állítsa a számítási környezetet a parancsfájlban [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark)értékre. Ebben a környezetben az R-függvények automatikusan elosztják a munkaterhelést az összes munkavégző csomópont között, és nincs beépített követelmény a feladatok vagy a várólista kezeléséhez. A Spark számítási környezet `RxSpark` jön `rxSparkConnect()` létre, vagy a Spark számítási `rxSparkDisconnect()` környezet létrehozása, és a helyi számítási környezetben való visszatéréshez használja. Az RStudio mezőbe írja be a következő kódot:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Lineáris modell illesztése

1. Az [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) függvénnyel egy lineáris `airDS` modellt illesztheti be az adatforrás használatával. Az RStudio mezőbe írja be a következő kódot:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Ennek a lépésnek 2-3 perc alatt el kell végeznie.

1. Tekintse meg az eredményeket. Az RStudio mezőbe írja be a következő kódot:

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

    Az eredmények azt mutatják, hogy feldolgozta az összes adatot, 6 millió megfigyelést, a megadott könyvtárban lévő összes CSV fájl használatával. Mivel a `cube = TRUE`megadott , van egy becsült együtthatója a hét minden napjára (és nem az elfogásra).

## <a name="use-composite-xdf-files"></a>Összetett XDF-fájlok használata

Mint látható, elemezheti CSV fájlokat közvetlenül R a Hadoop. De gyorsabban elvégezheti az elemzést, ha az adatokat hatékonyabb formátumban tárolja. Az R XDF fájlformátum hatékony, de kissé módosult a HDFS számára, így az egyes fájlok egyetlen HDFS blokkon belül maradnak. (A HDFS-blokk mérete telepítésről telepítésre változik, de általában 64 MB vagy 128 MB.) 

Ha a Hadoop [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) paranccsal hoz létre összetett XDF-fájlokat, meg kell adnia egy adatforrást, `RxTextData` például `AirDS` az inData-t és egy olyan adatforrást, `RxXdfData` amelyben a fileSystem hdfs fájlrendszerre van állítva outFile argumentumként. Ezután az `RxXdfData` objektumot használhatja adatargumentumként a következő R-elemzésekben.

1. Objektum `RxXdfData` definiálása. Az RStudio mezőbe írja be a következő kódot:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Állítsa be a blokk mérete 250000 sor, és adja meg, hogy olvassuk az összes adatot. Az RStudio mezőbe írja be a következő kódot:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importálja az `rxImport`adatokat a használatával. Az RStudio mezőbe írja be a következő kódot:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Ennek a lépésnek néhány perc alatt be kell fejeződnie.

1. Az új, gyorsabb adatforrás használatával újra kell becsülni ugyanazt a lineáris modellt. Az RStudio mezőbe írja be a következő kódot:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    A lépésnek egy percen belül be kell fejeződnie.

1. Tekintse meg az eredményeket. Az eredményeknek meg kell egyeznek a CSV-fájlok eredményeivel. Az RStudio mezőbe írja be a következő kódot:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>XDF konvertálása CSV-vé

### <a name="in-a-spark-context"></a>Spark-környezetben

Ha a CSV-fájlokat XDF fájlformátumra konvertálta az elemzések futtatása közben, de most vissza szeretné konvertálni az adatokat CSV-re, ezt az [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep)használatával teheti meg.

A CSV-fájlok mappájának létrehozásához először hozzon létre egy `RxTextData` objektumot egy könyvtárnév használatával fájlargumentumként. Ez az objektum azt a mappát jelöli, amelyben a CSV-fájlokat létre lehet hozni. Ez a könyvtár a `rxDataStep`. Ezután mutasson `RxTextData` erre `outFile` az objektumra a `rxDataStep`argumentumban. Minden létrehozott CSV a könyvtár neve alapján kerül elnevezésre, majd egy szám alapján.

Tegyük fel, hogy a logisztikus regresszió és előrejelzés végrehajtása után ki szeretne írni egy mappát a HDFS-ben lévő CSV-fájlokból az `airDataXdf` összetett XDF-ből, hogy az új CSV-fájlok tartalmazzák az előre jelzett értékeket és maradékokat. Az RStudio mezőbe írja be a következő kódot:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Ennek a lépésnek körülbelül 2,5 perc alatt el kell végeznie.

A `rxDataStep` írt ki egy CSV fájlt minden XDFD fájlt a bemeneti kompozit XDF fájlt. Ez az alapértelmezett viselkedés a CSV-fájlok kompozit XDF-fájlokból HDFS `RxSpark`fájlba írásához, ha a számítási környezet a értékre van állítva.

### <a name="in-a-local-context"></a>Helyi környezetben

Azt is megteheti, hogy ha végzett az elemzések elvégzésével, `local` visszaválthat a számítási `RxTextData` környezetre, hogy kihasználja a két argumentumot, amelyek `createFileSet` `rowsPerOutFile`kissé nagyobb ellenőrzést biztosítanak a CSV-fájlok HDFS-re való írásakor: és . Ha a `createFileSet` `TRUE`beállítás , a CSV-fájlok egy mappája a megadott könyvtárba kerül. Ha a `createFileSet` `FALSE`beállítás , egyetlen CSV-fájl van megírva. A második argumentumot `rowsPerOutFile`egész számra állíthatja, jelezve, hogy hány sort `createFileSet` `TRUE`kell írni az egyes CSV-fájlokba, ha a .

Az RStudio mezőbe írja be a következő kódot:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Ennek a lépésnek körülbelül 10 perc alatt el kell végeznie.

Ha számítási `RxSpark` környezetet használ, `createFileSet` alapértelmezés `TRUE` `rowsPerOutFile` szerint nincs hatása. Ezért ha egyetlen CSV-t szeretne létrehozni, vagy fájlonkénti sorok számát szeretné testreszabni, végezze el `rxDataStep` `local` a feladatot egy számítási környezetben (az adatok továbbra is hdfs-ben lehetnek).

## <a name="final-steps"></a>Utolsó lépések

1. Tisztítsa meg az adatokat. Az RStudio mezőbe írja be a következő kódot:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Állítsa le a távoli Spark-alkalmazást. Az RStudio mezőbe írja be a következő kódot:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Lépjen ki az R munkamenetből. Az RStudio mezőbe írja be a következő kódot:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat számítunk fel, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor nagyobbak, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

Fürt törléséről a [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával című](../hdinsight-delete-cluster.md)témakörben jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az R-függvényeket az Apache Sparkban, amelyek HDInsight Machine Learning-szolgáltatások fürtjein futnak. További információkért tekintse át a következő cikkeket:

* [Az Azure HDInsight Machine Learning-szolgáltatások fürtjének környezeti beállításainak kiszámítása](r-server-compute-contexts.md)
* [R függvények a Sparkhoz a Hadoopon](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
