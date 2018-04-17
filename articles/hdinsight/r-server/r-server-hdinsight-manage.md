---
title: A HDInsight - Azure R Server-fürt kezeléséhez |} Microsoft Docs
description: Útmutató az Azure HDInsight egy R Server-fürt kezeléséhez.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 827bcb7bb20f1def9acec8cb2043ea295801583a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="manage-r-server-cluster-on-azure-hdinsight"></a>Az Azure HDInsight R Server-fürt kezeléséhez

Ebből a cikkből megismerheti, hogyan R Server on Azure HDInsight mulitiple egyidejű felhasználók hozzáadása, a távolról csatlakozik egy R Server (Microsoft ML Server) vagy az ügyfél, a számítási környezet módosítása stb hasonló feladatok elvégzéséhez meglévő fürt kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

* **Az R Server-fürt a HDInsight**: útmutatásért lásd: [az R Server on HDInsight használatába](r-server-get-started.md).

* **Secure Shell- (SSH-) ügyfél**: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtathat parancsokat. További információkért lásd: [az SSH a Hdinsighttal.](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Több párhuzamos felhasználó engedélyezése

R Server-fürt a HDInsight több egyidejű felhasználók engedélyezheti a peremhálózati csomópont, amikor az Rstudióból közösségi verzió fut. további felhasználók hozzáadásával. HDInsight-fürt létrehozásakor két felhasználót kell megadnia, egy HTTP-felhasználót és egy SSH-felhasználót:

![1. párhuzamos felhasználó](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Fürt bejelentkezési felhasználóneve**: HTTP-felhasználó a létrehozott HDInsight-fürtöket védő HDInsight-átjárón át történő hitelesítéshez. Ez a HTTP-felhasználó használatos az Ambari, a YARN felhasználói felületének és más felhasználóifelület-összetevőknek az elérésére.
- **Secure Shell- (SSH-) felhasználónév**: SSH-felhasználó, aki a fürtöt biztonságos felületen keresztül éri el. Ez a felhasználó a Linux rendszerben az összes főcsomópont, munkavégző csomópont és élcsomópont felhasználója. Így Secure Shellt használhat a távoli fürt bármely csomópontjának elérésére.

Az R Server-fürt a HDInsight használt R Studio Server közösségi verzió bejelentkezési mechanizmusaként csak Linux felhasználónevet és jelszót fogad el. Nem támogatja a jogkivonatok átadását. Ezért amikor megpróbál hozzáférni az R Studio először egy R Server-fürt, kell bejelentkeznie kétszer.

- Először jelentkezzen be a HDInsight-átjárón keresztül a HTTP-felhasználó hitelesítő adataival. 

- Az SSH-felhasználói hitelesítő adatok segítségével jelentkezzen be Rstudióból.
  
Jelenleg HDInsight-fürt üzembe helyezésekor csak egy SSH felhasználói fiókot lehet létrehozni. Ezért a HDInsight R Server-fürt eléréséhez több felhasználó engedélyezéséhez kell létrehoznia további felhasználók a Linux rendszerben.

Mivel Rstudióból fut, a peremhálózati fürtcsomóponton, több lépésből áll itt:

1. Használja a meglévő SSH felhasználónak be kell jelentkeznie az élcsomóponthoz
2. További Linux-felhasználók hozzáadása az élcsomópontban
3. Az RStudio Community verziójának használata a létrehozott felhasználóval

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>1. lépés: A létrehozott SSH-felhasználó használata az élcsomópontra való bejelentkezéshez

Kövesse az utasításokat, [HDInsight (Hadoop) SSH használatával csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md) élcsomópont eléréséhez. A peremhálózati csomópont cím R Server-fürt a HDInsight `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>2. lépés: További Linux-felhasználók hozzáadása az élcsomópontban

Felhasználó élcsomóponthoz adásához hajtsa végre a következő parancsokat:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user 
    sudo passwd <yournewusername>

Az alábbi képernyőfelvételen látható a kimenetek.

![3. párhuzamos felhasználó](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Amikor a rendszer az „aktuális Kerberos-jelszót” kéri, nyomja meg az **Enter** billentyűt a figyelmen kívül hagyásához. A `useradd` parancs `-m` kapcsolója jelzi, hogy a rendszer létrehoz egy kezdőmappát a felhasználó számára, amely szükséges az RStudio Community verziójához.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>3. lépés: Az RStudio Community verziójának használata a létrehozott felhasználóval

Hozzáférés az Rstudióból https://CLUSTERNAME.azurehdinsight.net/rstudio/. Ha a fürt létrehozása után első alkalommal jelentkezik, adja meg a fürt rendszergazdai hitelesítő adatokat a most létrehozott SSH hitelesítő követ. Ha nem ez az első bejelentkezés, csak adja meg a hitelesítő adatait az SSH-felhasználó hozott létre.

Az eredeti hitelesítő adatokkal (alapértelmezés szerint ez az *sshuser*) egyidejűleg egy másik böngészőablakból is bejelentkezhet.

Figyelje meg azt is, hogy az újonnan felvett felhasználók nem rendelkeznek gyökérjogosultságokkal a Linux rendszerben, de ugyanolyan hozzáférésük van a távoli HDFS- és WASB-tárolón az összes fájlhoz.

## <a name="connect-remotely-to-microsoft-ml-server-or-client"></a>Távoli csatlakozás a Microsoft ML Server vagy az ügyfél

Között állítható be hozzáférés a HDInsight Hadoop Spark számítási környezethez Microsoft ML Server vagy a Microsoft ML Client az asztalon futó távoli példányához. Ehhez meg kell adnia a beállításokat (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches és sshProfileScript) Ha a RxSpark meghatározása számítási környezet az asztalon: például:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

További információkért lásd: a "Használatával Microsoft R Server, a Hadoop-ügyfelének" szakasz [Spark egy számítási környezet létrehozása](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Számítási környezet használata

A számítási környezetekkel vezérelheti, hogy a számítás helyben történik-e az élcsomóponton, vagy elosztottan a HDInsight-fürtben lévő csomópontok között.

1. Az RStudio Serverről vagy az R-konzolról (SSH-munkamenetben) a következő kód használatával töltse be a példaadatokat a HDInsight alapértelmezett tárolójába:

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
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

2. Ezután hozzon létre néhány adat információt, és két adatforrások meghatározása, hogy az adatok dolgozunk.

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

3. A helyi számítási környezetet használ az adatok egy logisztikai regresszió futtatnia.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Az alábbihoz hasonló sorokkal végződő kimenetnek kell megjelennie:

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

4. Futtassa a Spark-környezetet használ az azonos logisztikai regresszió. A Spark környezet elosztja a feldolgozást a HDInsight-fürt összes munkavégző csomópontja között.

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


   > [!NOTE]
   > A MapReduce eszközzel is eloszthatja a számítást a fürtcsomópontok között. A számítási környezetről további információért lásd: [Számítási környezeti beállítások a HDInsighton belüli R Server esetében](r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>R-kód elosztása több csomópontra

Az R Server on HDInsight, a meglévő R-kód érvénybe és futtassa a fürt több csomópontja között használatával `rxExec`. Ez a függvény akkor hasznos, amikor paraméteres frissítést vagy szimulációkat végez. A következő kód az `rxExec` használatának példája:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Ha továbbra is a Spark vagy a MapReduce környezetet használja, ez visszaadja azon munkavégző csomópontok csomópontnév értékét, amelyen a `(Sys.info()["nodename"])` kódot futtatta. Egy négy csomópontból álló fürtön például a következőhöz hasonló kimenetet kaphat:

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"


## <a name="access-data-in-hive-and-parquet"></a>Hozzáférés az adatokhoz a Hive és a Parquet eszközökben

Az R Server 9.1 és újabb verziókban elérhető funkciója lehetővé teszi az adatok közvetlen elérését a Hive és a Parquet eszközökben a Spark számítási környezet ScaleR-függvényei általi használatra. Ezek a képességek az RxHiveData és RxParquetData nevű új ScaleR adatforrás-függvényeken keresztül érhetők el, amelyek a Spark SQL-en keresztül töltenek adatokat közvetlenül a Spark DataFrame-be a ScaleR által végzett elemzéshez.  

A következő kód tartalmazza az új függvények használatának néhány mintakódját:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


További információt ezen új funkciók használatára az online súgójában ML Server használata a `?RxHivedata` és `?RxParquetData` parancsok.  

## <a name="install-additional-r-packages-on-the-cluster"></a>A fürtön telepíteni a további R csomagokat

### <a name="to-install-r-packages-on-the-edge-node"></a>Élcsomópont R csomagok telepítése

Ha azt szeretné, további R csomagok telepítése élcsomópont, használhatja `install.packages()` közvetlenül belül R konzol kapcsolódás után az élcsomóponthoz SSH-n keresztül. 

### <a name="to-install-r-packages-on-the-worker-node"></a>A munkavégző csomóponton R csomagok telepítése

A feldolgozó csomópontokon a fürt R csomagok telepítéséhez a parancsfájlművelet kell használnia. A szkriptműveletek olyan Bash-szkriptek, amelyekkel konfigurációs módosítások végezhetők a HDInsight-fürtön, vagy további szoftverek, például további R-csomagok telepíthetők. 

> [!IMPORTANT]
> A további R csomagok szkriptműveletekkel végzett telepítése csak a fürt létrehozása után használható. Ne használja ezt az eljárást a fürt létrehozása során, mivel a szkript az R Server teljes telepítésétől és konfigurálásától függ.
>
>

1. Kövesse a [parancsfájlművelet-fürtök testreszabása](../hdinsight-hadoop-customize-cluster-linux.md).

3. A **parancsfájlművelet**, adja meg a következő információkat:

   * A **parancsprogram típusa**, jelölje be **egyéni**.

   * A **neve**, adja meg a parancsfájlművelet nevét.

    * A **Bash parancsfájlok URI**, adja meg `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Ez az a parancsfájlt, amely további R csomagokat telepít a munkavégző csomópont

   * Jelölje be a jelölőnégyzetet, csak a **munkavégző**.

   * **Paraméterek**: A telepíteni kívánt R csomagok. Például: `bitops stringr arules`

   * A jelölőnégyzet bejelölésével **parancsfájlműveletet**.  

   > [!NOTE]
   > 1. Alapértelmezés szerint az összes R csomag a telepített R Server verziójának megfelelő Microsoft MRAN tár-pillanatfelvételéből van telepítve. Ha a csomagok újabb verzióját szeretné telepíteni, akkor van némi inkompatibilitási kockázat. Ez a fajta telepítés azonban lehetséges, ha a `useCRAN` parancsot adja meg a csomaglista első elemeként, például: `useCRAN bitops, stringr, arules`.  
   > 2. Néhány R csomag további Linux rendszerű könyvtárakat igényel. A kényelem érdekében előre telepítettük a 100 legnépszerűbb R csomag szükséges függőségeit. Ha azonban a telepített R csomag(ok) további könyvtárakat telepítését igényli(k), akkor le kell töltenie az itt használt alapvető szkriptet, és lépéseket kell hozzáadnia a rendszerkönyvtárak telepítéséhez. Ezután fel kell töltenie a módosított szkriptet egy nyilvános blob-tárolóba az Azure Storage-ben, és a módosított szkripttel kell telepítenie a csomagokat.
   >    A szkriptműveletek fejlesztésével kapcsolatos további információért lásd: [Szkriptművelet fejlesztése](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Szkriptműveletek hozzáadása](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Válassza a **Létrehozás** lehetőséget a szkript futtatásához. A szkript befejezése után az R csomagok elérhetők az összes munkavégző csomóponton.

## <a name="next-steps"></a>További lépések

* [R Server-fürt üzembe helyezése a HDInsighton](r-server-operationalize.md)
* [Számítási környezeti beállítások a HDInsighton belüli R Server-fürt esetében](r-server-compute-contexts.md)
* [Azure Storage-lehetőségek a HDInsighton belüli R Server-fürthöz](r-server-storage.md)
