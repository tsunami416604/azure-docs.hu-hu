---
title: "A fürtök - Azure HDInsight Linux-alapú hadooppal hue |} Microsoft Docs"
description: "Útmutató a Hue telepítése HDInsight-fürtök és a kérelmek Hue-hez irányítandó bújtatás használata. Hue segítségével keresse meg a tárolási, és futtassa a Hive vagy Pig."
keywords: hue hadoop
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 9e57fcca-e26c-479d-a745-7b80a9290447
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 5cef5e72af8a8b7c007b688b029f875e89d163ae
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Telepítheti és használhatja Hue HDInsight Hadoop-fürtök

Útmutató a Hue telepítése HDInsight-fürtök és a kérelmek Hue-hez irányítandó bújtatás használata.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Linux igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-is-hue"></a>Mi az a Hue?
Hue olyan fürtökkel a Hadoop fürtök folytatott kommunikációhoz használható webalkalmazás. Hue használja a storage (WASB, a HDInsight-fürtök esetén) Hadoop-fürthöz társított megkereséséhez, Hive-feladatok és a Pig-parancsfájlok futtatásához, és így tovább. A következő összetevők Hue telepített egy HDInsight Hadoop-fürt érhetők el.

* Méhviasz Hive szerkesztő
* Pig
* Metaadattárhoz manager
* Oozie
* FileBrowser (amely kiszolgálóhoz csatlakozik WASB alapértelmezett tároló)
* Feladat böngésző

> [!WARNING]
> A HDInsight-fürt összetevői teljes mértékben támogatottak, és a Microsoft Support fog help elkülönítésére, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők kapnak minden üzleti szempontból ésszerű támogatási segítséget nyújtanak a probléma további hibaelhárításához. A probléma megoldását, vagy kéri fel, a nyílt forráskódú technológiák, ahol a részletes segítséget, hogy a technológiát található elérhető csatorna végezhetnek eredményezhet. Például nincsenek sok közösségi webhelyek használható, például: [MSDN fórum hdinsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Is Apache projektek rendelkezik projekt helyek [http://apache.org](http://apache.org), például: [Hadoop](http://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>A Parancsfájlműveletek segítségével Hue telepítése

A Hue telepítése egy Linux-alapú HDInsight-fürt parancsfájl https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh érhető el. Ez a parancsfájl segítségével Hue telepítése vagy az Azure Storage Blobs (WASB), vagy az Azure Data Lake Store alapértelmezett tárolóként fürtökön.

Ez a témakör leírja, a parancsfájl használata, ha a fürt az Azure portál használatával.

> [!NOTE]
> Az Azure PowerShell, az Azure parancssori felület, a HDInsight .NET SDK vagy Azure Resource Manager-sablonok Parancsfájlműveletek alkalmazandó is használható. Már fut a fürtök Parancsfájlműveletek is alkalmazhat. További információkért lásd: [testreszabása HDInsight-fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Indítsa el a fürt kiépítése a lépések segítségével [Provision HDInsight-fürtök Linux rendszeren](hdinsight-hadoop-provision-linux-clusters.md), de kiépítése nem fejeződött be.

   > [!NOTE]
   > A HDInsight-fürtökön Hue telepítése, az ajánlott headnode mérete legalább A4 (8 mag, 14 GB memória).
   >
   >
2. Az a **opcionális konfigurációs** panelen válassza **Parancsfájlműveletek**, és adja meg az adatokat, alább látható módon:

    ![Adja meg parancsfájl művelet paramétereinek Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "nyújtása parancsfájl művelet paramétereinek Hue")

   * **NÉV**: Adja meg a parancsfájlművelet rövid nevét.
   * **PARANCSFÁJL URI azonosítója**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: ezt a beállítást
   * **MUNKAVÉGZŐ**: hagyja üresen a mezőt.
   * **ZOOKEEPER**: hagyja üresen a mezőt.
   * **PARAMÉTEREK**: hagyja üresen a mezőt.
3. Alján a **Parancsfájlműveletek**, használja a **válasszon** gombra kattintva mentse a konfigurációt. Végül a **válasszon** gomb alján a **opcionális konfigurációs** panelt, és mentse a nem kötelező konfigurációs adatokat.
4. Továbbra is a fürt kiépítése a [Provision HDInsight-fürtök Linux rendszeren](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Hue használata a HDInsight-fürtök

SSH-bújtatás Hue hozzáférni a fürtön, miután fut-e az egyetlen lehetőség. SSH-kapcsolaton keresztül Tunneling lehetővé teszi a forgalom lépjen közvetlenül a fürt headnode Hue futtató. Miután a fürt befejezte a kiépítést, a következő lépésekkel Hue használata a HDInsight Linux fürtökön.

> [!NOTE]
> Azt javasoljuk, hogy kövesse az alábbi utasításokat a Firefox webböngésző használatával.
>
>

1. Olvassa el a [használata SSH Tunneling Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie és egyéb webes felhasználói felület eléréséhez](hdinsight-linux-ambari-ssh-tunnel.md) SSH-alagút létrehozása a ügyfél rendszerről a HDInsight-fürthöz, és adja meg a webalkalmazás számára az SSH-alagút proxyként használandó böngésző.

2. Ha rendelkezik az SSH-alagút létrehozása és konfigurálása a böngészőben a proxy-forgalom rajta, keresse meg az elsődleges átjárócsomópont állomásnevét. Ehhez a fürthöz SSH 22-es port használatával. Például `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` ahol **felhasználónév** az SSH-felhasználónév és **CLUSTERNAME** a fürt neve.

    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

3. A csatlakozás után a következő paranccsal beszerzése az elsődleges headnode teljesen minősített tartománynevét:

        hostname -f

    Ez visszaadja azt egy nevet a következőhöz hasonló:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Az elsődleges headnode állomásnevét azt, ahol a Hue webhelyen-e.
4. A böngésző segítségével, http://HOSTNAME:8888 a Hue portál megnyitása. ÁLLOMÁSNÉV cserélje le az előző lépésben beolvasott nevére.

   > [!NOTE]
   > Amikor első alkalommal bejelentkezik, a rendszer kéri, jelentkezzen be a Hue portál fiók létrehozásához. Az itt megadott hitelesítő adatok nem kapcsolódnak a rendszergazda vagy a megadott kiépítési a fürtön SSH hitelesítő, és kizárólag a portál.
   >
   >

    ![A Hue portál bejelentkezni](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Hue portál hitelesítő adatok megadása")

### <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása
1. A Hue portál kattintson **lekérdezés-szerkesztő**, és kattintson a **Hive** a Hive szerkesztő megnyitásához.

    ![A Hive használata](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "a Hive használata")
2. Az a **Assist** lap **adatbázis**, megtekintheti az **hivesampletable**. Ez a modul a HDInsight Hadoop-fürtök a minta tábla. Adjon meg egy minta-lekérdezést a jobb oldali ablaktáblán, és a kimeneti tekintse meg a **eredmények** lapján az alábbi képernyőfelvételen látható módon.

    ![Hive-lekérdezések futtatása](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "futtatása Hive-lekérdezések")

    Használhatja a **diagram** lapon, láthatja az eredményt vizuális ábrázolását.

### <a name="browse-the-cluster-storage"></a>Keresse meg a fürttároló
1. A Hue portál kattintson **fájl böngésző** az egérrel a menüsoron jobb felső sarkában.
2. Alapértelmezés szerint a fájl böngészőben nyílik meg a **/felhasználó/myuser** könyvtár. Kattintson a perjel közvetlenül előtt a felhasználó címtár eléréséhez. a fürthöz tartozó Azure storage-tároló elérési útja.

    ![Használjon fájl böngésző](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "fájl böngésző használata")
3. Kattintson a jobb gombbal egy fájl vagy mappa a rendelkezésre álló műveletek megjelenítéséhez. Használja a **feltöltése** gombra a jobb sarkában található fájlok feltöltése az aktuális könyvtár. Használja a **új** új fájlok vagy könyvtárak létrehozása gombra.

> [!NOTE]
> A Hue-fájl böngésző csak is az alapértelmezett tároló, a HDInsight-fürthöz társított tartalmának megjelenítése. Bármely további fiókok/tárolókban, előfordulhat, hogy vannak társítva a fürt nem lesz elérhető a fájl böngészővel. A további tárolókat, a fürthöz tartozó azonban mindig lesz a Hive-feladatok számára. Például, ha a parancs beírásakor `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` a Hive szerkesztőben, tekintse meg a további tárolókat is beleértve tartalmát. A parancs **newcontainer** nincs az alapértelmezett tároló a fürthöz rendelt.
>
>

## <a name="important-considerations"></a>Fontos tudnivalók találhatók
1. A Hue telepítésére használt parancsfájl telepíti a szoftvert csak a fürt elsődleges headnode.

2. A telepítés során több Hadoop-szolgáltatás (HDFS, YARN, MR2, Oozie) frissítéséhez a konfigurációs újraindul. A parancsfájl befejezése Hue telepítése után eltarthat némi időbe, más Hadoop-szolgáltatás elindításához. A Hue teljesítmény kezdetben hatással lehet. Miután az összes szolgáltatás elindításához, Hue teljesen működőképes lesz.
3. Hue nem értelmezi Tez feladatokhoz, az aktuális alapértelmezett a Hive. Ha szeretné használni a Hive-végrehajtó motor MapReduce, frissítse a parancsfájlt, amellyel a parancsfájlban az alábbi paranccsal:

        set hive.execution.engine=mr;

4. Linux-fürtökkel akkor is egy olyan forgatókönyvet, ahol a szolgáltatások futnak a elsődleges headnode az erőforrás-kezelő sikertelen lehet a másodlagos futása közben. Ilyen esetben (lásd alább) hibákat eredményezhet, Hue használatakor a fürtön FUTÓ feladatok részletes adatainak megtekintéséhez. A feladat részleteit, megtekintheti a feldolgozás befejezése.

   ![Hue portál hiba](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Hue portál hiba")

   Ez egy ismert probléma okozza. A probléma megoldásához módosítsa Ambari, hogy az elsődleges headnode is fut a aktív erőforrás-kezelő.
5. Hue együttműködik a WebHDFS, amíg a HDInsight-fürtök használata az Azure Storage használatával `wasb://`. A parancsfájlművelet használt egyéni parancsfájl tehát WebWasb, amely a WebHDFS-kompatibilis szolgáltatás beszélgetne a WASB telepíti. Igen, annak ellenére, hogy a Hue portál szerint helyen HDFS (például ha Ön az egérmutatót a **fájl böngésző**), úgy kell értelmezni, WASB.

## <a name="next-steps"></a>Következő lépések
* [Giraph telepíthető a HDInsight-fürtök](hdinsight-hadoop-giraph-install-linux.md). A HDInsight Hadoop-fürtök Giraph telepítése a fürt testreszabási használatával. Giraph lehetővé teszi a Hadoop használatával graph feldolgozás elvégzéséhez, és az Azure HDInsight használható.
* [Solr telepíthető a HDInsight-fürtök](hdinsight-hadoop-solr-install-linux.md). A HDInsight Hadoop-fürtök Solr telepítése a fürt testreszabási használatával. Solr tárolt adatok hatékony keresési műveletek végrehajtását teszi lehetővé.
* [A HDInsight-fürtökön R telepítéséhez](hdinsight-hadoop-r-scripts-linux.md). A HDInsight Hadoop-fürtök R telepítése a fürt testreszabási használatával. R egy nyílt forráskódú nyelv és környezet, amely statisztikai számításokhoz. Több száz beépített statisztikai függvények és a saját programozási nyelv, amely a működési és objektumorientált programozási aspektusát kombinálja biztosít. Nagy mennyiségű grafikus képességeket is tartalmaz.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
