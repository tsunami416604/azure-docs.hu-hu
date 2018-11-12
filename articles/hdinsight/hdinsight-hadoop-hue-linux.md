---
title: Hue-Hadooppal a HDInsight Linux-alapú fürtök – Azure
description: Útmutató a HDInsight-fürtökön a Hue telepítése és irányíthatja a kérelmeket a Hue-Alagútkezelés használata. Tallózás a tárolóban, és futtassa a Hive és Pig a Hue használata.
keywords: a hue hadoop
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 6c1acf0e0b93ca6d3a8eae9f291f37d7bb0aa763
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51009283"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Telepítse, és a Hue használata a HDInsight Hadoop-fürtök

Útmutató a HDInsight-fürtökön a Hue telepítése és irányíthatja a kérelmeket a Hue-Alagútkezelés használata.

> [!IMPORTANT]
> A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-is-hue"></a>Mit jelent a Hue?
A hue egy Hadoop-fürtökkel folytatott kommunikációhoz használható webalkalmazás. A Hue használata a Hadoop-fürt (HDInsight-fürtök esetén WASB) társított tárhely, tallózással, Hive-feladatok és a Pig-parancsfájlok futtatásához, és így tovább. A Hue telepítése egy HDInsight Hadoop-fürtön a következő összetevők érhetők el.

* Méhviasz Hive szerkesztőben
* Pig
* Metaadattár manager
* Oozie
* FileBrowser (amely műsorgazdája WASB alapértelmezett tároló)
* A Feladatböngésző

> [!WARNING]
> A HDInsight-fürthöz megadott összetevők teljes mértékben támogatottak, és a Microsoft Support fog help elkülönítésére, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők annak érdekében, hogy a probléma további hibaelhárításához üzletileg ésszerű támogatást kapnak. Emiatt előfordulhat, hogy a probléma megoldásához vagy rákérdez arra, hogy a nyílt forráskódú technológiák, ahol található részletes szakértelmét, hogy a technológiát a rendelkezésre álló csatorna léphet. Számos, használható, például közösségi helyek vannak, például: [HDInsight az MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Is Apache projektek rendelkeznek projekt helyek [ http://apache.org ](http://apache.org), például: [Hadoop](http://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>A Parancsfájlműveletek segítségével a Hue telepítése

A parancsfájl a Hue telepítése egy Linux-alapú HDInsight-fürtön elérhető legyen https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Ez a szkript használatával a Hue telepítése az Azure Storage Blobs (WASB) segítségével vagy az Azure Data Lake Store az alapértelmezett tároló fürtökön.

Ez a szakasz a fürt az Azure portal használatával üzembe helyezésekor a parancsfájl használatával kapcsolatos útmutatást.

> [!NOTE]
> Az Azure PowerShell, az Azure klasszikus parancssori felület, a HDInsight .NET SDK vagy az Azure Resource Manager-sablonok a alkalmazni parancsfájlműveletekkel is használható. Már fut a fürtök parancsfájlműveletekkel is alkalmazhat. További információkért lásd: [testreszabása HDInsight fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Indítsa el a fürt kiépítése a lépéseket követve [Provision HDInsight-fürtök a linuxon futó](hdinsight-hadoop-provision-linux-clusters.md), azonban nem fejeződnek be kiépítése.

   > [!NOTE]
   > A Hue telepítése HDInsight-fürtökön, ajánlott átjárócsomópontjával mérete legalább A4 (8 mag, 14 GB memória).
   >
   >
2. A a **opcionális konfigurációs** panelen válassza ki **Szkriptműveletek**, és adja meg az adatokat alább látható módon:

    ![A Hue parancsfájl művelet paramétereinek megadása](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "Hue parancsfájl művelet paramétereinek megadása")

   * **NÉV**: Adjon egy rövid nevet a parancsprogram-művelet.
   * **SZKRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **A fő**: ezt a beállítást választva
   * **FELDOLGOZÓ**: hagyja üresen a mezőt.
   * **ZOOKEEPER**: hagyja üresen a mezőt.
   * **PARAMÉTEREK**: hagyja üresen a mezőt.
3. Alsó részén a **Parancsfájlműveletek**, használja a **kiválasztása** gombra a konfiguráció mentéséhez. Végül a **kiválasztása** gomb alsó részén a **opcionális konfigurációs** panel opcionális konfigurációs adatok mentéséhez.
4. A fürt kiépítése a leírtak szerint folytassa [Provision HDInsight-fürtök a linuxon futó](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>A Hue használata a HDInsight-fürtökkel

SSH-bújtatás az egyetlen módja a Hue eléréséhez a fürtön, miután fut-e. SSH-n keresztül Tunneling lehetővé teszi, hogy a forgalmat, hogy közvetlenül a fürt átjárócsomópontjával Hue futtató kiszolgáló. Miután a fürt befejezte a kiépítést, a következő lépések segítségével a Hue használata egy HDInsight Linux-fürtön.

> [!NOTE]
> Azt javasoljuk, hogy a Firefox böngésző használatával, kövesse az alábbi utasításokat.
>
>

1. Olvassa el az [SSH-bújtatással való eléréséhez Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie és egyéb webes felhasználói](hdinsight-linux-ambari-ssh-tunnel.md) az SSH-alagút létrehozása ügyfél rendszerről a HDInsight-fürthöz, és adja meg a webes böngésző-proxyként az SSH-alagút használatához.

2. Miután az SSH-alagút létrehozása és a böngészőben a proxy forgalmat rajta keresztül konfigurált, az állomásnév az elsődleges átjárócsomóponthoz, meg kell keresnie. Ehhez a fürthöz az SSH használata a 22-es portot. Például `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` ahol **felhasználónév** az SSH-felhasználónév és a **CLUSTERNAME** a fürt neve.

    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

3. A csatlakozás után a következő parancs használatával szerezze be az elsődleges átjárócsomóponton teljesen minősített tartománynevét:

        hostname -f

    Ez visszaad egy nevet a következőhöz hasonló:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Ez az az állomásnév az elsődleges átjárócsomóponthoz, ahol a Hue webhelyen megtalálható.
4. A böngészője segítségével, a Hue portál megnyitása http://HOSTNAME:8888. ÁLLOMÁSNÉV cserélje le az előző lépésben beszerzett nevét.

   > [!NOTE]
   > Amikor első alkalommal bejelentkezik, a rendszer kéri, jelentkezzen be a Hue portál-fiók létrehozása. Az itt megadott hitelesítő adatok lesz korlátozva, a portálon, és nem kapcsolódnak a rendszergazda vagy a megadott üzembe helyezése a fürtön SSH-felhasználói hitelesítő adatokat.
   >
   >

    ![Jelentkezzen be a Hue portál](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Hue portál hitelesítő adatok megadása")

### <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása
1. A Hue portál kattintson **lekérdezés szerkesztők**, és kattintson a **Hive** a Hive szerkesztő megnyitásához.

    ![A Hive használata](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "a Hive használata")
2. Az a **Assist** lap **adatbázis**, megtekintheti az **hivesampletable**. Ez a minta tábla, amely tartalmazza a szükséges összes, a HDInsight Hadoop-fürtökkel. Adjon meg egy minta lekérdezést a jobb oldali ablaktáblán, és a kimenet jelenik meg a **eredmények** lapon a panelen az alábbi képernyőfelvételen látható módon.

    ![Hive-lekérdezések futtatásához](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "futtatása Hive-lekérdezés")

    Is használhatja a **diagram** lapra vizuális megjelenítését, az eredmény megtekintéséhez.

### <a name="browse-the-cluster-storage"></a>Keresse meg a fürttároló
1. A Hue portál kattintson **Fájltallózó** a menüsor jobb felső sarkában.
2. Alapértelmezés szerint a fájl böngészőben nyílik meg a **/felhasználó/sajátfelhasználó** könyvtár. Kattintson a jobb előtt a felhasználó könyvtár elérési útját a fürthöz társított Azure storage-tároló gyökérkönyvtárában nyissa meg a perjel.

    ![Használat fájltallózó](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "használata fájltallózó")
3. Kattintson a jobb gombbal egy fájl vagy mappa a rendelkezésre álló műveletek megjelenítéséhez. Használja a **feltöltése** gombra a jobb sarokban található fájlok feltöltése az aktuális könyvtár. Használja a **új** gombra új fájlok vagy könyvtárak létrehozásához.

> [!NOTE]
> A Hue-fájl böngésző csak az alapértelmezett tároló a HDInsight-fürthöz társított tartalmát jeleníti meg. További storage fiókok/tárolókat, akkor előfordulhat, hogy rendelkezik a fürthöz társított nem lesz elérhető a fájl böngészővel. A további tárolókat a fürthöz társított azonban mindig lesz a Hive-feladatok számára. Például, ha a parancs `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` a Hive szerkesztőben, tekintse meg a tartalmát, valamint a további tárolókat. Ebben a parancsban **newcontainer** nem az a fürthöz társított alapértelmezett tároló.
>
>

## <a name="important-considerations"></a>Fontos szempontok
1. A Hue telepítésére használt parancsfájl telepíti a szoftvert csak a fürt elsődleges átjárócsomópontjával.

2. A telepítés során több Hadoop-szolgáltatásokhoz (HDFS, a YARN, MR2, az Oozie) frissítéséhez a konfiguráció újra lesz indítva. A Hue telepítése a szkript befejezése után eltarthat egy kis ideig elindításához más Hadoop-szolgáltatásokhoz. A Hue teljesítmény kezdetben hatással lehet. Miután az összes szolgáltatás elindításához, Hue teljesen működőképes lesz.
3. A hue nem érthető Tez-feladatok, a Hive, az aktuális alapértelmezett. Ha azt szeretné, használhatja a MapReduce, a Hive-végrehajtó motor, frissítse a parancsfájlt használja a következő parancsot a parancsfájlban:

        set hive.execution.engine=mr;

4. A Linux-fürtöket akkor is egy forgatókönyv, ahol a szolgáltatást futtatja az elsődleges átjárócsomóponton közben az erőforrás-kezelő is futhat, a másodlagos. Ilyen esetben a fürtön FUTÓ feladatok részleteinek megtekintéséhez a Hue használata esetén (lásd alább) hibákat eredményezhet. Azonban a feladat részleteit is megtekinthetik, amikor a feladat befejeződött.

   ![A hue portál hiba](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Hue portál hiba")

   Ez egy ismert probléma okozza. Áthidaló megoldásként módosítsa az Ambari, hogy az elsődleges átjárócsomóponton is fut, az aktív erőforrás-kezelő.
5. A hue WebHDFS tudomásul veszi, míg a HDInsight-fürtök használata az Azure Storage használatával `wasb://`. Így a parancsfájlművelet használt egyéni parancsfájl telepíti WebWasb, amely a WebHDFS-kompatibilis szolgáltatása a WASB folytatott kommunikációra. Igen, annak ellenére, hogy a Hue portál szerint HDFS helyeken (például amikor, vigye az egérmutatót a **Fájltallózó**), azt úgy kell értelmezni, WASB.

## <a name="next-steps"></a>További lépések
* [A Giraph telepítése HDInsight-fürtökön](hdinsight-hadoop-giraph-install-linux.md). Fürt testreszabása használatával a Giraph telepítése HDInsight Hadoop-fürtökön. A Giraph lehetővé teszi a hadoop diagramfeldolgozást, és használható az Azure HDInsight.
* [A Solr telepítése HDInsight-fürtökön](hdinsight-hadoop-solr-install-linux.md). Fürt testreszabása használatával a Solr telepítése HDInsight Hadoop-fürtökön. A Solr lehetővé teszi a tárolt adatok hatékony keresési műveletek végrehajtásához.
* [Az R telepítése HDInsight-fürtökön](hdinsight-hadoop-r-scripts-linux.md). Fürt testreszabása használatával R telepítheti a HDInsight Hadoop-fürtöket. Az R a egy nyílt forráskódú nyelv és környezet, amely statisztikai számításokhoz. Több száz beépített statisztikai függvények és a saját programozási nyelvet, amelyek funkcionális és objektumorientált programozási aspektusát kombinálja biztosít. Grafikus széles körű lehetőségeket is kínál.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
