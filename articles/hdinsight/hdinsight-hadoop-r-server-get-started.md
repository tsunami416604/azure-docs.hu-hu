---
title: "Az R Server használatának első lépései a HDInsightban – Azure| Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre R Servert tartalmazó Apache Sparkot a HDInsight-fürtön, majd hogyan küldhet el R-szkriptet a fürtön."
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/14/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 14e2a14c74e00709e18a80325fbdd3cbcd71da37
ms.contentlocale: hu-hu
ms.lasthandoff: 08/15/2017

---
# <a name="get-started-using-r-server-on-hdinsight"></a>R Server a HDInsightban – első lépések

A HDInsight olyan R Server beállítással rendelkezik, amely a HDInsight-fürtbe integrálható. Ez lehetővé teszi, hogy az R-szkriptek a Spark és a MapReduce eszközökkel elosztott számításokat futtathassanak. A dokumentumban foglaltakat követve elsajátíthatja az R Server létrehozását a HDInsight-fürtökön, majd R-szkriptet futtathat, amely a Spark elosztott R számításokhoz való használatát mutatja be.


## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. További információt az [ingyenes Microsoft Azure-próbafiók beszerzésével](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) foglalkozó cikkben talál.
* **Secure Shell- (SSH-) ügyfél**: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtathat parancsokat. További információ: [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).
* **Secure Shell- (SSH-) kulcsok (nem kötelező)**: A fürthöz való csatlakozáshoz használt SSH-fiókjának biztonságát jelszóval vagy nyilvános kulccsal biztosíthatja. A jelszó használata könnyebb, és lehetővé teszi, hogy nyilvános/titkos kulcspár létrehozása nélkül tegye meg az első lépéseket. A kulcs használata azonban biztonságosabb.

> [!NOTE]
> A dokumentum lépései azt feltételezik, hogy jelszót használ.


## <a name="automated-cluster-creation"></a>Fürt automatikus létrehozása

Azure Resource Manager-sablonok, az SDK, illetve a PowerShell használatával is automatizálható a HDInsight R Server-kiszolgálók létrehozása.

* Az R Server Azure Resource Management-sablonnal végzett létrehozásáról az [R Server HDInsight-fürt üzembe helyezésének leírásában](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) talál további információt.
* R Server létrehozása .NET SDK-val: [Linux-alapú fürtök létrehozása a HDInsightban a .NET SDK-val](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Az R Server PowerShell-lel történő üzembe helyezéséről a következővel foglalkozó cikkben talál további információkat: [R Server létrehozása a HDInsightban a PowerShell használatával](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Fürt létrehozása az Azure Portallal

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

2. Válassza az **ÚJ** -> **Intelligencia és elemzés**, -> **HDInsight** lehetőséget.

    ![Új fürt létrehozásának képe](./media/hdinsight-hadoop-r-server-get-started/newcluster.png)

3. A **Gyorslétrehozás** felületen írja be a fürt nevét a **Fürt neve** mezőbe. Ha több Azure-előfizetése van, az **Előfizetés** bejegyzésben válassza ki a használni kívánt előfizetést.

    ![Fürt neve és az előfizetés kiválasztása](./media/hdinsight-hadoop-r-server-get-started/clustername.png)

4. Válassza ki a **Fürt típusát** a **Fürtkonfiguráció** panel megnyitásához. A **Fürtkonfiguráció** panelen válassza ki a következő beállításokat:

    * **Fürt típusa**: R Server
    * **Verzió**: válassza ki az R Server fürtre telepítendő verzióját. A jelenleg elérhető verzió az ***R Server 9.1 (HDI 3.6)***. Az R Server egyes elérhető verzióinak kibocsátási megjegyzései [itt](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) érhetők el.
    * **R Studio community edition for R Server**: ez a böngésző alapú IDE alapértelmezés szerint telepítve van az élcsomópontra. Ha inkább nem szeretné, hogy telepítve legyen, akkor törölje a jelölőnégyzet jelölését. Ha úgy dönt, hogy telepíti, a fürt portálalkalmazás panelén találja az RStudio kiszolgáló bejelentkezésének elérésére szolgáló URL-t, miután a fürt létrejött.
    * Hagyja a többi beállítást az alapértelmezett értéken, és a **Kiválasztás** gombbal mentse a fürttípust.

        ![A Fürt típusa panel képernyőképe](./media/hdinsight-hadoop-r-server-get-started/clustertypeconfig.png)

5. Írja be a **Fürt bejelentkezési felhasználónevét** és a **Fürt bejelentkezési jelszavát**.

    Adjon meg egy **SSH-felhasználónevet**. Az SSH-val távolról csatlakozhat a fürthöz egy **Secure Shell- (SSH-)** ügyfél használatával. Megadhatja az SSH-felhasználót ezen a párbeszédpanelen vagy a fürt létrehozása után (a fürt Konfiguráció lapján). Az R Server úgy van konfigurálva, hogy a „remoteuser” **SSH-felhasználónevet** várja.  **Ha más felhasználónevet használ, egy további lépést kell elvégeznie a fürt létrehozása után.**

    Hagyja bejelölve a **Használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor** jelölőnégyzetet a **JELSZÓ** használatához hitelesítési típusként, ha nem szeretne nyilvános kulcsot használni.  Nyilvános/titkos kulcspárra van szüksége, ha a fürtön lévő R Servert egy távoli ügyfélen keresztül szeretné elérni, például RTVS-en, RStudión vagy másik asztali IDE-n keresztül. Ha az RStudio Server community edition kiadását telepíti, SSH-jelszót kell választania.     

    Nyilvános/titkos kulcspár használatához törölje a **Használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor** jelölőnégyzet jelölését, majd válassza a **NYILVÁNOS KULCS** elemet, és folytassa a következőképpen. Ezek az utasítások feltételezik, hogy a Cygwin with ssh-keygen vagy ennek megfelelő van telepítve.

    * Hozzon létre egy nyilvános/titkos kulcspárt a parancssorból a laptopján:

        ssh-keygen -t rsa -b 2048

    * Kövesse promptot a kulcsfájl elnevezéséhez, majd írjon be egy hozzáférési kódot a további biztonság érdekében. A képernyőjének a következő képhez hasonlóan kell kinéznie:

        ![SSH parancssor Windows rendszerben](./media/hdinsight-hadoop-r-server-get-started/sshcmdline.png)

    * Ez a parancs létrehoz egy titkos kulcsfájlt és egy nyilvános kulcsfájlt <titkos-kulcs-fájlnév>.pub néven, például furiosa és furiosa.pub néven.

        ![SSH dir](./media/hdinsight-hadoop-r-server-get-started/dir.png)

    * Ezután adja meg a nyilvános kulcsfájlt (&#42;.pub), amikor a HDI-fürt hitelesítő adatait rendeli hozzá, és végül erősítse meg az erőforráscsoportot és a régiót, és válassza a **Tovább** lehetőséget.

        ![Hitelesítő adatok panel](./media/hdinsight-hadoop-r-server-get-started/publickeyfile.png)  

   * Engedélyek módosítása a titkos kulcsfájlon a laptopján:

        chmod 600 <titkos-kulcs-fájlnév>

   * A titkos kulcsfájl használata SSH-val a távoli bejelentkezéshez:

        ssh –i <titkos-kulcs-fájlnév> remoteuser@<hostname public ip>

      Vagy a Hadoop Spark számítási környezet R Serverhez definíciójának részeként az ügyfélen. Lásd a **Microsoft R Server használata Hadoop-ügyfélként** alszakaszt a [számítási környezet Sparkhoz való létrehozásával](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) foglalkozó weblapon.

6. A gyorslétrehozás átirányítja a **Tárolás** panelre, ahol kiválaszthatja a fürt által használt HDFS-fájlrendszer elsődleges helyéhez használt tárfiók beállításait. Válasszon új vagy meglévő Azure Storage-fiókot vagy meglévő Data Lake tárfiókot.

    - Ha Azure Storage-fiókot választ, kiválaszthat egy meglévő tárfiókot a **Tárfiók kiválasztása** elem, majd a megfelelő fiók kiválasztásával. Hozzon létre egy új fiókot az **Új létrehozása** hivatkozással a **Tárfiók kiválasztása** szakaszban.

      > [!NOTE]
      > Ha az **Új** lehetőséget választja, be kell írnia egy nevet az új tárfiókhoz. Egy zöld pipa jelenik meg, ha a rendszer elfogadja a nevet.

      Az **Alapértelmezett tároló** alapértéke a fürt neve lesz. Hagyja meg ezt az alapértelmezett értéket.

      Ha új tárfiókot választott, megjelenik egy kérés a **Hely** kiválasztásához, ahol kiválaszthatja a tárfiók létrehozásának régióját.  

         ![Adatforrás panel](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > Az alapértelmezett adatforrás helyének kiválasztása a HDInsight-fürt helyét is beállítja. A fürtnek és az alapértelmezett adatforrásnak ugyanabban a régióban kell lennie.

    - Ha egy meglévő Data Lake Store használatát választja, akkor válassza ki a használni kívánt ADLS-tárfiókot, és adja a fürt *ADD*-identitását a fürthöz a tároló hozzáférésének engedélyezéséhez. Ezen folyamatról további információért tekintse át a [HDInsight-fürt létrehozása a Data Lake Store-ral az Azure Portalon](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal) szakaszt.

    A **Kiválasztás** gombbal mentse az adatforrás konfigurációját.


7. Ezután megjelenik az **Összegzés** panel az összes beállítás érvényesítéséhez. Itt módosíthatja a **Fürt méretét**, hogy módosítsa a fürtben lévő kiszolgálók számát, és megadhat minden futtatni kívánt **szkriptműveletet**. Ha tudja, hogy nincs szüksége nagy fürtre, hagyja a munkavégző csomópontok számát az alapértelmezett `4` értéken. A panelen megjelenik a fürt becsült költsége.

    ![fürt összegzése](./media/hdinsight-hadoop-r-server-get-started/clustersummary.png)

   > [!NOTE]
   > Szükség esetén később átméretezheti a fürtöt a portálon keresztül (**Fürt** -> **Beállítások** -> **Fürt méretezése**) a munkavégző csomópontok számának növelése vagy csökkentése érdekében.  Ez az átméretezés hasznos lehet a fürt lelassításához, amikor nincs használatban, vagy kapacitás hozzáadásához a nagyobb feladatok igényeinek kielégítése érdekében.
   >
   >

   A fürt, az adatcsomópontok és az élcsomópont átméretezésekor néhány figyelembe veendő tényező a következő:  

   * A Spark rendszeren az elosztott R Server elemzések teljesítménye arányos a munkavégző csomópontok számával, amikor nagyok az adatok.  

   * Az R Server elemzések teljesítménye arányos az elemzett adatok méretével. Példa:  

     * Kis és közepes méretű adatok esetében a teljesítmény akkor a legnagyobb, amikor az élcsomóponton helyi számítási környezetben van elemezve.  Azokról az alkalmazási helyzetekről, ahol a helyi és a Spark számítási környezetek a legjobban működnek, további információért lásd: Számítási környezeti beállítások a HDInsighton belüli R Server esetében.<br>
     * Ha bejelentkezik az élcsomópontra, és futtatja az R-szkriptet, akkor a ScaleR rx-függvényen kívül minden más<strong>helyileg</strong>, az élcsomóponton lesz végrehajtva. Ezért az élcsomópont memóriáját és magjainak számát ennek megfelelően kell méretezni. Ugyanez érvényes, ha az R Server on HDI-t távoli számítási környezetként használja a laptopjáról.

     ![Csomópontok árképzési szintjei panel](./media/hdinsight-hadoop-r-server-get-started/pricingtier.png)

     A **Kiválasztás** gombbal mentse a csomópontok árképzésének konfigurációját.

   Található egy **Sablon és paraméterek letöltése** hivatkozás is. Erre a hivatkozásra kattintva olyan szkriptek jelennek meg, amelyekkel automatizálható a fürtök létrehozása a kiválasztott konfigurációval. Ezek a szkriptek a fürt Azure Portal bejegyzéséről is elérhetők, miután az létrejött.

   > [!NOTE]
   > Némi időt vesz igénybe a fürt létrehozása, általában körülbelül 20 percet. A kezdőpulton vagy az **Értesítések** bejegyzésen az oldal bal oldalán lévő csempével ellenőrizheti a létrehozási folyamatot.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Csatlakozás az RStudio Serverhez

Ha úgy döntött, hogy belefoglalja az RStudio Server community edition kiadást a telepítésbe, akkor az RStudio bejelentkezési oldalát két módon érheti el.

1. Látogasson el a következő URL-re (ahol a **FÜRTNÉV** a létrehozott fürt neve):

    https://**FÜRTNÉV**.azurehdinsight.net/rstudio/

2. Nyissa meg a fürt bejegyzését az Azure Portalon, válassza az **R Server irányítópultok** gyorshivatkozást, majd az **R Studio irányítópultot**:

     ![Az R Studio irányítópult elérése](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![Az R Studio irányítópult elérése](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > A használt módszertől függetlenül, az első bejelentkezéskor kétszer kell elvégeznie a hitelesítést.  Az első hitelesítéskor adja meg a *fürt rendszergazdai felhasználói azonosítóját* és *jelszavát*. A második adatkéréskor adja meg az *SSH felhasználói azonosítót* és *jelszót*. A későbbi bejelentkezések csak az *SSH-jelszót* és a *felhasználói azonosítót* kérik.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Csatlakozás az R Server élcsomóponthoz

Csatlakoztassa a HDInsight-fürt R Server élcsomópontját SSH használatával, a következő paranccsal:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> A `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` címet megtalálhatja az Azure Portalon, ha kiválasztja a fürtöt, majd az **Összes beállítás** -> **Alkalmazások** -> **RServer** elemekre kattint. Ez megjeleníti az élcsomópont SSH végpontinformációit.
>
> ![Az élcsomópont SSH végpontjának képe](./media/hdinsight-hadoop-r-server-get-started/sshendpoint.png)
>
>

Ha az SSH-felhasználói fiókhoz jelszót használt, a rendszer felkéri annak megadására. Nyilvános kulcs használatakor lehetséges, hogy az `-i` paraméter használatára van szükség a megfelelő titkos kulcs megadásához. Példa:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

További információért lásd: [Csatlakozás a HDInsighthoz (Hadoop) SSH-val](hdinsight-hadoop-linux-use-ssh-unix.md).

Ha csatlakoztatva van, a következőhöz hasonló adatkérés érkezik:

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Több párhuzamos felhasználó engedélyezése

Több párhuzamos felhasználó úgy tud engedélyezni, ha több felhasználót is hozzáad ahhoz az élcsomóponthoz, amelyen az RStudio közösségi verziója fut.

HDInsight-fürt létrehozásakor két felhasználót kell megadnia, egy HTTP-felhasználót és egy SSH-felhasználót:

![1. párhuzamos felhasználó](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-1.png)

- **Fürt bejelentkezési felhasználóneve**: HTTP-felhasználó a létrehozott HDInsight-fürtöket védő HDInsight-átjárón át történő hitelesítéshez. Ez a HTTP-felhasználó használatos az Ambari, a YARN felhasználói felületének és más felhasználóifelület-összetevőknek az elérésére.
- **Secure Shell- (SSH-) felhasználónév**: SSH-felhasználó, aki a fürtöt biztonságos felületen keresztül éri el. Ez a felhasználó a Linux rendszerben az összes főcsomópont, munkavégző csomópont és élcsomópont felhasználója. Így Secure Shellt használhat a távoli fürt bármely csomópontjának elérésére.

A Microsoft R Serveren a HDInsight típusú fürtökben használt R Studio Server Community verziója bejelentkezési mechanizmusként csak Linux-felhasználónevet és -jelszót fogad el. Nem támogatja a jogkivonatok átadását. Tehát, ha létrehozott egy új fürtöt, és az R Studiót szeretné használni az eléréséhez akkor, kétszer kell bejelentkeznie.

- Először jelentkezzen be a HTTP felhasználói hitelesítő adatokkal a HDInsight-átjárón: 

    ![2a párhuzamos felhasználó](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-2a.png)

- Majd használja az SSH felhasználói hitelesítő adatokat az RStudióba való bejelentkezéshez:
  
    ![2b párhuzamos felhasználó](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-2b.png)

Jelenleg HDInsight-fürt üzembe helyezésekor csak egy SSH felhasználói fiókot lehet létrehozni. Ezért, ha HDInsight-fürtökben több felhasználó számára engedélyezni szeretné a Microsoft R Server elérését, akkor további felhasználókat kell létrehoznia a Linux rendszerben.

Mivel az RStudio Server Community kiadása a fürt élcsomópontján fut, több lépést is meg kell tenni:

1. A létrehozott SSH-felhasználó használata az élcsomópontra való bejelentkezéshez
2. További Linux-felhasználók hozzáadása az élcsomópontban
3. Az RStudio Community verziójának használata a létrehozott felhasználóval

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>1. lépés: A létrehozott SSH-felhasználó használata az élcsomópontra való bejelentkezéshez

Töltsön le egy SSH-eszközt (például a Putty-t), és használja a meglévő SSH-felhasználót a bejelentkezéshez. Ezután az élcsomópont eléréséhez kövesse a [Csatlakozás a HDInsighthoz (Hadoop) SSH-val](hdinsight-hadoop-linux-use-ssh-unix.md) szakasz utasításait. Az élcsomópont címe az R Serverhez a HDInsight-fürtön a következő: *clustername-ed-ssh.azurehdinsight.net*


### <a name="step-2-add-more-linux-users-in-edge-node"></a>2. lépés: További Linux-felhasználók hozzáadása az élcsomópontban

Felhasználó élcsomóponthoz adásához hajtsa végre a következő parancsokat:

    sudo useradd yournewusername -m
    sudo passwd yourusername

A programnak a következő elemeket kell visszaadnia: 

![3. párhuzamos felhasználó](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-3.png)

Amikor a rendszer az „aktuális Kerberos-jelszót” kéri, nyomja meg az **Enter** billentyűt a figyelmen kívül hagyásához. A `useradd` parancs `-m` kapcsolója jelzi, hogy a rendszer létrehoz egy kezdőmappát a felhasználó számára, amely szükséges az RStudio Community verziójához.


### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>3. lépés: Az RStudio Community verziójának használata a létrehozott felhasználóval

A létrehozott felhasználóval jelentkezhet be az RStudióba:

![4. párhuzamos felhasználó](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-4.png)

Az RStudio jelzi, hogy az új felhasználót használja a fürtre való bejelentkezéshez (itt például az *sshuser6* felhasználót): 

![5. párhuzamos felhasználó](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-5.png)

Az eredeti hitelesítő adatokkal (alapértelmezés szerint ez az *sshuser*) egyidejűleg egy másik böngészőablakból is bejelentkezhet.

Feladat küldéséhez használhatja a scaleR-függvényeket. Íme egy példa a feladat futtatására használt parancsokra:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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


Figyelje meg, hogy a beküldött munkák más felhasználónév alatt szerepelnek a YARN felhasználói felületén:

![6. párhuzamos felhasználó](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-6.png)

Figyelje meg azt is, hogy az újonnan felvett felhasználók nem rendelkeznek gyökérjogosultságokkal a Linux rendszerben, de ugyanolyan hozzáférésük van a távoli HDFS- és WASB-tárolón az összes fájlhoz.


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Az R-konzol használata

1. Az SSH-munkamenetből a következő paranccsal indíthatja el az R-konzolt:  

        R

2. A következőhöz hasonló kimenetnek kell megjelennie:
    
    R 3.2.2 (2015-08-14) verzió – „Fire Safety”  Copyright (C) 2015 The R Foundation for Statistical Computing  Platform: x86_64-pc-linux-gnu (64 bites)

    Az R egy ingyenes szoftver, és EGYÁLTALÁN SEMMILYEN JÓTÁLLÁS SEM vonatkozik rá.
    Bizonyos feltételek teljesítése esetén nyugodtan terjesztheti.
    A terjesztésre vonatkozó részletekért írja be a 'license()' vagy a 'licence()' parancsot.

    Támogatja a természetes nyelveket, de angol területi beállítással fut

    Az R egy együttműködésen alapuló projekt, sok hozzájárulóval.
    További információért írja be a 'contributors()' parancsot, az R vagy az R-csomagok publikációkban történő idézésének módját pedig a 'citation()' beírásával ismerheti meg.

    Írja be a 'demo()' parancsot néhány bemutató, a 'help()' parancsot az online súgó megtekintéséhez vagy a 'help.start()' parancsot, ha HTML-böngészőfelületen szeretné megtekinteni a súgót.
    Írja be a 'q()' parancsot a kilépéshez.

    Microsoft R Server 8.0-s verzió: az R Microsoft-csomagok továbbfejlesztett terjesztési verziója Copyright (C) 2016 Microsoft Corporation

    A kiadási megjegyzések megtekintéséhez írja be a 'readme()' parancsot.
    >

3. A `>` adatkérésben beírhatja az R-kódot. Az R Server olyan csomagokat tartalmaz, amelyekkel könnyedén használhatja a Hadoopot és futtathat elosztott számításokat. A HDInsight-fürt alapértelmezett fájlrendszerének gyökerét például a következő paranccsal tekintheti meg:

    rxHadoopListFiles("/")

4. A WASB stíluscímzést is használhatja.

    rxHadoopListFiles("wasb:///")


## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Az R Server használata HDI-n a Microsoft R Server vagy a Microsoft R ügyfél egy távoli példányáról

A HDI Hadoop Spark számítási környezetet el lehet érni a Microsoft R Server vagy a Microsoft R-ügyfél számítógépen vagy laptopon futó távoli példányáról. Lásd a **Microsoft R Server használata Hadoop-ügyfélként** alszakaszt a [számítási környezet Sparkhoz való létrehozásával](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started.md) foglalkozó weblapon. Ehhez meg kell adnia a következő beállításokat, amikor meghatározza az RxSpark számítási környezetet a laptopon: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches és sshProfileScript. Példa:


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


## <a name="use-a-compute-context"></a>Számítási környezet használata

A számítási környezetekkel vezérelheti, hogy a számítás helyben történik-e az élcsomóponton, vagy elosztottan a HDInsight-fürtben lévő csomópontok között.

1. Az RStudio Serverről vagy az R-konzolról (SSH-munkamenetben) a következő kód használatával töltse be a példaadatokat a HDInsight alapértelmezett tárolójába:

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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

2. Ezután hozzon létre adatinformációkat, és határozzon meg két adatforrást, hogy használhassuk az adatokat.

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

3. Futtasson logisztikai regressziót az adatokon a helyi számítási környezettel.

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

4. Ezután futtassa ugyanezt a logisztikai regressziót a Spark környezettel. A Spark környezet elosztja a feldolgozást a HDInsight-fürt összes munkavégző csomópontja között.

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
   > A MapReduce eszközzel is eloszthatja a számítást a fürtcsomópontok között. A számítási környezetről további információért lásd: [Számítási környezeti beállítások a HDInsighton belüli R Server esetében](hdinsight-hadoop-r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>R-kód elosztása több csomópontra

Az R Serverrel könnyedén futtathatja a meglévő R-kódokat a fürt több csomópontján az `rxExec` használatával. Ez a függvény akkor hasznos, amikor paraméteres frissítést vagy szimulációkat végez. A következő kód az `rxExec` használatának példája:

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


## <a name="accessing-data-in-hive-and-parquet"></a>Adatok elérése a Hive és a Parquet eszközökben

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


Ezen új függvények használatával kapcsolatos további információkat az R Server online súgójában találhat a `?RxHivedata` és a `?RxParquetData` parancsok használatával.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>További R-csomagok telepítése az élcsomópontra

Ha további R csomagokat szeretne telepíteni az élcsomóponton, az `install.packages()` parancsot használhatja közvetlenül az R-konzolról, amikor SSH-n keresztül csatlakozik az élcsomóponthoz. Ha azonban a fürt munkavégző csomópontjaira kell R csomagokat telepítenie, szkriptműveletet kell használnia.

A szkriptműveletek olyan Bash-szkriptek, amelyekkel konfigurációs módosítások végezhetők a HDInsight-fürtön, vagy további szoftverek, például további R-csomagok telepíthetők. Ha szkriptművelettel szeretne további csomagokat telepíteni, használja a következő lépéseket:

> [!IMPORTANT]
> A további R csomagok szkriptműveletekkel végzett telepítése csak a fürt létrehozása után használható. Ne használja ezt az eljárást a fürt létrehozása során, mivel a szkript az R Server teljes telepítésétől és konfigurálásától függ.
>
>

1. Az [Azure Portalon](https://portal.azure.com) válassza ki az R Servert a HDInsight-fürtön.

2. A **Beállítások** panelen válassza a **Szkriptműveletek** elemet, majd az **Új küldése** elemet egy új szkriptművelet elküldéséhez.

   ![A Szkriptműveletek panel képe](./media/hdinsight-hadoop-r-server-get-started/scriptaction.png)

3. A **Szkriptművelet küldése** panelen adja meg a következő információkat:

   * **Név**: A szkriptet azonosító egyszerű név

   * **Bash-szkript URI azonosítója**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Fej**: Ez az elem **ne legyen bejelölve**

   * **Feldolgozó**: Ez az elem **legyen bejelölve**

   * **Szegélycsomópontok**: Ez az elem **ne legyen bejelölve**.

   * **Zookeeper**: Ez az elem **ne legyen bejelölve**

   * **Paraméterek**: A telepíteni kívánt R csomagok. Például: `bitops stringr arules`

   * **Ha megőrzi a...**: Ez az elem **legyen bejelölve**  

   > [!NOTE]
   > 1. Alapértelmezés szerint az összes R csomag a telepített R Server verziójának megfelelő Microsoft MRAN tár-pillanatfelvételéből van telepítve. Ha a csomagok újabb verzióját szeretné telepíteni, akkor van némi inkompatibilitási kockázat. Ez a fajta telepítés azonban lehetséges, ha a `useCRAN` parancsot adja meg a csomaglista első elemeként, például: `useCRAN bitops, stringr, arules`.  
   > 2. Néhány R csomag további Linux rendszerű könyvtárakat igényel. A kényelem érdekében előre telepítettük a 100 legnépszerűbb R csomag szükséges függőségeit. Ha azonban a telepített R csomag(ok) további könyvtárakat telepítését igényli(k), akkor le kell töltenie az itt használt alapvető szkriptet, és lépéseket kell hozzáadnia a rendszerkönyvtárak telepítéséhez. Ezután fel kell töltenie a módosított szkriptet egy nyilvános blob-tárolóba az Azure Storage-ben, és a módosított szkripttel kell telepítenie a csomagokat.
   >    A szkriptműveletek fejlesztésével kapcsolatos további információért lásd: [Szkriptművelet fejlesztése](hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Szkriptműveletek hozzáadása](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. Válassza a **Létrehozás** lehetőséget a szkript futtatásához. A szkript befejezése után az R csomagok elérhetők az összes munkavégző csomóponton.


## <a name="using-microsoft-r-server-operationalization"></a>A Microsoft R Server operacionalizálás használata

Amikor elkészült az adatmodellezés, működőképessé teheti a modellt, hogy előrejelzéseket végezzen. A Microsoft R Server működőképessé tételhez való konfigurálásához kövesse az alábbi lépéseket:

Először jelentkezzen be SSH-n keresztül az élcsomópontba. Például: 

    ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Az SSH használata után lépjen a megfelelő verzió könyvtárára, és használja a következő sudo dotnet dll-t: 

- Microsoft R Server 9.1 esetén:

    cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0   sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

- Microsoft R Server 9.0 esetén:

    cd /usr/lib64/microsoft-deployr/9.0.1   sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

Ha a Microsoft R Server működőképessé tételét egy beépített konfigurációban szeretné konfigurálni, tegye a következőt:

1. Válassza a „Configure R Server for Operationalization” (Az R Server konfigurálása a Működőképessé tétel művelethez) lehetőséget
2. Válassza az „A. One-box (web + compute nodes)” (Beépített (web + számítási csomópontok)) elemet
3. Írja be a **rendszergazdai** felhasználó jelszót

![one box op](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

Választható lépésként diagnosztikai ellenőrzéseket végezhet az alább látható diagnosztikai tesztelés futtatásával:

1. Válassza a „6. Run diagnostic tests” (Diagnosztikai tesztek futtatása) elemet
2. Válassza az „A. Test configuration” (Tesztkonfiguráció) elemet
3. Írja be a Username = „admin” parancsot, valamint a jelszót az előző konfigurációs lépésből
4. Erősítse meg az Overall Health = pass parancsot
5. Lépjen ki az admin segédprogramból
6. Lépjen ki az SSH-ból

![Diagnostic for op](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>**Hosszú késések a webszolgáltatások Sparkban történő felhasználásakor**
>
>Ha hosszú késleltetést tapasztal, amikor egy Spark számítási környezetben mrsdeploy függvényekkel létrehozott webszolgáltatást próbál felhasználni, előfordulhat, hogy hozzá kell adnia néhány hiányzó mappát. A Spark-alkalmazás egy *rserve2* nevű felhasználóhoz tartozik, ha egy mrsdeploy függvényeket használó webszolgáltatásból hívja meg. Megkerülő megoldás a problémára:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Az operacionalizálás konfigurációja ezzel befejeződött. Most már használhatja az „mrsdeploy” csomagot RClientben, hogy kapcsolódhasson az élcsomóponti operacionalizáláshoz, és elkezdhet alkalmazni olyan szolgáltatásokat, mint a [távoli végrehajtás](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) és [webszolgáltatás](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). Attól függően, hogy fürt virtuális hálózaton van-e beállítva, szükség lehet porttovábbító bújtatás kialakítására SSH-bejelentkezésen keresztül. Az alábbi szakaszok ismertetik, hogyan állíthatja be ezt az alagutat.

### <a name="rserver-cluster-on-virtual-network"></a>Az RServer fürt virtuális hálózaton van

Bizonyosodjon meg róla, hogy engedélyezett a forgalom az 12800-as porton az élcsomópont felé. Így az élcsomópont használatával kapcsolódhat az operacionalizálási szolgáltatáshoz.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Ha a `remoteLogin()` metódus nem tud kapcsolódni az élcsomóponthoz, de SSH-n be tud jelentkezni az élcsomópontba, győződjön meg róla, hogy a szabály, amely engedélyezi a forgalmat az 12800-as porton, megfelelően van-e beállítva. Ha a probléma továbbra is jelentkezik, egy másik megoldás segítségével is beállíthat porttovábbító alagutat az SSH-n keresztül. Útmutatásért lásd a következő szakaszt.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Az RServer fürt nem virtuális hálózaton van beállítva

Ha a fürt nem a virtuális hálózaton van beállítva vagy problémás a kapcsolódás a virtuális hálózaton keresztül, akkor használhatja az SSH porttovábbító alagutat:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

A Putty szoftveren is beállítható.

![putty ssh kapcsolat](./media/hdinsight-hadoop-r-server-get-started/putty.png)

Ha az SSH-munkamenet aktív, a rendszer számítógépe 12800-as portjáról az élcsomópont 12800-as portjára továbbítja a forgalmat az SSH-munkameneten keresztül. A `127.0.0.1:12800` címet használja a `remoteLogin()` metódusban. Ezzel a porttovábbításon keresztül jelentkezik be az élcsomóponti operacionalizálásra.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Hogyan méretezhetők a Microsoft R Server operacionalizálási számítási csomópontjai a HDInsight feldolgozó csomópontjain?

### <a name="decommission-the-worker-nodes"></a>A feldolgozó csomópont(ok) leszerelése

A Microsoft R Servert jelenleg nem a Yarnon keresztül kezeli a rendszer. Ha a feldolgozó csomópontokat nem szereli le, a Yarn Resource Manager nem a várakozásoknak megfelelően fog működni, mert nem fogja látni a kiszolgáló által felhasznált erőforrásokat. Ennek a helyzetnek az elkerülésére javasoljuk a feldolgozó csomópontok leszerelését a számítási csomópontok horizontális felskálázása előtt.

A feldolgozó csomópontok leszerelésének lépései:

* Jelentkezzen be a HDI-fürt Ambari konzoljába, és kattintson a „Hosts” (Gazdagépek) lapra.
* Jelölje ki a leszerelendő feldolgozó csomópontokat, és kattintson az „Actions” (Műveletek) > „Selected Hosts” (Kiválasztott gazdagépek) > „Hosts” (Gazdagépek) panelen a „Turn ON Maintenance Mode” (Karbantartási mód bekapcsolása) elemre. A következő képen például a wn3 és a wn4 pontokat választottuk ki leszerelésre.  

   ![feldolgozó csomópont(ok) leszerelése](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* Válassza az **Actions**(Műveletek) > **Selected Hosts**(Kiválasztott csomópontok) > **DataNodes** (AdatCsomópontok) elemet > kattintson a **Decommission** (Leszerelés) gombra
* Válassza az **Actions**(Műveletek) > **Selected Hosts**(Kiválasztott csomópontok) > **NodeManagers** (CsomópontKezelők) elemet > kattintson a **Decommission** (Leszerelés) gombra
* Válassza az **Actions**(Műveletek) > **Selected Hosts**(Kiválasztott csomópontok) > **DataNodes** (AdatCsomópontok) elemet > kattintson a **Stop** gombra
* Válassza az **Actions**(Műveletek) > **Selected Hosts**(Kiválasztott csomópontok) > **NodeManagers** (CsomópontKezelők) elemet > kattintson a **Stop** gombra
* Válassza az **Actions**(Műveletek) > **Selected Hosts**(Kiválasztott gazdagépek) > **Hosts** (Gazdagépek) elemet > kattintson a **Stop All Components** (Összes összetevő leállítása) gombra
* Szüntesse meg a feldolgozó csomópontok kijelölését, és jelölje ki az élcsomópontokat
* Válassza az **Actions**(Műveletek) > **Selected Hosts** (Kiválasztott gazdagépek) > "**Hosts**(Gazdagépek) > **Restart All Components**(Összes gazdagép újraindítása) elemet

### <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Számítási csomópontok konfigurálása az összes leszerelt feldolgozó csomóponton

1. Jelentkezzen be SSH-n keresztül minden egyes leszerelt feldolgozó csomópontba.
2. Futtassa az admin segédprogramot a következő paranccsal: `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Adja meg az „1” értéket „Configure R Server for Operationalization” (Az R Server konfigurálása a Működőképessé tétel művelethez) lehetőség kijelöléséhez.
4. Írja be a „c” karaktert a „C. Compute node” (Számítási csomópont) lehetőség kijelöléséhez. Ez konfigurálja a számítási csomópontot a feldolgozó csomóponton.
5. Lépjen ki az admin segédprogramból.

### <a name="add-compute-nodes-details-on-web-node"></a>Számítási csomópontok részleteinek megadása a Web csomóponton

Ha minden leszerelt feldolgozó csomópontot konfigurált a számítási csomópont futtatására, térjen vissza az élcsomóponthoz, és adja hozzá a leszerelt feldolgozó csomópontok IP címét a Microsoft R Server webcsomópontjának konfigurációjában:

* Jelentkezzen be SSH-n keresztül az élcsomópontba.
* Futtassa az `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` parancsot.
* Keresse meg az „URIs” szakaszt, és adja hozzá a feldolgozó csomópontok IP-címét és portrészleteit.

    ![feldolgozó csomópont(ok) leszerelési parancssora](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Következő lépések

Mostanra biztosan megértette, hogyan kell R Servert tartalmazó HDInsight-fürtöt létrehozni, és tisztában van az R-konzol SSH-munkamenetből történő használatának alapjaival. A következő témakörök az R Server HDInsighton történő kezelésének és az azzal történő munkavégzésnek egyéb módjait ismertetik:

* [RStudio Server hozzáadása a HDInsight szolgáltatáshoz (ha a fürt létrehozása közben nem telepítette)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Számítási környezeti beállítások a HDInsighton belüli R Server esetében](hdinsight-hadoop-r-server-compute-contexts.md)
* [Azure Storage lehetőségek a HDInsighton belüli R Server esetében](hdinsight-hadoop-r-server-storage.md)

