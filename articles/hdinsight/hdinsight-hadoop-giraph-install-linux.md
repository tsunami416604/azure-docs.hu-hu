---
title: A Giraph telepítése és használata az Azure HDInsight
description: Megtudhatja, hogyan telepítheti a Giraph-t HDInsight-fürtökre parancsfájl-műveletek használatával. Az Azure-felhőben a Giraph használatával végezhet gráf-feldolgozást Apache Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/26/2019
ms.openlocfilehash: 1f6fd88ec492f26f6819dff099ec8fe53364ba0b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552253"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Az Apache Giraph telepítése a HDInsight Hadoop-fürtökön, valamint a Giraph használata a nagyméretű diagramok feldolgozásához

Ismerje meg, hogyan telepítheti az Apache Giraph-t egy HDInsight-fürtön. A HDInsight parancsfájl-művelet funkciója lehetővé teszi a fürt testreszabását bash-szkript futtatásával. A parancsfájlok segítségével a fürtöket a fürt létrehozása során és után is testreszabhatja.

## <a name="what-is-giraph"></a>Mi az a Giraph?

Az [Apache Giraph](https://giraph.apache.org/) lehetővé teszi a Hadoop használatával végzett gráfok feldolgozását, és az Azure HDInsight használható. Az objektumok közötti kapcsolati modelleket ábrázolja. Például az útválasztók közötti kapcsolatok egy nagyméretű hálózaton, például az interneten, illetve a közösségi hálózatokban lévő személyek közötti kapcsolatok. A Graph-feldolgozás lehetővé teszi a diagramok objektumai közötti kapcsolatok okát, például:

* A lehetséges barátok azonosítása az aktuális kapcsolatok alapján.

* A legrövidebb útvonal azonosítása a hálózat két számítógépe között.

* Weblapok rangsorolásának kiszámítása.

> [!WARNING]  
> A HDInsight-fürttel biztosított összetevők teljes mértékben támogatottak – Microsoft ügyfélszolgálata segít elkülöníteni és elhárítani az ezen összetevőkkel kapcsolatos problémákat.
>
> Az egyéni összetevők, például a Giraph, kereskedelmileg ésszerű támogatást kapnak a probléma további megoldásához. Előfordulhat, hogy a Microsoft ügyfélszolgálata fel tudja oldani a problémát. Ha nem, akkor tekintse meg a nyílt forráskódú közösségeket, ahol az adott technológia mélyreható szakértelmét keresi. Többek között számos közösségi webhely használható, például a [következőhöz: HDInsight MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) [https://stackoverflow.com](https://stackoverflow.com). Emellett az Apache-projektek [https://apache.org on ](https://apache.org)is rendelkeznek projekt-webhelyekkel, például: [Hadoop](https://hadoop.apache.org/).

## <a name="what-the-script-does"></a>A parancsfájl funkciója

Ez a szkript a következő műveleteket hajtja végre:

* Telepíti a Giraph `/usr/hdp/current/giraph`.

* A `giraph-examples.jar` fájl másolása a fürt alapértelmezett tárolójába (WASB): `/example/jars/giraph-examples.jar`.

## <a name="install-giraph-using-script-actions"></a>A Giraph telepítése parancsfájl-műveletek használatával

A Giraph HDInsight-fürtön való telepítésére szolgáló minta-parancsfájl a következő címen érhető el: `https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`

Ez a szakasz azt ismerteti, hogyan használható a minta parancsfájl a fürt a Azure Portal használatával történő létrehozásakor.

> [!NOTE]  
> Parancsfájl-művelet a következő módszerek bármelyikével alkalmazható:
> * Azure PowerShell
> * Azure CLI
> * A HDInsight .NET SDK
> * Azure Resource Manager-sablonok
> 
> Parancsfájl-műveleteket is alkalmazhat már futó fürtökre. További információ: HDInsight- [fürtök testreszabása parancsfájl-műveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

1. Hozzon létre egy fürtöt a [Linux-alapú HDInsight-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-portal.md)című témakör lépéseit követve, de ne fejezze be a létrehozást. A **klasszikus létrehozási élményt** és az **Egyéni (méret, beállítások, alkalmazások)** szolgáltatást kell használnia.

1. A **fürt mérete** szakaszban győződjön meg arról, hogy a **munkavégző csomópontok száma** legalább 2, ebben a példában.

1. A **parancsfájl-műveletek** szakaszban adja meg a következő információkat:

    |Tulajdonság |Value (Díj) |
    |---|---|
    |Parancsfájl típusa|– Egyéni|
    |Név|A Giraph telepítése|
    |Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`|
    |Csomópont típusa (i)|Fej|
    |Paraméterek|Hagyja üresen|

    További információ: parancsfájl- [művelet használata a fürt létrehozása során](./hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation).

1. Folytassa a fürt létrehozását a [Linux-alapú HDInsight-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-portal.md)című témakörben leírtak szerint.

## <a name="how-do-i-use-giraph-in-hdinsight"></a>Hogyan Giraph használ a HDInsight-ben?

A fürt létrehozása után a következő lépésekkel futtathatja a Giraph-ben található SimpleShortestPathsComputation-példát. Ez a példa az alapszintű [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) megvalósítását használja a diagram objektumai közötti lehető legrövidebb útvonal megkereséséhez.

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](./hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A következő parancs használatával hozzon létre egy **tiny_graph. txt**nevű fájlt:

    ```bash
    nano tiny_graph.txt
    ```

    A fájl tartalma legyen a következő szöveg:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Ezek az adatkapcsolatok egy irányított gráf objektumai közötti kapcsolatot írják le a `[source_id, source_value,[[dest_id], [edge_value],...]]`formátum használatával. Minden sor egy `source_id` objektum és egy vagy több `dest_id` objektum közötti kapcsolatot jelöl. A `edge_value` a `source_id` és a `dest\_id`közötti kapcsolat erősségét vagy távolságát lehet meggondolni.

    Kirajzolva, az objektumok közötti távolságként pedig az érték (vagy a súlyozás) használatával az adatai a következő ábrához hasonlóan jelennek meg:

    ![tiny_graph. txt rajzolt körökkel, különböző távolságú vonalakkal](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. A fájl mentéséhez használja a **CTRL + X billentyűkombinációt**, majd az **Y**billentyűt, és végül **adja meg** a fájl nevét.

4. Az alábbi paranccsal tárolhatja az adatait a HDInsight-fürt elsődleges tárolójába:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Futtassa a SimpleShortestPathsComputation példát a következő parancs használatával:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    > [!IMPORTANT]
    > A `-w`nak átadott értéknek a munkavégző csomópontok tényleges számánál kisebbnek vagy azzal egyenlőnek kell lennie.

    Az ezzel a paranccsal használt paramétereket az alábbi táblázat ismerteti:

   | Paraméter | Funkció |
   | --- | --- |
   | jar |A példákat tartalmazó jar-fájl. |
   | org. Apache. giraph. GiraphRunner |A példák elindításához használt osztály. |
   | org. Apache. giraph. examples. SimpleShortestPathsComputation |A használt példa. Ebben a példában az 1. azonosító és a gráf összes többi azonosítója közötti legrövidebb útvonalat számítja ki. |
   | -CA mapred. job. Tracker |A fürt átjárócsomóponthoz. |
   | – vif |A bemeneti adatokhoz használandó bemeneti formátum. |
   | – VIP |A bemeneti adatfájl. |
   | – VOF |A kimeneti formátum. Ebben a példában az azonosító és az érték egyszerű szövegként szerepel. |
   | -op |A kimeneti hely. |
   | -w 2 |A használni kívánt feldolgozók száma. Ebben a példában a 2. |

    További információ ezekről és a Giraph-mintákhoz használt egyéb paraméterekről: [Giraph](https://giraph.apache.org/quick_start.html)gyors útmutató.

6. Ha a feladatot befejezte, a rendszer az eredményeket a **/example/output/shortestpaths** könyvtárban tárolja. A kimeneti fájlnevek a " **m" résszel** kezdődnek, és egy olyan számmal végződik, amely az első, a második és a hasonló értéket jelöli. A kimenet megtekintéséhez használja az alábbi parancsot:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    A kimenet az alábbi szöveghez hasonlóan jelenik meg:

    ```output
    0    1.0
    4    5.0
    2    2.0
    1    0.0
    3    1.0
    ```

    A SimpleShortestPathComputation példa az 1. AZONOSÍTÓJÚ objektummal való indításhoz szükséges, és a lehető legrövidebb útvonalat keresi a többi objektumhoz. A kimenet `destination_id` és `distance`formátumban van. A `distance` az 1. objektumazonosító és a cél-azonosító között átutazó élek értéke (vagy súlya).

    Az adatmegjelenítéssel ellenőrizheti az eredményeket az 1. azonosító és az összes többi objektum közötti legrövidebb elérési utak beutazásával. Az 1. és a 4. azonosító közötti legrövidebb út 5. Ez az érték az 1. és a 3. azonosító, majd a 3. és a 4. azonosító közötti teljes távolság.

    ![Objektumok rajzolása körökben a legrövidebb útvonalak között](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Következő lépések

[HDInsight-fürtökön a Hue telepítése és használata](hdinsight-hadoop-hue-linux.md).
