---
title: Színárnyalat a Hadoop-tal HDInsight Linux-alapú fürtökön – Azure
description: Megtudhatja, hogyan telepítheti a Hue-t a HDInsight-fürtökre, és hogyan használhatja a bújtatást a kérelmek Hue-ra történő továbbításához. A Hue segítségével tallózhat a tárolóban, és futtathat Hive vagy Pig.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 69acfd4f2edab9be1b1dcfbb52eafbd00aec712f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934561"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>A Hue telepítése és használata HDInsight Hadoop-fürtökön

Megtudhatja, hogyan telepítheti a Hue-t a HDInsight-fürtökre, és hogyan használhatja a bújtatást a kérelmek Hue-ra történő továbbításához.

## <a name="what-is-hue"></a>Mi az a Hue?

A Hue az Apache Hadoop-fürttel való interakcióra használt webalkalmazások készlete. A Hue segítségével tallózhat a Hadoop-fürthöz (WASB, HDInsight-fürtök esetében) társított tárolóban, futtathatja a Hive-feladatokat és a Pig-parancsfájlokat, és így tovább. A következő összetevők érhetők el a Hue telepítések egy HDInsight Hadoop-fürtön.

* Méhviasz kaptár szerkesztő
* Apache sertés
* Metastore menedzser
* Apacs Oozie
* FileBrowser (amely beszél WASB alapértelmezett tároló)
* Feladat böngésző

> [!WARNING]  
> A HDInsight-fürthöz tartozó összetevők teljes mértékben támogatottak, és a Microsoft támogatási szolgálata segít az összetevőkkel kapcsolatos problémák elkülönítésében és megoldásában.
>
> Az egyéni összetevők üzletileg ésszerű támogatást kapnak a probléma további elhárításához. Ez a probléma megoldásához vezethet, vagy megkéri, hogy vegyen részt a nyílt forráskódú technológiák elérhető csatornáiban, ahol az adott technológiával kapcsolatos mély szakértelem található. Például számos közösségi webhely használható, például: [MSDN fórum a HDInsighthoz](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Szintén Apache projektek projekt [https://apache.org](https://apache.org)oldalak , például: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>A Színárnyalat telepítése parancsfájlműveletek használatával

Használja az alábbi táblázatban található információkat a parancsfájl-művelethez. A Parancsfájl-műveletek használatával kapcsolatos speciális utasításokat a [HDInsight-fürtök testreszabása parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md) című témakörben találja.

> [!NOTE]  
> A Hue HDInsight-fürtökre való telepítéséhez a fejcsomópont ajánlott mérete legalább A4 (8 mag, 14 GB memória).

|Tulajdonság |Érték |
|---|---|
|Parancsfájl típusa:|- Egyéni|
|Név|Színárnyalat telepítése|
|Bash parancsfájl URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Csomóponttípus(ok):|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>A Hue használata HDInsight-fürtökkel

SSH tunneling az egyetlen módja annak, hogy a fürt önfuttatását követően a Hue eléréséhez. Az SSH-n keresztüli bújtatás lehetővé teszi, hogy a forgalom közvetlenül a Hue-t futtató fürt csomópontjára lépjen. Miután a fürt kiépített, a következő lépésekkel használja a Hue-t egy HDInsight-fürtön.

> [!NOTE]  
> Javasoljuk, hogy a Firefox böngészőt használja az alábbi utasítások követéséhez.

1. Az [SSH-bújtatás használata az Apache Ambari webes felhasználói felületének, a ResourceManagernek, a JobHistory-nak, a NameNode-nak, az Oozie-nek és más webes felhasználói felületnek az információival](hdinsight-linux-ambari-ssh-tunnel.md) ssh-alagutat hozhat létre az ügyfélrendszerből a HDInsight-fürthöz, majd konfigurálhatja a webböngészőt az SSH-alagút proxyként való használatára.

1. Az [ssh paranccsal](./hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A csatlakozás után a következő paranccsal szerezze be az elsődleges főcsomópont teljesen minősített tartománynevét:

    ```bash
    hostname -f
    ```

    Ez a következőhöz hasonló nevet ad vissza:

        myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Ez annak az elsődleges csomópontnak a gazdaneve, ahol a Hue webhely található.

1. A böngészősegítségével nyissa meg `http://HOSTNAME:8888`a Hue portált a alkalmazásban. Cserélje le a HOSTNAME nevet az előző lépésben kapott névre.

   > [!NOTE]  
   > Amikor először jelentkezik be, a rendszer kéri, hogy hozzon létre egy fiókot a Hue portálra való bejelentkezéshez. Az itt megadott hitelesítő adatok a portálra korlátozódnak, és nem kapcsolódnak a fürt kiépítése során megadott rendszergazdai vagy SSH-felhasználói hitelesítő adatokhoz.

    ![HDInsight színárnyalat portál bejelentkezési ablak](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Hitelesítő adatok megadása a Színszín portálhoz")

### <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

1. A Hue portálon válassza a **Lekérdezésszerkesztők**lehetőséget, majd a **Hive-szerkesztő** megnyitásához válassza a Hive-szerkesztőt.

    ![A HDInsight színárnyalat portál jattot használ](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Hive használata")

2. A **Segédlap** **Adatbázis**csoportjában a **hivesampletable**jelenik meg. Ez egy mintatábla, amely a HDInsight összes Hadoop-fürtjével együtt kerül kiszállításra. Írjon be egy mintalekérdezést a jobb oldali ablaktáblába, és tekintse meg a kimenetet az alábbi ablaktábla **Eredmények** lapján, ahogy az a képernyőfelvételen látható.

    ![HDInsight színárnyalat portálstruktúra-lekérdezés](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Hive-lekérdezés futtatása")

    A **Diagram** lapon az eredmény vizuális ábrázolását is megtekintheti.

### <a name="browse-the-cluster-storage"></a>Böngészés a fürttárolóban

1. A Színezet portálon válassza a Menüsor jobb felső sarkában található **Fájltallózó** lehetőséget.
2. Alapértelmezés szerint a fájlböngésző a **/user/myuser** könyvtárban nyílik meg. Válassza ki az előre perjel közvetlenül a felhasználói könyvtár az elérési úton, hogy a fürthöz társított Azure storage tároló gyökér.

    ![HDInsight színárnyalat portálfájl-böngésző](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Fájlböngésző használata")

3. Az elérhető műveletek megtekintéséhez kattintson a jobb gombbal egy fájlra vagy mappára. A jobb sarokban található **Feltöltés** gombbal fájlokat tölthet fel az aktuális könyvtárba. Az **Új** gombbal új fájlokat vagy könyvtárakat hozhat létre.

> [!NOTE]  
> A Hue fájl böngészőcsak a HDInsight-fürthöz társított alapértelmezett tároló tartalmát tudja megjeleníteni. A fürthöz társított további tárfiókok/tárolók nem lesznek elérhetők a fájlböngészőhasználatával. Azonban a fürthöz társított további tárolók mindig elérhető a Hive-feladatok. Ha például beírja `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` a parancsot a Hive-szerkesztőbe, láthatja a további tárolók tartalmát is. Ebben a **parancsban** az új tároló nem a fürthöz társított alapértelmezett tároló.

## <a name="important-considerations"></a>Fontos szempontok

1. A Hue telepítéséhez használt parancsfájl csak a fürt elsődleges csomópontjára telepíti azt.

1. A telepítés során több Hadoop-szolgáltatás (HDFS, YARN, MR2, Oozie) újraindul a konfiguráció frissítéséhez. Miután a parancsfájl befejezte a Hue telepítését, eltarthat egy ideig, amíg más Hadoop-szolgáltatások elindulnak. Ez kezdetben hatással lehet hue teljesítményére. Amint minden szolgáltatás elindul, a Hue teljesen működőképes lesz.

1. A Hue nem érti az Apache Tez-feladatokat, ami a Hive jelenlegi alapértelmezett beállítása. Ha a MapReduce programot szeretné használni a Hive végrehajtási motorjaként, frissítse a parancsfájlt úgy, hogy a parancsfájlban a következő parancsot használja:

        set hive.execution.engine=mr;

1. Linux-fürtök, egy forgatókönyv, amelyben a szolgáltatások futnak az elsődleges csomópont, míg az erőforrás-kezelő lehet, hogy fut a másodlagos. Egy ilyen forgatókönyv hibákat okozhat (alább látható), ha a Hue használatával a fürtön futó feladatok részleteinek megtekintéséhez. A feladat részleteit azonban megtekintheti, ha a feladat befejeződött.

   ![Színárnyalat portál – mintaüzenet](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Színárnyalat portál hiba")

   Ez egy ismert probléma miatt történt. Kerülő megoldásként módosítsa az Ambari-t úgy, hogy az aktív erőforrás-kezelő is futaz elsődleges csomóponton.

1. A Hue a WebHDFS szolgáltatást értelmezi, `wasbs://`míg a HDInsight-fürtök az Azure Storage-ot használják a használatával. Tehát a parancsfájlművelethez használt egyéni parancsfájl telepíti a WebWasb-ot, amely egy WebHDFS-kompatibilis szolgáltatás a WASB-szel való beszélgetéshez. Tehát, bár a Hue portál azt mondja, HDFS helyeken (mint amikor az egeret a **file browser),** meg kell értelmezni WASB.

## <a name="next-steps"></a>További lépések

[Telepítse az R-t a HDInsight-fürtökre](hdinsight-hadoop-r-scripts-linux.md). A fürt testreszabásával telepítse az R-t a HDInsight Hadoop-fürtökre. Az R a statisztikai számítástechnika nyílt forráskódú nyelve és környezete. Több száz beépített statisztikai funkciót és saját programozási nyelvet biztosít, amely egyesíti a funkcionális és objektumorientált programozás szempontjait. Ez is szolgáltat kiterjedt grafikus képességek.
