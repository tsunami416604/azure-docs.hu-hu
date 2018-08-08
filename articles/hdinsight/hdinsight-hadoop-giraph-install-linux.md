---
title: Giraph telepítése és használata a HDInsight (Hadoop) – Azure
description: Ismerje meg, a Giraph telepítése Linux-alapú HDInsight-fürtökön parancsfájlműveletekkel. A Szkriptműveletek testre szabhatja a fürt létrehozása során, a fürt konfigurációjának módosítása, vagy a szolgáltatások és segédprogramok telepítése teszi lehetővé.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.openlocfilehash: a96bd4e55a82e4896da7ed38d29fa7c04f08696b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600727"
---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>A Giraph telepítése a HDInsight Hadoop-fürtöket, és a Giraph használata nagyméretű gráfok feldolgozásához

Ismerje meg, az Apache Giraph telepítése egy HDInsight-fürtön. A szkript HDInsight művelet funkciója lehetővé teszi, hogy testre szabhatja a fürt egy bash-szkript futtatásával. Fürtök testreszabása során és a fürt létrehozása után használható parancsprogramok.

> [!IMPORTANT]
> A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whatis"></a>Mi az a Giraph

[Az Apache Giraph](http://giraph.apache.org/) diagramfeldolgozási Hadoop használatával végezhető el, és az Azure HDInsight is használható. Gráfok objektumok közötti kapcsolatok modellezésére. Ha például a kapcsolatok között nagyobb hálózatokon, mint például az interneten útválasztók és a közösségi hálózatokon személyek közötti kapcsolatok. Gráffeldolgozási lehetővé teszi a grafikon, objektumok közötti kapcsolatok vonatkozó döntések meghozatalát például:

* A jelenlegi kapcsolatok alapján lehetséges barátok azonosítása.

* A hálózat két számítógép közötti legrövidebb útvonal azonosítása.

* Weblapok oldalon belüli rangjának kiszámítása.

> [!WARNING]
> A HDInsight-fürthöz megadott összetevők teljes mértékben támogatottak,-Support segít elkülöníteni, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők, például a Giraph, annak érdekében, hogy a probléma további hibaelhárításához üzletileg ésszerű támogatást kapnak. Microsoft Support lehet oldani a problémát. Ha nem, a nyílt forráskódú Közösségek részletes szakértelmét, hogy a technológiát találhatók egyeztetnie kell. Számos, használható, például közösségi helyek vannak, például: [HDInsight az MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Is Apache projektek rendelkeznek projekt helyek [ http://apache.org ](http://apache.org), például: [Hadoop](http://hadoop.apache.org/).


## <a name="what-the-script-does"></a>A parancsfájl leírása

Ez a szkript a következő műveleteket hajtja végre:

* A Giraph telepítése `/usr/hdp/current/giraph`

* Másolja a `giraph-examples.jar` fájl alapértelmezett Storage (WASB) a fürt számára: `/example/jars/giraph-examples.jar`

## <a name="install"></a>A Parancsfájlműveletek segítségével a Giraph telepítése

A parancsfájlpéldát a Giraph telepítése egy HDInsight-fürtön a következő helyen érhető el:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Ez a szakasz útmutatást az Azure portal használatával a fürt létrehozásakor a minta parancsfájl használatával.

> [!NOTE]
> Az alábbi módszerek bármelyikével szkriptműveletet lehet alkalmazni:
> * Azure PowerShell
> * Az Azure CLI
> * A HDInsight .NET SDK-val
> * Azure Resource Manager-sablonok
> 
> Már fut a fürtök parancsfájlműveletekkel is alkalmazhat. További információkért lásd: [testreszabása HDInsight fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

1. Indítsa el a fürt létrehozása a lépéseket követve [Linux-alapú HDInsight-fürtök](hdinsight-hadoop-create-linux-clusters-portal.md), azonban nem fejeződnek be létrehozása.

2. Az a **opcionális konfigurációs** szakaszban jelölje be **Parancsfájlműveletek**, és adja meg a következő információkat:

   * **NÉV**: Adjon egy rövid nevet a parancsprogram-művelet.

   * **SZKRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **A fő**: Ez a bejegyzés ellenőrzése

   * **FELDOLGOZÓ**: ne jelölje be ezt a bejegyzést

   * **ZOOKEEPER**: ne jelölje be ezt a bejegyzést

   * **PARAMÉTEREK**: hagyja üresen a mezőt

3. Alsó részén a **Parancsfájlműveletek**, használja a **kiválasztása** gombra a konfiguráció mentéséhez. Végül a **kiválasztása** gomb alsó részén a **opcionális konfigurációs** szakasz az opcionális konfigurációs adatok mentéséhez.

4. A fürt létrehozása a leírtak szerint folytassa [Linux-alapú HDInsight-fürtök](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>A Giraph használata a HDInsight

Ha a fürt létrejött, használja az alábbi lépéseket a Giraph mellékelt SimpleShortestPathsComputation példa futtatásához. Ez a példa az alapszintű [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) megvalósítása a grafikus objektumok közötti legrövidebb útvonal megkeresése.

1. Csatlakozzon SSH-val a HDInsight-fürthöz:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A következő paranccsal hozzon létre egy fájlt **tiny_graph.txt**:

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

    Ezeket az adatokat egy irányított gráfban, a következő formátumban objektumok közötti kapcsolatot ismerteti `[source_id, source_value,[[dest_id], [edge_value],...]]`. Az egyes sorok közötti kapcsolatot jelent egy `source_id` objektum és a egy vagy több `dest_id` objektumokat. A `edge_value` erőssége vagy közötti kapcsolat távolság tekinthető `source_id` és `dest\_id`.

    Dolgozni, és az objektumok közötti távolságot a értéket (vagy a súly) használ, az adatok a következőhöz hasonló lehet a következő ábra:

    ![különböző távolságát sornyi körök Megrajzolás tiny_graph.txt](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Mentse a fájlt, használja a **Ctrl + X**, majd **Y**, és végül **Enter** fogadja el a fájlt.

4. A HDInsight-fürt elsődleges storage-bA az adatok tárolásához használja a következő:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Futtassa a SimpleShortestPathsComputation példa a következő paranccsal:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Ezzel a paranccsal használt paraméterek a következő táblázat ismerteti:

   | Paraméter | Művelet |
   | --- | --- |
   | `jar` |A példákat tartalmazó jar-fájlt. |
   | `org.apache.giraph.GiraphRunner` |Indítsa el a példákban használt osztály. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |A példában használt. Ebben a példában kiszámítja a legrövidebb útvonal azonosító 1 és a gráf összes egyéb azonosítók között. |
   | `-ca mapred.job.tracker` |Az a fürt átjárócsomópontjával. |
   | `-vif` |A bemeneti formátum a bemeneti adatok használatához. |
   | `-vip` |A bemeneti adatfájlt. |
   | `-vof` |A kimeneti formátumot. Ebben a példában, azonosítója és egyszerű szöveges érték. |
   | `-op` |A kimeneti helyet. |
   | `-w 2` |A használandó feldolgozók száma. Ebben a példában a 2. |

    Ezeket és más paramétereket, a Giraph minták használt további információkért lásd: a [Giraph rövid](http://giraph.apache.org/quick_start.html).

6. A feladat befejeztével az eredmények tárolása az **/example/out/shotestpaths** könyvtár. A kimeneti fájl neve kezdődhet **. rész – m -** és az első, a második, stb. fájl jelző szám. A kimenet megtekintéséhez használja az a következő parancsot:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    A kimenetben megjelenik az alábbi szöveghez hasonló:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    A SimpleShortestPathComputation, például rögzített, a kezdéshez kódolt azonosító 1 objektum, és más objektumok legrövidebb útvonal megkeresése. A kimenet a formátumban `destination_id` és `distance`. A `distance` a szélén, amennyi objektum azonosítója 1 és a cél-azonosítót. közötti érték (vagy súlya)

    Megjeleníteni az adatokat, ellenőrizheti az eredményeket a legrövidebb elérési utak utazás azonosító 1 és az összes többi objektum között. ID 1 és 4 azonosító közötti legrövidebb útvonal érték 5. Teljes közötti távolság értéke <span style="color:orange">azonosító 1. és 3</span>, majd <span style="color:red">azonosító 3. és 4</span>.

    ![Az objektumok Rajzolás körök, a legrövidebb elérési utak között](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>További lépések

* [Telepítse, és a Hue használata a HDInsight-fürtökön](hdinsight-hadoop-hue-linux.md).

* [A Solr telepítése HDInsight-fürtökön](hdinsight-hadoop-solr-install-linux.md).
