---
title: Storm Starter-példák a HDInsight-alapú Apache Storm rendszerben – Azure
description: Ismerje meg az Apache Storm használatával történő valós idejű big data-elemzést és adatfeldolgozást, valamint a HDInsight-alapú Storm Starter-példákat.
keywords: storm starter, apache storm-példa
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 900180c9991932f4efaa07f9881e9f3f897cd99e
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498283"
---
# <a name="get-started-with-apache-storm-on-hdinsight-using-the-storm-starter-examples"></a>A HDInsight-alapú Apache Storm rendszer használatának első lépései Storm Starter-példákkal

Ismerje meg, hogyan használható [Apache Storm](http://storm.apache.org/) a HDInsight a storm-starter példák segítségével.

Az Apache Storm egy skálázható, hibatűrő, elosztott, valós idejű számítási rendszer az adatstreamek feldolgozására. A Storm on Azure HDInsight segítségével olyan felhőalapú Storm-fürtöket hozhat létre, amelyek valós időben végeznek big data elemzést.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **SSH- és SCP-ismeretek**. További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-storm-cluster"></a>Az Apache Storm-fürt létrehozása

HDInsight alatt futó Storm-fürt létrehozásához kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **+ Erőforrás létrehozása**, **Data + Analytics**, majd a **HDInsight** elemet.

    ![HDInsight-fürt létrehozása](./media/apache-storm-tutorial-get-started-linux/create-hdinsight.png)

2. Az **Alapvető beállítások** szakaszban adja meg a következőket:

    * **Fürt neve**: A HDInsight-fürt neve.
    * **Előfizetés**: Válassza ki a használni kívánt előfizetést.
    * **Fürt bejelentkezési felhasználóneve** és **Fürt bejelentkezési jelszava**: A fürt HTTPS-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Ezekkel a hitelesítő adatokkal érheti el az olyan szolgáltatásokat, mint az Ambari webes felület vagy a REST API.
    * **SSH-felhasználónév**: A fürt SSH-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Alapértelmezés szerint a jelszó megegyezik a fürt bejelentkezési jelszavával.
    * **Erőforráscsoport**: Az az erőforráscsoport, amelyben a fürt létre lesz hozva.
    * **Hely**: Az az Azure-régió, amelyben a fürt létre lesz hozva.

   ![Előfizetés kiválasztása](./media/apache-storm-tutorial-get-started-linux/hdinsight-basic-configuration.png)

3. Válassza ki a **Fürt típusát**, majd állítsa be a következő értékeket a **Fürtkonfiguráció** szakaszban:

    * **Fürt típusa**: Storm

    * **Operációs rendszer**: Linux

    * **Verzió**: Storm 1.1.0 (HDI 3.6)

   Végül mentse a beállításokat a **Kiválasztás** gomb használatával.

    ![Fürttípus kiválasztása](./media/apache-storm-tutorial-get-started-linux/set-hdinsight-cluster-type.png)

4. A fürt típusának kijelölése után erősítse meg a beállítást a __Kiválasztás__ gombbal. Ezután kattintson a __Tovább__ gombra az alapszintű konfiguráció befejezéséhez.

5. A **Tárolás** szakaszban válasszon ki vagy hozzon létre egy Storage-fiókot. A jelen dokumentumban leírt lépésekben a jelen szakaszban található többi mező alapértelmezett értékét ne módosítsa. Kattintson a __Tovább__ gombra a tárolókonfiguráció mentéséhez. A 2. generációs Data Lake Storage használatáról [a fürtök HDInsightban történő beállításáról szóló rövid útmutatóban](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) talál további információt.

    ![A tárfiók HDInsight-beállításainak konfigurálása](./media/apache-storm-tutorial-get-started-linux/set-hdinsight-storage-account.png)

6. Az **Összegzés** szakaszban tekintse át a fürt konfigurációját. A __Szerkesztés__ hivatkozásai használatával módosítsa a hibás beállításokat. Végül kattintson a __Létrehozás__ gombra a fürt létrehozásához.

    ![A fürtkonfiguráció összegzése](./media/apache-storm-tutorial-get-started-linux/hdinsight-configuration-summary.png)

    > [!NOTE]
    > A fürt létrehozása 20 percig is eltarthat.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Storm Starter-példa futtatása a HDInsightban

1. Csatlakozzon SSH-val a HDInsight-fürthöz:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!TIP]
    > Lehetséges, hogy az SSH-ügyfél szerint a gazdaszámítógép nem hitelesíthető. Ebben az esetben írja be a `yes` parancsot a folytatáshoz.

    > [!NOTE]
    > Ha az SSH-felhasználói fiókhoz jelszót használt, a rendszer felkéri annak megadására. Nyilvános kulcs használatakor lehetséges, hogy az `-i` paraméter használata szükséges a megfelelő titkos kulcs megadásához. Például: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Használja az alábbi parancsot példatopológia indításához:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount

    Ez a parancs elindítja a fürtön a WordCount példatopológiát. Ez a topológia véletlenszerűen állít elő mondatokat, majd az egyes szavak előfordulását számolja meg a mondatokban. A topológia rövid neve: `wordcount`.

    > [!NOTE]
    > A saját topológiák a fürtre történő elküldésekor a fürtöket tartalmazó jar-fájlt a `storm` parancs használata előtt kell másolnia. A fájl másolásához használja az `scp` parancsot. Például: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > A WordCount-példa és más Storm Starter-példák már megtalálhatók a fürtön a következő helyen: `/usr/hdp/current/storm-client/contrib/storm-starter/`.

A Storm Starter-példák forráskódját a következő helyen tekintheti meg: [https://github.com/apache/storm/tree/1.1.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.1.x-branch/examples/storm-starter). A hivatkozás a Storm 1.1.x-es verzió példáira mutat, amely a HDInsight 3.6-ban érhető el. A Storm egyéb verziói esetében használja a __Branch__ (Ág) gombot a lap tetején egy másik Storm-verzió kiválasztásához.

## <a name="monitor-the-topology"></a>A topológia figyelése

A HDInsight-fürtön elérhető Storm webes felhasználói felületet biztosít a futó topológiákkal való munkavégzéshez.

Kövesse az alábbi lépéseket a topológia a Storm felhasználói felületével történő figyeléséhez:

1. A Storm felhasználói felületének megjelenítéséhez egy webböngészőben nyissa meg a `https://CLUSTERNAME.azurehdinsight.net/stormui` oldalt. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.

    > [!NOTE]
    > Ha a rendszer felkéri a felhasználónév és a jelszó megadására, a fürt létrehozásakor használt fürtrendszergazda (rendszergazda) nevét és jelszavát adja meg.

2. A **Topology summary** (Topológia összegzése) területen válassza ki a **wordcount** bejegyzést a **Name** (Név) oszlopban. Megjelennek a topológiával kapcsolatos információk.

    ![A Storm irányítópultja a Storm Starter WordCount-topológiára vonatkozó információkkal.](./media/apache-storm-tutorial-get-started-linux/topology-summary.png)

    Ezen a lapon az alábbi információk találhatóak meg:

    * **Topológiastatisztikák** – Alapszintű információkat tartalmaz a topológia teljesítményével kapcsolatban, időtartományokba rendezve.

        > [!NOTE]
        > Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.

    * **Spoutok** – Alapszintű információkat tartalmaz a spoutokkal kapcsolatban, beleértve az egyes spoutok által visszaadott legutóbbi hibaüzenetet is.

    * **Boltok** – Alapszintű információkat tartalmaz a boltokkal kapcsolatban.

    * **Topológiakonfiguráció** – Részletes információkat tartalmaz a topológia konfigurációjával kapcsolatban.

    Ezen a lapon a következő topológiaműveletek szerepelnek:

    * **Aktiválás** – Folytatja az inaktivált topológia feldolgozását.

    * **Inaktiválás** – Megszakítja a futó topológiát.

    * **Visszaegyensúlyozás** – Beállítja a topológia párhuzamosságát. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Az újraegyensúlyozás beállítja a párhuzamosságot a fürtben található csomópontok számának növekedése/csökkenése kiegyensúlyozása érdekében. További információkért lásd: [ismertetése az Apache Storm-topológia párhuzamosságát](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    * **Törlés** – A Storm-topológia leállítása bizonyos időtúllépést követően.

3. Válassza ki a jelen lapon található **Spoutok** vagy **Boltok** szakaszok bejegyzéseinek egyikét. Ekkor információk jelennek meg a kiválasztott összetevővel kapcsolatban.

    ![A Storm irányítópultja a kiválasztott összetevőkkel kapcsolatos információkkal.](./media/apache-storm-tutorial-get-started-linux/component-summary.png)

    Ezen a lapon az alábbi információk találhatóak meg:

    * **Spout-/Bolt-statisztikák** – Alapszintű információkat tartalmaz az egyes összetevők teljesítményével kapcsolatban, időtartományokba rendezve.

        > [!NOTE]
        > Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.

    * **Beviteli statisztikák** (csak boltok esetében) – Információkat tartalmaz a bolt által feldolgozott adatokat előállító összetevőkről.

    * **Kimeneti statisztikák** – Információkat tartalmaz a bolt által kibocsátott adatokról.

    * **Végrehajtók** – Információkat tartalmaz a jelen összetevő példányairól.

    * **Hibák** – A jelen összetevő által visszaadott hibaüzenetek.

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

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a id="next"></a>Következő lépések

Ebből az Apache Storm-oktatóanyagból megismerhette a Storm HDInsightban való használatának alapjait. Következő lépésként megtanulhatja, hogyan [fejlesztés Java-alapú topológiák az Apache Maven segítségével](apache-storm-develop-java-topology.md).

Ha már ismeri a Java-alapú topológiák fejlesztését, akkor tekintse meg a következő dokumentumot: [HDInsighton futó Apache Storm-topológiák üzembe helyezése és kezelése](apache-storm-deploy-monitor-topology-linux.md).

A.NET-fejlesztők C#- vagy hibrid C#/Java-topológiákat hozhatnak létre a Visual Studio használatával. További információkért lásd: [Develop C# alatt futó Apache Hadoop-eszközök használata a Visual Studióhoz készült HDInsight Apache Storm-topológiák](apache-storm-develop-csharp-visual-studio-topology.md).

A HDInsight alatt futó Stormmal használható példatopológiákat az alábbiakban talál:

* [HDInsight alatt futó Apache Storm példatopológiái](apache-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[preview-portal]: https://portal.azure.com/
