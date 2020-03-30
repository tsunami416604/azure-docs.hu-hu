---
title: Apache Storm-topológiák üzembe helyezése és kezelése az Azure HDInsightban
description: Ismerje meg, hogyan telepítheti, figyelheti és kezelheti az Apache Storm topológiákat a Storm Dashboard linuxos HDInsight-alapú használatával. Használja a Hadoop eszközöket a Visual Studio-hoz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: e890289230b3215bd102d8c5a78dca4f1b7b90f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271901"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Apache Storm-topológiák üzembe helyezése és kezelése az Azure HDInsightban

Ebben a dokumentumban ismerje meg az Apache Storm-topológiák HDInsight-fürtökön futó Storm-topológiák kezelésének és figyelésének alapjait. [Apache Storm](https://storm.apache.org/)

## <a name="prerequisites"></a>Előfeltételek

* Apache Storm-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon,](../hdinsight-hadoop-create-linux-clusters-portal.md) és válassza a **Storm** for **Cluster típus**lehetőséget.

* (Nem kötelező) A Secure Shell (SSH) és a Secure Copy (SCP) ismerete. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

* (Nem kötelező) Visual Studio, Az Azure SDK 2.5.1 vagy újabb, és a Data Lake Tools for Visual Studio. További információ: [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Topológia beküldése a Visual Studio használatával

A Data Lake Tools for Visual Studio segítségével C# vagy hibrid topológiákat küldhet a Storm-fürtbe. A következő lépések mintaalkalmazást használnak. A Data Lake Tools használatával történő topológia létrehozásáról az [Apache Storm topológiák visual studiókkal és c# témakörben](apache-storm-develop-csharp-visual-studio-topology.md)talál.

1. Ha még nem telepítette a Visual Studio Data Lake eszközeinek legújabb verzióját, olvassa el a [Data Lake Tools for Visual Studio című](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)témakört.

    > [!NOTE]  
    > Az Azure Data Lake és a Stream Analytics Tools korábban hdinsight-eszközök a Visual Studio.
    >
    > Az Azure Data Lake és a Stream Analytics Tools for Visual Studio a Visual Studio 2019 **Azure fejlesztési** munkaterhelése részét képezi.

1. Indítsa el a Visual Studiót.

1. A **Start** ablakban válassza **az Új projekt létrehozása**lehetőséget.

1. Az **Új projekt létrehozása** ablakban jelölje ki `Storm`a keresőmezőt, és írja be a mezőbe. Ezután válassza a **Storm Sample elemet** az eredménylistából, és válassza a **Tovább**gombot.

1. Az **Új projekt konfigurálása** ablakban adja meg a **Projekt nevét**, és lépjen vagy hozzon létre egy **helyet** az új projekt mentéséhez. Ezután válassza **a Létrehozás lehetőséget.**

    ![Az új projektablak, a Visual Studio konfigurálása](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. A **Kiszolgálókezelőből**kattintson a jobb gombbal az **Azure-ra,** és válassza a Csatlakozás a Microsoft **Azure-előfizetéshez lehetőséget,** és fejezze be a bejelentkezési folyamatot.

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, és válassza **a Küldés a viharba parancsot a HDInsight on**.

    > [!NOTE]  
    > Ha a rendszer kéri, adja meg az Azure-előfizetés bejelentkezési hitelesítő adatait. Ha egynél több előfizetéssel rendelkezik, jelentkezzen be abba, amely a Storm a HDInsight-fürtön tartalmazza.

1. A **Topológia küldése** párbeszédpanel **Storm Cluster** legördülő listájában válassza ki a Storm on HDInsight-fürtöt, majd kattintson a **Küldés gombra.** A Kimenet ablaktábla megtekintésével figyelheti, hogy a küldés sikeres **volt-e.**

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Topológia beküldése az SSH és a Storm paranccsal

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az ssh munkamenetből a következő paranccsal indítsa el a **WordCount** példatopológiáját:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Ez a parancs elindítja a fürtön a WordCount példatopológiát. Ez a topológia véletlenszerűen generál mondatokat, majd megszámolja az egyes szavak előfordulását a mondatokban.

    > [!NOTE]  
    > A topológia fürtbe való beküldésekor a `storm` parancs használata előtt először másolja a fürtöt tartalmazó .jar fájlt. A fájl fürtbe másolásához használja `scp` a parancsot. Adja meg például a következőt: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > A *WordCount* példa és más viharindító példák már `/usr/hdp/current/storm-client/contrib/storm-starter/`szerepelnek a fürtön a rendszerben.

## <a name="submit-a-topology-programmatically"></a>Topológia beküldése programozott módon

A Nimbus szolgáltatás használatával programozott módon telepíthet egy topológiát. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)Egy példa Java alkalmazás, amely bemutatja, hogyan telepítheti és indíthat el egy topológia a Nimbus szolgáltatáson keresztül.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Topológia figyelése és kezelése a Visual Studióban

Amikor a Visual Studio használatával küld el egy topológiát, megjelenik a **Storm Topologies View** ablak. Válassza ki a topológiát a listából a futó topológia adatainak megtekintéséhez.

![Monitor topoológia, Storm Topológiák Nézet ablak, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> A Storm **Topológiákat** a **Server Explorer**programból is megtekintheti. Bontsa ki **az Azure** > **HDInsight csomópontot,** kattintson a jobb gombbal egy Storm elemre a HDInsight-fürtön, majd válassza **a Storm-topológiák megtekintése parancsot.**

Jelölje ki a spoutok vagy csavarok alakját az összetevőkre vonatkozó információk megtekintéséhez. Megjelenik egy összetevőadatokat tartalmazó elemleírás a kijelölt elemhez.

### <a name="deactivate-and-reactivate-a-topology"></a>Topológia inaktiválása és újraaktiválása

A topológia inaktiválása szünetelteti, amíg a topológiát el nem ölik vagy újra aktiválják. A műveletek hez használja a **Storm Topologies View** ablak tetején található **Műveletek** terület **Inaktiválása** és **újraaktiválása** gombját.

### <a name="rebalance-a-topology"></a>A topológia egyensúlyának helyreállítása

A topológia kiegyensúlyozása lehetővé teszi, hogy a rendszer felülvizsgálja a topológia párhuzamosságát. Ha például átméretezte a fürtöt további megjegyzések hozzáadásához, az újraegyensúlyozás lehetővé teszi, hogy a topológia láthassa az új csomópontokat.

A topológia újraegyensúlyozásához használja az **Újraegyensúly** gombot a **Storm Topologies View** ablak **Műveletek** területén.

> [!WARNING]  
> A topológia kiegyensúlyozása inaktiválja a topológiát, egyenletesen osztja el a dolgozókat a fürtön, majd visszaadja a topológiát annak az állapotnak, amelyben az újraegyensúlyozás előtt volt. Ha a topológia aktív volt, újra aktívvá válik. Ha a topológiát inaktiválták, akkor továbbra is inaktiválva marad.

### <a name="kill-a-running-topology"></a>Ölj meg egy futó topológia

A storm topológiák mindaddig futnak, amíg le nem állítják őket, vagy a fürtet nem törlik. A topológia leállításához használja a **Műveletek** terület **Ölés** gombját.

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Topológia figyelése és kezelése az SSH és a Storm paranccsal

A `storm` segédprogram lehetővé teszi, hogy a parancssorból futó topológiákkal dolgozzon. A `storm -h` parancsok teljes listájához használható.

### <a name="list-topologies"></a>Topológiák listája

Az alábbi paranccsal listázza az összes futó topológiát:

```shell
storm list
```

Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Topológia inaktiválása és újraaktiválása

A topológia inaktiválása szünetelteti, amíg a topológiát el nem ölik vagy újra aktiválják. Az alábbi parancsokkal inaktiválhatja vagy újraaktiválhatja:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Ölj meg egy futó topológia

Vihar topológiák, ha egyszer elindult, továbbra is fut, amíg megállt. A topológia leállításához használja a következő parancsot:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>A topológia egyensúlyának helyreállítása

A topológia kiegyensúlyozása lehetővé teszi, hogy a rendszer felülvizsgálja a topológia párhuzamosságát. Ha például átméretezte a fürtöt további megjegyzések hozzáadásához, az újraegyensúlyozás lehetővé teszi, hogy a topológia láthassa az új csomópontokat.

> [!WARNING]  
> A topológia kiegyensúlyozása inaktiválja a topológiát, egyenletesen osztja el a dolgozókat a fürtön, majd visszaadja a topológiát annak az állapotnak, amelyben az újraegyensúlyozás előtt volt. Ha a topológia aktív volt, újra aktívvá válik. Ha deaktiválták, akkor továbbra is ki van kapcsolva.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Topológia figyelése és kezelése a Storm felhasználói felületével

A Storm felhasználói felület egy webes felületet biztosít a topológiák futtatásához, és a HDInsight-fürtön is megtalálható. A Storm felhasználói felületének megtekintéséhez egy `https://CLUSTERNAME.azurehdinsight.net/stormui`webböngészővel nyissa meg a böngészőt, ahol a *CLUSTERNAME* a fürt neve.

> [!NOTE]  
> Ha a rendszer felhasználónevet és jelszót kér, adja meg a fürt rendszergazdájának felhasználónevét és jelszavát, amelyet a fürt létrehozásakor használt.

### <a name="storm-ui-main-page"></a>Storm felhasználói felület főoldala

A Storm felhasználói felületének főoldala a következő információkat tartalmazza:

| Section | Leírás |
| --- | --- |
| Fürt összegzése| Alapvető információk a Storm-fürtről. |
| Nimbus összefoglaló | Az alapvető Nimbus-információk listája. |
| Topológia összefoglalása | A futó topológiák listája. Ha egy adott topológiával kapcsolatos további információt szeretne megtekinteni, jelölje ki annak hivatkozását a **Név** oszlopban. |
| Felügyeleti összefoglaló | Információ a Viharfelügyelőről. Ha meg szeretné tekinteni egy adott felügyelőhöz társított dolgozói erőforrásokat, jelölje ki a hivatkozást a **Gazdagép** vagy **az Azonosító** oszlopban. |
| Nimbus konfiguráció | Nimbus konfiguráció a fürthöz. |

A Storm felhasználói felülete a következő weboldalhoz hasonlóan néz ki:

![Főoldal, Storm UI, Apache Storm topológiák, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Topológia összefoglalása

A **Topológia összesítő** szakaszából származó hivatkozás kiválasztása a következő információkat jeleníti meg a topológiával kapcsolatban:

| Section | Leírás |
| --- | --- |
| Topológia összefoglalása | Alapvető információk a topológiáról. |
| Topológia műveletek| A topológiával kapcsolatos felügyeleti műveletek. A rendelkezésre álló műveleteket a szakasz későbbi részében ismertetik. |
| Topológia statisztika | Statisztika a topológiáról. Ha ebben a szakaszban szeretné beállítani egy bejegyzés időkeretét, jelölje ki annak hivatkozását az **Ablak** oszlopban. |
| Spouts *(időkeret)* | A topológia által használt spoutok. Ha egy adott kifolyóval kapcsolatos további információkat szeretne megtekinteni, jelölje ki annak hivatkozását az **Id** oszlopban. |
| Csavarok *(időkeret)* | A retopológia által használt csavarok. Ha egy adott csavarról további információt szeretne megtekinteni, jelölje ki a hivatkozását az **Id** oszlopban. |
| Dolgozói erőforrások | A dolgozói erőforrások listája. Ha egy adott dolgozói erőforrással kapcsolatos további információkat szeretne megtekinteni, jelölje ki annak hivatkozását a **Gazda oszlopban.** |
| Topológia megjelenítés | A **Képimegjelenítések megjelenítése** gomb, amely a topológia vizualizációját jeleníti meg. |
| Topológia konfigurációja | A kijelölt topológia konfigurációja. |

A Storm topológia összefoglaló lapja a következő weboldalhoz hasonlóan néz ki:

![Topológia összefoglaló oldal, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

A **Topológiaműveletek szakaszban** a következő gombokat választhatja ki egy művelet hez:

| Gomb | Leírás |
| --- | --- |
| Aktiválás | Folytatja az inaktivált topológia feldolgozását. |
| Inaktiválás | Szünetelteti a futó topológiát. |
| Egyensúly helyreállítása | Beállítja a topológia párhuzamosságát. A futó topológiák egyensúlyba hozása a fürtben lévő csomópontok számának módosítása után. Ez a művelet lehetővé teszi, hogy a topológia a párhuzamosság beállításához kompenzálja a fürt további vagy csökkentett számú csomópontját.<br/><br/>További információ: <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Az Apache Storm topológia párhuzamosságának megértése</a>című témakörben talál.
| Megölni | A storm-topológia leállítása a megadott időhosszabbítás után. |
| Hibakeresés | Elindítja a futó topológiához való hibakeresési munkamenetet. |
| Hibakeresés leállítása | Befejezi a futó topológia hibakeresési munkamenetét. |
| Naplószint módosítása | Módosítja a hibakeresési napló szintjét. |

##### <a name="spout-and-bolt-summary"></a>Kifolyó és csavar összegzése

Ha kijelöl egy **spoutot a Spouts** vagy a **Bolts** szakaszból, a következő információkat jeleníti meg a kijelölt elemről:

| Section | Leírás |
| --- | --- |
| Összetevő összegzése | Alapvető információk a kifolyóról vagy a csavarról. |
| Összetevő-műveletek | **Hibakeresés** és **A hibakeresés leállítása** gombokkal. |
| Spout statisztika vagy bolt statisztika | Statisztika a kifolyóról vagy a csavarról. Ha ebben a szakaszban szeretné beállítani egy bejegyzés időkeretét, jelölje ki annak hivatkozását az **Ablak** oszlopban. |
| (Csak bolt)<br/>Bemeneti statisztika *(időkeret)* | Információ a bolt által felhasznált bemeneti adatfolyamokról. |
| Kimeneti statisztikák *(időkeret)* | A kifolyó vagy a csavar által kibocsátott adatfolyamokról szóló információk. |
| Profilkészítés és hibakeresés | Az ezen a lapon található összetevők profilkészítésének és hibakeresésének vezérlői. Beállíthatja az **Állapot / Időtúloldal (Perc)** értéket, és kiválaszthatja a **JStack,** **a Worker újraindítása**és a **halom**gombokat. |
| Végrehajtók *(időkeret)* | Információ a kifolyó vagy a csavar példányairól. A példányhoz készített diagnosztikai adatok naplójának **Port** megtekintéséhez válassza ki egy adott végrehajtó portbejegyzését. Az adott végrehajtóhoz társított dolgozói erőforrásokat is megtekintheti, ha kiválasztja annak hivatkozását a **Gazda oszlopban.** |
| Hibák | A kifolyóval vagy a csavarral kapcsolatos hibainformációk. |

A Storm bolt összefoglaló oldal hasonlít erre a weboldalra:

![Bolt összefoglaló oldal, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>A topológia figyelése és kezelése a REST API használatával

A Storm felhasználói felülete a REST API-ra épül, így hasonló felügyeleti és figyelési feladatokat is elérhet a REST API használatával. A REST API segítségével egyéni eszközöket hozhat létre a Storm-topológiák kezeléséhez és figyeléséhez.

További információ: [Apache Storm UI REST API.](https://storm.apache.org/releases/current/STORM-UI-REST-API.html) Az alábbi információk a REST API-t a HDInsight-on az Apache Storm használatával kapcsolatosak.

> [!IMPORTANT]  
> A Storm REST API nem érhető el nyilvánosan az interneten keresztül. SSH-alagúttal kell elérni a HDInsight fürtfőcsomóponthoz. Az SSH-alagutak létrehozásáról és használatáról az [SSH-bújtatás használata az Azure HDInsight eléréséhez](../hdinsight-linux-ambari-ssh-tunnel.md)című témakörben talál további információt.

### <a name="base-uri"></a>Alap URI

A LINUX-alapú HDInsight-fürtök REST API-jának alap `https://HEADNODEFQDN:8744/api/v1/`URI-ja az URL-címen érhető el, ahol a *HEADNODEFQDN-t* a főcsomópontra cseréli. A fő csomópont tartományneve a fürt létrehozása során jön létre, és nem statikus.

A fürtfőcsomópont teljesen minősített tartománynevét (FQDN) többféleképpen is megtalálhatja:

| FQDN felderítési módszer | Leírás |
| --- | --- |
| SSH-munkamenet | Használja a `headnode -f` parancsot egy SSH-munkamenetből a fürtbe. |
| Ambari Web | Az Ambari cluster weblapon (`https://CLUSTERNAME.azurehdinsight.net`), válassza a lap tetején a **Szolgáltatások** elemet, majd a **Storm**lehetőséget. Az **Összegzés** lapon válassza a **Storm UI Server lehetőséget.** A Storm UI és REST API-t tartalmazó csomópont teljes tartományneve a lap tetején jelenik meg. |
| Ambari REST API | A paranccsal `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` információt kérhet a storm felhasználói felületés a REST API-ról. Cserélje le a *CLUSTERNAME* két példányát a fürtnevére. Amikor a rendszer kéri, adja meg a felhasználói (rendszergazdai) fiók jelszavát. A válaszban a JSON-kimenet "host_name" bejegyzése tartalmazza a csomópont teljes tartománynát. |

### <a name="authentication"></a>Hitelesítés

A REST API-nak érkező kérelmeknek *alapfokú hitelesítést*kell használniuk, ezért a HDInsight-fürt rendszergazdai nevét és jelszavát kell használnia.

> [!NOTE]  
> Mivel az alapfokú hitelesítés küldése titkosítatás, a fürttel folytatott kommunikáció biztonságossá tétele *érdekében mindig* HTTPS protokollt kell használnia.

### <a name="return-values"></a>Visszaadott értékek

A REST API-ból visszaadott információk csak a fürtből használhatók. Például a teljesen minősített tartománynév (FQDN) vissza [adott Apache ZooKeeper](https://zookeeper.apache.org/) kiszolgálók nem érhető el az internetről.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [fejleszthet Java-alapú topológiákat az Apache Maven használatával.](apache-storm-develop-java-topology.md)

További példatopológiák listáját az [Azure HDInsight Példa Apache Storm-topológiák című témaköre tartalmazza.](apache-storm-example-topology.md)
