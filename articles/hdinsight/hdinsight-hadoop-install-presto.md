---
title: Azure HDInsight Linux fürtökön Presto telepítése |} Microsoft Docs
description: Megtudhatja, hogyan telepíthetők Presto és Airpal Parancsfájlműveletek Linux-alapú HDInsight Hadoop-fürtök.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 32b7925b7414f00dfdd7d5c8a45b3601bf58942e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Telepítheti és használhatja Presto HDInsight Hadoop-fürtök

Ebből a dokumentumból megismerheti, hogyan Presto telepítése a HDInsight Hadoop-fürtök parancsfájlművelet használatával. Is megismerheti, hogyan Airpal telepítsen egy meglévő Presto HDInsight-fürtre.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések szükséges egy **HDInsight 3.5 Hadoop-fürt** , amely Linux használ. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight-verziókról](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>Mi az az Presto?
[Presto](https://prestodb.io/overview.html) egy gyors elosztott SQL lekérdezési motor a big data. Presto alkalmas adatmennyiségig interaktív lekérdezése. Presto, és hogyan működnek együtt a-összetevők további információkért lásd: [Presto fogalmak](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]
> A HDInsight-fürt összetevői teljes mértékben támogatottak, és a Microsoft Support fog help elkülönítésére, és ezeket az összetevőket kapcsolatos problémák megoldásához.
> 
> Egyéni összetevők, például Presto, minden üzleti szempontból ésszerű terméktámogatási segítséget nyújtanak a probléma további hibaelhárításához. A probléma megoldását, vagy kéri fel, a nyílt forráskódú technológiák, ahol a részletes segítséget, hogy a technológiát található elérhető csatorna végezhetnek eredményezhet. Például nincsenek sok közösségi webhelyek használható, például: [MSDN fórum hdinsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Is Apache projektek rendelkezik projekt helyek [ http://apache.org ](http://apache.org), például: [Hadoop](http://hadoop.apache.org/).
> 
> 


## <a name="install-presto-using-script-action"></a>Parancsfájl műveletével Presto telepítése

Ez a szakasz útmutatásai parancsfájlpélda használatával, ha az új fürt létrehozása az Azure portál használatával. 

1. Indítsa el a fürt kiépítése a lépések segítségével [Provision Linux-alapú HDInsight-fürtök](hdinsight-hadoop-create-linux-clusters-portal.md). Győződjön meg arról, hogy a fürt használata a **egyéni** fürt létrehozási folyamata. A fürt az alábbi követelményeknek kell megfelelnie.

    * A 3.5-ös verziója HDInsight Hadoop-fürttel kell lennie.

    * Azure Storage azt kell használnia, mint a tárolót. Presto használatával olyan fürtön, a tárolási lehetőség az Azure Data Lake Store használó még nem támogatott. 

    ![Egyéni beállítások használata a HDInsight-fürt létrehozása](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Az a **speciális beállítások** területen válassza **Parancsfájlműveletek**, és az alábbi adatokat:
   
   * **NÉV**: Adja meg a parancsfájlművelet rövid nevét.
   * **Bash-szkript URI azonosítója**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HEAD**: ezt a beállítást
   * **MUNKAVÉGZŐ**: ezt a beállítást
   * **ZOOKEEPER**: törölje a jelet a jelölőnégyzetből
   * **PARAMÉTEREK**: ezt a mezőt hagyja üresen


3. Alján a **Parancsfájlműveletek** területen kattintson a **válasszon** gombra kattintva mentse a konfigurációt. Végül kattintson a **kiválasztása** gomb alján a **speciális beállítások** terület menteni a konfigurációs adatait.

4. Továbbra is a fürt kiépítése a [Provision Linux-alapú HDInsight-fürtök](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > Az Azure PowerShell, az Azure parancssori felület, a HDInsight .NET SDK vagy Azure Resource Manager-sablonok Parancsfájlműveletek alkalmazandó is használható. Már fut a fürtök Parancsfájlműveletek is alkalmazhat. További információkért lásd: [testreszabása HDInsight-fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Presto használata a hdinsight eszközzel

Dolgozunk Presto a HDInsight-fürtöt, tegye a következőket:

1. Csatlakozzon SSH-val a HDInsight-fürthöz:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Indítsa el a következő parancsot a Presto rendszerhéj.
   
        presto --schema default

3. Lekérdezés futtatható egy mintatáblát **hivesampletable**, alapértelmezés szerint az összes HDInsight-fürtök elérhető.
   
        select count (*) from hivesampletable;
   
    Alapértelmezés szerint [Hive](https://prestodb.io/docs/current/connector/hive.html) és [TPCH](https://prestodb.io/docs/current/connector/tpch.html) csatlakozók a Presto már be van állítva. Hive összekötő használatát a alapértelmezés szerint telepített Hive telepítés Hive összes táblájának automatikusan láthatók lesznek a Presto van konfigurálva.

    További információkért lásd: [Presto dokumentáció](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Presto Airpal használata

[Airpal](https://github.com/airbnb/airpal#airpal) Presto van egy nyílt forráskódú webes lekérdezési felületet. A Airpal további információkért lásd: [Airpal dokumentáció](https://github.com/airbnb/airpal#airpal).

Élcsomópont Airpal telepítéséhez tegye a következőket:

1. A HDInsight-fürt Presto telepített headnode csatlakozni SSH használatával:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Miután csatlakozott, a következő parancsot.

        sudo slider registry  --name presto1 --getexp presto 
   
    A következő JSON hasonló kimenet jelenik meg:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. A kimenetben jegyezze fel az értéket a **érték** tulajdonság. Ez az érték kell a fürt edgenode Airpal telepítése közben. A fenti kimenetben, amelyre szüksége lesz értéke **10.0.0.12:9090**.

4. A sablon **[Itt](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** egy HDInsight-fürt edgenode létrehozásához, és adja meg az értékeket, az alábbi képernyőfelvételen látható módon.

    ![HDInsight telepítés Airpal Presto fürtön](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Kattintson a **Purchase** (Vásárlás) gombra.

6. Miután a módosításai érvényesek lesznek a fürtkonfiguráció, a Airpal webes felülete az alábbi lépéseket követve végezheti el.

    1. A fürt párbeszédpanelen kattintson a **alkalmazások**.

        ![HDInsight indítási Airpal Presto fürtön](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. Az a **telepített alkalmazások** területen kattintson a **Portal** airpal ellen.

        ![HDInsight indítási Airpal Presto fürtön](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. Amikor a rendszer kéri, írja be a HDInsight Hadoop-fürt létrehozásakor megadott rendszergazdai hitelesítő adatokat.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>A HDInsight-fürt Presto telepítés testreszabása

A telepítés testreszabásához használja az alábbi lépéseket:

1. A HDInsight-fürt Presto telepített headnode csatlakozni SSH használatával:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A konfigurációs módosításokat a fájlban `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Presto konfiguráció további információkért lásd: [YARN-alapú fürtök Presto konfigurációs](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Állítsa le és kill Presto aktuális futó példányát.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Egy új példányát Presto kezdje a Testreszabás.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Várjon, amíg az új példány készen áll, és jegyezze fel az presto koordinátorának címe.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Teljesítményteszt adatok Presto futtató HDInsight-fürtök létrehozása

TPC-DS iparági szabvány sok döntési támogatási rendszerek, beleértve a big data-rendszereket teljesítményének méréséhez. Segítségével Presto hozhat létre adatokat, és összehasonlítja azt a saját HDInsight teljesítményteszt adataival kiértékeléséhez. További információkért lásd: [Itt](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Lásd még
* [Telepítheti és használhatja a HDInsight-fürtök Hue](hdinsight-hadoop-hue-linux.md). Hue webes felhasználói felület, amely megkönnyíti a létrehozásához futtassa, és mentse a Pig és Hive feladat.

* [Giraph telepíthető a HDInsight-fürtök](hdinsight-hadoop-giraph-install-linux.md). A HDInsight Hadoop-fürtök Giraph telepítése a fürt testreszabási használatával. Giraph lehetővé teszi a végrehajtását diagramfeldolgozás Hadoop használatával, és az Azure HDInsight használható.

* [Solr telepíthető a HDInsight-fürtök](hdinsight-hadoop-solr-install-linux.md). A HDInsight Hadoop-fürtök Solr telepítése a fürt testreszabási használatával. Solr tárolt adatok hatékony keresési műveletek végrehajtását teszi lehetővé.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
