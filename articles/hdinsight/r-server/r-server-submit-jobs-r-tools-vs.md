---
title: Feladatok beküldése az R Tools for Visual Studio szolgáltatásból – Azure HDInsight
description: R-feladatok küldése a helyi Visual Studio-gépről egy HDInsight-fürtbe.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 73d1478ec2d6c90428f22a30ec82634df115d2f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435252"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Feladatok beküldése az R Tools for Visual Studio használatával

[Az R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) a Visual Studio 2017 és a Visual Studio 2015 3-as vagy újabb frissítésének ingyenes, nyílt forráskódú bővítménye a Visual Studio [2017](https://www.visualstudio.com/downloads/)és [a Visual Studio 2015 3-as](https://go.microsoft.com/fwlink/?LinkId=691129) vagy újabb frissítése számára. Az RTVS nem érhető el a [Visual Studio 2019-ben.](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019)

RTVS növeli az R munkafolyamat kínál eszközöket, mint például az [R Interactive ablak](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (kód kiegészítés), telek [megjelenítés](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) r könyvtárak, mint a ggplot2 és ggviz, R [kód hibakeresés](https://docs.microsoft.com/visualstudio/rtvs/debugging), és így tovább.

## <a name="set-up-your-environment"></a>A környezet kialakítása

1. Telepítse [az R Tools for Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio)alkalmazást .

    ![Az RTVS telepítése a Visual Studio 2017-ben](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Válassza ki az *adatelemzési és elemzési alkalmazások* számítási feladatok, majd válassza ki az **R nyelvi támogatás**, **Runtime támogatás R fejlesztési**és a Microsoft R **ügyfél** beállításokat.

3. Az SSH-hitelesítéshez nyilvános és titkos kulcsokra van szükség.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Telepítse [az ML Server alkalmazást](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) a gépre. Ml Server [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) biztosítja `RxSpark` a és a funkciókat.

5. Telepítse [a PuTTY-t,](https://www.putty.org/) hogy `RevoScaleR` számítási környezetet biztosítson a helyi ügyfél függvényei futtatásához a HDInsight-fürthöz.

6. Lehetősége van az adatelemzési beállítások alkalmazására a Visual Studio-környezetre, amely új elrendezést biztosít az R-eszközök munkaterületéhez.
   1. A Visual Studio aktuális beállításainak mentéséhez használja az **Eszközök > importálási és exportálási beállítások parancsot,** majd válassza **a Kijelölt környezeti beállítások exportálása** lehetőséget, és adja meg a fájlnevét. A beállítások visszaállításához használja ugyanazt a parancsot, és válassza **a Kijelölt környezeti beállítások importálása lehetőséget**.

   2. Nyissa meg az **R Eszközök** menüpontot, és válassza **az Adatelemzési beállítások...** lehetőséget.

       ![Visual Studio adatelemzési beállításai](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Az 1. **Data Science Settings**

## <a name="execute-local-r-methods"></a>Helyi R-metódusok végrehajtása

1. Hozza létre a HDInsight ML Services-fürtöt.
2. Telepítse az [RTVS kiterjesztést](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Töltse le a [mintákat zip fájlt](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Nyissa `examples/Examples.sln` meg a megoldás elindítását a Visual Studióban.
5. Nyissa `1-Getting Started with R.R` meg a `A first look at R` fájlt a megoldás mappában.
6. A fájl tetejétől kezdve nyomja le a Ctrl+Enter billentyűkombinációt, ha minden sort egyenként elküldazna az R Interactive ablakba. Egyes sorok a csomagok telepítésekor eltarthat egy ideig.
    * Másik lehetőségként kijelölheti az R fájl összes sorát (Ctrl+A), majd végrehajthatja az összeset (Ctrl+Enter), vagy az eszköztáron az Interaktív végrehajtása ikont.

        ![Visual Studio interaktív végrehajtása](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. A parancsfájl összes sorának futtatása után a következőhöz hasonló kimenetet kell látnia:

    ![Visual Studio-munkaterület R eszközei](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Feladatok küldése HDInsight ML Services-fürtbe

A Microsoft ML Server/Microsoft R client segítségével egy PuTTY-val felszerelt Windows-számítógépről `RevoScaleR` létrehozhat egy számítási környezetet, amely elosztott függvényeket futtat a helyi ügyféltől a HDInsight-fürtig. A `RxSpark` számítási környezet létrehozásához, a felhasználónév, az Apache Hadoop-fürt peremhálózati csomópontjának, az SSH-kapcsolóknak stb.

1. Az ML Services hálózati csomópont címe `CLUSTERNAME-ed-ssh.azurehdinsight.net` `CLUSTERNAME` a HDInsight, ahol az ML Services-fürt neve.

1. Illessze be a következő kódot a Visual Studio R Interactive ablakába, és módosítsa a beállítási változók értékeit a környezetnek megfelelően.

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

   ![Apache spark beállítása a környezetben](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Hajtsa végre a következő parancsokat az R Interactive ablakban:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ![Sikeres rx](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) parancs végrehajtása
1. Ellenőrizze, `rxHadoopCopy` hogy a `people.json` sikeresen másolt a fájlt `/user/RevoShare/newUser` a példa adatmappából az újonnan létrehozott mappába:

    1. Az Azure HDInsight ML Services fürtpaneljében válassza a **Storage-fiókok** lehetőséget a bal oldali menüből.

        ![Azure HDInsight Storage-fiókok](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Válassza ki a fürt alapértelmezett tárfiókját, jegyezve meg a tároló/könyvtár nevét.

    3. Válassza a tárolófiók ablaktáblájának bal oldali menüjében válassza a **Tárolók** lehetőséget.

        ![Azure HDInsight storage-tárolók](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Válassza ki a fürt tárolójának nevét, keresse meg a **felhasználói** mappát (előfordulhat, hogy a lista alján a *További betöltés* e gombra kell kattintania), majd válassza a *RevoShare*, majd az **újFelhasználó**lehetőséget. A `people.json` fájlnak a `newUser` mappában kell lennie.

        ![A HDInsight másolt fájlmappa helye](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Miután befejezte az aktuális Apache Spark-környezet használatát, le kell állítania. Egyszerre nem futtathat több környezetet.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>További lépések

* [Az ML-szolgáltatások környezeti beállításainak számítási környezete a HDInsightban](r-server-compute-contexts.md)
* [A ScaleR és a SparkR kombinálása](../hdinsight-hadoop-r-scaler-sparkr.md) példaként a légitársaságok járatának késési előrejelzésére.
