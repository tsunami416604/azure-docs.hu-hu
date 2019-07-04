---
title: 'Gyors útmutató: Hozzon létre, és az Azure HDInsight az Apache Storm-topológia figyelése'
description: A rövid útmutatóban megtudhatja, hogyan hozhat létre és figyelhet egy Azure HDInsight az Apache Storm-topológia.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 12001aef970d3b465a7f5c8e0c7af072b8f4ec80
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428446"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Gyors útmutató: Létrehoz és felügyel egy Azure HDInsight az Apache Storm-topológia

Az Apache Storm egy skálázható, hibatűrő, elosztott, valós idejű számítási rendszer az adatstreamek feldolgozására. A Storm on Azure HDInsight segítségével olyan felhőalapú Storm-fürtöket hozhat létre, amelyek valós időben végeznek big data elemzést.

Ebben a rövid útmutatóban használja példaként, az Apache [storm-starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projekt hozhat létre és figyelhet az Apache Storm-topológia egy meglévő Apache Storm-fürt.

## <a name="prerequisites"></a>Előfeltételek

* Egy HDInsight az Apache Storm-fürt. Lásd: [Apache Hadoop-fürtök létrehozása az Azure portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) válassza **Storm** a **fürt típusa**.

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>A topológia létrehozása

1. A Storm-fürt csatlakozni. Az alábbi parancsot szerkesztése lecserélésével `CLUSTERNAME` alatt futó Storm nevére a fürt, és írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A **WordCount** példa tartalmazza a HDInsight-fürtön, `/usr/hdp/current/storm-client/contrib/storm-starter/`. A topológia állít elő mondatokat véletlenszerű, és megszámolja a szavak fordulhat elő, hogy hány alkalommal. A következő paranccsal indítsa el a **wordcount** topológia a fürtön:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>A topológia figyelése

A Storm webes felületet biztosít a futó topológiákkal való munkavégzéshez, és tartalmazza a HDInsight-fürtön.

Kövesse az alábbi lépéseket a topológia a Storm felhasználói felületével történő figyeléséhez:

1. A Storm felhasználói felületének megjelenítéséhez egy webböngészőben nyissa meg a `https://CLUSTERNAME.azurehdinsight.net/stormui` oldalt. Cserélje le a `CLUSTERNAME` elemet a fürt nevére.

2. A **topológia összegzése**, jelölje be a **wordcount** bejegyzést a **neve** oszlop. Megjelennek a topológiával kapcsolatos információk.

    ![A Storm irányítópultja a Storm Starter WordCount-topológiára vonatkozó információkkal.](./media/apache-storm-quickstart/topology-summary.png)

    Az új oldal a következő információkat biztosítja:

    |Tulajdonság | Leírás |
    |---|---|
    |Topológiastatisztikák|Alapszintű információkat tartalmaz a topológia teljesítményével kapcsolatban, időtartományokba. Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.|
    |Spoutok|Alapszintű információkat tartalmaz a spoutokkal kapcsolatban, beleértve az utolsó hiba egyes spoutok által visszaadott.|
    |A boltok|Alapszintű információkat tartalmaz a boltokkal kapcsolatban.|
    |Topológia konfigurálása|Topológia konfigurációjával kapcsolatos részletes információkat.|
    |Aktiválás|Folytatja az inaktivált topológia feldolgozásra.|
    |inaktiválása|Megszakítja a futó topológiát.|
    |Újraegyensúlyozása|Beállítja a topológia párhuzamosságát. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Az újraegyensúlyozás beállítja a párhuzamosságot a fürtben található csomópontok számának növekedése/csökkenése kiegyensúlyozása érdekében. További információkért lásd: [ismertetése az Apache Storm-topológia párhuzamosságát](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Kill|Storm-topológia leállítása időtúllépést követően.|

3. Válassza ki a jelen lapon található **Spoutok** vagy **Boltok** szakaszok bejegyzéseinek egyikét. Ekkor információk jelennek meg a kiválasztott összetevővel kapcsolatban.

    ![A Storm irányítópultja a kiválasztott összetevőkkel kapcsolatos információkkal.](./media/apache-storm-quickstart/component-summary.png)

    Az új lap az alábbi információkat jeleníti meg:

    |Tulajdonság | Leírás |
    |---|---|
    |Spout vagy Bolt-statisztikák|Az összetevő teljesítménye, időtartományokba alapszintű információkat tartalmaz. Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.|
    |Beviteli statisztikák (csak bolt)|Információk a bolt által feldolgozott adatokat előállító összetevőkről.|
    |Kimeneti statisztikák|Információkat tartalmaz a bolt által kibocsátott adatokról.|
    |Végrehajtóval|Információ a jelen összetevő példányairól.|
    |Hibák|Ez az összetevő által létrehozott hibaüzeneteket.|

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

Miután végzett a gyors üzembe helyezéssel, érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Törölje a fürtöt, tekintse meg a [törlése egy HDInsight-fürtön a böngészőben, a PowerShell vagy az Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban egy, az Apache származó példa használt [storm-starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projekt hozhat létre és figyelhet az Apache Storm-topológia egy meglévő Apache Storm-fürt. Folytassa a következő cikkben talál a kezelése és figyelése Apache Storm-topológiák alapjait.

> [!div class="nextstepaction"]
>[Üzembe helyezés és kezelés az Azure HDInsight az Apache Storm-topológiák](./apache-storm-deploy-monitor-topology-linux.md)