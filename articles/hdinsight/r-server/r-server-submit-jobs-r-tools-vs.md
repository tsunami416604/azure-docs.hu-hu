---
title: Az R eszközök feladatok elküldéséhez a Visual Studio - Azure HDInsight |} Microsoft Docs
description: HDInsight-fürtök a helyi gépről Visual Studio R feladatok elküldéséhez.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: c6f6e691ef4b317854aef1d7397d5fb840d25ff2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Feladatok beküldése az R Tools for Visual Studio használatával

[R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) egy ingyenes, nyílt forráskódú kiterjesztése a Community (ingyenes), Professional és Enterprise kiadás együtt [Visual Studio 2017](https://www.visualstudio.com/downloads/), és [Visual Studio 2015 Update 3](http://go.microsoft.com/fwlink/?LinkId=691129)vagy újabb verzióját.

RTVS kibővíti az R munkafolyamat eszközök felajánlásával a [R interaktív ablak](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL) intellisense (kód) befejezése után [képi megjelenítés tőzsdei](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) R szalagtárak például ggplot2 és ggviz, keresztül[R-kód hibakeresés](https://docs.microsoft.com/visualstudio/rtvs/debugging), stb.

## <a name="set-up-your-environment"></a>A környezet kialakítása

1. Telepítés [R Tools for Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![A Visual Studio 2017 RTVS telepítése](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Válassza ki a *adattudomány és analitikai alkalmazások* munkaterhelés –, majd válassza ki a **R nyelven támogatása**, **R fejlesztési futásidejű támogatási**, és  **Microsoft R ügyfél** beállítások.

3. Nyilvános és titkos kulcsokat az SSH hitelesítés van szükség.
<!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Telepítés [R Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) a számítógépen. R Server biztosít a [ `RevoScaleR` ](https://msdn.microsoft.com/microsoft-r/scaler/scaler) és `RxSpark` funkciók.

5. Telepítés [PuTTY](http://www.putty.org/) arra, hogy a számítási környezet futtatásához `RevoScaleR` funkciók a helyi ügyfélről a HDInsight-fürthöz.

6. Lehetősége van a tudományos-beállítások alkalmazása a Visual Studio környezet, amely új elrendezés nyújt a munkaterület az R-eszközök.
    1. A jelenlegi Visual Studio beállítások mentéséhez használja a **eszközök > importálási és exportálási beállítások** parancsot, majd válasszon **kijelölt környezeti beállítások exportálása** és adjon meg egy fájlnevet. Ezek a beállítások visszaállításához használja ugyanezt a parancsot, és válassza ki **importálási kijelölt környezeti beállítások**.

    2. Lépjen a **R eszközök** menü elemet, majd válassza ki **tudományos beállítások...** .

        ![Tudományos beállítások...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

    > [!NOTE]
    > Az 1. lépésben a módszert használva is mentheti, és állítsa vissza a testreszabott adatok tudósok elrendezés helyett a **tudományos beállítások** parancsot.

## <a name="execute-local-r-methods"></a>Helyi R metódusok végrehajtása

1. Hozzon létre a [R Server HDInsight-fürt](r-server-get-started.md).
2. Telepítse a [RTVS bővítmény](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Töltse le a [minták a zip-fájl](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Nyissa meg `examples/Examples.sln` elindíthatja a Visual Studio megoldás.
5. Nyissa meg a `1-Getting Started with R.R` fájlt a `A first look at R` mappát.
6. Elkezdi a fájl elején a Ctrl + Enter küldését soronként, egyenként, az R interaktív ablak. Néhány sor csomagok telepítésének igénybe vehet.
    * Azt is megteheti válassza ki az összes sort az R-fájl (Ctrl + A), majd hajtható végre az összes (Ctrl + Enter), vagy jelölje ki a végrehajtás interaktív ikont az eszköztáron.
        ![Interaktív végrehajtása](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Után a sorokat a parancsfájlban futtatja, egy hasonló kimenetnek kell megjelennie:

    ![Tudományos beállítások...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-r-cluster"></a>Az HDInsight R fürt feladatok elküldéséhez

Egy Microsoft R Server vagy a Microsoft R-ügyfélprogram segítségével PuTTY ellátott Windows rendszerű számítógépről egy elosztott futó számítási környezetet hozhat létre `RevoScaleR` funkciók a helyi ügyfélről a HDInsight-fürthöz. Használjon `RxSpark` létrehozása a számítási környezet, adja meg a felhasználónevet, a Hadoop-fürt élcsomópont, SSH kapcsolók, és így tovább.

1. A élcsomópont állomásnév található, nyissa meg az Azure HDInsight R fürt ablaktábláján, majd válassza ki a **Secure Shell (SSH)** a felső menüjében az Áttekintés ablaktábláján.

    ![Secure Shell (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Másolás a **peremhálózati csomópont állomásnév** érték.

    ![Határcsomópont állomásneve](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Az alábbi kód beillesztése az R interaktív ablak a Visual Studio, a környezet megfelelő telepítő-változók értékeit módosítása.

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

4. Az R interaktív ablakban hajtható végre a következő parancsokat:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ![A sikeres a rx parancs végrehajtása](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Ellenőrizze, hogy a `rxHadoopCopy` sikeresen másolt a `people.json` fájlt adatmappából a példa az újonnan létrehozott `/user/RevoShare/newUser` mappába:

    1. Az Azure-ban a HDInsight R fürt ablaktáblán válassza ki a **tárfiókok** a bal oldali menüből.

        ![Tárfiókok](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Válassza ki a fürthöz, és jegyezze fel a tároló/könyvtár nevét, az alapértelmezett tárfiók.

    3. Válassza ki **tárolók** a a storage-fiók panelen a bal oldali menüből.

        ![Containers](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Válassza ki a fürt tároló nevét, keresse meg a a **felhasználói** mappában (Előfordulhat, hogy kattintson *betöltése több* a lista alján) elemre, majd válassza *RevoShare*, majd **Új_felhasználó**. A `people.json` fájl kell megjelennie a `newUser` mappát.

        ![Másolt fájl](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Miután befejezte az aktuális Spark-környezetet használ, le kell állítania. Egyszerre több környezet nem futtatható.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>További lépések

* [Számítási környezeti beállítások a HDInsighton belüli R Server esetében](r-server-compute-contexts.md)
* [ScaleR és SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) légitársaság repülési késleltetés előrejelzéseket egy példát.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
