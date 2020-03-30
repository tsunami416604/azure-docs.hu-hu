---
title: 'Rövid útmutató: Apache Storm-topológia létrehozása/kezelése – Azure HDInsight'
description: A rövid útmutatóból megtudhatja, hogyan hozhat létre és figyelhet Apache Storm-topológiát az Azure HDInsightban.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 24173b553f30f652caf20b1ec7500fd9c4d2f7a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73241221"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Rövid útmutató: Apache Storm-topológia létrehozása és figyelése az Azure HDInsightban

Az Apache Storm egy skálázható, hibatűrő, elosztott, valós idejű számítási rendszer az adatstreamek feldolgozására. A Storm on Azure HDInsight segítségével olyan felhőalapú Storm-fürtöket hozhat létre, amelyek valós időben végeznek big data elemzést.

Ebben a rövid útmutatóban az Apache [storm-starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projekt egy apache storm-topológia létrehozásához és figyeléséhez egy meglévő Apache Storm-fürt példáját használhatja.

## <a name="prerequisites"></a>Előfeltételek

* Apache Storm-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon,](../hdinsight-hadoop-create-linux-clusters-portal.md) és válassza a **Storm** for **Cluster típus**lehetőséget.

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="create-the-topology"></a>A topológia létrehozása

1. Csatlakozzon a Storm-fürthöz. Az alábbi parancs szerkesztése a Storm-fürt nevének cseréjével, `CLUSTERNAME` majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A **WordCount** példa megtalálható a HDInsight-fürtben a.-on. `/usr/hdp/current/storm-client/contrib/storm-starter/` A topológia véletlenszerű mondatokat hoz létre, és megszámolja, hogy hányszor fordulnak elő szavak. A következő paranccsal indítsa el a **wordcount** topológiát a fürtön:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>A topológia figyelése

A Storm webes felületet biztosít a topológiák futtatásához, és a HDInsight-fürtrésze.

Kövesse az alábbi lépéseket a topológia a Storm felhasználói felületével történő figyeléséhez:

1. A Storm felhasználói felületének megjelenítéséhez egy webböngészőben nyissa meg a `https://CLUSTERNAME.azurehdinsight.net/stormui` oldalt. Cserélje le a `CLUSTERNAME` elemet a fürt nevére.

2. A **Topológia összegzése**csoportban jelölje ki a **wordcount** bejegyzést a **Név** oszlopban. Megjelennek a topológiával kapcsolatos információk.

    ![A Storm irányítópultja a Storm Starter WordCount-topológiára vonatkozó információkkal.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    Az új oldal a következő információkat tartalmazza:

    |Tulajdonság | Leírás |
    |---|---|
    |Topológia statisztika|Alapvető információk a topológia teljesítményéről, időablakokba rendezve. Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.|
    |Masszírozó|Alapvető információk a spoutokról, beleértve az egyes spoutok által visszaadott utolsó hibát.|
    |Csavarok|Alapvető információk a csavarokról.|
    |Topológia konfigurációja|Részletes információ a topológia konfigurációjáról.|
    |Aktiválás|Folytatja az inaktivált topológia feldolgozását.|
    |Inaktiválás|Szünetelteti a futó topológiát.|
    |Egyensúly helyreállítása|Beállítja a topológia párhuzamosságát. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Az újraegyensúlyozás beállítja a párhuzamosságot a fürtben található csomópontok számának növekedése/csökkenése kiegyensúlyozása érdekében. További információ: [Az Apache Storm topológia párhuzamosságának megértése](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)című témakörben talál.|
    |Megölni|A storm-topológia leállítása a megadott időhosszabbítás után.|

3. Válassza ki a jelen lapon található **Spoutok** vagy **Boltok** szakaszok bejegyzéseinek egyikét. Ekkor információk jelennek meg a kiválasztott összetevővel kapcsolatban.

    ![A Storm irányítópultja a kiválasztott összetevőkkel kapcsolatos információkkal.](./media/apache-storm-quickstart/hdi-component-summary.png)

    Az új oldal a következő információkat jeleníti meg:

    |Tulajdonság | Leírás |
    |---|---|
    |Kifolyó/Csavar statisztika|Alapvető információk az összetevő teljesítményéről, időablakokba rendezve. Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.|
    |Bemeneti statisztika (csak csavar)|A csavar által felhasznált adatokat előállító összetevőkre vonatkozó információk.|
    |Kimeneti statisztika|A csavar által kibocsátott adatokra vonatkozó információk.|
    |Végrehajtók|Információ az összetevő példányairól.|
    |Hibák|Az összetevő által okozott hibák.|

4. Egy adott spout vagy bolt részletes adatainak megtekintésekor az összetevők adott példánya részletes adatainak megtekintéséhez jelöljön ki egy bejegyzést a **Port** oszlopból az **Executors** (Végrehajtók) szakaszban.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    Ebben a példában a **seven** szó 1 493 957 alkalommal fordul elő. A szó előfordulásainak számlálása a topológia indításától kezdve történik.

## <a name="stop-the-topology"></a>A topológia leállítása

Lépjen vissza a **Topology summary** (Topológia összegzése) lapra a word-count topológiához, majd válassza a **Kill** (Törlés) gombot a **Topology actions** (Topológiaműveletek) szakaszban. Amikor a rendszer kéri, adjon meg 10 másodperces értéket a topológia leállítása előtti várakozási időként. Az időtúllépés lejáratát követően az adott topológia már nem jelenik meg az irányítópult **Storm felhasználói felülete** szakaszában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Fürt törléséről a [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával című](../hdinsight-delete-cluster.md)témakörben jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az Apache [storm-starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projekt egy apache storm-topológia létrehozásához és figyeléséhez egy meglévő Apache Storm-fürthöz használt egy példát. A következő cikkhez továbbvezető cikkből megtudhatja, hogy miaz az Apache Storm topológiák kezelésének és felügyeletének alapjai.

> [!div class="nextstepaction"]
>[Apache Storm-topológiák üzembe helyezése és kezelése az Azure HDInsightban](./apache-storm-deploy-monitor-topology-linux.md)