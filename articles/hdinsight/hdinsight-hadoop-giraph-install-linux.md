---
title: A Giraph telepítése és használata az Azure HDInsight
description: Megtudhatja, hogyan telepítheti a Giraph-t HDInsight-fürtökre parancsfájl-műveletek használatával. Az Azure-felhőben a Giraph használatával végezhet gráf-feldolgozást Apache Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: f1ca536ffa2166df4ef6cf51654b7b410e72ea66
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962073"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Az Apache Giraph telepítése a HDInsight Hadoop-fürtökön, valamint a Giraph használata a nagyméretű diagramok feldolgozásához

Ismerje meg, hogyan telepítheti az Apache Giraph-t egy HDInsight-fürtön. A HDInsight parancsfájl-művelet funkciója lehetővé teszi a fürt testreszabását bash-szkript futtatásával. A parancsfájlok segítségével a fürtöket a fürt létrehozása során és után is testreszabhatja.

## <a name="whatis"></a>Mi az a Giraph?

Az [Apache Giraph](https://giraph.apache.org/) lehetővé teszi a Hadoop használatával végzett gráfok feldolgozását, és az Azure HDInsight használható. Az objektumok közötti kapcsolati modelleket ábrázolja. Például az útválasztók közötti kapcsolatok egy nagyméretű hálózaton, például az interneten, illetve a közösségi hálózatokban lévő személyek közötti kapcsolatok. A Graph-feldolgozás lehetővé teszi a diagramok objektumai közötti kapcsolatok okát, például:

* A lehetséges barátok azonosítása az aktuális kapcsolatok alapján.

* A legrövidebb útvonal azonosítása a hálózat két számítógépe között.

* Weblapok rangsorolásának kiszámítása.

> [!WARNING]  
> A HDInsight-fürttel biztosított összetevők teljes mértékben támogatottak – Microsoft ügyfélszolgálata segít elkülöníteni és elhárítani az ezen összetevőkkel kapcsolatos problémákat.
>
> Az egyéni összetevők, például a Giraph, kereskedelmileg ésszerű támogatást kapnak a probléma további megoldásához. Előfordulhat, hogy a Microsoft ügyfélszolgálata fel tudja oldani a problémát. Ha nem, akkor tekintse meg a nyílt forráskódú közösségeket, ahol az adott technológia mélyreható szakértelmét keresi. Például számos közösségi webhely használható, például: [MSDN-fórum a HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)- [https://stackoverflow.com](https://stackoverflow.com)hoz. Emellett az Apache [https://apache.org](https://apache.org)-projektek is rendelkeznek projekt-webhelyekkel, például: [Hadoop](https://hadoop.apache.org/).


## <a name="what-the-script-does"></a>A parancsfájl funkciója

Ez a szkript a következő műveleteket hajtja végre:

* A Giraph telepítése`/usr/hdp/current/giraph`

* A `giraph-examples.jar` fájl másolása a fürt alapértelmezett tárolójába (WASB):`/example/jars/giraph-examples.jar`

## <a name="install"></a>A Giraph telepítése parancsfájl-műveletek használatával

A Giraph egy HDInsight-fürtön való telepítésére szolgáló parancsfájl a következő helyen érhető el:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Ez a szakasz azt ismerteti, hogyan használható a minta parancsfájl a fürt a Azure Portal használatával történő létrehozásakor.

> [!NOTE]  
> Parancsfájl-művelet a következő módszerek bármelyikével alkalmazható:
> * Azure PowerShell
> * Azure CLI
> * A HDInsight .NET SDK
> * Azure Resource Manager-sablonok
> 
> Parancsfájl-műveleteket is alkalmazhat már futó fürtökre. További információ: HDInsight- [fürtök testreszabása parancsfájl-műveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

1. Hozzon létre egy fürtöt a [Linux-alapú HDInsight-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-portal.md)című témakör lépéseit követve, de ne fejezze be a létrehozást.

2. A **választható konfiguráció** szakaszban válassza a **parancsfájlok műveletei**lehetőséget, és adja meg a következő információkat:

   * **NÉV**: Adja meg a parancsfájl-művelet rövid nevét.

   * **PARANCSFÁJL URI-JA**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: Ennek a bejegyzésnek a bejelölése.

   * FELDOLGOZÓ: Hagyja üresen ezt a bejegyzést.

   * **ZOOKEEPER**: Hagyja üresen ezt a bejegyzést.

   * **PARAMÉTEREK**: Hagyja üresen ezt a mezőt.

3. A **parancsfájl műveleteinek**alján a **kiválasztás** gombbal mentheti a konfigurációt. Végül **a választható konfigurációs szakasz** alján található **kiválasztás** gombra kattintva mentheti a választható konfigurációs adatokat.

4. Folytassa a fürt létrehozását a [Linux-alapú HDInsight-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-portal.md)című témakörben leírtak szerint.

## <a name="usegiraph"></a>Hogyan Giraph használ a HDInsight-ben?

A fürt létrehozása után a következő lépésekkel futtathatja a Giraph-ben található SimpleShortestPathsComputation-példát. Ez a példa az alapszintű [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) megvalósítását használja a diagram objektumai közötti lehető legrövidebb útvonal megkereséséhez.

1. Csatlakozzon SSH-val a HDInsight-fürthöz:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

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

    Ezek az adatértékek egy irányított gráf objektumai közötti kapcsolatot írják le a formátum `[source_id, source_value,[[dest_id], [edge_value],...]]`használatával. Minden sor egy `source_id` objektum és egy vagy több `dest_id` objektum közötti kapcsolatot jelöl. A `edge_value` a `source_id` és`dest\_id`a közötti kapcsolat erősségét vagy távolságát is meggondolhatja.

    Kirajzolva, az objektumok közötti távolságként pedig az érték (vagy a súlyozás) használatával az adatai a következő ábrához hasonlóan jelennek meg:

    ![a tiny_graph. txt olyan körökkel rajzolt, amelyek eltérő távolságú vonalakkal rendelkeznek](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. A fájl mentéséhez használja a **CTRL + X billentyűkombinációt**, majd az **Y**billentyűt, és végül **adja meg** a fájl nevét.

4. Az alábbi paranccsal tárolhatja az adatait a HDInsight-fürt elsődleges tárolójába:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Futtassa a SimpleShortestPathsComputation példát a következő parancs használatával:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Az ezzel a paranccsal használt paramétereket az alábbi táblázat ismerteti:

   | Paraméter | Funkció |
   | --- | --- |
   | `jar` |A példákat tartalmazó jar-fájl. |
   | `org.apache.giraph.GiraphRunner` |A példák elindításához használt osztály. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |A használt példa. Ebben a példában az 1. azonosító és a gráf összes többi azonosítója közötti legrövidebb útvonalat számítja ki. |
   | `-ca mapred.job.tracker` |A fürt átjárócsomóponthoz. |
   | `-vif` |A bemeneti adatokhoz használandó bemeneti formátum. |
   | `-vip` |A bemeneti adatfájl. |
   | `-vof` |A kimeneti formátum. Ebben a példában az azonosító és az érték egyszerű szövegként szerepel. |
   | `-op` |A kimeneti hely. |
   | `-w 2` |A használni kívánt feldolgozók száma. Ebben a példában a 2. |

    További információ ezekről és a Giraph-mintákhoz használt egyéb paraméterekről: [Giraph](https://giraph.apache.org/quick_start.html)gyors útmutató.

6. Ha a feladatot befejezte, a rendszer az eredményeket a **/example/out/shortestpaths** könyvtárban tárolja. A kimeneti fájlnevek a " **m" résszel** kezdődnek, és a végén egy szám jelzi az első, második stb. fájlt. A kimenet megtekintéséhez használja az alábbi parancsot:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    A kimenet az alábbi szöveghez hasonlóan jelenik meg:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    A SimpleShortestPathComputation példa az 1. AZONOSÍTÓJÚ objektummal való indításhoz szükséges, és a lehető legrövidebb útvonalat keresi a többi objektumhoz. A kimenet formátuma `destination_id` : és `distance`. A `distance` az 1. azonosítójú objektum és a cél azonosítója között átutazó élek értéke (vagy súlya).

    Az adatmegjelenítéssel ellenőrizheti az eredményeket az 1. azonosító és az összes többi objektum közötti legrövidebb elérési utak beutazásával. Az 1. és a 4. azonosító közötti legrövidebb út 5. Ez az érték az <span style="color:orange">1. és a 3. azonosító</span>, majd a <span style="color:red">3</span>. és a 4. azonosító közötti teljes távolság.

    ![Objektumok rajzolása körökben a legrövidebb útvonalak között](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtökön a Hue telepítése és használata](hdinsight-hadoop-hue-linux.md).
