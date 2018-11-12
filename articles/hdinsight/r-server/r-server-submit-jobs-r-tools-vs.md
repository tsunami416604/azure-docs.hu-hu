---
title: Feladatok beküldése az R Tools for Visual Studio – Azure HDInsight
description: Egy HDInsight-fürtön a Visual Studio helyi gépről R-feladatok elküldéséhez.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 400cb16e4f4440283a783116c4ee843bc0a7344c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248571"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Feladatok beküldése az R Tools for Visual Studio használatával

[Az R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) egy ingyenes, nyílt forráskódú bővítmény a Community (ingyenes), Professional, és Enterprise kiadás mindkét [Visual Studio 2017](https://www.visualstudio.com/downloads/), és [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129)vagy újabb verziója.

RTVS bővíti az R-munkafolyamat eszközök felajánlásával a [R Interaktivní okno](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), az intellisense (kódkiegészítés), [jeleníti meg képi megjelenítés](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) keresztül R-kódtárak ggplot2 és ggviz, például[R-kód hibakeresése](https://docs.microsoft.com/visualstudio/rtvs/debugging), stb.

## <a name="set-up-your-environment"></a>A környezet kialakítása

1. Telepítés [R Tools for Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![A Visual Studio 2017 telepítése RTVS](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Válassza ki a *adatelemzési és analitikai alkalmazások* számítási feladatot, majd válassza ki a **R nyelv támogatása**, **futásidő-támogatás az R-fejlesztéshez**, és  **A Microsoft R Client** beállítások.

3. Szüksége lesz az SSH-hitelesítésre szolgáló nyilvános és titkos kulcsokat.
<!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Telepítés [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) a gépen. Machine Learning-kiszolgáló biztosítja az [ `RevoScaleR` ](https://msdn.microsoft.com/microsoft-r/scaler/scaler) és `RxSpark` funkciók.

5. Telepítse [PuTTY](http://www.putty.org/) biztosít számítási környezetek használatával futtassa `RevoScaleR` függvények a helyi ügyfélről a HDInsight-fürthöz.

6. Lehetősége van az adatok adatelemzési beállítások alkalmazásához, amellyel az új elrendezéssel bővültek a munkaterület az R tools for Visual Studio környezetében.
    1. A jelenlegi Visual Studio-beállítások mentéséhez használja a **eszközök > importálási és exportálási beállítások** parancsot, majd válassza a **kijelölt környezeti beállítások exportálása** , és adjon meg egy fájlnevet. Ezek a beállítások visszaállításához használja ugyanazt a parancsot, és válassza ki **importálás kiválasztott környezeti beállítások**.

    2. Nyissa meg a **R Tools** menüben, majd válassza ki **Data Science beállítások...** .

        ![Data Science beállítások...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

    > [!NOTE]
    > A megközelítéssel az 1. lépésben, is mentheti, és állítsa vissza a személyre szabott adatokat adatszakértő elrendezés helyett a **Data Science beállítások** parancsot.

## <a name="execute-local-r-methods"></a>Hajtsa végre a helyi R-metódusok

1. Hozzon létre a [Machine Learning-szolgáltatások HDInsight-fürt](r-server-get-started.md).
2. Telepítse a [RTVS bővítmény](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Töltse le a [minták zip-fájl](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Nyissa meg `examples/Examples.sln` elindíthatja a megoldást a Visual Studióban.
5. Nyissa meg a `1-Getting Started with R.R` fájlt a `A first look at R` megoldásmappára.
6. Indítása a fájl elején a Ctrl + Enter küldése minden egyes sorban, egyenként, az R interaktív ablakban. Néhány sort a csomagok telepítési eltarthat egy ideig.
    * Azt is megteheti válassza ki az összes sort az R-fájl (Ctrl + A), majd hajtsa végre az összes (Ctrl + Enter), vagy a végrehajtás interaktív ikonra az eszköztáron.
        ![Végrehajtás interaktív](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Után minden sorban a parancsfájlban futtatja, akkor ehhez hasonló kimenetnek kell megjelennie:

    ![Data Science beállítások...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Machine Learning-szolgáltatások HDInsight-fürthöz-feladatok elküldése

PuTTY ellátott Windows számítógépre a Microsoft ML Server vagy a Microsoft R ügyfél használata esetén létrehozhat egy számítási környezetet, futtatandó elosztott `RevoScaleR` függvények a helyi ügyfélről a HDInsight-fürthöz. Használat `RxSpark` hozhat létre a számítási környezet, adja meg a felhasználónevet, a Hadoop-fürt élcsomópont, SSH-kapcsolók, és így tovább.

1. Az élcsomópont állomásnév megkereséséhez az Azure-ban a Machine Learning-szolgáltatások HDInsight-fürt panel megnyitásához, majd jelölje ki **Secure Shell (SSH)** az áttekintő panel felső menüjében.

    ![Secure Shell (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Másolás a **Határcsomópont állomásneve** értéket.

    ![Határcsomópont állomásneve](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Illessze be az alábbi kód az R interaktív ablakban, a Visual Studióban, a változók értékeit, a telepítő a környezet megfelelő módosítása.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
      sshHostname = mySshHostname,
      sshSwitches = mySshSwitches,
      sshProfileScript = mySshProfileScript,
      consoleOutput = TRUE,
      hdfsShareDir = myHdfsShareDir,
      shareDir = myShareDir,
      sshClientDir = mySshClientDir
    )
    
    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```
    
    ![A Spark környezet beállítása](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. Az R interaktív ablakban hajtsa végre a következő parancsokat:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ![Sikeres rx parancs végrehajtása](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Ellenőrizze, hogy a `rxHadoopCopy` a rendszer sikeresen átmásolta a `people.json` fájlt a példa adatok mappából az újonnan létrehozott `/user/RevoShare/newUser` mappa:

    1. Az Azure-ban a Machine Learning-szolgáltatások HDInsight fürt ablaktáblán válassza **tárfiókok** elemet a bal oldali menüben.

        ![Tárfiókok](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Válassza ki a fürthöz, és jegyezze fel a tároló/könyvtár nevét, ami az alapértelmezett tárfiók.

    3. Válassza ki **tárolók** a a storage-fiók panelen a bal oldali menüből.

        ![Containers](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Válassza ki a fürt tároló nevét, keresse meg a a **felhasználói** mappa (szükség lehet a kattintson *Továbbiak betöltése* a lista alján), majd *RevoShare*, majd **Új_felhasználó**. A `people.json` fájlt kell megjelennie a `newUser` mappát.

        ![Másolt fájl](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Miután végzett, a jelenlegi Spark-környezetet használja, le kell állítania. Több környezet nem futtathatók egyszerre.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>További lépések

* [Számítási környezeti beállítások a HDInsight a Machine Learning-szolgáltatások](r-server-compute-contexts.md)
* [ScaleR és SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) légitársaság járatkésések becsléséhez példát tartalmaz.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
