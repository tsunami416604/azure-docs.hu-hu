---
title: "A HDInsight alatt futó Apache Kafka bemutatása | Microsoft Docs"
description: "Ez a cikk a HDInsight alatt futó Apache Kafka ismertetését tartalmazza. Leírja, mi ez, mire szolgál, illetve hol találhat példákat és információt a használatának első lépéseiről."
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
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: a3ceca6cd0f470a5cd6849c345867f094b870a85
ms.lasthandoff: 04/19/2017

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

## <a name="use-cases"></a>Használati esetek

* **Üzenetkezelés**: sokszor használják a Kafkát üzenetközvetítőként, mivel támogatja a közzétételi-feliratkozási üzenetmintát.

* **Tevékenységkövetés**: mivel a Kafka lehetővé teszi a rekordok érkezési sorrend szerinti naplózását, használható tevékenységek nyomon követésére és ismételt létrehozására. Ilyen tevékenységek például a felhasználók műveletei egy webhelyen vagy egy alkalmazásban.

* **Összesítés**: streamfeldolgozással összesítheti a különböző streamek információit, hogy működési adatokká egyesítse és központosítsa az információt.

* **Átalakítás**: streamfeldolgozás használatával egyesítheti és bővítheti az adatokat több bemeneti témakörből egy vagy több kimeneti témakörbe.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

További információ a Kafka-fürt létrehozásáról a Kafka használatáról (Java-alapú példákkal az előállítói, a fogyasztói és a streamelési API használatáról): [A HDInsighton futó Kafka használatának első lépései](hdinsight-apache-kafka-get-started.md)

## <a name="next-steps"></a>Következő lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [A HDInsighton futó Kafka használatának első lépései](hdinsight-apache-kafka-get-started.md)

* [A MirrorMaker használata a Kafka replikájának HDInsighton való létrehozásához](hdinsight-apache-kafka-mirroring.md)

* [Az Apache Storm használata a HDInsighton futó Kafkával](hdinsight-apache-storm-with-kafka.md)

* [Az Apache Spark használata a Kafkával a HDInsighton](hdinsight-apache-spark-with-kafka.md)

* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](hdinsight-apache-kafka-connect-vpn-gateway.md)
