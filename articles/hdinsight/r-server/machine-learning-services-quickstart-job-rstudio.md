---
title: 'Rövid útmutató: Az RStudio Server & ML-szolgáltatások R-hez – Azure HDInsight'
description: A rövid útmutatóban egy R-parancsfájlt hajt végre egy ML Services-fürtön az Azure HDInsightban az RStudio Server használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 8a6a204ee5080e3acf99c13ecba1e1c7664d68b4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73241882"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Rövid útmutató: R-parancsfájl végrehajtása ml-szolgáltatások fürtén az Azure HDInsightban az RStudio Server használatával

Ml-szolgáltatások az Azure HDInsight lehetővé teszi, hogy az R-parancsfájlok az Apache Spark és az Apache Hadoop MapReduce használatával futtatják az elosztott számításokat. Az ML-szolgáltatások a számítási környezet beállításával szabályozzák a hívások végrehajtását. A fürt peremhálózati csomópontja kényelmes helyet biztosít a fürthöz való csatlakozáshoz és az R-parancsfájlok futtatásához. Egy peremhálózati csomópont esetén lehetősége van a RevoScaleR párhuzamos elosztott függvényeinek futtatására a peremhálózati csomópont-kiszolgáló magjain. A RevoScaleR Hadoop-térképcsökkentés vagy az Apache Spark számítási környezeteinek használatával is futtathatja őket a fürt csomópontjain.

Ebben a rövid útmutatóban megtudhatja, hogyan futtathat r-parancsfájlt az RStudio-kiszolgálóval, amely bemutatja a Spark használatát az elosztott R-számításokhoz. Számítási környezetet határoz meg, hogy helyileg hajtson végre számításokat egy peremhálózati csomóponton, és ismét elosztva a HDInsight-fürt csomópontjai között.

## <a name="prerequisite"></a>Előfeltétel

Ml services-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon,](../hdinsight-hadoop-create-linux-clusters-portal.md) és válassza az **ML-szolgáltatások** **fürttípushoz lehetőséget.**

## <a name="connect-to-rstudio-server"></a>Csatlakozás az RStudio Serverhez

Az RStudio-kiszolgáló a fürt peremhálózati csomópontján fut. Nyissa meg a `CLUSTERNAME` következő URL-címet, ahol a létrehozott ML-szolgáltatások fürt neve található:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Az első bejelentkezéskor kétszer kell hitelesítenie magát. Az első hitelesítési kérdés, adja meg a fürt `admin`rendszergazdai bejelentkezési és jelszó, az alapértelmezett . A második hitelesítési kérdés, adja meg az SSH bejelentkezési és jelszó, az `sshuser`alapértelmezett . A későbbi bejelentkezések csak az SSH hitelesítő adatokat igényelnek.

A csatlakozást követően a képernyőnek a következőhöz hasonlóan kell kinéznie:

![R studio webkonzol áttekintése](./media/ml-services-quickstart-job-rstudio/connect-to-r-studio1.png)

## <a name="use-a-compute-context"></a>Számítási környezet használata

1. Az RStudio Server kiszolgálóról a következő kód használatával töltse be a példaadatokat a HDInsight alapértelmezett tárolójába:

    ```RStudio
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Ez a lépés körülbelül 8 percet is igénybe vehet.

1. Hozzon létre néhány adatinformációt, és határozzon meg két adatforrást. Írja be a következő kódot az RStudio-ban:

    ```RStudio
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Logisztikai regressziót futtataz adatokon a **helyi** számítási környezet használatával. Írja be a következő kódot az RStudio-ban:

    ```RStudio
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    A számítások körülbelül 7 perc alatt befejeződnek. A következő kódrészlethez hasonló vonalakkal végződő kimenetnek kell megtekintenie:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Futtassa ugyanazt a logisztikai regressziót a **Spark-környezet** használatával. A Spark környezet elosztja a feldolgozást a HDInsight-fürt összes munkavégző csomópontja között. Írja be a következő kódot az RStudio-ban:

    ```RStudio
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    A számítások nak körülbelül 5 perc alatt befejeződnek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Fürt törléséről a [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával című](../hdinsight-delete-cluster.md)témakörben jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan futtathat R-parancsfájlt az RStudio Server rel, amely a Spark használatát mutatta be az elosztott R-számításokhoz.  A következő cikkre lépve ismerje meg, hogy milyen lehetőségek állnak rendelkezésre annak meghatározásához, hogy a végrehajtás párhuzamos-e a peremhálózati csomópont vagy a HDInsight-fürt magjai között, és ha az okat.

> [!div class="nextstepaction"]
>[Az ML-szolgáltatások környezeti beállításainak számítási környezete a HDInsightban](./r-server-compute-contexts.md)

> [!NOTE]
> Ez az oldal az RStudio szoftver funkcióit ismerteti. A Microsoft Azure HDInsight nem áll kapcsolatban az RStudio, Inc. vállalattal.
