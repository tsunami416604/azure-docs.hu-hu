---
title: Ml Services-fürt kezelése a HDInsightban – Azure
description: Ismerje meg, hogyan kezelheti a különböző feladatokat az ML Services-fürtön az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: b2c16c27c0dfc0c30a99c52544cc4d2278eadfc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647730"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Ml Services-fürt kezelése az Azure HDInsightban

Ebben a cikkben megtudhatja, hogyan kezelheti az Azure HDInsight meglévő ML Services-fürtjét olyan feladatok elvégzéséhez, mint például több egyidejű felhasználó hozzáadása, távoli csatlakozás egy ML Services-fürthöz, a számítási környezet módosítása stb.

## <a name="prerequisites"></a>Előfeltételek

* Ml services-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon,](../hdinsight-hadoop-create-linux-clusters-portal.md) és válassza az **ML-szolgáltatások** **fürttípushoz lehetőséget.**

* Secure Shell- (SSH-) ügyfél: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtathat parancsokat. További információ: [SSH használata a HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md). .

## <a name="enable-multiple-concurrent-users"></a>Több párhuzamos felhasználó engedélyezése

Több egyidejű felhasználót engedélyezhet az ML Services-fürthöz a HDInsight-on, ha további felhasználókat ad hozzá ahhoz a peremhálózati csomóponthoz, amelyen az RStudio közösségi verziója fut. HDInsight-fürt létrehozásakor két felhasználót kell megadnia, egy HTTP-felhasználót és egy SSH-felhasználót:

![HDI Azure Portal bejelentkezési paraméterek](./media/r-server-hdinsight-manage/hdi-concurrent-users1.png)

- **Fürt bejelentkezési felhasználóneve**: HTTP-felhasználó a létrehozott HDInsight-fürtöket védő HDInsight-átjárón át történő hitelesítéshez. Ez a HTTP-felhasználó az Apache Ambari UI, az Apache Hadoop YARN UI, valamint más felhasználói felületi összetevők elérésére szolgál.
- **Secure Shell- (SSH-) felhasználónév**: SSH-felhasználó, aki a fürtöt biztonságos felületen keresztül éri el. Ez a felhasználó a Linux rendszerben az összes főcsomópont, munkavégző csomópont és élcsomópont felhasználója. Így Secure Shellt használhat a távoli fürt bármely csomópontjának elérésére.

A HDInsight ML Services fürtjében használt R Studio Server Community verzió csak Linux-felhasználónevet és -jelszót fogad el bejelentkezési mechanizmusként. Nem támogatja a jogkivonatok átadását. Így amikor először próbál hozzáférni az R Studio-hoz egy ML Services-fürtön, kétszer kell bejelentkeznie.

- Először jelentkezzen be a HTTP-felhasználói hitelesítő adatok használatával a HDInsight-átjárón keresztül.

- Ezután az SSH felhasználói hitelesítő adatok segítségével jelentkezzen be az RStudio.Then use the SSH user credentials to sign in to RStudio.
  
Jelenleg HDInsight-fürt üzembe helyezésekor csak egy SSH felhasználói fiókot lehet létrehozni. Így ahhoz, hogy több felhasználó is hozzáférhessen az ML Services fürthöz a HDInsight-on, további felhasználókat kell létrehoznia a Linux rendszerben.

Mivel az RStudio a fürt peremhálózati csomópontján fut, itt több lépésben is megjelenik:

1. A meglévő SSH-felhasználó használatával jelentkezzen be a peremhálózati csomópontba
2. További Linux-felhasználók hozzáadása az élcsomópontban
3. Az RStudio Community verziójának használata a létrehozott felhasználóval

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>1. lépés: A létrehozott SSH-felhasználó használatával jelentkezzen be a peremhálózati csomópontba

Kövesse a [Connect to HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md) az SSH használatával a peremhálózati csomópont eléréséhez kövesse az utasításokat. A HDInsight ML Services-fürt peremhálózati csomópontcíme `CLUSTERNAME-ed-ssh.azurehdinsight.net`a.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>2. lépés: További Linux-felhasználók hozzáadása az élcsomópontban

Felhasználó élcsomóponthoz adásához hajtsa végre a következő parancsokat:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

A következő képernyőkép a kimeneteket mutatja.

![képernyőkép kimeneti egyidejű felhasználók](./media/r-server-hdinsight-manage/hdi-concurrent-users2.png)

Amikor a rendszer a "Jelenlegi Kerberos jelszó:" kifejezést kéri, csak nyomja le az **Enter** billentyűt, hogy figyelmen kívül hagyja. A `useradd` parancs `-m` kapcsolója jelzi, hogy a rendszer létrehoz egy kezdőmappát a felhasználó számára, amely szükséges az RStudio Community verziójához.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>3. lépés: Az RStudio Community verziójának használata a létrehozott felhasználóval

Az RStudio `https://CLUSTERNAME.azurehdinsight.net/rstudio/`elérése a programból. Ha a fürt létrehozása után először jelentkezik be, adja meg a fürt felügyeleti hitelesítő adatait, majd a létrehozott SSH-felhasználói hitelesítő adatokat. Ha nem ez az első bejelentkezés, csak a létrehozott SSH-felhasználó hitelesítő adatait adja meg.

Az eredeti hitelesítő adatokkal is bejelentkezhet (alapértelmezés szerint *sshuser)* egy másik böngészőablakból.

Figyelje meg azt is, hogy az újonnan felvett felhasználók nem rendelkeznek gyökérjogosultságokkal a Linux rendszerben, de ugyanolyan hozzáférésük van a távoli HDFS- és WASB-tárolón az összes fájlhoz.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Csatlakozás távolról a Microsoft ML-szolgáltatásokhoz

Beállíthatja a HDInsight Spark számítási környezethez való hozzáférést az asztalon futó ML-ügyfél távoli példányából. Ehhez meg kell adnia a beállításokat (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches, és sshProfileScript) az RxSpark számítási környezet ének meghatározásakor az asztalon: Például:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
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

További információt a [RevoScaleR használata Apache Spark-alapú számítási környezetben](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) című, "A Microsoft Machine Machine Learning Server használata Apache Hadoop-ügyfélként" című szakaszban talál.

## <a name="use-a-compute-context"></a>Számítási környezet használata

A számítási környezetekkel vezérelheti, hogy a számítás helyben történik-e az élcsomóponton, vagy elosztottan a HDInsight-fürtben lévő csomópontok között.  Az RStudio Server számítási környezetének beállítását lásd: [R-parancsfájl végrehajtása egy ML Services-fürtön az Azure HDInsightban az RStudio Server használatával című témakörben.](machine-learning-services-quickstart-job-rstudio.md)

## <a name="distribute-r-code-to-multiple-nodes"></a>R-kód elosztása több csomópontra

A HDInsight ML-szolgáltatásaival a meglévő R-kódot a fürt több csomópontján `rxExec`is futtathatja a használatával. Ez a függvény akkor hasznos, amikor paraméteres frissítést vagy szimulációkat végez. A következő kód az `rxExec` használatának példája:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Ha továbbra is a Spark-környezetet használja, ez a parancs visszaadja a `(Sys.info()["nodename"])` kód futtatásához használt munkavégző csomópontok csomópontnév értékét. Egy négycsomós fürtön például a következő kódrészlethez hasonló kimenet várható:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Hozzáférés az Apache Hive és a Parketta adataihoz

A HDInsight ML-szolgáltatások közvetlen hozzáférést biztosítanak a Hive-ben és a Parquet-ben lévő adatokhoz a ScaleR-függvények számára a Spark számítási környezetben. Ezek a képességek az RxHiveData és RxParquetData nevű új ScaleR adatforrás-függvényeken keresztül érhetők el, amelyek a Spark SQL-en keresztül töltenek adatokat közvetlenül a Spark DataFrame-be a ScaleR által végzett elemzéshez.

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


Az új függvények használatával kapcsolatos további információkért tekintse meg az `?RxHivedata` `?RxParquetData` ML-szolgáltatások online súgóját a és a parancsok használatával.  

## <a name="install-additional-r-packages-on-the-cluster"></a>További R-csomagok telepítése a fürtre

### <a name="to-install-r-packages-on-the-edge-node"></a>R csomagok telepítése a peremcsomóponton

Ha további R-csomagokat szeretne telepíteni a peremhálózati `install.packages()` csomópontra, akkor közvetlenül az R konzolon belül is használhatja, miután az SSH-n keresztül csatlakozott a peremcsomóponthoz. 

### <a name="to-install-r-packages-on-the-worker-node"></a>R-csomagok telepítése a munkavégző csomópontra

Ha R-csomagokat szeretne telepíteni a fürt munkavégző csomópontjaira, parancsfájlműveletet kell használnia. A szkriptműveletek olyan Bash-szkriptek, amelyekkel konfigurációs módosítások végezhetők a HDInsight-fürtön, vagy további szoftverek, például további R-csomagok telepíthetők. 

> [!IMPORTANT]  
> A további R csomagok szkriptműveletekkel végzett telepítése csak a fürt létrehozása után használható. Ne használja ezt az eljárást a fürt létrehozása során, mivel a parancsfájl az ML-szolgáltatások teljes konfigurálásán alapul.

1. Kövesse a [Fürtök testreszabása parancsfájlművelettel](../hdinsight-hadoop-customize-cluster-linux.md)című csoport lépéseit.

3. A **Parancsfájl küldése művelethez**adja meg a következő információkat:

   * A **Parancsfájltípus mezőben**válassza az **Egyéni**lehetőséget.

   * A **Name (Név)** mezőbe adja meg a parancsfájlművelet nevét.

     * A **Bash parancsfájl URI-ja**mezőbe írja be a ( bash parancsfájl URI) mezőbe. `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh` Ez az a parancsfájl, amely további R-csomagokat telepít a munkavégző csomópontra

   * Csak a **Worker**jelölőnégyzet bejelölése.

   * **Paraméterek**: A telepíteni kívánt R csomagok. Például: `bitops stringr arules`

   * Jelölje be a **parancsfájlművelet megőrzése jelölőnégyzetet.**  

   > [!NOTE]
   > 1. Alapértelmezés szerint az összes R-csomag a Microsoft MRAN-tárház pillanatképéből történik, amely összhangban van a telepített ML-kiszolgáló verziójával. Ha a csomagok újabb verzióját szeretné telepíteni, akkor van némi inkompatibilitási kockázat. Ez a fajta telepítés azonban lehetséges, ha a `useCRAN` parancsot adja meg a csomaglista első elemeként, például: `useCRAN bitops, stringr, arules`.  
   > 2. Néhány R csomag további Linux rendszerű könyvtárakat igényel. A kényelem érdekében a HDInsight ML-szolgáltatások előre telepítve vannak a 100 legnépszerűbb R-csomag által szükséges függőségekkel. Ha azonban a telepített R csomag(ok) további könyvtárakat telepítését igényli(k), akkor le kell töltenie az itt használt alapvető szkriptet, és lépéseket kell hozzáadnia a rendszerkönyvtárak telepítéséhez. Ezután fel kell töltenie a módosított szkriptet egy nyilvános blob-tárolóba az Azure Storage-ben, és a módosított szkripttel kell telepítenie a csomagokat.
   >    A szkriptműveletek fejlesztésével kapcsolatos további információért lásd: [Szkriptművelet fejlesztése](../hdinsight-hadoop-script-actions-linux.md).

   ![Az Azure Portal parancsfájl-műveletküldése](./media/r-server-hdinsight-manage/submit-script-action.png)

4. Válassza a **Létrehozás** lehetőséget a szkript futtatásához. A szkript befejezése után az R csomagok elérhetők az összes munkavégző csomóponton.

## <a name="next-steps"></a>További lépések

* [Az ML-szolgáltatások fürtjének üzembe helyezése a HDInsighton](r-server-operationalize.md)
* [Az ML service fürt környezeti beállításainak számítási környezete a HDInsighton](r-server-compute-contexts.md)
* [Azure Storage-lehetőségek az ML-szolgáltatások HDInsighton belüli fürtjéhez](r-server-storage.md)
