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
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: 5e05f07f2d9a15ab57f993e88b7cdeb03550f8ac
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="introducing-apache-kafka-on-hdinsight-preview"></a>A HDInsight alatt futó Apache Kafka (előzetes verzió) bemutatása

Az [Apache Kafka](https://kafka.apache.org) egy nyílt forráskódú elosztott streamelési platform streamadatfolyamatok és -alkalmazások létrehozásához. A Kafka az üzenetsorokhoz hasonló üzenetközvetítő funkciót is biztosít, amellyel adatstreameket tehet közzé, illetve feliratkozhat rájuk. A HDInsight alatt futó Kafka felügyelt, rugalmasan méretezhető és magas rendelkezésre állású szolgáltatást biztosít önnek a Microsoft Azure-felhőben.

## <a name="why-use-kafka-on-hdinsight"></a>Miért érdemes a HDInsight alatt futó Kafkát használni?

A Kafka a következő funkciókat biztosítja:

* Közzétételi-feliratkozási üzenetkezelési minta: A előállítói API-t biztosít a rekordok Kafka-témakörökbe való közzétételéhez. A fogyasztói API-ra a témakörökre való feliratkozáskor van szükség.

* Streamfeldolgozás: A Kafkát gyakran használják valós idejű streamfeldolgozásra az Apache Stormmal vagy Sparkkal. A Kafka 0.10.0.0 (HDInsight 3.5-ös verzió) egy olyan streamelési API-t vezetett be, amely lehetővé teszi a streammegoldások Storm vagy Spark nélküli fejlesztését.

* Horizontális skálázhatóság: a Kafka szétosztja a streameket a HDInsight-fürtben található csomópontok között. A fogyasztói folyamatok társíthatók az egyes partíciókkal, így biztosítható a terheléselosztás a rekordok használatakor.

* Érkezési sorrendben történő kézbesítés: a stream minden egyes partíción belül érkezési sorrendben tárolja a rekordokat. Partíciónként egy fogyasztói folyamat társításával garantálhatja, hogy a rekordok feldolgozása érkezési sorrendben történjen.

* Hibatűrés: A partíciók replikálhatók a csomópontok között a hibatűrés biztosításához.

* Integráció az Azure Managed Disks szolgáltatással: Az Azure Managed Disks jobb méretezést és teljesítményt biztosít a HDInsight-fürt által használt virtuális gépek lemezei számára.

    A felügyelt lemezek alapértelmezés szerint engedélyezettek a HDInsighton futó Kafkához. A csomópontonként használt lemezek száma a HDInsight létrehozása során konfigurálható. További tudnivalók a felügyelt lemezekről: [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

    További tudnivalók a felügyelt lemezek HDInsight-alapú Kafkával való konfigurálásáról: [A HDInsight-alapú Kafka méretezhetőségének javítása](apache-kafka-scalability.md).

## <a name="use-cases"></a>Használati esetek

* **Üzenetkezelés**: sokszor használják a Kafkát üzenetközvetítőként, mivel támogatja a közzétételi-feliratkozási üzenetmintát.

* **Tevékenységkövetés**: mivel a Kafka lehetővé teszi a rekordok érkezési sorrend szerinti naplózását, használható tevékenységek nyomon követésére és ismételt létrehozására. Ilyen tevékenységek például a felhasználók műveletei egy webhelyen vagy egy alkalmazásban.

* **Összesítés**: streamfeldolgozással összesítheti a különböző streamek információit, hogy működési adatokká egyesítse és központosítsa az információt.

* **Átalakítás**: streamfeldolgozás használatával egyesítheti és bővítheti az adatokat több bemeneti témakörből egy vagy több kimeneti témakörbe.

## <a name="architecture"></a>Architektúra

![Kafka-fürtkonfiguráció](./media/apache-kafka-introduction/kafka-cluster.png)

Ez az ábra egy tipikus Kafka-konfigurációt mutat be, amely felhasználói csoportok, particionálás és replikálás használatával biztosítja az események párhuzamos olvasását hibatűréssel. Az Apache ZooKeeper az egyidejű, rugalmas és kis késleltetésű tranzakciókhoz készült, mivel a Kafka-fürt állapotát kezeli. A Kafka *témakörökben* tárolja a rekordokat. A rekordokat *előállítók* hozzák létre, és *fogyasztók* használják fel. Az előállítók *Kafka-közvetítőktől* kérik le a rekordokat. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő. Minden felhasználóhoz tartozik egy partíció, amely lehetővé teszi a streamadatok párhuzamos feldolgozását. A partíciók csomópontok közötti elosztása érdekében a rendszer replikációt alkalmaz, amely védelmet nyújt a csomópontok (közvetítők) leállásával szemben. Az *(L)* jelölésű partíció az adott partíció vezetője. Az előállítói forgalmat a csomópontok vezetőjéhez irányítja a rendszer a ZooKeeper által kezelt állapot segítségével.

> [!IMPORTANT]
> A Kafka nem észleli az Azure-adatközpontban lévő alapul szolgáló hardvereket (rackszekrényeket). A partíciók az alapul szolgáló hardverek közötti megfelelő kiegyensúlyozásához lásd: [az adatok magas szintű rendelkezésre állásának konfigurálásával (Kafka)](apache-kafka-high-availability.md) kapcsolatos cikket.

## <a name="next-steps"></a>Következő lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [A HDInsighton futó Kafka használatának első lépései](apache-kafka-get-started.md)

* [A MirrorMaker használata a Kafka replikájának HDInsighton való létrehozásához](apache-kafka-mirroring.md)

* [Az Apache Storm használata a HDInsighton futó Kafkával](../hdinsight-apache-storm-with-kafka.md)

* [Az Apache Spark használata a Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)

* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](apache-kafka-connect-vpn-gateway.md)