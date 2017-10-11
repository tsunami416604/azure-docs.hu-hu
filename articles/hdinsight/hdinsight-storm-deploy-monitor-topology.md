---
title: "Központi telepítése és kezelése a HDInsight alatt futó Apache Storm-topológiák |} Microsoft Docs"
description: "Ismerje meg, hogyan telepítheti, figyelheti és kezelheti a Storm irányítópultjának használata a HDInsight alatt futó Apache Storm-topológiák. Visual Studio Hadoop-eszközök használata."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5e542072-f014-42aa-82d6-2694a76df520
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 34072574f83b51280e60e2f8766c6c5d5a33c307
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Központi telepítése és kezelése a Windows-alapú HDInsight alatt futó Apache Storm-topológiák

A Storm irányítópultjának segítségével egyszerűen regisztrálhat és futtathat Apache Storm-topológiák a HDInsight-fürthöz, a webböngésző használatával. Az Irányítópult segítségével figyelheti és kezelheti a futó topológiákat. Visual Studio használata esetén a HDInsight Tools for Visual Studio adja meg a Visual Studio hasonló szolgáltatásokat.

A Storm irányítópultjának és a HDInsight Tools Storm funkciók segítségével hozza létre a sajátját figyelés alatt futó Storm REST API és kezelési megoldásai támaszkodnak.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy Storm on HDInsight-fürt által használt Windows operációs rendszer szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Telepítésével és a HDInsight-fürtök által használt Linux Storm-topológiák kezelésével kapcsolatos információkért lásd: [központi telepítése és kezelése a Linux-alapú HDInsight alatt futó Apache Storm-topológiák](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="prerequisites"></a>Előfeltételek

* **A HDInsight alatt futó Apache Storm** -lásd [első lépései a HDInsight alatt futó Apache Storm](hdinsight-apache-storm-tutorial-get-started.md) fürt létrehozásának lépései.

* Az a **a Storm irányítópultja**: HTML5-támogatással rendelkező modern webböngésző.

* A **Visual Studio** -2.5.1-es verzióval készült Azure SDK újabb vagy és a HDInsight Tools for Visual Studio. Lásd: [első lépései a HDInsight Tools for Visual Studio használatával](hdinsight-hadoop-visual-studio-tools-get-started.md) telepítése és konfigurálása a HDInsight tools for Visual Studio.

    A Visual Studio következő verzióinak valamelyike:

  * A Visual Studio 2012 Update 4

  * Visual Studio 2013 4. frissítéssel vagy Visual Studio 2013 Community

  * A Visual Studio 2015 (minden kiadás)

  * A Visual Studio 2017 (minden kiadás)

## <a name="storm-dashboard"></a>A Storm irányítópultja

A Storm irányítópultja a Storm-fürt elérhető weblapon. Az URL-címe **https://&lt;clustername >.azurehdinsight.net/**, ahol **clustername** a Storm on HDInsight-fürt neve.

Válassza ki a Storm irányítópultjának felső részén **nyújt topológia**. A lapon, a minta topológia futtatásához vagy feltölteni, majd futtassa a topológia létrehozott kövesse az utasításokat.

![a Küldés topológia lapon][storm-dashboard-submit]

### <a name="storm-ui"></a>A Storm felhasználói felülete

A Storm irányítópultjának, válassza ki a **Storm felhasználói felülete** hivatkozásra. Ez a fürt bármely futó topológiákat mellett információit jeleníti meg.

![a storm felhasználói felülete][storm-dashboard-ui]

> [!NOTE]
> Az Internet Explorer egyes verziói azt tapasztalhatja, hogy a Storm felhasználói felülete nem frissíti először ellátogatott után. Például akkor lehet, hogy jelenjen meg az új topológiák meg, vagy azt előfordulhat, hogy a topológia aktívnak amikor korábban inaktív. Microsoft ismeri a problémát, és a megoldás működik.

#### <a name="main-page"></a>Főoldala

A Storm felhasználói felület a fő lapján az alábbi információkat tartalmazza:

* **A fürt összefoglaló**: a Storm-fürt alapvető adatait.

* **Összegző topológia**: futó topológiákat. Ebben a szakaszban a hivatkozások segítségével további információkat szeretne az adott topológiákat.

* **Összegző felügyelő**: a Storm felügyelő kapcsolatos információkat.

* **Nimbus konfigurációs**: Nimbus konfigurációját a fürtben.

#### <a name="topology-summary"></a>Topológia összegzése

A hivatkozás kiválasztása a **topológia összegzése** csoportban megjelennek a topológia a következő információkat:

* **Összegző topológia**: a topológia alapvető adatait.

* **Topológia műveletek**: kezelési műveleteket hajthat végre a topológia.

  * **Aktiválása**: folytatása inaktivált topológia feldolgozását.

  * **Inaktiválása**: megszakítja a futó topológiát.

  * **Visszaegyensúlyozás**: Beállítja a topológia párhuzamosságát. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Ez lehetővé teszi a topológia párhuzamosságának kiegyensúlyozása érdekében a növelő vagy csökkentő a fürtben található csomópontok számát.

      További információkért lásd: [ismertetése a Storm-topológia (http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) párhuzamosságát](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

  * **Kill**: a Storm-topológia leállítása bizonyos időtúllépést követően.

* **Topológiastatisztikák**: a topológia statisztikája. A hivatkozások a **ablak** oszlop időkeretének megadására a többieknek beállításához a lap.

* **Spoutok**: A spoutokkal kapcsolatban, használja a topológiát. Ebben a szakaszban a hivatkozások segítségével megtekintheti az adott spoutokkal kapcsolatban további információt.

* **Boltok**: A boltokhoz a topológia használják. Ebben a szakaszban található hivatkozások segítségével további információkat szeretne az adott boltokhoz.

* **Topológiakonfiguráció**: a kiválasztott topológia beállításától.

#### <a name="spout-and-bolt-summary"></a>Spout és Bolt összegzése

Az egy spout kiválasztása a **Spoutok** vagy **boltok** szakaszok megjeleníti a kijelölt elem a következő információkat:

* **Az összetevő összegzés**: a spout vagy bolt alapvető adatait.

* **Spout vagy Bolt statisztikák**: a spout vagy bolt statisztikája. A hivatkozások a **ablak** oszlop időkeretének megadására a többieknek beállításához a lap.

* **Beviteli statisztikák** (csak boltok esetében): a bemeneti adatfolyamot tartalmaz a bolt által felhasznált információk.

* **Kimeneti statisztikák**: az adatfolyamokat, ez által kibocsátott információ spout vagy boltok esetében.

* **Végrehajtója**: a spout vagy bolt példányaira vonatkozó információkat. Válassza ki a **Port** egy adott végrehajtó diagnosztikai adatok megtekintését, a bejegyzés erre a példányra.

* **Hibák**: bármilyen hiba adatokat a spout vagy boltok esetében.

## <a name="hdinsight-tools-for-visual-studio"></a>HDInsight Tools for Visual Studio

A HDInsight Tools elküldeni a C# vagy hibrid topológiák a Storm fürthöz használható. Az alábbi lépéseket a mintaalkalmazás használja. A HDInsight Tools használatával saját topológiák létrehozásával kapcsolatos további információkért lásd: [fejlesztésére C#-topológiák a HDInsight Tools for Visual Studio használatával](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Az alábbi lépések segítségével telepít egy alatt futó Storm on HDInsight-fürt, akkor megtekintheti, és a topológia kezelése.

1. Ha még nem telepítette a legújabb verzióját a HDInsight Tools for Visual Studio, lásd: [első lépései a HDInsight Tools for Visual Studio használatával](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Nyissa meg a Visual Studio, válassza ki **fájl** > **új** > **projekt**.

3. Az a **új projekt** párbeszédpanelen bontsa ki **telepített** > **sablonok**, majd válassza ki **HDInsight**. Válassza ki a listáról a sablonok **Storm minta**. A párbeszédpanel alján írja be az alkalmazás nevét.

    ![Kép](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

4. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **Submit a HDInsight alatt futó Storm**.

   > [!NOTE]
   > Ha a rendszer kéri, adja meg a bejelentkezési hitelesítő adatok az Azure-előfizetéshez. Ha egynél több előfizetéssel rendelkezik, jelentkezzen be, amely tartalmazza a Storm on HDInsight-fürt.

5. Válassza ki a Storm on HDInsight-fürt a a **Storm-fürt** legördülő listából válassza ki, és válassza **Submit**. -E a küldése sikeres használatával figyelheti a **kimeneti** ablak.

6. Ha a topológia küldése sikeres volt, a **Storm-topológiák** megjelenjen-e a fürt számára. Válassza ki a topológiát a futó topológiát vonatkozó információk megtekintése a listából.

    ![a Visual studio-figyelő](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > Megtekintheti továbbá **Storm-topológiák** a **Server Explorer** kibontásával **Azure** > **HDInsight**, és, majd kattintson a jobb gombbal a Storm on HDInsight-fürt, majd válassza **nézet Storm-topológiák**.

    Válassza ki az alakzat a spoutokkal kapcsolatban, vagy a boltokhoz ezeket az összetevőket vonatkozó információk megtekintése. Minden elem kijelölve egy új ablakban nyílik meg.

   > [!NOTE]
   > A topológia neve nem az osztály nevét a topológia (ebben az esetben `HelloWord`,) az időbélyegzőnek lesz hozzáfűzve.

7. Az a **topológia összegzése** nézetben jelölje ki **Kill** a topológia leállítása.

   > [!NOTE]
   > Storm-topológiák továbbra is fut le vannak állítva, vagy a fürt törlésekor.


## <a name="rest-api"></a>REST API

A Storm felhasználói felülete a REST API-t épül, ezért hasonló felügyeleti és figyelési funkcióit, a REST API használatával végezheti el. A REST API-t hozhat létre egyéni eszközök felügyelheti és figyelheti a Storm-topológiák.

További információkért lásd: [Storm UI REST API](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). Az alábbi információk csak a HDInsight alatt futó Apache Storm a REST API használatával az elő.

### <a name="base-uri"></a>Alap URI

Az alap URI a HDInsight-fürtök REST API **https://&lt;clustername >.azurehdinsight.net/stormui/api/v1/**, ahol **clustername** a Storm on HDInsight-fürt neve.

### <a name="authentication"></a>Authentication

A REST API-kérésnek kell használnia **az egyszerű hitelesítés**, így a HDInsight fürt rendszergazdája nevet és jelszót használják.

> [!NOTE]
> Egyszerű hitelesítést a rendszer a tiszta szöveges küldi el, mert akkor **mindig** HTTPS protokoll használatával biztonságos kommunikáció a fürtöt.

### <a name="return-values"></a>Visszatérési érték

Információ a REST API visszaadott csak lehet a fürt vagy a virtuális gépek azonos Azure virtuális hálózaton a fürt belül használható. Például a teljesen minősített tartománynevét (FQDN) adott vissza a rendszer a Zookeeper kiszolgálók nem lehet elérhető az internetről.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte segítségével telepítheti és figyelheti a topológia a Storm irányítópultjának használatával megtudhatja, hogyan lehet:

* [Fejlesztésére C#-topológiák a HDInsight Tools for Visual Studio használatával](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Maven használatával Java-alapú topológiák fejlesztése](hdinsight-storm-develop-java-topology.md)

További példa topológiák listájáért lásd: [a HDInsight alatt futó Storm](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png
