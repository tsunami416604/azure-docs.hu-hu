---
title: Apache Storm-topológiák üzembe helyezése és kezelése az Azure HDInsight
description: Megtudhatja, hogyan helyezhet üzembe, figyelheti és felügyelheti Apache Storm topológiákat a Linux-alapú HDInsight található Storm irányítópult használatával. A Visual studióhoz készült Hadoop Tools használata.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 1c219c85836eb4730fa90918385555c433a12449
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915102"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Apache Storm-topológiák üzembe helyezése és kezelése az Azure HDInsight 

Ebből a dokumentumból megtudhatja, hogyan kezelheti és figyelheti [Apache Storm](https://storm.apache.org/) a Storm on HDInsight-fürtökön futó topológiákat.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Storm-fürt a HDInsight-on. Lásd: [hozzon létre Apache Hadoop fürtöket a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) , és válassza a **Storm** a **fürt típusa**lehetőséget.


* Választható Az SSH és az SCP ismerete: További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Választható Visual Studio: Az Azure SDK 2.5.1-es vagy újabb verziója, valamint a Visual studióhoz készült Data Lake Tools. További információ: a [Data Lake Tools for Visual Studio használatának első lépései](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    A Visual Studio következő verzióinak egyike:

  * Visual Studio 2012 a 4. frissítéssel

  * Visual Studio 2013 a 4. frissítéssel vagy a [Visual studio 2013 Közösséggel](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (bármely kiadás)

  * Visual Studio 2017 (bármely kiadás). A Visual Studio 2017 Data Lake eszközei az Azure munkaterhelés részeként települnek.

## <a name="submit-a-topology-visual-studio"></a>Topológia elküldése: Visual Studio

A HDInsight-eszközök segítségével elküldheti C# vagy hibrid topológiákat a Storm-fürthöz. A következő lépések egy minta alkalmazást használnak. A HDInsight-eszközök használatával történő létrehozásával kapcsolatos információkért lásd [: C# topológiák fejlesztése a HDInsight Tools for Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md).

1. Ha még nem telepítette a Visual studióhoz készült Data Lake Tools legújabb verzióját, tekintse meg a [Data Lake Tools for Visual Studio használatának első lépéseit](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)ismertető témakört.

    > [!NOTE]  
    > A Visual studióhoz készült Data Lake-eszközöket korábban a Visual studióhoz készült HDInsight-eszközök hívták.
    >
    > A Visual studióhoz készült Data Lake Tools a Visual Studio 2017 __Azure__ -beli számítási feladatának részét képezi.

2. Nyissa meg a Visual studiót, válassza a **fájl** > **új** > **projekt**lehetőséget.

3. Az **új projekt** párbeszédpanelen bontsa ki a **telepített** > **sablonok**csomópontot, majd válassza a **HDInsight**lehetőséget. A sablonok listájában válassza a **Storm Sample**elemet. A párbeszédpanel alján adja meg az alkalmazás nevét.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

4. **Megoldáskezelő**kattintson a jobb gombbal a projektre, és válassza a **Küldés a Storm on HDInsight**lehetőséget.

   > [!NOTE]  
   > Ha a rendszer kéri, adja meg az Azure-előfizetéséhez tartozó bejelentkezési hitelesítő adatokat. Ha egynél több előfizetéssel rendelkezik, jelentkezzen be a HDInsight-fürtön található Stormot tartalmazóba.

5. Válassza ki a Storm on HDInsight-fürtöt a **Storm-fürt** legördülő listából, majd válassza a **Küldés**lehetőséget. A **kimeneti** ablak segítségével figyelheti, hogy a Küldés sikeres volt-e.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Topológia elküldése: SSH és a Storm parancs

1. Az SSH használatával csatlakozzon a HDInsight-fürthöz. A **Felhasználónév** helyére írja be az ssh-bejelentkezés nevét. Cserélje le a **CLUSTERNAME** -t a HDInsight-fürt nevére:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Az SSH HDInsight-fürthöz való kapcsolódásával kapcsolatos további információkért lásd: az [SSH használata a HDInsight használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Használja az alábbi parancsot példatopológia indításához:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Ez a parancs elindítja a fürtön a WordCount példatopológiát. Ez a topológia véletlenszerűen generál mondatokat, majd megszámolja az egyes szavak előfordulását a mondatokban.

   > [!NOTE]  
   > A topológia a fürtre történő elküldésekor a fürtöt tartalmazó jar-fájlt a `storm` parancs használata előtt kell másolnia. A fájlnak a fürtbe való másolásához használhatja az `scp` parancsot. Például: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > A WordCount-példa és egyéb Storm Starter-példák megtalálhatóak a fürtön a következő helyen: `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Topológia beküldése: programozott módon

A Nimbus szolgáltatás használatával programozott módon helyezhet üzembe egy topológiát. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)egy példás Java-alkalmazást biztosít, amely bemutatja, hogyan helyezhet üzembe és indíthat el topológiát a Nimbus szolgáltatáson keresztül.

## <a name="monitor-and-manage-visual-studio"></a>Figyelés és kezelés: Visual Studio

Amikor a rendszer a Visual Studióval küldi el a topológiát, megjelenik a **Storm-topológiák** nézet. Válassza ki a topológiát a listából a futó topológiával kapcsolatos információk megtekintéséhez.

![Visual Studio-figyelő](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> A **Storm-topológiákat** a **Server Explorerben** is megtekintheti **Az Azure** > -**HDInsight**kibontásával, majd a jobb gombbal a HDInsight-fürtön található Storm elemre kattintva, majd a **Storm-topológiák megtekintése**lehetőség kiválasztásával.

Válassza ki a kiöntő vagy a bolt alakzatát, és tekintse meg az ezen összetevőkre vonatkozó információkat. Megnyílik egy új ablak minden kijelölt elemmel.

### <a name="deactivate-and-reactivate"></a>Inaktiválás és újraaktiválás

A topológia inaktiválása szünetelteti a felfüggesztést, amíg a rendszer nem távolítja el vagy nem aktiválja újra. Ezeknek a műveleteknek a végrehajtásához használja a __topológia összefoglalásának__tetején található __inaktiválás__ és __újraaktiválás__ gombokat.

### <a name="rebalance"></a>Újra egyensúlyba hozni

A topológia újraelosztása lehetővé teszi a rendszer számára a topológia párhuzamosságának módosítását. Ha például úgy méretezi át a fürtöt, hogy további megjegyzéseket adjon hozzá, a kiegyenlítő lehetővé teszi, hogy az új csomópontok láthassák a topológiát.

A topológia újraelosztásához használja a __topológia összegzésének__felső részén található __újraelosztás__ gombot.

> [!WARNING]  
> A topológia újraelosztása először inaktiválja a topológiát, majd egyenletesen osztja el a feldolgozókat a fürtön, majd végül visszaadja a topológiát arra az állapotra, amely az újrakiegyensúlyozás előtt volt. Tehát ha a topológia aktív volt, akkor ismét aktívvá válik. Ha inaktiválva van, inaktiválva marad.

### <a name="kill-a-topology"></a>Topológia leölése

A Storm-topológiák addig futnak, amíg le nem állnak, vagy a fürt törlődik. A topológia leállításához használja a __topológia összegzésének__tetején található __kill (ölés__ ) gombot.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Figyelés és kezelés: SSH és a Storm parancs

A `storm` segédprogram lehetővé teszi, hogy a parancssorból futtassa a futó topológiákat. A `storm -h` parancsok teljes listáját használja.

### <a name="list-topologies"></a>Topológiák listázása

A következő parancs használatával listázhatja az összes futó topológiát:

    storm list

Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Inaktiválás és újraaktiválás

A topológia inaktiválása szünetelteti a felfüggesztést, amíg a rendszer nem távolítja el vagy nem aktiválja újra. Az alábbi paranccsal inaktiválhatja és újraaktiválhatja a következő parancsot:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Futó topológia leölése

Storm-topológiák – az indítás után folytassa a futtatást a Leállításig. A topológia leállításához használja a következő parancsot:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Újra egyensúlyba hozni

A topológia újraelosztása lehetővé teszi a rendszer számára a topológia párhuzamosságának módosítását. Ha például úgy méretezi át a fürtöt, hogy további megjegyzéseket adjon hozzá, a kiegyenlítő lehetővé teszi, hogy az új csomópontok láthassák a topológiát.

> [!WARNING]  
> A topológia újraelosztása először inaktiválja a topológiát, majd egyenletesen osztja el a feldolgozókat a fürtön, majd végül visszaadja a topológiát arra az állapotra, amely az újrakiegyensúlyozás előtt volt. Tehát ha a topológia aktív volt, akkor ismét aktívvá válik. Ha inaktiválva van, inaktiválva marad.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Figyelés és kezelés: Storm felhasználói felület

A HDInsight-fürtön elérhető Storm webes felhasználói felületet biztosít a futó topológiákkal való munkavégzéshez. A Storm felhasználói felületének megtekintéséhez nyisson meg **https://CLUSTERNAME.azurehdinsight.net/stormui** egy böngészőt, ahol a **CLUSTERNAME** a fürt neve.

> [!NOTE]  
> Ha a rendszer felkéri a felhasználónév és a jelszó megadására, a fürt létrehozásakor használt fürtrendszergazda (rendszergazda) nevét és jelszavát adja meg.

### <a name="main-page"></a>Kezdőlap

A Storm felhasználói felületének fő lapja a következő információkat tartalmazza:

* **Fürt összegzése**: Alapszintű információk a Storm-fürtről.
* **Topológia összegzése**: A futó topológiák listája. Az ebben a szakaszban található hivatkozásokra kattintva további információkat jeleníthet meg az adott topológiákkal kapcsolatban.
* **Felügyelői összefoglalás**: A Storm felettesének információi.
* **Nimbus-konfiguráció**: Nimbus-konfiguráció a fürthöz.

### <a name="topology-summary"></a>Topológia összegzése

A **topológia összegzése** szakasz egy hivatkozásának kiválasztásával az alábbi információk jelennek meg a topológiával kapcsolatban:

* **Topológia összegzése**: A topológiával kapcsolatos alapvető információk.
* **Topológiai műveletek**: A topológia számára végrehajtható felügyeleti műveletek.

  * **Aktiválás**: Folytatja a deaktivált topológia feldolgozását.
  * **Inaktiválás**: Szünetelteti a futó topológiát.
  * **Újraelosztás**: A topológia párhuzamosságának módosítása. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Ez a művelet lehetővé teszi, hogy a topológia a párhuzamosságot a fürtben lévő csomópontok megnövekedett vagy csökkenő számának kompenzálására kényszerítse.

    További információ: <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Apache Storm topológia párhuzamosságának megismerése</a>.
  * **Kill**: A megadott időkorlát után leállítja a Storm-topológiát.
* **Topológiai statisztikák**: A topológiával kapcsolatos statisztikák. Az oldalon található többi bejegyzés időkeretének megadásához használja az **ablak** oszlopban található hivatkozásokat.
* **Kiöntő**: A topológia által használt kiöntők. Az ebben a szakaszban található hivatkozásokra kattintva további információkat jeleníthet meg a kiöntő szolgáltatásról.
* **Csavarok**: A topológia által használt csavarok. Az ebben a szakaszban található hivatkozásokra kattintva további információkat jeleníthet meg az adott csavarokról.
* **Topológia konfigurációja**: A kiválasztott topológia konfigurációja.

### <a name="spout-and-bolt-summary"></a>Kiöntő és bolt összefoglalása

**A kiöntő vagy a** **boltokból** származó kiöntő kiválasztásakor az alábbi információk jelennek meg a kijelölt elemről:

* **Összetevő összegzése**: Alapszintű információk a kiöntő vagy a boltról.
* **Kiöntő/bolt statisztikái**: A kiöntő vagy a bolt statisztikája. Az oldalon található többi bejegyzés időkeretének megadásához használja az **ablak** oszlopban található hivatkozásokat.
* **Bemeneti statisztika** (csak bolt): A bolt által felhasznált bemeneti adatfolyamokra vonatkozó információk.
* **Kimeneti statisztika**: Információk a kiöntő vagy a bolt által kibocsátott adatfolyamokról.
* **Végrehajtók**: Információk a kiöntő vagy a bolt példányairól. Egy adott végrehajtó **portjának** kiválasztásával megtekintheti az ehhez a példányhoz létrehozott diagnosztikai információk naplóját.
* **Hibák**: A kiöntő vagy a boltra vonatkozó bármilyen hiba.

## <a name="monitor-and-manage-rest-api"></a>Figyelés és kezelés: REST API

A Storm felhasználói felülete a REST APIra épül, így a REST API segítségével hasonló felügyeleti és figyelési funkciókat is elvégezhet. A REST API használatával egyéni eszközöket hozhat létre a Storm-topológiák kezeléséhez és figyeléséhez.

További információ: [Apache Storm felhasználói felület REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Az alábbi információk a REST API Apache Storm HDInsight való használatára vonatkoznak.

> [!IMPORTANT]  
> A Storm REST API nem nyilvánosan elérhető az interneten keresztül, és SSH-alagúton keresztül kell elérnie a HDInsight-fürt fő csomópontja számára. Az SSH-alagutak létrehozásával és használatával kapcsolatos információkért lásd: az [SSH-bújtatás használata az Apache Ambari webes felhasználói felület, a erőforráskezelő, a JobHistory, a NameNode, az Apache Oozie és más webes felület eléréséhez](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Alap URI

A Linux-alapú HDInsight-fürtökön a REST API alap URI-ja elérhető a következő helyen: **https:\//HEADNODEFQDN: 8744/API/v1/** . A fő csomópont tartományneve a fürt létrehozása során jön létre, és nem statikus.

A fürt fő csomópontjának teljes tartományneve (FQDN) több különböző módon is megkereshető:

* **Egy SSH-munkamenetből**: Használja a parancsot `headnode -f` egy SSH-munkamenetből a fürtre.
* **A Ambari web**: Válassza a **szolgáltatások** lehetőséget az oldal tetején, majd válassza a **Storm**lehetőséget. Az **Összefoglalás** lapon válassza a **Storm UI-kiszolgáló**lehetőséget. A Storm felhasználói felületet futtató csomópont teljes tartományneve és REST API a lap tetején jelenik meg.
* **A Ambari REST API**: A paranccsal `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` információkat kérhet le arról a csomópontról, amelyen a Storm kezelőfelülete és REST API fut. Cserélje le az **CLUSTERNAME** -t a fürt nevére. Ha a rendszer kéri, adja meg a bejelentkezési (rendszergazdai) fiók jelszavát. A válaszban a "host_name" bejegyzés tartalmazza a csomópont teljes tartománynevét.

### <a name="authentication"></a>Authentication

A REST APIra irányuló kérelmeknek **alapszintű hitelesítést**kell használniuk, ezért a HDInsight-fürt rendszergazdájának nevét és jelszavát kell használnia.

> [!NOTE]  
> Mivel az egyszerű hitelesítés titkosítatlan szöveggel van ellátva, **mindig** a HTTPS protokollt használja a fürttel folytatott kommunikáció biztonságossá tételéhez.

### <a name="return-values"></a>Visszatérési értékek

A REST API által visszaadott adatok csak a fürtön belül használhatók. A [Apache ZooKeeper](https://zookeeper.apache.org/) -kiszolgálók által visszaadott teljes tartománynév (FQDN) például nem érhető el az internetről.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [fejleszthet Java-alapú topológiákat az Apache Maven használatával](apache-storm-develop-java-topology.md).

A több példás topológiák listáját itt tekintheti [meg: példa topológiák Apache Storm a HDInsight](apache-storm-example-topology.md).
