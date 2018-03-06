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
ms.openlocfilehash: aa7f2e6f44036738756391ecaa265c57c093c42c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="get-started-with-r-server-on-hdinsight"></a>A HDInsighton futó R Server használatának első lépései

Az Azure HDInsight olyan R Server beállítással rendelkezik, amely a HDInsight-fürtbe integrálható. Ez lehetővé teszi, hogy az R-szkriptek a Spark és a MapReduce eszközökkel elosztott számításokat futtathassanak. Ebből a cikkből megtudhatja, hogyan hozható létre egy R Server-kiszolgálót egy HDInsight-fürtön. Ezután megismerheti, hogyan futtathat egy R-szkriptet, amely a Spark elosztott R számításokhoz való használatát mutatja be.


## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. További információk: [Ingyenes Microsoft Azure-próbafiók beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Secure Shell- (SSH-) ügyfél**: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtathat parancsokat. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).
* **SSH-kulcsok (nem kötelező)**: A fürthöz való csatlakozáshoz használt SSH-fiókjának biztonságát jelszóval vagy nyilvános kulccsal biztosíthatja. A jelszó használata könnyebb, és lehetővé teszi, hogy nyilvános/titkos kulcspár létrehozása nélkül tegye meg az első lépéseket. A kulcs használata azonban biztonságosabb.

  > [!NOTE]
  > A cikkben ismertetett lépések azt feltételezik, hogy jelszót használ.


## <a name="automate-cluster-creation"></a>Fürt létrehozásának automatizálása

Azure Resource Manager-sablonok, az SDK, és a PowerShell használatával automatizálható a HDInsight R Server-példányok létrehozása.

* R Server-példány Azure Resource Manager-sablonnal végzett létrehozásáról szóló információkért az [R Server HDInsight-fürt üzembe helyezésének leírásában](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) talál további információt
* R Server-példány létrehozása .NET SDK-val: [Linux-alapú fürtök létrehozása a HDInsightban a .NET SDK-val.](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* R Server-példány üzembe helyezése PowerShell-lel: [Linux-alapú fürtök létrehozása a HDInsightban az Azure PowerShell-lel](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-a-cluster-by-using-the-azure-portal"></a>Fürt létrehozása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza az **ÚJ** > **Intelligencia és elemzés** > **HDInsight** lehetőséget.

    ![Új fürt létrehozásának képe](./media/r-server-get-started/newcluster.png)

3. A **Gyors létrehozás** felületen írja be a fürt nevét a **Fürt neve** mezőbe. Ha több Azure-előfizetéssel rendelkezik, az **Előfizetés** mezőben válassza ki a használni kívánt előfizetést.

    ![Fürt neve és az előfizetés kiválasztása](./media/r-server-get-started/clustername.png)

4. Válassza ki a **Fürt típusát** a **Fürtkonfiguráció** ablaktábla megnyitásához. A **Fürtkonfiguráció** ablaktáblán válassza ki a következő beállításokat:

    * **Fürt típusa**: Válassza az **R Server** lehetőséget.
    * **Verzió**: válassza ki az R Server fürtre telepítendő verzióját. A jelenleg elérhető verzió az **R Server 9.1 (HDI 3.6)**. Az R Server elérhető verzióinak kibocsátási megjegyzései a [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91) webhelyen érhetők el.
    * **R Studio community edition for R Server**: ez a böngésző alapú IDE alapértelmezés szerint telepítve van az élcsomópontra. Ha inkább nem szeretné, hogy telepítve legyen, törölje a jelölőnégyzet jelölését. Ha úgy dönt, hogy telepíti, a fürt portálalkalmazás ablaktábláján találja az RStudio kiszolgáló bejelentkezésének elérésére szolgáló URL-t, miután a fürt létrejött.
    * Hagyja a többi beállítást az alapértelmezett értéken, és a **Kiválasztás** gombbal mentse a fürttípust.

        ![A „Fürt típusa” ablaktábla képernyőképe](./media/r-server-get-started/clustertypeconfig.png)

5. Az **Alapok** ablaktáblán a **Fürt bejelentkezési felhasználóneve** és a **Fürt bejelentkezési jelszava** mezőben adjon meg egy felhasználónevet és jelszót a fürthöz.

6. A **Secure Shell- (SSH-) felhasználónév** mezőben adja meg az SSH-felhasználónevet. Az SSH-val távolról csatlakozhat a fürthöz egy SSH-ügyfél használatával. Megadhatja az SSH-felhasználót ebben a mezőben vagy a fürt létrehozása után (a fürt **Konfiguráció** lapján).
   
   > [!NOTE] 
   > Az R Server úgy van konfigurálva, hogy a „remoteuser” SSH-felhasználónevet várja. Ha más felhasználónevet használ, egy további lépést kell elvégeznie a fürt létrehozása után.

7. Hagyja bejelölve a **Használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor** jelölőnégyzetet a **JELSZÓ** használatához hitelesítési típusként, ha nem szeretne nyilvános kulcsot használni. Nyilvános/titkos kulcspárra van szüksége, ha a fürtön lévő R Servert egy távoli ügyfélen keresztül szeretné elérni, például az R Tools for Visual Studio, az Rstudio vagy egyéb asztali IDE használatával. Ha az RStudio Server community edition kiadását telepíti, SSH-jelszót kell választania.     

   Nyilvános/titkos kulcspár létrehozásához és használatához törölje a **Használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor** jelölőnégyzet jelölését. Ezután válassza a **NYILVÁNOS KULCS** lehetőséget és folytassa a következők szerint. Ezek az utasítások feltételezik, hogy a Cygwin with ssh-keygen vagy ennek megfelelő van telepítve.

   a. Hozzon létre egy nyilvános/titkos kulcspárt a parancssorból a laptopján:

        ssh-keygen -t rsa -b 2048

   b. Kövesse promptot a kulcsfájl elnevezéséhez, majd írjon be egy hozzáférési kódot a további biztonság érdekében. A képernyőjének a következő képhez hasonlóan kell kinéznie:

      ![SSH-parancssor Windows rendszerben](./media/r-server-get-started/sshcmdline.png)

      Ez a parancs létrehoz egy titkos kulcsfájlt és egy nyilvános kulcsfájlt <titkos-kulcs-fájlnév>.pub néven. Jelen példában a fájlok neve furiosa és furiosa.pub:

      ![A dir parancs eredményének példái](./media/r-server-get-started/dir.png)

   c. Adja meg a nyilvános kulcsfájlt (&#42;.pub) a HDI-fürt hitelesítő adatainak hozzárendelésekor. Erősítse meg az erőforráscsoportot és a régiót, majd válassza a **Tovább** lehetőséget.

      ![Hitelesítő adatok ablaktábla](./media/r-server-get-started/publickeyfile.png)  

   d. Engedélyek módosítása a titkos kulcsfájlon a laptopján:

        chmod 600 <private-key-filename>

   e. A titkos kulcsfájl használata SSH-val a távoli bejelentkezéshez:

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      Vagy használja a titkos kulcsfájlt a Hadoop Spark számítási környezet definíciójának részeként az R Serverhez az ügyfélen. További információkért lásd: [Számítási környezet létrehozása a Sparkhoz](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark).

8. A gyors létrehozás átirányítja a **Tároló** ablaktáblára. Itt kiválaszthatja a fürt által használt HDFS-fájlrendszer elsődleges helyéhez használt tárfiók beállításait. Válasszon ki egy új vagy meglévő Azure Storage-fiókot vagy egy meglévő Azure Data Lake Store-fiókot.

    - Ha Azure Storage-fiókot választ, kiválaszthat egy meglévő tárfiókot a **Tárfiók kiválasztása** elem, majd a releváns fiók kiválasztásával. Hozzon létre egy új fiókot az **Új létrehozása** hivatkozással a **Tárfiók kiválasztása** szakaszban.

      > [!NOTE]
      > Ha az **Új** lehetőséget választja, be kell írnia egy nevet az új tárfiókhoz. Egy zöld pipa jelenik meg, ha a rendszer elfogadja a nevet.

      Az **Alapértelmezett tároló** alapértéke a fürt neve lesz. Hagyja meg ezt az alapértelmezett értéket.

      Ha az új tárfiók kiválasztása lehetőséget választotta, használja a parancsablak **Hely** elemét a tárfiókhoz tartozó régió kiválasztásához.  

         ![Adatforrás ablaktábla](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > Az alapértelmezett adatforrás helyének kiválasztása a HDInsight-fürt helyét is beállítja. A fürtnek és az alapértelmezett adatforrásnak ugyanabban a régióban kell lennie.

    - Ha egy meglévő Data Lake Store-fiókot szeretne használni, akkor válassza ki a használni kívánt fiókot. Ezután adja a fürt *ADD*-identitását a fürthöz a tárolóhoz való hozzáférés engedélyezéséhez. Ezen folyamatról további információért tekintse meg a [HDInsight-fürt létrehozása a Data Lake Store-ral az Azure Portalon](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal) szakaszt.

    A **Kiválasztás** gombbal mentse az adatforrás konfigurációját.


9. Ezután megjelenik az **Összegzés** ablaktábla, ahol érvényesítheti az összes beállítást. Itt módosíthatja a fürt méretét a fürtben lévő kiszolgálók számának módosításához. A futtatni kívánt szkriptműveleteket is itt adhatja meg. Ha tudja, hogy nincs szüksége nagy fürtre, hagyja a munkavégző csomópontok számát az alapértelmezett **4** értéken. Az ablaktáblán megjelenik a fürt becsült költsége is.

    ![Fürt összegzése](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > Szükség esetén később átméretezheti a fürtöt a portálon keresztül (**Fürt** > **Beállítások** > **Fürt méretezése**) a munkavégző csomópontok számának növelése vagy csökkentése érdekében. Ez az átméretezés hasznos lehet a fürt lelassításához, amikor nincs használatban, vagy kapacitás hozzáadásához a nagyobb feladatok igényeinek kielégítése érdekében.
   >
   >

   A fürt, az adatcsomópontok és az élcsomópont átméretezésekor például a következő tényezőket kell figyelembe venni:  

   * A Spark rendszeren az elosztott R Server elemzések teljesítménye arányos a munkavégző csomópontok számával, amikor nagyok az adatok.  

   * Az R Server elemzések teljesítménye arányos az elemzett adatok méretével. Például:  

     * Kis és közepes méretű adatok esetében a teljesítmény akkor a legnagyobb, amikor az adatok az élcsomóponton helyi számítási környezetben vannak elemezve. Azokról az alkalmazási helyzetekről, ahol a helyi és a Spark számítási környezetek a legjobban működnek, további információkat talál a[ Számítási környezeti beállítások a HDInsighton belüli R Server esetében](r-server-compute-contexts.md) című szakaszban.<br>
     * Ha bejelentkezik az élcsomópontra, és futtatja az R-szkriptet, akkor a ScaleR rx-függvényen kívül minden más *helyileg*, az élcsomóponton lesz végrehajtva. Ezért az élcsomópont memóriáját és magjainak számát ennek megfelelően kell méretezni. Ugyanez érvényes, ha az R Server on HDI-t távoli számítási környezetként használja a laptopjáról.

   A **Kiválasztás** gombbal mentse a csomópontok árképzésének konfigurációját.

   ![Csomópont árképzési szintjei ablaktábla](./media/r-server-get-started/pricingtier.png)

   Található egy **Sablon és paraméterek letöltése** hivatkozás is. Erre a hivatkozásra kattintva olyan szkriptek jelennek meg, amelyekkel automatizálható a fürtök létrehozása a kiválasztott konfigurációval. Ezek a szkriptek a fürt Azure Portal bejegyzéséről is elérhetők, miután az létrejött.

   > [!NOTE]
   > Némi időt vesz igénybe a fürt létrehozása, általában körülbelül 20 percet. A létrehozási folyamatot a kezdőpulton vagy az **Értesítések** bejegyzésen, az oldal bal oldalán lévő csempével ellenőrizheti.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Csatlakozás az RStudio Serverhez

Ha úgy döntött, hogy belefoglalja az RStudio Server community edition kiadást a telepítésbe, akkor az RStudio bejelentkezési oldalát két módon érheti el:

- Látogasson el a következő URL-címre (ahol a *FÜRTNÉV* a létrehozott fürt neve):

    https://*FÜRTNÉV*.azurehdinsight.net/rstudio/

- Nyissa meg a fürt bejegyzését az Azure Portalon, válassza az **R Server irányítópultok** gyorshivatkozást, majd az **R Studio irányítópultot**:

  ![Az RStudio irányítópultjának elérése](./media/r-server-get-started/rstudiodashboard1.png)

  ![Az RStudio irányítópultjának elérése](./media/r-server-get-started/rstudiodashboard2.png)

> [!IMPORTANT]
> A használt módszertől függetlenül, az első bejelentkezéskor kétszer kell elvégeznie a hitelesítést. Az első hitelesítéskor adja meg a fürt rendszergazdai felhasználói azonosítóját és jelszavát. A második adatkéréskor adja meg az SSH-felhasználói azonosítót és -jelszót. A későbbi bejelentkezésekkor csak az SSH-felhasználói azonosítót és -jelszót kell megadnia.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Csatlakozás az R Server élcsomóponthoz

Csatlakozzon a HDInsight-fürt R Server élcsomópontjához az SSH segítségével az alábbi paranccsal:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> A `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` címet az Azure Portalon találja. Válassza ki a fürtöt, majd a **Minden beállítás** > **Alkalmazások** > **RServer** lehetőséget. Ez megjeleníti az élcsomópont SSH-végpontjának információit.
>
> ![Az élcsomópont SSH-végpontjának képe](./media/r-server-get-started/sshendpoint.png)
>
>

Ha az SSH-felhasználói fiók biztosításához jelszót használt, a rendszer felkéri annak megadására. Nyilvános kulcs használatakor lehetséges, hogy az `-i` paraméter használatára van szükség a megfelelő titkos kulcs megadásához. Például:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

További információért lásd: [Csatlakozás a HDInsighthoz (Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

Miután csatlakozott, a következőhöz hasonló adatkérés érkezik:

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Több párhuzamos felhasználó engedélyezése

Több párhuzamos felhasználó úgy tud engedélyezni, ha több felhasználót is hozzáad ahhoz az élcsomóponthoz, amelyen az RStudio közösségi verziója fut.

HDInsight-fürt létrehozásakor két felhasználót kell megadnia: egy HTTP-felhasználót és egy SSH-felhasználót.

![Hitelesítő adatok megadása a fürtfelhasználóhoz és az SSH-felhasználóhoz](./media/r-server-get-started/concurrent-users-1.png)

- **Fürt bejelentkezési felhasználóneve**: HTTP-felhasználó a létrehozott HDInsight-fürtöket védő HDInsight-átjárón át történő hitelesítéshez. Ez a HTTP-felhasználó használatos az Ambari és a YARN felhasználói felületének, valamint más felhasználóifelület-összetevőknek az elérésére.
- **Secure Shell- (SSH-) felhasználónév**: SSH-felhasználó, aki a fürtöt biztonságos felületen keresztül éri el. Ez a felhasználó a Linux rendszerben az összes főcsomópont, munkavégző csomópont és élcsomópont felhasználója. Így SSH-t használhat a távoli fürt bármely csomópontjának elérésére.

A Microsoft R Serveren a HDInsight típusú fürtökben használt RStudio Server Community verziója bejelentkezési mechanizmusként csak Linux-felhasználónevet és -jelszót fogad el. Nem támogatja a jogkivonatok átadását. Tehát, ha létrehozott egy új fürtöt, és az RStudiót szeretné használni az eléréséhez, akkor kétszer kell bejelentkeznie.

1. Jelentkezzen be a HTTP-felhasználói hitelesítő adatokkal a HDInsight-átjárón: 

    ![A HTTP-felhasználói hitelesítő adatok megadása](./media/r-server-get-started/concurrent-users-2a.png)

2. Használja az SSH-felhasználói hitelesítő adatokat az RStudióba való bejelentkezéshez:
  
    ![SSH hitelesítő adatok megadása](./media/r-server-get-started/concurrent-users-2b.png)

Jelenleg HDInsight-fürt üzembe helyezésekor csak egy SSH-felhasználói fiókot hozhat létre. A Microsoft R Server elérésének engedélyezéséhez több felhasználó számára a HDInsight-fürtökben további felhasználókat kell létrehoznia a Linux rendszerben.

Mivel az RStudio Server Community verziója a fürt élcsomópontján fut, három lépést kell elvégezni:

1. A létrehozott SSH-felhasználó használata az élcsomópontra való bejelentkezéshez.
2. További Linux-felhasználók hozzáadása az élcsomóponthoz.
3. Az RStudio Community verziójának használata a létrehozott felhasználóval.

### <a name="use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>A létrehozott SSH-felhasználó használata az élcsomópontra való bejelentkezéshez

Töltsön le egy SSH-eszközt (például a PuTTY-t), és használja a meglévő SSH-felhasználót a bejelentkezéshez. Ezután az élcsomópont eléréséhez kövesse a [Csatlakozás a HDInsighthoz (Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md) szakasz utasításait. Az élcsomópont címe az R Serverhez a HDInsight-fürtön a következő: **clustername-ed-ssh.azurehdinsight.net**


### <a name="add-more-linux-users-to-the-edge-node"></a>További Linux-felhasználók hozzáadása az élcsomóponthoz

Felhasználó élcsomóponthoz adásához futtassa ezeket a parancsokat:

    sudo useradd yournewusername -m
    sudo passwd yourusername

A programnak a következő elemeket kell visszaadnia: 

![A sudo parancsok kimenete](./media/r-server-get-started/concurrent-users-3.png)

Amikor a rendszer az aktuális Kerberos-jelszót kéri, nyomja meg az Enter billentyűt a figyelmen kívül hagyáshoz. A `useradd` parancs `-m` kapcsolója jelzi, hogy a rendszer létrehoz egy kezdőmappát a felhasználó számára. Ez a mappa szükséges az RStudio Community verziójához.


### <a name="use-the-rstudio-community-version-with-the-created-user"></a>Az RStudio Community verziójának használata a létrehozott felhasználóval

Használja a létrehozott felhasználót az RStudióba való bejelentkezéshez:

![Rstudio bejelentkezési oldala](./media/r-server-get-started/concurrent-users-4.png)

Az RStudio jelzi, hogy az új felhasználót használja a fürtre való bejelentkezéshez (itt például az **sshuser6** felhasználót): 

![Az új felhasználó helye az RStudio parancssávján](./media/r-server-get-started/concurrent-users-5.png)

Az eredeti hitelesítő adatokkal (alapértelmezés szerint ez az **sshuser**) egyidejűleg egy másik böngészőablakból is bejelentkezhet.

Feladat küldéséhez használhatja a ScaleR-függvényeket. Íme egy példa a feladat futtatására használt parancsokra:

    # Set the HDFS (Azure Blob storage) location of example data
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storing data temporarily
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

    # Set the directory in bigDataDirRoot to load the data
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (Blob storage) file system
    hdfsFS <- RxHdfsFileSystem()

    # Create an info list for the airline data
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in the local system
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

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


Figyelje meg, hogy a beküldött feladatok más felhasználónév alatt szerepelnek a YARN felhasználói felületén:

![Felhasználók listája a YARN felhasználói felületén](./media/r-server-get-started/concurrent-users-6.png)

Figyelje meg azt is, hogy az újonnan felvett felhasználók nem rendelkeznek gyökérjogosultságokkal a Linux rendszerben. Azonban ugyanolyan hozzáférésük van a távoli HDFS-fájlrendszerben és a Blob-tárolóban található összes fájlhoz.


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Az R-konzol használata

1. Az SSH-munkamenetből a következő paranccsal indíthatja el az R-konzolt:  

        R

2. A következőhöz hasonló kimenetnek kell megjelennie:
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.
        >

3. A `>` adatkérésben beírhatja az R-kódot. Az R Server olyan csomagokat tartalmaz, amelyekkel könnyedén használhatja a Hadoopot és futtathat elosztott számításokat. A HDInsight-fürt alapértelmezett fájlrendszerének gyökerét például a következő paranccsal tekintheti meg:

        rxHadoopListFiles("/")

4. A Blob-tároló címzési stílusát is használhatja:

        rxHadoopListFiles("wasb:///")


## <a name="use-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Az R Server használata HDI-n a Microsoft R Server vagy a Microsoft R ügyfél egy távoli példányáról

A HDI Hadoop Spark számítási környezetet el lehet érni a Microsoft R Server vagy a Microsoft R-ügyfél számítógépen vagy laptopon futó távoli példányáról. További információkért lásd a [Számítási környezet létrehozása Sparkhoz](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) című cikk „Microsoft R Server használata Hadoop-ügyfélként” szakaszát. Ehhez adja meg a következő beállításokat, amikor meghatározza az RxSpark számítási környezetet a laptopon: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches és sshProfileScript. Például:


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

A számítási környezetek használatával vezérelheti, hogy a számítás helyben történik-e az élcsomóponton, vagy elosztottan a HDInsight-fürtben lévő csomópontok között.

1. Az RStudio Serverről vagy az R-konzolról (SSH-munkamenetben) a következő kód használatával töltse be a példaadatokat a HDInsight alapértelmezett tárolójába:

        # Set the HDFS (Blob storage) location of example data
        bigDataDirRoot <- "/example/data"

        # Create a local folder for storing data temporarily
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

        # Set the directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Hozzon létre adatinformációkat, és határozzon meg két adatforrást, hogy használhassa az adatokat:

        # Define the HDFS (Blob storage) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create an info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # Get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in HDFS
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in the local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # Formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Futtasson logisztikai regressziót az adatokon a helyi számítási környezettel:

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

4. Futtassa ugyanezt a logisztikai regressziót a Spark környezet használatával. A Spark környezet elosztja a feldolgozást a HDInsight-fürt összes munkavégző csomópontja között.

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

Az R Serverrel könnyedén futtathatja a meglévő R-kódokat a fürt több csomópontján az `rxExec` használatával. Ez a függvény akkor hasznos, amikor paraméteres frissítést vagy szimulációkat végez. A következő kód az `rxExec` használatának példája:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Ha továbbra is a Spark vagy a MapReduce környezetet használja, ez a parancs visszaadja azon munkavégző csomópontok `nodename` értékét, amelyeken a `(Sys.info()["nodename"])` kódot futtatta. Egy négy csomópontból álló fürtön például a következőhöz hasonló kimenetet kaphat:

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

Az R Server 9.1 és újabb verziókban elérhető funkciója lehetővé teszi az adatok közvetlen elérését a Hive és a Parquet eszközökben a Spark számítási környezet ScaleR-függvényei általi használatra. Ezek a képességek az RxHiveData és RxParquetData nevű új ScaleR adatforrás-függvényeken keresztül érhetők el. Ezek a függvények a Spark SQL használatán keresztül töltenek adatokat közvetlenül a Spark DataFrame-be a ScaleR által végzett elemzéshez.  

Az alábbi kód az új függvények használatának módjáról mutat példákat:

    #Create a Spark compute context
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model
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

    #Check on Spark data objects, clean up, and close the Spark session
    lsObj <- rxSparkListData() #Two data objects are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() #It should show an empty list
    rxSparkDisconnect(myHadoopCluster)


Az új függvényekről az R Server online súgójában találhat további információt az `?RxHivedata` és az `?RxParquetData` parancsok használatával.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>További R-csomagok telepítése az élcsomópontra

Ha további R-csomagokat szeretne telepíteni az élcsomóponton, az `install.packages()` parancsot használhatja közvetlenül az R-konzolról, amikor SSH-n keresztül csatlakozik az élcsomóponthoz. Ha azonban a fürt munkavégző csomópontjaira kell R-csomagokat telepítenie, szkriptműveletet kell használnia.

A szkriptműveletek olyan Bash-szkriptek, amelyekkel konfigurációs módosítások végezhetők a HDInsight-fürtön, vagy további szoftverek, például további R-csomagok telepíthetők. Ha szkriptművelettel szeretne további csomagokat telepíteni, használja a következő lépéseket:

> [!IMPORTANT]
> A további R csomagok szkriptműveletekkel végzett telepítése csak a fürt létrehozása után használható. Ne használja ezt az eljárást a fürt létrehozása során, mivel a szkript az R Server teljes telepítésére és konfigurálására támaszkodik.
>
>

1. Az [Azure Portalon](https://portal.azure.com) válassza ki az R Servert a HDInsight-fürtön.

2. A **Beállítások** ablaktáblán válassza a **Szkriptműveletek** > **Új küldése** lehetőséget.

   ![A Szkriptműveletek ablaktábla képe](./media/r-server-get-started/scriptaction.png)

3. A **Szkriptművelet küldése** ablaktáblán adja meg a következő információkat:

   * **Név**: A szkriptet azonosító egyszerű név.

   * **Bash-szkript URI azonosítója**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Fej**: Ez az elem ne legyen bejelölve.

   * **Feldolgozó**: Ez az elem ne legyen bejelölve.

   * **Zookeeper**: Ez az elem ne legyen bejelölve.

   * **Élcsomópontok**: Ezt az elemet be kell jelölni.

   * **Paraméterek**: A telepíteni kívánt R-csomagok, például: `bitops stringr arules`.

   * **Ha megőrzi a...**: Ezt az elemet be kell jelölni.  

   > [!NOTE]
   > Alapértelmezés szerint az összes R-csomag a telepített R Server verziójának megfelelő Microsoft R Application Network tár-pillanatfelvételéből van telepítve. Ha a csomagok újabb verzióját szeretné telepíteni, akkor van némi inkompatibilitási kockázat. Azonban ez a fajta telepítés lehetséges, ha a `useCRAN` parancsot a csomaglista első elemeként adja meg, például: `useCRAN bitops, stringr, arules`.  
   > 
   > Néhány R csomag további Linux rendszerű könyvtárakat igényel. A kényelem érdekében előre telepítettük a 100 legnépszerűbb R-csomaghoz szükséges függőségeket. Ha a telepített R-csomagok további könyvtárakat igényelnek, le kell töltenie az itt használt alapvető szkriptet, és lépéseket kell hozzáadnia a rendszerkönyvtárak telepítéséhez. Ezután fel kell töltenie a módosított szkriptet egy nyilvános blob-tárolóba az Azure Storage-ben, és a módosított szkripttel kell telepítenie a csomagokat.
   >
   > A szkriptműveletek fejlesztésével kapcsolatos további információkért lásd: [Szkriptművelet fejlesztése](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Szkriptműveletek hozzáadása](./media/r-server-get-started/submitscriptaction.png)

4. Válassza a **Létrehozás** lehetőséget a szkript futtatásához. A szkript befejezése után az R csomagok elérhetők az összes munkavégző csomóponton.


## <a name="configure-microsoft-r-server-operationalization"></a>A Microsoft R Server operacionalizálásának konfigurálása

Amikor elkészült az adatmodellezés, működőképessé teheti a modellt, hogy előrejelzéseket végezzen. A Microsoft R Server operacionalizálásának konfigurálásához kövesse az alábbi lépéseket:

1. Használja az `ssh` parancsot az élcsomóponthoz, például: 

       ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

2. Lépjen a megfelelő verzió könyvtárára, és használja az `sudo dotnet` parancsot a .dll-fájlhoz. 

   Microsoft R Server 9.1 esetén:

       cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
       sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

   Microsoft R Server 9.0 esetén:

       cd /usr/lib64/microsoft-deployr/9.0.1
       sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Ha a Microsoft R Server operacionalizálását egy beépített konfigurációban szeretné konfigurálni, tegye a következőt:

   a. Válassza a(z) `Configure R Server for Operationalization` lehetőséget.

   b. Válassza a(z) `A. One-box (web + compute nodes)` lehetőséget.

   c. Adja meg az `admin`-felhasználó jelszavát.

   ![Beépített operacionalizálás](./media/r-server-get-started/admin-util-one-box-.png)

4. Választható lépésként, az alábbi módon végezhet diagnosztikai tesztelést:

   a. Válassza a(z) `6. Run diagnostic tests` lehetőséget.

   b. Válassza a(z) `A. Test configuration` lehetőséget.

   c. Felhasználónéként írja be a `admin` kifejezést, majd adja meg az előző konfigurációs lépésben használt jelszót.

   d. Erősítse meg a következőt: `Overall Health = pass`.

   e. Lépjen ki az admin segédprogramból.

   f. Lépjen ki az SSH-ból.

   ![Diagnosztika az operacionalizáláshoz](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>Ha hosszú késleltetést tapasztal, amikor egy Spark számítási környezetben mrsdeploy függvényekkel létrehozott webszolgáltatást próbál felhasználni, előfordulhat, hogy hozzá kell adnia néhány hiányzó mappát. A Spark-alkalmazás egy *rserve2* nevű felhasználóhoz tartozik, ha egy webszolgáltatásból hívja meg mrsdeploy függvényeken keresztül. Megkerülő megoldás a problémára:

    #Create these required folders for user rserve2 in local and HDFS
    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    #Create a new Spark compute context 
    rxSparkConnect(reset = TRUE)


Az operacionalizálás konfigurációja ezzel befejeződött. Most már használhatja az mrsdeploy csomagot R Clienten, hogy kapcsolódhasson az élcsomóponti operacionalizáláshoz. Ezután megkezdheti a szolgáltatások, például a [távoli futtatás](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) és a [webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) használatát. Attól függően, hogy a fürt virtuális hálózaton van-e beállítva, szükség lehet porttovábbító bújtatás kialakítására az SSH-bejelentkezésen keresztül.

### <a name="r-server-cluster-on-a-virtual-network"></a>R Server-fürt egy virtuális hálózaton

Győződjön meg arról, hogy engedélyezett a forgalom az 12800-as porton az élcsomópont felé. Így az élcsomópont használatával kapcsolódhat az operacionalizálási szolgáltatáshoz.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Ha a `remoteLogin()` metódus nem tud csatlakozni az élcsomóponthoz, de SSH használatával tud csatlakozni az élcsomóponthoz, ellenőrizze, hogy a szabály, amely engedélyezi a forgalmat az 12800-as porton, megfelelően van-e beállítva. Ha a probléma továbbra is jelentkezik, egy másik megoldás segítségével is beállíthat porttovábbító alagutat az SSH-n keresztül. Útmutatásért lásd a következő szakaszt.

### <a name="r-server-cluster-not-set-up-on-a-virtual-network"></a>Nem virtuális hálózaton beállított R Server-fürt

Ha a fürt nem a virtuális hálózaton van beállítva vagy a virtuális hálózaton keresztül való kapcsolódás során probléma merül fel, akkor használhatja az SSH porttovábbító alagutat:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Beállíthatja a PuTTY-on is:

![PuTTY SSH kapcsolat](./media/r-server-get-started/putty.png)

Miután az SSH-munkamenet aktív, a rendszer a számítógép 12800-as portjáról az élcsomópont 12800-as portjára továbbítja a forgalmat az SSH-munkameneten keresztül. Győződjön meg arról, hogy a `127.0.0.1:12800` címet használja a `remoteLogin()` metódusban. Ez a metódus a porttovábbításon keresztül jelentkezik be az élcsomópont működőképessé tételébe.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Microsoft R Server operacionalizálási számítási csomópontok méretezése a HDInsight feldolgozó csomópontjain

### <a name="decommission-the-worker-nodes"></a>A feldolgozó csomópontok leszerelése

A Microsoft R Servert jelenleg nem a Yarnon keresztül kezeli a rendszer. Ha a feldolgozó csomópontokat nem szereli le, a Yarn Resource Manager nem a várakozásoknak megfelelően fog működni, mert nem fogja látni a kiszolgáló által használt erőforrásokat. Ezen helyzet elkerülése érdekében javasoljuk a munkavégző csomópontok leszerelését a számítási csomópontok horizontális felskálázása előtt.

Feldolgozó csomópontok leszerelése:

1. Jelentkezzen be a HDI-fürt Ambari konzoljába, és válassza a **Gazdagépek** lapot.
2. Jelölje ki a leszerelendő feldolgozó csomópontokat, és válassza a **Műveletek** > **Kiválasztott gazdagépek** > **Gazdagépek** > **Karbantartási mód bekapcsolása** lehetőséget. A következő képen például a wn3 és a wn4 pontokat választottuk ki leszerelésre.  

   ![A karbantartási mód bekapcsolására szolgáló parancsok képernyőképe](./media/r-server-get-started/get-started-operationalization.png)  

3. Válassza a **Műveletek** > **Kiválasztott csomópontok** > **AdatCsomópontok** > **Leszerelés** lehetőséget.
4. Válassza a **Műveletek** > **Kiválasztott csomópontok** > **CsomópontKezelők** > **Leszerelés** lehetőséget.
5. Válassza a **Műveletek** > **Kiválasztott csomópontok** > **AdatCsomópontok** > **Leállítás** lehetőséget.
6. Válassza a **Műveletek** > **Kiválasztott csomópontok** > **CsomópontKezelők** > **Leállítás** lehetőséget.
7. Válassza a **Műveletek** > **Kiválasztott gazdagépek** > **Gazdagépek** >  **Összes összetevő leállítása** lehetőséget.
8. Szüntesse meg a feldolgozó csomópontok kijelölését, és jelölje ki az átjárócsomópontokat.
9. Válassza a **Műveletek** > **Kiválasztott gazdagépek** > **Gazdagépek** >  **Összes összetevő újraindítása** lehetőséget.

### <a name="configure-compute-nodes-on-each-decommissioned-worker-node"></a>Számítási csomópontok konfigurálása az összes leszerelt feldolgozó csomóponton

1. Csatlakozzon az összes leszerelt feldolgozó csomóponthoz SSH használatával.
2. Futtassa a rendszergazdai segédprogramot a `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll` használatával.
3. Az `Configure R Server for Operationalization` lehetőség kiválasztásához írja be az `1` kifejezést.
4. Az `C. Compute node` lehetőség kiválasztásához írja be az `c` kifejezést. Ez a lépés konfigurálja a számítási csomópontot a feldolgozó csomóponton.
5. Lépjen ki az admin segédprogramból.

### <a name="add-compute-nodes-details-on-the-web-node"></a>Számítási csomópontok részleteinek megadása a webcsomóponton

Miután minden leszerelt feldolgozó csomópontot konfigurált a számítási csomóponton történő futtatásra, térjen vissza az élcsomóponthoz, és adja hozzá a leszerelt feldolgozó csomópontok IP címét a Microsoft R Server webcsomópontjának konfigurációjában:

1. Csatlakozzon az élcsomóponthoz SSH használatával.
2. Futtassa az `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` parancsot.
3. Keresse meg az `URIs` szakaszt, és adja hozzá a munkavégző csomópontok IP-címét és portrészleteit.

    ![Az élcsomóponthoz tartozó parancssor](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>További lépések

Mostanra biztosan megismerte az R Servert tartalmazó HDInsight-fürt létrehozásának módját. Továbbá az R-konzol SSH-munkamenetből történő használatának alapjait is megismerhette. A következő témakörök az R Server HDInsighton történő kezelésének és az azzal történő munkavégzésnek egyéb módjait ismertetik:

* [Számítási környezeti beállítások a HDInsighton belüli R Server esetében](r-server-compute-contexts.md)
* [Azure Storage lehetőségek a HDInsighton belüli R Server esetében](r-server-storage.md)
