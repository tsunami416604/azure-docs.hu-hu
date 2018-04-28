---
title: Telepítheti és használhatja Giraph HDInsight (Hadoop) - Azure |} Microsoft Docs
description: Megtudhatja, hogyan Giraph telepítése Linux-alapú HDInsight-fürtök Parancsfájlműveletek használatával. A Parancsfájlműveletek engedélyezi, hogy testre szabhatja a fürt létrehozásakor fürtkonfiguráció módosításával, vagy a szolgáltatások és segédprogramok telepítése.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9fcac906-8f06-4002-9fe8-473e42f8fd0f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 03e72c29bedf6a3125a5ae0272e93cdf58632bc6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>HDInsight Hadoop-fürtök Giraph telepítse, és nagy méretű diagramjait feldolgozásához Giraph használni

Útmutató Apache Giraph telepítse a HDInsight-fürtöt. A HDInsight a parancsfájl művelet a szolgáltatás lehetővé teszi a fürt testreszabását bash parancsfájl futtatásával. Parancsfájlok segítségével testre szabhatja a fürtök alatt és a fürt létrehozása után.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Linux igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whatis"></a>Mi az a Giraph

[Apache Giraph](http://giraph.apache.org/) lehetővé teszi a végrehajtását diagramfeldolgozás Hadoop használatával, és az Azure HDInsight használható. Diagramokat objektumok közötti kapcsolatok modellezésére. Például a nagy hálózati útválasztók közötti kapcsolatok, mint például az internetről, vagy a közösségi hálózatokkal személyek közötti kapcsolatok. Graph feldolgozás lehetővé teszi a ok egy grafikonon objektumok közötti kapcsolatok, mint:

* A jelenlegi kapcsolatok alapján lehetséges ismerősök azonosítása.

* Azonosítja a legrövidebb útvonal hálózatban két számítógép között.

* A lap rangot a weblapok kiszámítása.

> [!WARNING]
> A HDInsight-fürt összetevői teljes mértékben támogatottak, mert a Microsoft Support segít elkülöníteni, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők, például Giraph, minden üzleti szempontból ésszerű terméktámogatási segítséget nyújtanak a probléma további hibaelhárításához. Microsoft Support lehet a probléma megoldását. Ha nem, ahol részletes segítséget, hogy a technológiát található nyílt forráskódú Közösségek kell tájékozódnia. Például nincsenek sok közösségi webhelyek használható, például: [MSDN fórum hdinsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Is Apache projektek rendelkezik projekt helyek [ http://apache.org ](http://apache.org), például: [Hadoop](http://hadoop.apache.org/).


## <a name="what-the-script-does"></a>A parancsfájl funkciója

Ezt a parancsfájlt a következő műveleteket hajtja végre:

* Giraph történő telepítése `/usr/hdp/current/giraph`

* Másolja a `giraph-examples.jar` fájl alapértelmezett Storage (WASB) a fürt: `/example/jars/giraph-examples.jar`

## <a name="install"></a>A Parancsfájlműveletek segítségével Giraph telepítése

Egy HDInsight-fürtök Giraph telepítendő parancsfájlt a következő helyen érhető el:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Ez a szakasz utasításokat biztosít a minta-parancsfájl használata a fürt létrehozásakor az Azure portál használatával.

> [!NOTE]
> A parancsfájl művelet a következő módszerekkel történő alkalmazhatók:
> * Azure PowerShell
> * Az Azure parancssori felület
> * A HDInsight .NET SDK
> * Azure Resource Manager-sablonok
> 
> Már fut a fürtök Parancsfájlműveletek is alkalmazhat. További információkért lásd: [testreszabása HDInsight-fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

1. Indítsa el a fürt létrehozása a lépések segítségével [létrehozása Linux-alapú HDInsight-fürtök](hdinsight-hadoop-create-linux-clusters-portal.md), de nem hajtja végre létrehozása.

2. Az a **opcionális konfigurációs** szakaszban jelölje be **Parancsfájlműveletek**, és adja meg a következő információkat:

   * **NÉV**: Adja meg a parancsfájlművelet rövid nevét.

   * **PARANCSFÁJL URI AZONOSÍTÓJA**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: Ez a bejegyzés ellenőrzése

   * **MUNKAVÉGZŐ**: hagyja bejelölve ezt a bejegyzést

   * **ZOOKEEPER**: hagyja bejelölve ezt a bejegyzést

   * **PARAMÉTEREK**: ezt a mezőt hagyja üresen

3. Alján a **Parancsfájlműveletek**, használja a **válasszon** gombra kattintva mentse a konfigurációt. Végül a **válasszon** gomb alján a **opcionális konfigurációs** szakasz menti az opcionális konfigurációs információkat.

4. A fürt létrehozása, a folytatáshoz [létrehozása Linux-alapú HDInsight-fürtök](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Giraph használata a Hdinsightban

Ha a fürt létrejött, a következő lépésekkel a mellékelt Giraph SimpleShortestPathsComputation példa futtatásához. Ez a példa a basic [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) megvalósítása a legrövidebb elérési út egy grafikonon objektumok közötti kereséséhez.

1. Csatlakozzon SSH-val a HDInsight-fürthöz:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Az alábbi parancs segítségével hozzon létre egy fájlt **tiny_graph.txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Ez a fájl tartalmát a következő szöveg használata:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Ezek az adatok irányított gráf, a következő formátumban objektumok közötti kapcsolatot ismerteti `[source_id, source_value,[[dest_id], [edge_value],...]]`. Minden egyes sorban közötti kapcsolatot jelent a `source_id` objektum és egy vagy több `dest_id` objektumok. A `edge_value` -re a erőssége vagy a kapcsolat közötti távolság `source_id` és `dest\_id`.

    Jelenik meg, és objektumok közötti távolságot a érték (vagy súly) használ, az adatok nézhet ki például az alábbi ábra:

    ![különböző távolságát sornyi kör Megrajzolás tiny_graph.txt](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Mentse a fájlt, használja a **Ctrl + X**, majd **Y**, és végül **Enter** fogadja el a fájlt.

4. A HDInsight-fürtjéhez elsődleges tárba. az adatok tárolásához használja a következőket:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Futtassa a SimpleShortestPathsComputation például a következő parancsot:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Ezzel a paranccsal használt paraméterek a következő táblázat ismerteti:

   | Paraméter | Művelet |
   | --- | --- |
   | `jar` |A példák tartalmazó jar-fájlt. |
   | `org.apache.giraph.GiraphRunner` |Az osztály a példák indításához használt. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |A példában használt. Ebben a példában kiszámítja a legrövidebb elérési azonosítója 1 és a Graph más azonosítók között. |
   | `-ca mapred.job.tracker` |A fürt headnode. |
   | `-vif` |A bemeneti az formátumát a bemeneti adatok. |
   | `-vip` |A bemeneti adatfájlt. |
   | `-vof` |A kimeneti formátum. A példa, azonosítója és egyszerű szövegként érték. |
   | `-op` |A kimeneti helyet. |
   | `-w 2` |A munkavállalók használandó száma. Ebben a példában, 2. |

    Ezeket és más Giraph minták használt paraméterek további információkért lásd: a [Giraph gyors üzembe helyezés](http://giraph.apache.org/quick_start.html).

6. Ha a feladat befejeződött, az eredmények tárolódnak a **/example/out/shotestpaths** könyvtár. A kimeneti fájl kezdődő **rész-m -** és véget a száma, amelyben az első, a második, a fájl stb. A következő paranccsal eredményének megtekintéséhez:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    A kimenet az alábbihoz hasonló jelenik meg:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    A kódolt kezdődnie példája rögzített SimpleShortestPathComputation azonosítója 1 objektum, és keresse meg a legrövidebb más objektumok elérési útja. A kimeneti formátumban van `destination_id` és `distance`. A `distance` objektum azonosítója 1 és a célként megadott azonosító közötti távolság széleinek érték (vagy súlya)

    Ezek az adatok megjelenítése, ellenőrizheti az eredményeket a legrövidebb elérési utak utazás azonosítója 1 és egyéb objektumok között. A legrövidebb azonosítója 1 és 4 azonosító közötti elérési út 5. Az értéket nem teljes távolságát <span style="color:orange">azonosítója 1. és 3</span>, majd <span style="color:red">azonosító 3. és 4</span>.

    ![Az objektumok rajzolási körök mint a legrövidebb elérési utak között](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>További lépések

* [Telepítheti és használhatja a HDInsight-fürtök Hue](hdinsight-hadoop-hue-linux.md).

* [Solr telepíthető a HDInsight-fürtök](hdinsight-hadoop-solr-install-linux.md).
