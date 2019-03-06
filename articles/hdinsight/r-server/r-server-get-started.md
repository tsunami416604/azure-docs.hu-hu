---
title: Az ML-szolgáltatások használatának első lépései a HDInsightban – Azure
description: Megtudhatja, hogyan hozhat létre ML-szolgáltatásokat tartalmazó Apache Sparkot a HDInsight-fürtön, és hogyan küldhet R-szkripteket a fürtön.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: dce345c39918e7471531710051797c848a3c7cde
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444072"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Az ML-szolgáltatások használatának első lépései az Azure HDInsightban

Az Azure HDInsight segítségével létrehozhat egy fürtöt az ML-szolgáltatások számára. Ez a beállítás lehetővé teszi, hogy az R-szkriptek a [Apache Spark](https://spark.apache.org/) és [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) eszközökkel elosztott számításokat futtathassanak. A cikkben foglaltakat követve elsajátíthatja az ML-szolgáltatási fürtök létrehozását a HDInsightban, valamint az olyan R-szkriptek futtatását, amelyek a Spark elosztott R-számításokhoz való használatát mutatják be.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. További információk: [Ingyenes Microsoft Azure-próbafiók beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **A Secure Shell (SSH) ügyfél**: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtasson parancsokat szolgál. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Fürt létrehozása az Azure Portallal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Navigáljon a **+ erőforrás létrehozása** > **Analytics** > **HDInsight**.

3. Az **Alapvető beállítások** képernyőn adja meg a következő információkat:

    * **Fürt neve**: A HDInsight-fürt neve.
    * **Előfizetés**: Válassza ki a használni kívánt előfizetést.
    * **Fürt bejelentkezési felhasználóneve** és **fürt bejelentkezési jelszavának**: A bejelentkezés, a fürt HTTPS-kapcsolaton keresztüli elérésekor. Ezek a hitelesítő adatok használatával például az Apache Ambari webes felhasználói felületen vagy REST API-szolgáltatások eléréséhez.
    * **Secure Shell (SSH-) felhasználónév**: A fürt SSH-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Alapértelmezés szerint a jelszó megegyezik a fürt bejelentkezési jelszavával.
    * **Erőforráscsoport**: Az erőforráscsoport, amelyben a fürt létre lesz hozva.
    * **Hely**: Az Azure-régió, amelyben a fürt létre lesz hozva.

        ![Fürt alapvető adatai](./media/r-server-get-started/clustername.png)

4. Válassza ki a **Fürt típusát**, majd állítsa be a következő értékeket a **Fürtkonfiguráció** szakaszban:

    * **Fürt típusa**: ML-szolgáltatások

    * **Operációs rendszer**: Linux

    * **Verzió**: ML Server 9.3 (HDI 3.6). Az ML Server 9.3 kibocsátási megjegyzései a [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server) webhelyen érhetők el.

    * **Az R Studio community Edition kiadást a gépi tanulás Server**: Ez a böngésző alapú IDE az élcsomóponti operacionalizáláshoz alapértelmezés szerint telepítve van. Ha nem szeretné, hogy telepítve legyen, törölje a jelölőnégyzet jelölését. Ha úgy dönt, hogy telepíti, a fürt portálalkalmazás-paneljén találja az RStudio Server bejelentkezésének elérésére szolgáló URL-címet, miután a fürt létrejött.

        ![Fürt alapvető adatai](./media/r-server-get-started/clustertypeconfig.png)

4. A fürt típusának kijelölése után erősítse meg a beállítást a __Kiválasztás__ gombbal. Ezután kattintson a __Tovább__ gombra az alapszintű konfiguráció befejezéséhez.

5. A **Tárolás** szakaszban válasszon ki vagy hozzon létre egy Storage-fiókot. A jelen dokumentumban leírt lépésekben a jelen szakaszban található többi mező alapértelmezett értékét ne módosítsa. Kattintson a __Tovább__ gombra a tárolókonfiguráció mentéséhez.

    ![A tárfiók HDInsight-beállításainak konfigurálása](./media/r-server-get-started/cluster-storage.png)

6. Az **Összegzés** szakaszban tekintse át a fürt konfigurációját. A __Szerkesztés__ hivatkozásai használatával módosítsa a hibás beállításokat. Végül kattintson a __Létrehozás__ gombra a fürt létrehozásához.

    ![A tárfiók HDInsight-beállításainak konfigurálása](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]  
    > A fürt létrehozása 20 percig is eltarthat.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Csatlakozás az RStudio Serverhez

Ha úgy döntött, hogy telepíti az RStudio Server Community Edition kiadást a HDInsight-fürt részeként, akkor az RStudio bejelentkezési oldalát az alábbi két módon érheti el:

* **1. lehetőség** – Látogasson el a következő URL-címre (ahol a **CLUSTERNAME** a létrehozott ML-szolgáltatási fürt neve):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **2. lehetőség** – az Azure Portalon.
A portálról:
  1. Válassza ki **minden szolgáltatás** a bal oldali menüből.
  2. A **ANALYTICS**válassza **HDInsight-fürtök**.
  3. Válassza ki a fürt nevét, a **HDInsight-fürtök** lapot.
  4. A **Machine Learning-szolgáltatások irányítópultok**válassza **R Studio server**. 

    ![A tárfiók HDInsight-beállításainak konfigurálása](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]  
   > A használt módszertől függetlenül, az első bejelentkezéskor kétszer kell elvégeznie a hitelesítést.  Az első hitelesítési kérésnél adja meg a *fürt rendszergazdai felhasználói azonosítóját* és *jelszavát*. A második hitelesítési kérésnél adja meg az *SSH felhasználói azonosítót* és *jelszót*. Későbbi napló-modulok csak az SSH hitelesítő adatok szükségesek.

A csatlakozást követően a képernyőnek a következőhöz hasonlóan kell kinéznie:

![Csatlakozás az RStudióhoz](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Mintafeladat futtatása

Feladat küldéséhez használjon ScaleR-függvényeket. Íme egy példa a feladat futtatására használt parancsokra:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
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

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Csatlakozás a fürt határcsomópontjához

Ebben a szakaszban azt ismertetjük, hogyan csatlakozhat az ML-szolgáltatási HDInsight-fürt határcsomópontjához az SSH segítségével. Az SSH használatával kapcsolatos ismeretekért lásd: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

Az ML-szolgáltatási fürt határcsomópontjához a következő SSH-paranccsal csatlakozhat:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

A fürthöz tartozó SSH-parancs megkereséséhez az Azure Portalon kattintson a fürt nevére, kattintson az **SSH + Bejelentkezés a fürtbe** elemre, majd az **Eszköznév** lehetőségnél válassza ki a határcsomópontot. Ez megjeleníti az élcsomópont SSH végpontinformációit.

![Az élcsomópont SSH végpontjának képe](./media/r-server-get-started/sshendpoint.png)

Ha az SSH-felhasználói fiókhoz jelszót használt, a rendszer felkéri annak megadására. Nyilvános kulcs használatakor lehetséges, hogy az `-i` paraméter használatára van szükség a megfelelő titkos kulcs megadásához. Példa:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Ha csatlakoztatva van, a következőhöz hasonló adatkérés érkezik:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Az R-konzol használata

1. Az SSH-munkamenetből a következő paranccsal indíthatja el az R-konzolt:  

        R

2. A kimenetben egyéb információk mellett az ML Server verziója is megjelenik.
    
3. A `>` adatkérésben beírhatja az R-kódot. A HDInsighton futó ML-szolgáltatások olyan csomagokat tartalmaznak, amelyekkel könnyedén használhatja a Hadoopot, és elosztott számításokat futtathat. A HDInsight-fürt alapértelmezett fájlrendszerének gyökerét például a következő paranccsal tekintheti meg:

        rxHadoopListFiles("/")

4. A WASB stíluscímzést is használhatja.

        rxHadoopListFiles("wasb:///")

5. Ha ki szeretne lépni az R-konzolból, használja az alábbi parancsot:

        quit()

## <a name="automated-cluster-creation"></a>Fürt automatikus létrehozása

Az SDK és a PowerShell használatával automatizálható a HDInsight ML-szolgáltatási fürtök létrehozása.

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* Az ML-szolgáltatási fürt .NET SDK-val történő létrehozásáról a [Linux-alapú fürtök HDInsightban, a .NET SDK-val történő létrehozását](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) ismertető témakör tartalmaz további részleteket.
* Az ML-szolgáltatási fürt PowerShell-lel történő létrehozásával kapcsolatban tekintse meg a [HDInsight-fürtök Azure PowerShell-lel történő létrehozását](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md) ismertető cikket.

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre új Machine Learning-szolgáltatások fürtöt az Azure HDInsight és az R-konzol SSH-munkamenetből történő használatának alapjaival. A következő cikkek az ML-szolgáltatások HDInsighton történő kezelésének és használatának egyéb módjait ismertetik:

* [Feladatok beküldése az R Tools for Visual Studio használatával](r-server-submit-jobs-r-tools-vs.md)
* [Az ML-szolgáltatások fürtjének kezelése a HDInsighton](r-server-hdinsight-manage.md)
* [Az ML-szolgáltatások fürtjének üzembe helyezése a HDInsighton](r-server-operationalize.md)
* [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)
* [Azure Storage-lehetőségek az ML-szolgáltatások HDInsighton belüli fürtjéhez](r-server-storage.md)
