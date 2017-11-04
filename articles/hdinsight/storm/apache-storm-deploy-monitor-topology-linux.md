---
title: "Központi telepítése és kezelése a Linux-alapú HDInsight alatt futó Apache Storm-topológiák |} Microsoft Docs"
description: "Ismerje meg, hogyan telepítheti, figyelheti és kezelheti a Storm irányítópultjának használata a Linux-alapú HDInsight alatt futó Apache Storm-topológiák. Visual Studio Hadoop-eszközök használata."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 35086e62-d6d8-4ccf-8cae-00073464a1e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/07/2017
ms.author: larryfr
ms.openlocfilehash: 338ffaabbc3b47bf31cd90c16ea0f710e7713db6
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-and-manage-apache-storm-topologies-on-hdinsight"></a>Központi telepítése és kezelése a HDInsight alatt futó Apache Storm-topológiák

Ebben a dokumentumban alapismeretei kezelése és figyelése a Storm a HDInsight-fürtökön futó Storm-topológiák.

> [!IMPORTANT]
> A cikkben leírt lépéseket a Linux-alapú Storm on HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement). 
>
> A telepítését és megfigyelését futó Windows-alapú HDInsight-topológiák további információkért lásd: [központi telepítése és kezelése a Windows-alapú HDInsight alatt futó Apache Storm-topológiák](apache-storm-deploy-monitor-topology.md)


## <a name="prerequisites"></a>Előfeltételek

* **A Linux-alapú Storm on HDInsight-fürt**: lásd: [első lépései a HDInsight alatt futó Apache Storm](apache-storm-tutorial-get-started-linux.md) fürt létrehozásának lépései

* (Választható) **SSH és az SCP ismeretét**: további információkért lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Választható) **Visual Studio**: Azure SDK 2.5.1-es vagy újabb és a Data Lake Tools for Visual Studio. További információkért lásd: [Ismerkedés a Data Lake Tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    A Visual Studio következő verzióinak valamelyike:

  * A Visual Studio 2012 [4. frissítés](http://www.microsoft.com/download/details.aspx?id=39305)

  * A Visual Studio 2013-as verziójának [4. frissítés](http://www.microsoft.com/download/details.aspx?id=44921) vagy [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)
  * [A Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * A Visual Studio 2015 (minden kiadás)

  * A Visual Studio 2017 (minden kiadás). A Data Lake Tools for Visual Studio 2017 Azure tárterületet részeként telepített.

## <a name="submit-a-topology-visual-studio"></a>Küldje el a topológia: Visual Studio

A HDInsight Tools elküldeni a C# vagy hibrid topológiák a Storm fürthöz használható. Az alábbi lépéseket a mintaalkalmazás használja. A HDInsight Tools használatával létrehozásával kapcsolatos további információkért lásd: [fejlesztésére C#-topológiák a HDInsight Tools for Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md).

1. Ha még nem telepítette a legújabb verzióját a Data Lake tools for Visual Studio, lásd: [Ismerkedés a Data Lake Tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]
    > A Data Lake Tools for Visual Studio volt korábbi nevén a HDInsight Tools for Visual Studio.
    >
    > A Data Lake Tools for Visual Studio szerepelnek a __Azure munkaterhelés__ a Visual Studio 2017.

2. Nyissa meg a Visual Studio, válassza ki **fájl** > **új** > **projekt**.

3. Az a **új projekt** párbeszédpanelen bontsa ki **telepített** > **sablonok**, majd válassza ki **HDInsight**. Válassza ki a listáról a sablonok **Storm minta**. A párbeszédpanel alján írja be az alkalmazás nevét.

    ![Kép](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **Submit a HDInsight alatt futó Storm**.

   > [!NOTE]
   > Ha a rendszer kéri, adja meg a bejelentkezési hitelesítő adatok az Azure-előfizetéshez. Ha egynél több előfizetéssel rendelkezik, jelentkezzen be, amely tartalmazza a Storm on HDInsight-fürt.

5. Válassza ki a Storm on HDInsight-fürt a a **Storm-fürt** legördülő listából válassza ki, és válassza **Submit**. -E a küldése sikeres használatával figyelheti a **kimeneti** ablak.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Küldje el a topológia: SSH és a Storm parancs

1. Az SSH használata a HDInsight-fürthöz való csatlakozáshoz. Cserélje le **felhasználónév** az SSH-bejelentkezéskor nevét. Cserélje le **CLUSTERNAME** a HDInsight fürt nevű:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    További információ az SSH használatával kapcsolódni HDInsight-fürtjéhez, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Használja az alábbi parancsot példatopológia indításához:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    A paranccsal elindítja a példa WordCount-topológia a fürtön. Ez a topológia véletlenszerűen állít elő, a mondatok, és majd megjeleníti az összes szó a mondatok előfordulását.

   > [!NOTE]
   > A topológia a fürtre történő elküldésekor a fürtöt tartalmazó jar-fájlt a `storm` parancs használata előtt kell másolnia. A fájl átmásolása a fürtre, használja a `scp` parancsot. Például: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > A WordCount-példa és egyéb Storm Starter-példák megtalálhatóak a fürtön a következő helyen: `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Küldje el a topológia: programozott módon

A topológia a Nimbus szolgáltatással programozott módon telepítheti. [https://github.com/Azure-Samples/hdinsight-Java-Deploy-Storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) példával Java-alkalmazás, amely bemutatja, hogyan kell telepíteni és elindítani a topológia a Nimbus szolgáltatáson keresztül.

## <a name="monitor-and-manage-visual-studio"></a>Megfigyelés és kezelés: Visual Studio

Amikor egy topológia a Visual Studio használatával a **Storm-topológiák** nézet jelenik meg. Válassza ki a topológiát a futó topológiát vonatkozó információk megtekintése a listából.

![a Visual studio-figyelő](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]
> Megtekintheti továbbá **Storm-topológiák** a **Server Explorer** kibontásával **Azure** > **HDInsight**, és, majd kattintson a jobb gombbal a Storm on HDInsight-fürt, majd válassza **nézet Storm-topológiák**.

Válassza ki az alakzat a spoutokkal kapcsolatban, vagy a boltokhoz ezeket az összetevőket vonatkozó információk megtekintése. Minden elem kijelölve egy új ablakban nyílik meg.

### <a name="deactivate-and-reactivate"></a>Inaktiválása és újraaktiválása

A topológia inaktiválása felfüggeszti, amíg következtében leállt, vagy újra aktiválni. Ezek a műveletek végrehajtásához használja a __Deactivate__ és __újraaktiválása__ gombok tetején a __topológia összegzése__.

### <a name="rebalance"></a>Visszaegyensúlyozás

A topológia újraelosztás lehetővé teszi, hogy a rendszer a topológia párhuzamosságát ellenőrzéséhez. Például ha a fürt további megjegyzéseket hozzáadni átméretezte, újraelosztás lehetővé teszi, hogy tekintse meg az új csomópontok topológiát.

A topológia egyensúlyba, használja a __Visszaegyensúlyozás__ gomb tetején a __topológia összegzése__.

> [!WARNING]
> A topológia újraelosztás először inaktiválja a topológia munkavállalók egyenletesen újraterjeszti a fürtön, majd végül a újraelosztás történt előtti állapotba tér vissza a topológia. Ezért ha a topológia már aktív volt, akkor ismét aktívvá válik. Inaktiváltuk, ha a leválasztást inaktív.

### <a name="kill-a-topology"></a>A topológia leállítása

Storm-topológiák továbbra is fut le vannak állítva, vagy a fürt törlésekor. A topológia leállításához használja a __Kill__ gomb tetején a __topológia összegzése__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Megfigyelés és kezelés: SSH és a Storm parancs

A `storm` segédprogram lehetővé teszi a futó topológiákkal a parancssorból. Használjon `storm -h` parancsok teljes listáját.

### <a name="list-topologies"></a>Lista topológiák

Használja a következő parancs futtatásával listázza az összes futó topológiákkal:

    storm list

Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Inaktiválása és újraaktiválása

A topológia inaktiválása felfüggeszti, amíg következtében leállt, vagy újra aktiválni. A következő paranccsal inaktiválása és újraaktiválása:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>A futó topológiát leállítása

Storm-topológiák elindításakor, akkor folytassa csak a futó. A topológia leállítása, használja a következő parancsot:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Visszaegyensúlyozás

A topológia újraelosztás lehetővé teszi, hogy a rendszer a topológia párhuzamosságát ellenőrzéséhez. Például ha a fürt további megjegyzéseket hozzáadni átméretezte, újraelosztás lehetővé teszi, hogy tekintse meg az új csomópontok topológiát.

> [!WARNING]
> A topológia újraelosztás először inaktiválja a topológia munkavállalók egyenletesen újraterjeszti a fürtön, majd végül a újraelosztás történt előtti állapotba tér vissza a topológia. Ezért ha a topológia már aktív volt, akkor ismét aktívvá válik. Inaktiváltuk, ha a leválasztást inaktív.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Megfigyelés és kezelés: Storm felhasználói felülete

A HDInsight-fürtön elérhető Storm webes felhasználói felületet biztosít a futó topológiákkal való munkavégzéshez. A Storm felhasználói felülete segítségével tekintheti egy webes böngésző megnyitásához **https://CLUSTERNAME.azurehdinsight.net/stormui**, ahol **CLUSTERNAME** a fürt neve.

> [!NOTE]
> Ha a rendszer felkéri a felhasználónév és a jelszó megadására, a fürt létrehozásakor használt fürtrendszergazda (rendszergazda) nevét és jelszavát adja meg.

### <a name="main-page"></a>Főoldala

A Storm felhasználói felület a fő lapján az alábbi információkat tartalmazza:

* **A fürt összefoglaló**: a Storm-fürt alapvető adatait.
* **Összegző topológia**: futó topológiákat. Ebben a szakaszban a hivatkozások segítségével további információkat szeretne az adott topológiákat.
* **Összegző felügyelő**: a Storm felügyelő kapcsolatos információkat.
* **Nimbus konfigurációs**: Nimbus konfigurációját a fürtben.

### <a name="topology-summary"></a>Topológia összegzése

A hivatkozás kiválasztása a **topológia összegzése** csoportban megjelennek a topológia a következő információkat:

* **Összegző topológia**: a topológia alapvető adatait.
* **Topológia műveletek**: kezelési műveleteket hajthat végre a topológia.

  * **Aktiválása**: folytatása inaktivált topológia feldolgozását.
  * **Inaktiválása**: megszakítja a futó topológiát.
  * **Visszaegyensúlyozás**: Beállítja a topológia párhuzamosságát. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Ez a művelet lehetővé teszi, hogy a topológia párhuzamosságának kiegyensúlyozása érdekében a növelő vagy csökkentő a fürtben található csomópontok számát.

    További információ: <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of a Storm topology</a> (A Storm-topológia párhuzamosságának ismertetése).
  * **Kill**: a Storm-topológia leállítása bizonyos időtúllépést követően.
* **Topológiastatisztikák**: a topológia statisztikája. Az oldalon időkeretének megadására a többieknek beállításához használja a hivatkozások a **ablak** oszlop.
* **Spoutok**: A spoutokkal kapcsolatban, használja a topológiát. Ebben a szakaszban a hivatkozások segítségével megtekintheti az adott spoutokkal kapcsolatban további információt.
* **Boltok**: A boltokhoz a topológia használják. Ebben a szakaszban található hivatkozások segítségével további információkat szeretne az adott boltokhoz.
* **Topológiakonfiguráció**: a kiválasztott topológia beállításától.

### <a name="spout-and-bolt-summary"></a>Spout és Bolt összegzése

Az egy spout kiválasztása a **Spoutok** vagy **boltok** szakaszok megjeleníti a kijelölt elem a következő információkat:

* **Az összetevő összegzés**: a spout vagy bolt alapvető adatait.
* **Spout vagy Bolt statisztikák**: a spout vagy bolt statisztikája. Az oldalon időkeretének megadására a többieknek beállításához használja a hivatkozások a **ablak** oszlop.
* **Beviteli statisztikák** (csak boltok esetében): a bemeneti adatfolyamot tartalmaz a bolt által felhasznált információk.
* **Kimeneti statisztikák**: az adatfolyamokat a spout vagy bolt által kibocsátott kapcsolatos információkat.
* **Végrehajtója**: a spout vagy bolt példányaira vonatkozó információkat. Válassza ki a **Port** egy adott végrehajtó diagnosztikai adatok megtekintését, a bejegyzés erre a példányra.
* **Hibák**: bármely spout vagy bolt hiba információi.

## <a name="monitor-and-manage-rest-api"></a>Megfigyelés és kezelés: REST API-n

A Storm felhasználói felülete a REST API-t épül, ezért hasonló felügyeleti és figyelési funkcióit, a REST API használatával végezheti el. A REST API-t hozhat létre egyéni eszközök felügyelheti és figyelheti a Storm-topológiák.

További információkért lásd: [Storm UI REST API](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Az alábbi információk csak a HDInsight alatt futó Apache Storm a REST API használatával az elő.

> [!IMPORTANT]
> A Storm REST API nincs nyilvánosan elérhető az interneten keresztül, és a HDInsight fürt átjárócsomópontjába az SSH-alagúton keresztül kell elérni. Létrehozásával és az SSH-alagút használatával további információkért lásd: [használata SSH Tunneling Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie és egyéb web UI eléréséhez](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Alap URI

Az alap URI-Azonosítójának a Linux-alapú HDInsight-fürtök REST API érhető el, az átjárócsomópont **https://HEADNODEFQDN:8744/api/v1/**. A tartomány nevét az átjárócsomópont jön létre a fürt létrehozása során, és nincs statikus.

A teljesen minősített tartománynevét (FQDN) találhat az átjárócsomóponthoz meg több különböző módon:

* **Az SSH-munkamenetet**: a parancs `headnode -f` az SSH-munkamenetet a fürthöz.
* **Az Ambari webes**: válasszon **szolgáltatások** a lap tetején, majd válassza ki **Storm**. Az a **összegzés** lapon jelölje be **Storm felhasználói felület Server**. A csomópont a Storm felhasználói felülete és a REST API-t üzemeltető teljes Tartománynevét a lap tetején jelenik meg.
* **Az Ambari REST API**: a parancs `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` a csomópontot, a Storm felhasználói felület és a REST API-n futó vonatkozó információk lekéréséhez. Cserélje le **jelszó** a fürt rendszergazdai jelszóval. Cserélje le **CLUSTERNAME** a fürt nevéhez. A válaszban a "gazdaszámítógép_neve"-bejegyzése tartalmazza a csomópont teljesen minősített Tartománynevét.

### <a name="authentication"></a>Authentication

A REST API-kérésnek kell használnia **az egyszerű hitelesítés**, így a HDInsight fürt rendszergazdája nevet és jelszót használják.

> [!NOTE]
> Egyszerű hitelesítést a rendszer a tiszta szöveges küldi el, mert akkor **mindig** HTTPS protokoll használatával biztonságos kommunikáció a fürtöt.

### <a name="return-values"></a>Visszatérési érték

Információ a REST API visszaadott csak lehet a fürtön belül használható. Visszaadott Zookeeper kiszolgálók teljesen minősített tartománynevét (FQDN) például az internetről nem érhető el.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [Maven használatával fejlesztése Java-alapú topológiák](apache-storm-develop-java-topology.md).

További példa topológiák listájáért lásd: [a HDInsight alatt futó Storm](apache-storm-example-topology.md).
