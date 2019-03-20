---
title: Presto telepítése Linux-alapú Azure HDInsight-fürtökön
description: Ismerje meg, hogyan telepíthet Presto és Airpal Linux-alapú HDInsight Hadoop-fürtökön parancsfájlműveletekkel segítségével.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: fe3727c90819a96b7e1785362e557edd5cbbdf02
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199324"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Telepítheti és használhatja Presto Hadoop-alapú HDInsight-fürtök

Ez a cikk ismerteti a Presto telepítése Adobe Hadoop-alapú HDInsight-fürtökön parancsfájlműveletekkel. Azt is megtudhatja, hogyan Airpal telepítése egy meglévő Presto HDInsight-fürtön.

HDInsight az Apache Hadoop-fürtök buborék Presto kérelem is kínál. További információkért lásd: [külső Apache Hadoop-alkalmazások telepítése az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications).

> [!IMPORTANT]  
> A jelen cikkben ismertetett lépések a HDInsight 3.5-ös Hadoop-fürt által használt Linux szükséges. Linux az egyetlen operációs rendszer használt a HDInsight 3.4-es vagy újabb verzió. További információkért lásd: [HDInsight-verziók](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Presto mi?
[Presto](https://prestodb.io/overview.html) fast elosztott SQL lekérdezési motorja big Data-van. A presto ideális interaktív több petabájtnyi adat lekérdezését. Presto, és hogyan működnek együtt az összetevőkről további információkért lásd: [Presto fogalmak](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]  
> A HDInsight-fürthöz megadott összetevők teljes mértékben támogatottak. A Microsoft Support fog help elkülönítésére, és ezeket az összetevőket kapcsolatos problémák megoldásához.
> 
> Egyéni összetevők, például a Presto segítséget a probléma további hibaelhárításához üzletileg ésszerű támogatást kapnak. Ez a támogatás előfordulhat, hogy a probléma megoldásához. Vagy megkérheti a nyílt forráskódú technológiák, ahol található részletes szakértelmét, hogy a technológiát a rendelkezésre álló csatorna végezhetnek. Nincsenek számos közösségi helyek használható. Példa [HDInsight az MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) és [Stack Overflow](https://stackoverflow.com). 
>
> Apache projektek is rendelkeznek projekt helyek a [Apache webhely](https://apache.org). Például [Hadoop](https://hadoop.apache.org/).


## <a name="install-presto-by-using-script-actions"></a>A Presto telepítése szkriptműveletek használatával

Ez a szakasz azt ismerteti, hogyan használja a minta parancsfájlt, ha egy új fürt létrehozása az Azure portal használatával: 

1. Indítsa el a fürt kiépítéséhez a lépések végrehajtásával [létrehozása Linux-alapú fürtök a HDInsight az Azure portal használatával](hdinsight-hadoop-create-linux-clusters-portal.md). Ellenőrizze, hogy a fürt használatával hoz létre a **egyéni** a fürt létrehozási folyamat. A fürt a következő követelményeknek kell megfelelnie:

   * Hadoop-fürt a HDInsight 3.6-os verzióját kell lennie.

   * Azure Storage azt kell használnia, mint az adattárban. A beállítást, az Azure Data Lake Storage használó fürtön Presto használatával nincs lehetőség még.

     ![HDInsight, egyéni (méret, beállítások, alkalmazások)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Az a **speciális beállítások** területen válassza **Parancsfájlműveletek**. Adja meg a következő információkat. Azt is beállíthatja a **Presto telepítése** lehetőséget a parancsprogram típusát:
   
   * **NAME**. Adjon egy rövid nevet a parancsprogram-művelet.
   * **Bash parancsfájl URI azonosítója**. `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`.
   * **A FŐ**. Válassza ezt a beállítást.
   * **FELDOLGOZÓ**. Válassza ezt a beállítást.
   * **ZOOKEEPER**. Hagyja üresen ezt a jelölőnégyzetet.
   * **PARAMÉTEREK**. Hagyja üresen a mezőt.


3. Alsó részén a **Szkriptműveletek** területen válassza ki a **válassza** gombra a konfiguráció mentéséhez. Végül válassza a **kiválasztása** gomb alsó részén a **speciális beállítások** terület menteni a konfigurációs adatokat.

4. A fürt üzembe helyezése, leírtak szerint folytassa [létrehozása Linux-alapú fürtök a HDInsight az Azure portal használatával](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > Az Azure PowerShell, az Azure klasszikus parancssori felület, a HDInsight .NET SDK vagy az Azure Resource Manager-sablonok a alkalmazni parancsfájlműveletekkel is használható. Már fut a fürtök parancsfájlműveletekkel is alkalmazhat. További információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>A Presto használata a HDInsight

Dolgozunk Presto egy HDInsight-fürtön, az alábbi lépéseket:

1. Csatlakozás a HDInsight-fürthöz SSH használatával:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    További információkért lásd: [HDInsight (az Apache Hadoop) SSH használatával csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. A Presto shell indítása a következő parancs futtatásával:
   
    `presto --schema default`

3. -Lekérdezést futtathat egy minta tábla **hivesampletable**, alapértelmezés szerint minden HDInsight fürtön elérhető:
   
    `select count (*) from hivesampletable;`
   
    Alapértelmezés szerint [Apache Hive](https://prestodb.io/docs/current/connector/hive.html) és [TPCH](https://prestodb.io/docs/current/connector/tpch.html) Presto összekötők már konfigurálva vannak. A Hive-összekötőt az alapértelmezett Hive telepítés használatára van konfigurálva. Így a Hive a táblák a Presto automatikusan látható.

    További információkért lásd: [Presto dokumentáció](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>A Presto Airpal használata

[Airpal](https://github.com/airbnb/airpal#airpal) Presto egy nyílt forráskódú webes lekérdezési felületet szól. Airpal további információkért lásd: [Airpal dokumentáció](https://github.com/airbnb/airpal#airpal).

A következő lépésekkel Airpal telepíteni az élcsomóponton:

1. Az SSH használatával, amely Presto telepített HDInsight-fürt átjárócsomópontjához csatlakozik:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    További információkért lásd: [HDInsight (az Apache Hadoop) SSH használatával csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Miután csatlakozott, futtassa a következő parancsot:

    `sudo slider registry  --name presto1 --getexp presto` 
   
    A következő JSON hasonló kimenet jelenik meg:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. A kimenetben, vegye figyelembe az értéket a **érték** tulajdonság. Ez az érték kell a fürt határcsomópontjához a Airpal telepítése közben. A fenti kimenetből a szükséges érték **10.0.0.12:9090**.

4. Használat [ezzel a sablonnal](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json) hozhat létre egy HDInsight-fürt határcsomópontjához. Adja meg az alábbi képernyőképen látható értékeket.

    ![Egyéni üzembe helyezés](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Válassza a **Beszerzés** lehetőséget.

6. Miután a módosítások a fürtkonfiguráció, eléréséhez a Airpal webes felület végrehajtja a következő lépéseket a [az Azure portal](https://portal.azure.com):

    1. A bal oldali menüben válassza ki a **minden szolgáltatás**.

    1. A **ANALYTICS**válassza **HDInsight-fürtök**.

    1. Válassza ki a fürtöt a listából, amely az alapértelmezett nézetének megnyitása.

    1. Az alapértelmezett nézet a csoportban **beállítások**, jelölje be **alkalmazások**.

        ![HDInsight, alkalmazások](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. Az a **telepített alkalmazások** lapon, keresse meg a tábla bejegyzés **airpal**. Válassza ki **portál**.

        ![Telepített alkalmazások](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. Amikor a rendszer kéri, adja meg a Hadoop-alapú HDInsight-fürt létrehozásakor megadott rendszergazdai hitelesítő adatait.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>A Presto telepítése HDInsight-fürt testreszabása

A telepítés testreszabásához hajtsa végre az alábbi lépéseket:

1. Az SSH használatával, amely Presto telepített HDInsight-fürt átjárócsomópontjához csatlakozik:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    További információkért lásd: [HDInsight (az Apache Hadoop) SSH használatával csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A konfigurációs módosításokat a fájlban `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. A Presto konfigurációs további információkért lásd: [Presto beállítási lehetőségei a YARN-alapú fürtök](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Állítsa le, és az aktuális futó példányát, Presto kill:

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. Indítsa el a testreszabási Presto egy új példányát:

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. Várjon, amíg az új példány kész lesz. A Presto koordinátor-cím. Megjegyzés:

    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Számításiteljesítmény-mérési adatok Presto futtató HDInsight-fürtök létrehozása

TPC-DS-ben az iparági szabvány számos Döntéstámogatás rendszerek, beleértve a big data-rendszereket a teljesítmény méréséhez. Segítségével Presto adatokat hozhat létre és kiértékelheti, hogyan viszonyul a saját HDInsight számításiteljesítmény-mérési adatokkal. További információkért lásd: [tpcds-hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="next-steps"></a>További lépések
* [Telepítse, és a Hue használata a HDInsight Hadoop-fürtök](hdinsight-hadoop-hue-linux.md). A hue webes felhasználói felület, amely megkönnyíti a létrehozása, futtatása és mentése Apache Pig- és Hive-feladatok.

* [Telepítse az Apache Giraph HDInsight Hadoop-fürtöket, és a Giraph használata nagyméretű gráfok feldolgozásához](hdinsight-hadoop-giraph-install-linux.md). Fürt testreszabása használatával telepíteni a Giraph Hadoop-alapú HDInsight-fürtökön. A Giraph gráffeldolgozási Hadoop használatával is elvégezheti. Is használható az Azure HDInsight.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
