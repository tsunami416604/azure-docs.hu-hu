---
title: 'Gyors útmutató: Az R-szkriptek végrehajtása az ML szolgáltatásokkal-fürtön az Azure HDInsight, R-konzol használatával'
description: A rövid útmutatóhoz, hajtsa végre az R-szkriptek egy Machine Learning-szolgáltatások-fürtön az Azure HDInsight, R-konzol használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 682ee4f44dcdd2619668645fa7a8aa22cb645273
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450925"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>Gyors útmutató: Az R-szkriptek végrehajtása az ML szolgáltatásokkal-fürtön az Azure HDInsight, R-konzol használatával

Machine Learning-szolgáltatások az Azure HDInsight lehetővé teszi, hogy az R-szkriptek használata Apache Spark és Apache Hadoop MapReduce eszközökkel elosztott számításokat futtathassanak. Machine Learning-szolgáltatások a hívások a számítási környezet beállításával végrehajtásának módját szabályozza. A fürt élcsomópontjához kényelmes megoldás az R-szkriptek futtatása és a fürthöz való csatlakozáshoz. Az élcsomópont lehetősége van a futó párhuzamos működésű elosztott funkcióit RevoScaleR a Processzormagok száma, a peremhálózati kiszolgáló között. Is futtathatja őket a fürt csomópontjai között RevoScaleR a Hadoop Mapreduce használatával, vagy az Apache Spark számítási környezetek.

Ez a rövid útmutatóban megismerheti, hogyan futtathat egy R-szkriptet az R-konzol, amely bemutatja a Spark elosztott R számításokhoz való használatával. Számítási környezetek használatával az élcsomóponti operacionalizáláshoz helyileg hajtsa végre a számítások meghatározzuk, és újra a HDInsight-fürt csomópontjain elosztva.

## <a name="prerequisites"></a>Előfeltételek

* Egy Machine Learning-szolgáltatások HDInsight fürt. Lásd: [Apache Hadoop-fürtök létrehozása az Azure portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) válassza **Machine Learning-szolgáltatások** a **fürt típusa**.

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="connect-to-r-console"></a>Csatlakozhat az R-konzol

1. Csatlakozás az élcsomóponthoz, egy Machine Learning-szolgáltatások HDInsight-fürthöz SSH használatával. Az alábbi parancsot szerkesztése lecserélésével `CLUSTERNAME` a fürt nevét és adja meg a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetből a következő paranccsal indíthatja el az R-konzolt:

    ```
    R
    ```

    A kimenetben egyéb információk mellett az ML Server verziója is megjelenik.


## <a name="use-a-compute-context"></a>Számítási környezet használata

1. A `>` adatkérésben beírhatja az R-kódot. A következő kód használatával töltse be az alapértelmezett tároló a példaadatokat HDInsight:

    ```R
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

    Ebben a lépésben körülbelül 10 percet vehet végrehajtásához.

1. Hozzon létre adatinformációkat, és határozzon meg két adatforrást. Adja meg az R-konzolról az alábbi kódot:

    ```R
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

1. Futtasson logisztikai regressziót az adatokat az keresztül a **helyi** számítási környezetet. Adja meg az R-konzolról az alábbi kódot:

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    A számítások körülbelül 7 percet vesz igénybe. Az alábbi kódrészlethez hasonló sorokkal végződő kimenetnek kell megjelennie:

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

1. Futtatás a logisztikai regressziós használatával a **Spark** környezetben. A Spark környezet elosztja a feldolgozást a HDInsight-fürt összes munkavégző csomópontja között. Adja meg az R-konzolról az alábbi kódot:

    ```R
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

    A számítások körülbelül 5 percet vesz igénybe.

1. Ha ki szeretne lépni az R-konzolból, használja az alábbi parancsot:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a gyors üzembe helyezéssel, érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Törölje a fürtöt, tekintse meg a [törlése egy HDInsight-fürtön a böngészőben, a PowerShell vagy az Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóból megtudhatta, hogyan futtathat egy R-szkript R-konzolról, ahogyan a Spark elosztott R számításokhoz való használatával.  Folytassa a következő cikkben talál a beállításokat, adja meg, hogyan végrehajtási párhuzamosíthatók-e között a peremhálózati csomópont vagy a HDInsight-fürt magok számára elérhető.

> [!div class="nextstepaction"]
>[Számítási környezeti beállítások a HDInsight a Machine Learning-szolgáltatások](./r-server-compute-contexts.md)