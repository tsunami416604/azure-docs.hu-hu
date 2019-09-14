---
title: Hue a Hadoop on HDInsight Linux-alapú fürtökön – Azure
description: Megtudhatja, hogyan telepítheti a Hue-t a HDInsight-fürtökre, és hogyan használhatja a bújtatást a kérések Színezetbe irányításához A Hue használatával böngészhet a tárolóban, és futtathatja a kaptár vagy a Pig-t.
keywords: Hue Hadoop
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 67f338b583ef428b8dd04e859a5204fd708ce434
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962010"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>A Hue telepítése és használata a HDInsight Hadoop-fürtökön

Megtudhatja, hogyan telepítheti a Hue-t a HDInsight-fürtökre, és hogyan használhatja a bújtatást a kérések Színezetbe irányításához

## <a name="what-is-hue"></a>Mi az a Hue?
A Hue egy Apache Hadoop-fürttel való interakcióhoz használt webalkalmazások összessége. A Hue használatával megkeresheti a Hadoop-fürthöz társított tárolót (WASB, HDInsight-fürtök esetén), és futtathatja a kaptár-feladatokat és a Pig-parancsfájlokat, és így tovább. Az alábbi összetevők HDInsight Hadoop-fürtön található Hue-telepítésekkel érhetők el.

* Méhviasz-struktúra szerkesztője
* Apache Pig
* Metaadattár-kezelő
* Apache Oozie
* Fájlböngésző (amely a WASB alapértelmezett tárolóra beszél)
* Feladatok böngészője

> [!WARNING]  
> A HDInsight-fürthöz biztosított összetevők teljes mértékben támogatottak, és Microsoft ügyfélszolgálata az ezen összetevőkkel kapcsolatos problémák elkülönítésében és megoldásában is segítséget nyújt.
>
> Az egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a probléma további megoldásához. Ez a probléma megoldásához vezethet, vagy megkérdezheti, hogy a nyílt forráskódú technológiákhoz elérhető csatornákat szeretne-e felvenni. Például számos közösségi webhely használható, például: [MSDN-fórum a HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)- [https://stackoverflow.com](https://stackoverflow.com)hoz. Emellett az Apache [https://apache.org](https://apache.org)-projektek is rendelkeznek projekt-webhelyekkel, például: [Hadoop](https://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>A Hue telepítése parancsfájl-műveletek használatával

A Hue Linux-alapú HDInsight-fürtön való telepítéséhez szükséges parancsfájl a következő https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh címen érhető el:. Ezt a parancsfájlt használhatja az Azure Storage-Blobokkal (WASB) rendelkező fürtökön vagy a Azure Data Lake Storage alapértelmezett tárolóként történő telepítésére is.

Ez a szakasz azt ismerteti, hogyan használható a parancsfájl a fürt a Azure Portal használatával történő kiépítés során.

> [!NOTE]  
> A Azure PowerShell, a klasszikus Azure CLI, a HDInsight .NET SDK vagy a Azure Resource Manager-sablonok is használhatók parancsfájl-műveletek alkalmazására. Parancsfájl-műveleteket is alkalmazhat már futó fürtökre. További információ: HDInsight- [fürtök testreszabása parancsfájl-műveletekkel](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. A fürt kiépítésének megkezdéséhez használja a [HDInsight-fürtök Linuxon](hdinsight-hadoop-provision-linux-clusters.md)való üzembe helyezésének lépéseit, de ne fejezze be a telepítést.

   > [!NOTE]  
   > A Hue HDInsight-fürtökön való telepítéséhez az ajánlott átjárócsomóponthoz mérete legalább a4 (8 mag, 14 GB memória).
   >
   >
2. A **választható konfiguráció** panelen válassza a **parancsfájlok műveletei**lehetőséget, és adja meg az adatokat az alábbiak szerint:

    ![Parancsfájl műveleti paramétereinek megadása a Hue számára](./media/hdinsight-hadoop-hue-linux/hdi-hue-script-action.png "Parancsfájl műveleti paramétereinek megadása a Hue számára")

   * **NÉV**: Adja meg a parancsfájl-művelet rövid nevét.
   * **PARANCSFÁJL URI-JA**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: A beállítás bejelölése.
   * FELDOLGOZÓ: Hagyja üresen a mezőt.
   * **ZOOKEEPER**: Hagyja üresen a mezőt.
   * **PARAMÉTEREK**: Hagyja üresen a mezőt.
3. A **parancsfájl műveleteinek**alján a **kiválasztás** gombbal mentheti a konfigurációt. Végül **a választható konfigurációs** panel alján található **kiválasztás** gombbal mentheti a választható konfigurációs adatokat.
4. Folytassa a fürt kiépítését a [Linuxon futó HDInsight-fürtök kiépítése](hdinsight-hadoop-provision-linux-clusters.md)című témakörben leírtak szerint.

## <a name="use-hue-with-hdinsight-clusters"></a>A Hue használata HDInsight-fürtökkel

Az SSH-bújtatás az egyetlen módszer a színárnyalat elérésére a fürtön, ha fut. Az SSH-n keresztüli bújtatás lehetővé teszi, hogy a forgalom közvetlenül annak a fürtnek a átjárócsomóponthoz menjen, ahol a Hue fut. Miután a fürt befejezte az üzembe helyezést, a következő lépésekkel használhatja a Hue-t egy HDInsight Linux-fürtön.

> [!NOTE]  
> Javasoljuk, hogy kövesse az alábbi utasításokat a Firefox böngésző használatával.
>
>

1. Az [SSH-bújtatás használata az Apache Ambari webes felhasználói felület, a erőforráskezelő, a JobHistory, a NameNode, a Oozie és az egyéb webes kezelőfelületek eléréséhez](hdinsight-linux-ambari-ssh-tunnel.md) használja az SSH-alagutat az ügyfél rendszeréről a HDInsight-fürtre, majd konfigurálja a webböngészőt a következő használatára: SSH-alagút proxyként.

2. Miután létrehozott egy SSH-alagutat, és konfigurálta a böngészőt a proxyn keresztüli adatforgalomhoz, meg kell találnia az elsődleges fő csomópont állomásnevét. Ezt úgy teheti meg, hogy a 22-es porton keresztül csatlakozik a fürthöz az SSH használatával. `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` Ha például a **Felhasználónév** az SSH-Felhasználónév, a **CLUSTERNAME** pedig a fürt neve.

    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Csatlakozás után a következő paranccsal szerezheti be az elsődleges átjárócsomóponthoz teljes tartománynevét:

        hostname -f

    Ez a következőhöz hasonló nevet ad vissza:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Ez az az elsődleges átjárócsomóponthoz állomásneve, ahol a Hue webhely található.
4. A böngészőben nyissa meg a Hue portált a következő\/helyen: http:/hostname: 8888. Cserélje le az ÁLLOMÁSNÉV nevet az előző lépésben beszerzett névre.

   > [!NOTE]  
   > Amikor első alkalommal jelentkezik be, a rendszer felszólítja, hogy hozzon létre egy fiókot a Hue Portalra való bejelentkezéshez. Az itt megadott hitelesítő adatokat a rendszer a portálra korlátozza, és nem kapcsolódik a fürt kiépítésekor megadott rendszergazdai vagy SSH-felhasználói hitelesítő adatokhoz.
   >
   >

    ![Bejelentkezés a Hue Portalra](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Hitelesítő adatok megadása a Hue Portalhoz")

### <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása
1. A Hue Portalon kattintson a **lekérdezési szerkesztők**elemre, majd kattintson a **struktúra** elemre a kaptár-szerkesztő megnyitásához.

    ![Struktúra használata](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Struktúra használata")
2. Az **assziszt** lapon az **adatbázis**területen tekintse meg a **hivesampletable**. Ez egy minta tábla, amely a HDInsight összes Hadoop-fürtjével együtt kapható. Adjon meg egy minta lekérdezést a jobb oldali ablaktáblán, és tekintse meg az alábbi ablaktábla **eredmények** lapján látható kimenetet, ahogy az a képernyőfelvételen látható.

    ![Struktúra-lekérdezés futtatása](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Struktúra-lekérdezés futtatása")

    A **diagram** lapon megtekintheti az eredmény vizuális ábrázolását is.

### <a name="browse-the-cluster-storage"></a>A fürt tárterületének tallózása
1. A Hue Portalon kattintson a menüsáv jobb felső sarkában található **fájlkezelő** elemre.
2. Alapértelmezés szerint a fájlkezelő a **/User/myuser** könyvtárban nyílik meg. A fürthöz társított Azure Storage-tároló gyökeréhez való ugráshoz kattintson a perjel közvetlenül az elérési út előtt lévő felhasználói könyvtár elemre.

    A ![fájl böngésző használata] A (./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "fájl böngésző használata")
3. Kattintson a jobb gombbal egy fájlra vagy mappára az elérhető műveletek megtekintéséhez. A jobb oldali sarokban található **feltöltés** gomb használatával fájlokat tölthet fel az aktuális könyvtárba. Új fájlok vagy könyvtárak létrehozásához használja az **új** gombot.

> [!NOTE]  
> A Hue file Browser csak a HDInsight-fürthöz társított alapértelmezett tároló tartalmát jeleníti meg. A fürthöz esetlegesen hozzárendelt további Storage-fiókok vagy tárolók nem lesznek elérhetők a fájlkezelő használatával. A fürthöz társított további tárolók azonban mindig elérhetővé válnak a kaptár-feladatok számára. Ha például megadja a parancsot `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` a kaptár-szerkesztőben, a további tárolók tartalmát is megtekintheti. Ebben a parancsban a **newcontainer** nem a fürthöz társított alapértelmezett tároló.
>
>

## <a name="important-considerations"></a>Fontos szempontok
1. A Hue telepítéséhez használt parancsfájl csak a fürt elsődleges átjárócsomóponthoz telepíti.

2. A telepítés során a rendszer több Hadoop szolgáltatást (HDFS, FONALat, MR2, Oozie) indít el a konfiguráció frissítéséhez. Miután a parancsfájl befejezte a Hue telepítését, eltarthat egy ideig, amíg a többi Hadoop-szolgáltatás elindul. Ez hatással lehet a Hue teljesítményére. Az összes szolgáltatás elindítása után a Hue teljes mértékben működőképes lesz.
3. A Hue nem érti az Apache TEZ-feladatokat, ami a struktúra aktuális alapértelmezett értéke. Ha a MapReduce-t a kaptár-végrehajtó motorként szeretné használni, frissítse a parancsfájlt a következő parancs használatára a parancsfájlban:

        set hive.execution.engine=mr;

4. A Linux-fürtök esetében lehet olyan forgatókönyv, amelyben a szolgáltatások az elsődleges átjárócsomóponthoz futnak, miközben a Resource Manager a másodlagos gépen is fut. Egy ilyen forgatókönyv hibákat eredményezhet (alább látható), ha a Hue használatával megtekinti a fürtön futó feladatok részleteit. Megtekintheti azonban a feladatok részleteit a feladatok befejeződése után.

   ![Hue-portál hibája](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Hue-portál hibája")

   Ezt egy ismert probléma okozza. Áthidaló megoldásként módosítsa a Ambari, hogy az Active Resource Manager is az elsődleges átjárócsomóponthoz fusson.
5. A Hue megérti a WebHDFS, miközben a HDInsight- `wasb://`fürtök az Azure Storage-t használják a használatával. Így a parancsfájl-művelettel használt egyéni szkript telepíti a WebWasb-t, amely egy WebHDFS-kompatibilis szolgáltatás, amely a WASB-re mutat. Tehát bár a Hue Portal a HDFS (például ha az egérmutatót a **fájlkezelőben**helyezi át), akkor a WASB kell értelmezni.

## <a name="next-steps"></a>További lépések
* [Telepítse az Apache Giraph-t a HDInsight-fürtökre](hdinsight-hadoop-giraph-install-linux.md). A Giraph telepítése a HDInsight Hadoop-fürtökön a fürt testreszabása segítségével. A Giraph lehetővé teszi a Hadoop használatával végzett gráf-feldolgozást, és az Azure HDInsight használható.
* [Telepítse az R-t a HDInsight-fürtökön](hdinsight-hadoop-r-scripts-linux.md). A fürt testreszabásával telepítse az R-t a HDInsight Hadoop-fürtökön. Az R egy nyílt forráskódú nyelv és környezet statisztikai számítástechnikai szolgáltatásokhoz. Több száz beépített statisztikai funkciót és saját programozási nyelvet biztosít, amely ötvözi a funkcionális és az objektumorientált programozás szempontjait. Emellett kiterjedt grafikus képességeket is biztosít.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
