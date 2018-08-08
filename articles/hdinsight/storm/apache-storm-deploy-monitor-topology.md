---
title: Üzembe helyezés és kezelés az Azure HDInsight az Apache Storm-topológiák
description: Ismerje meg, hogyan helyezheti üzembe, figyelheti és kezelheti a Storm irányítópultjának használata a HDInsight Windows-alapú Apache Storm-topológiák. Hadoop tools for Visual Studio használata.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 1ad1d6662d276039ae1e01e49c60fc06682cb54a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622816"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Üzembe helyezés és kezelés a Windows-alapú HDInsight Apache Storm-topológiák

A Storm irányítópultjának lehetővé teszi egyszerű üzembe helyezését, és futtassa az Apache Storm-topológiák a HDInsight-fürthöz a webböngésző használatával. Az irányítópult használatával figyelheti és kezelheti a futó topológiákat. Ha a Visual Studio használata esetén a HDInsight Tools for Visual Studio hasonló funkciók a Visual Studióban adjon meg.

A Storm irányítópultjának és a Storm-szolgáltatások, a HDInsight Tools támaszkodik a Storm REST API, amely segítségével létrehozhatja a saját figyelési és felügyeleti megoldásokat.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépésekben a Storm on HDInsight-fürt által használt Windows operációs rendszer szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Üzembe helyezéséről és felügyeletéről egy HDInsight-fürt által használt Linux Storm-topológiák további információkért lásd: [üzembe helyezése és kezelése a HDInsight Linux-alapú Apache Storm-topológiák](apache-storm-deploy-monitor-topology-linux.md)

## <a name="prerequisites"></a>Előfeltételek

* **Az Apache Storm on HDInsight** – lásd: [Apache Storm on HDInsight – első lépések](apache-storm-tutorial-get-started-linux.md) fürt létrehozásának lépéseit.

* Az a **a Storm irányítópultja**: modern böngésző támogatja a HTML5-alapú.

* A **Visual Studio** – Azure SDK 2.5.1-es vagy újabb verzió és a HDInsight Tools for Visual Studio. Lásd: [első lépései a HDInsight Tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) telepítése és konfigurálása a HDInsight tools for Visual Studio.

    A Visual Studio következő verzióinak valamelyike:

  * A Visual Studio 2012 Update 4

  * A Visual Studio 2013 4. frissítéssel vagy Visual Studio 2013 Community

  * A Visual Studio 2015 (bármely kiadás esetén)

  * A Visual Studio 2017 (bármely kiadás esetén)

## <a name="storm-dashboard"></a>A Storm irányítópultja

A Storm irányítópultja a Storm-fürtön elérhető weblap. Az URL-cím **https://&lt;clustername >.azurehdinsight.net/**, ahol **clustername** a Storm on HDInsight-fürt neve.

A Storm irányítópultjának tetején válassza **elküldése topológia**. Kövesse az utasításokat a minta topológia vagy a feltöltés és a egy Ön által létrehozott topológia lapon.

![a Küldés topológia lap][storm-dashboard-submit]

### <a name="storm-ui"></a>A Storm felhasználói felület

A Storm irányítópultjának, válassza ki a **Storm felhasználói felülete** hivatkozásra. Ez a fürt minden futó topológiák mellett információit jeleníti meg.

![a storm felhasználói felülete][storm-dashboard-ui]

> [!NOTE]
> Egyes verziói az Internet Explorer azt tapasztalhatja, hogy a Storm felhasználói felülete nem frissül után először járt ott. Például akkor lehet, hogy nem jelennek meg, az új topológiák küldte el, valószínűleg egy topológia aktívnak, ha korábban már inaktiválva. A Microsoft a probléma és megoldás dolgozik.

#### <a name="main-page"></a>Fő lapján

A fő lapján, a Storm felhasználói felülete a következő információkat biztosítja:

* **Fürt összegzése**: a Storm-fürt alapvető adatait.

* **Összefoglaló topológia**: futó topológiák listáját. Ebben a szakaszban a hivatkozások segítségével megtekintheti az adott topológiák további információt.

* **Összefoglaló felügyelő**: a Storm felügyelő kapcsolatos információkat.

* **Nimbus-konfiguráció**: Nimbus a fürt konfigurációját.

#### <a name="topology-summary"></a>A topológia összegzése

A hivatkozás kiválasztása a **topológia összegzése** szakasz jelenít meg a topológia a következő információkat:

* **Összefoglaló topológia**: alapszintű, a topológiával kapcsolatos információk.

* **Topológia műveletek**: felügyeleti műveleteket hajthat végre a topológia.

  * **Aktiválja**: folytatja inaktivált topológia feldolgozását.

  * **Inaktiválás**: megszakítja a futó topológiát.

  * **Újraegyensúlyozására**: Beállítja a topológia párhuzamosságát. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Ez lehetővé teszi a topológia párhuzamosságának kompenzálják a fürtben található csomópontok száma nagyobb vagy csökkent.

      További információkért lásd: [Storm-topológia párhuzamosságát ismertetése (http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

  * **Kill**: leállítja a Storm-topológia időtúllépést követően.

* **Topológiastatisztikák**: a topológia statisztikája. Hivatkozásai között találhat a **ablak** oszlop beállítása a többieknek időkeretét az oldalon.

* **Spoutok**: A spoutok által a topológia használt. Ebben a szakaszban a hivatkozások segítségével megtekintheti az adott tartalmaz a spoutokkal kapcsolatban további információt.

* **Boltok**: használja a topológia a boltokhoz. Ebben a szakaszban a hivatkozások segítségével megtekintheti az adott tartalmaz a boltokkal kapcsolatban további információt.

* **Topológiakonfiguráció**: A konfiguráció a kiválasztott topológia.

#### <a name="spout-and-bolt-summary"></a>Spout és Bolt összegzése

Egy a spout kiválasztása a **Spoutok** vagy **boltok** szakaszok megjeleníti a kiválasztott elem a következő információkat:

* **Összetevő összefoglalás**: a spout vagy bolt alapvető adatait.

* **Spout vagy Bolt-statisztikák**: a spout vagy bolt statisztikája. Hivatkozásai között találhat a **ablak** oszlop beállítása a többieknek időkeretét az oldalon.

* **Beviteli statisztikák** (csak boltok esetében): a bemeneti streamekhez a bolt által felhasznált információk.

* **Kimeneti statisztikák**: hatással a streameket információ spout vagy bolt.

* **Végrehajtóval**: a példányok a spout vagy bolt kapcsolatos információkat. Válassza ki a **Port** diagnosztikai adatok megtekintését, egy adott végrehajtó bejegyzés előállítása a ezen a példányon.

* **Hibák**: bármilyen hiba adatokat a spout vagy bolt.

## <a name="hdinsight-tools-for-visual-studio"></a>HDInsight Tools for Visual Studio

A HDInsight Tools használható a C# vagy hibrid topológiák a Storm fürthöz való elküldéséhez. Az alábbi lépéseket egy minta alkalmazást kell használni. HDInsight-eszközök segítségével a saját topológiák létrehozásával kapcsolatos információkért lásd: [C#-topológiák fejlesztése a HDInsight Tools for Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md).

Az alábbi lépések segítségével üzembe helyezhet egy mintát, a Storm on HDInsight-fürtöt, majd tekintheti meg és kezelheti a topológia.

1. Ha Ön még nem telepítette a legújabb verzióját a HDInsight Tools for Visual Studio, [első lépései a HDInsight Tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Nyissa meg a Visual Studióban, válassza ki **fájl** > **új** > **projekt**.

3. Az a **új projekt** párbeszédpanelen bontsa ki **telepített** > **sablonok**, majd válassza ki **HDInsight**. A sablonok listájából válassza **Storm minta**. A párbeszédpanel alján írja be az alkalmazás nevét.

    ![image](./media/apache-storm-deploy-monitor-topology/sample.png)

4. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, és válassza ki **Küldés a HDInsight alatt futó Stormmal**.

   > [!NOTE]
   > Ha a rendszer kéri, adja meg a bejelentkezési hitelesítő adatokat az Azure-előfizetésében. Ha több előfizetéssel rendelkezik, jelentkezzen be a Storm on HDInsight-fürt tartalmazza.

5. Válassza ki a Storm, a HDInsight-fürtön a **Storm-fürt** legördülő listából válassza ki, és válassza ki **küldés**. -E a küldés sikeres használatával figyelheti a **kimeneti** ablak.

6. A topológia sikeresen el lett küldve, amikor a **Storm-topológiák** meg kell jelennie a fürt számára. Válassza ki a topológiát a működő topológia kapcsolatos információk megtekintéséhez a listából.

    ![a Visual studio-figyelő](./media/apache-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > Megtekintheti továbbá **Storm-topológiák** a **Server Explorer** kibontásával **Azure** > **HDInsight**, majd Kattintson a jobb gombbal a Storm on HDInsight-fürtöt, majd válassza ki **Zobrazit Topologie Stormu**.

    Válassza ki az alakzatot a spoutok vagy boltok ezeket az összetevőket kapcsolatos információk megtekintéséhez. Egy új ablakban nyílik meg minden kijelölt elemek.

   > [!NOTE]
   > A topológia név az osztály nevét a topológia (ebben az esetben `HelloWord`,) fűzött időbélyeggel kezdődnek.

7. Az a **topológia összegzése** nézetben válassza **Kill** a topológia leállítása.

   > [!NOTE]
   > Storm-topológiák továbbra is fut, le vannak állítva, vagy a fürt törlésekor.


## <a name="rest-api"></a>REST API

A Storm felhasználói felülete a REST API-t épül, így hasonló felügyeleti és figyelési funkcióit, a REST API használatával is elvégezheti. A REST API használatával hozhat létre egyéni kezeléséhez és monitorozásához Storm-topológiák.

További információkért lásd: [Storm UI REST API](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). Az alábbi adatokat csak a REST API-val, az Apache Storm on HDInsight.

### <a name="base-uri"></a>Alap URI-t

Az alap URI a REST API-hoz a HDInsight-fürtökön **https://&lt;clustername >.azurehdinsight.net/stormui/api/v1/**, ahol **clustername** neve, a HDInsight alatt futó Stormmal a fürt.

### <a name="authentication"></a>Hitelesítés

A REST API-kérésnek kell használnia **alapszintű hitelesítés**, így a HDInsight fürt rendszergazdai felhasználónevét és jelszavát használja.

> [!NOTE]
> Alapszintű hitelesítést a rendszer tiszta szöveges használatával küldi el, mert kell **mindig** HTTPS használatával biztonságos kommunikáció a fürtöt.

### <a name="return-values"></a>Visszatérési értékek

Lehet, hogy a REST API-tól visszaadott információk csak a fürt vagy a virtuális gépeket, a fürt azonos Azure virtuális hálózaton belül használható. Ha például a teljesen minősített tartománynevét (FQDN) adja vissza a rendszer a Zookeeper-kiszolgálók nem lehet elérhető az internetről.

## <a name="next-steps"></a>További lépések

Most, hogy bemutattuk, hogyan helyezhet üzembe és topológiák figyelheti a Storm irányítópultjának használatával, megtudhatja, hogyan lehet:

* [A HDInsight Tools for Visual Studio használatával C# topológiák fejlesztése](apache-storm-develop-csharp-visual-studio-topology.md)

* [Maven használata Java-alapú topológiák fejlesztése](apache-storm-develop-java-topology.md)

További példa topológiák listáját lásd: [a HDInsight alatt futó Storm példatopológiái](apache-storm-example-topology.md).

[hdinsight-dashboard]: ./media/apache-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/apache-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/apache-storm-deploy-monitor-topology/storm-ui-summary.png
