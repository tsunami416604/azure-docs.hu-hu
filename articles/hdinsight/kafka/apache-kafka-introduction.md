---
title: "Az Azure HDInsight-alapú Apache Kafka bemutatása | Microsoft Docs"
description: "Ismerje meg a HDInsight-alapú Apache Kafkát: Mi ez, mire való, hol találhat rá példákat, és hol találhatja meg az első lépésekre vonatkozó információt?"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: 945b16553d56d5138b17e7768e43a298b310551d
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="introducing-apache-kafka-on-hdinsight"></a>A HDInsight alatt futó Apache Kafka bemutatása

Az [Apache Kafka](https://kafka.apache.org) egy nyílt forráskódú elosztott streamelési platform streamadatfolyamatok és -alkalmazások létrehozásához. A Kafka az üzenetsorokhoz hasonló üzenetközvetítő funkciót is biztosít, amellyel adatstreameket tehet közzé, illetve feliratkozhat rájuk. A HDInsight alatt futó Kafka felügyelt, rugalmasan méretezhető és magas rendelkezésre állású szolgáltatást biztosít önnek a Microsoft Azure-felhőben.

## <a name="why-use-kafka-on-hdinsight"></a>Miért érdemes a HDInsight alatt futó Kafkát használni?

A HDInsight alatt futó Kafka a következő szolgáltatásokat biztosítja:

* __99%-os szolgáltatói szerződés (SLA) a Kafka üzemidejével kapcsolatban__: További információkért lásd [a HDInsight szolgáltatói szerződésével kapcsolatos információkat ismertető](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) dokumentumot.

* __Hibatűrés és állványfigyelés__: A Kafkát egy egydimenziós állványnézettel tervezték, amely jól működik bizonyos környezetekben. Viszont az olyan környezetek esetében, mint amilyen az Azure, az állvány két dimenzióra oszlik: a frissítési tartományokra (UD-k) és a tartalék tartományokra (FD-k). A Microsoft biztosítja azokat az eszközöket, amelyek kiegyenlítik a Kafka-partíciókat és -replikákat a frissítési és tartalék tartományok között. 

    További információ: [Magas rendelkezésre állás a HDInsight-alapú Kafkában](apache-kafka-high-availability.md).

* **Integráció az Azure Managed Disks szolgáltatással**: A felügyelt lemezek jobb méretezést és teljesítményt biztosítanak a HDInsight-alapú Kafka által használt lemezek számára a fürtben, akár csomópontonként 16 TB-ig.

    További tudnivalók a felügyelt lemezek HDInsight-alapú Kafkával való konfigurálásáról: [A HDInsight-alapú Kafka méretezhetőségének javítása](apache-kafka-scalability.md).

    További tudnivalók a felügyelt lemezekről: [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* **Riasztások kezelése, monitorozás és prediktív karbantartás**: A HDInsight-alapú Kafka az Azure Log Analytics használatával monitorozható. A Log Analytics megjeleníti a virtuális gépek szintadatait, például a lemezek és hálózati adapterek metrikáit és a Kafka JMX-metrikáit.

    További információ: [A HDInsight-alapú Kafka naplóinak elemzése](apache-kafka-log-analytics-operations-management.md).

* **Kafka-adatok replikálása**: A Kafka biztosítja a MirrorMaker eszközt, amely replikálja az adatokat a Kafka-fürtök között.

    A MirrorMaker használatával kapcsolatban lásd: [Kafka-témakörök replikálása a HDInsight-alapú Kafka esetében](apache-kafka-mirroring.md).

* **Fürtméretezés**: A HDInsight lehetővé teszi a feldolgozó csomópontok számának módosítását (amelyeken a Kafka-közvetítő fut) a fürt létrehozását követően. A fürtök vertikálisan felskálázhatók a számítási feladatok növekedése esetén, vagy leskálázhatók a költségek csökkentése érdekében. A méretezés elvégezhető az Azure Portal, az Azure PowerShell vagy egyéb Azure-kezelőfelületek segítségével. A Kafka esetében érdemes kiegyenlíteni a partíciók replikáit a méretezési műveleteket követően. A partíciók kiegyenlítése lehetővé teszi, hogy a Kafka kihasználja a feldolgozó csomópontok új mennyiségét.

    További információ: [Magas rendelkezésre állás a HDInsight-alapú Kafkában](apache-kafka-high-availability.md).

* **Közzétételi-feliratkozási üzenetkezelési minta**: A Kafka egy előállítói API-t biztosít a rekordok Kafka-témakörökbe való közzétételéhez. A fogyasztói API-ra a témakörökre való feliratkozáskor van szükség.

    További információ: [A HDInsight-alapú Kafka használatának első lépései](apache-kafka-get-started.md).

* **Streamfeldolgozás**: A Kafkát gyakran használják valós idejű streamfeldolgozásra az Apache Stormmal vagy Sparkkal. A Kafka 0.10.0.0 (HDInsight 3.5-ös és 3.6-os verzió) egy olyan streamelési API-t vezetett be, amely lehetővé teszi a streammegoldások Storm vagy Spark nélküli fejlesztését.

    További információ: [A HDInsight-alapú Kafka használatának első lépései](apache-kafka-get-started.md).

* **Horizontális skálázhatóság**: a Kafka szétosztja a streameket a HDInsight-fürtben található csomópontok között. A fogyasztói folyamatok társíthatók az egyes partíciókkal, így biztosítható a terheléselosztás a rekordok használatakor.

    További információ: [A HDInsight-alapú Kafka használatának első lépései](apache-kafka-get-started.md).

* **Érkezési sorrendben történő kézbesítés**: a stream minden egyes partíción belül érkezési sorrendben tárolja a rekordokat. Partíciónként egy fogyasztói folyamat társításával garantálhatja, hogy a rekordok feldolgozása érkezési sorrendben történjen.

    További információ: [A HDInsight-alapú Kafka használatának első lépései](apache-kafka-get-started.md).

## <a name="use-cases"></a>Használati esetek

* **Üzenetkezelés**: sokszor használják a Kafkát üzenetközvetítőként, mivel támogatja a közzétételi-feliratkozási üzenetmintát.

* **Tevékenységkövetés**: mivel a Kafka lehetővé teszi a rekordok érkezési sorrend szerinti naplózását, használható tevékenységek nyomon követésére és ismételt létrehozására. Ilyen tevékenységek például a felhasználók műveletei egy webhelyen vagy egy alkalmazásban.

* **Összesítés**: streamfeldolgozással összesítheti a különböző streamek információit, hogy működési adatokká egyesítse és központosítsa az információt.

* **Átalakítás**: streamfeldolgozás használatával egyesítheti és bővítheti az adatokat több bemeneti témakörből egy vagy több kimeneti témakörbe.

## <a name="architecture"></a>Architektúra

![Kafka-fürtkonfiguráció](./media/apache-kafka-introduction/kafka-cluster.png)

Ez az ábra egy tipikus Kafka-konfigurációt mutat be, amely felhasználói csoportok, particionálás és replikálás használatával biztosítja az események párhuzamos olvasását hibatűréssel. Az Apache ZooKeeper az egyidejű, rugalmas és kis késleltetésű tranzakciókhoz készült, mivel a Kafka-fürt állapotát kezeli. A Kafka *témakörökben* tárolja a rekordokat. A rekordokat *előállítók* hozzák létre, és *fogyasztók* használják fel. Az előállítók *Kafka-közvetítőktől* kérik le a rekordokat. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő. Minden felhasználóhoz tartozik egy partíció, amely lehetővé teszi a streamadatok párhuzamos feldolgozását. A partíciók csomópontok közötti elosztása érdekében a rendszer replikációt alkalmaz, amely védelmet nyújt a csomópontok (közvetítők) leállásával szemben. Az *(L)* jelölésű partíció az adott partíció vezetője. Az előállítói forgalmat a csomópontok vezetőjéhez irányítja a rendszer a ZooKeeper által kezelt állapot segítségével.

Mindegyik Kafka-közvetítő az Azure Managed Disks szolgáltatást használja. A lemezek számát a felhasználó adja meg, és közvetítőnként akár 16 TB tárhely is biztosítható.

> [!IMPORTANT]
> A Kafka nem észleli az Azure-adatközpontban lévő alapul szolgáló hardvereket (rackszekrényeket). A partíciók az alapul szolgáló hardverek közötti megfelelő kiegyensúlyozásához lásd [az adatok magas szintű rendelkezésre állásának konfigurálásával (Kafka)](apache-kafka-high-availability.md) kapcsolatos dokumentumot.

## <a name="next-steps"></a>További lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [A HDInsighton futó Kafka használatának első lépései](apache-kafka-get-started.md)

* [A MirrorMaker használata a Kafka replikájának HDInsighton való létrehozásához](apache-kafka-mirroring.md)

* [Az Apache Storm használata a HDInsighton futó Kafkával](../hdinsight-apache-storm-with-kafka.md)

* [Az Apache Spark használata a Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)

* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](apache-kafka-connect-vpn-gateway.md)