---
title: A HDInsight-alapú Apache Kafka bemutatása – Azure
description: 'Ismerje meg a HDInsight-alapú Apache Kafkát: Mi ez, mire való, hol találhat rá példákat, és hol találhatja meg az első lépésekre vonatkozó információt?'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: 92f56f3b405470bc8ae0e9ebab2450ddc31b3c6a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77672174"
---
# <a name="what-is-apache-kafka-in-azure-hdinsight"></a>Mi az Apache Kafka az Azure HDInsightban?

Az [Apache Kafka](https://kafka.apache.org) egy nyílt forráskódú elosztott streamelési platform streamadatfolyamatok és -alkalmazások létrehozásához. A Kafka az üzenetsorokhoz hasonló üzenetközvetítő funkciót is biztosít, amellyel adatstreameket tehet közzé, illetve feliratkozhat rájuk.

A HDInsight-alapú Kafka jellegzetességei:

* Ez egy felügyelt szolgáltatás, amely egyszerűsített konfigurációs folyamatot biztosít. Ennek eredménye egy, a Microsoft által tesztelt és támogatott konfiguráció.

* A Microsoft 99,9%-os szolgáltatói szerződést (SLA) nyújt a Kafka üzemidejével kapcsolatban. További információkért lásd [a HDInsight szolgáltatói szerződésével kapcsolatos információkat ismertető](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) dokumentumot.

* A Kafka az Azure Managed Disks szolgáltatást használja háttértárként. A Managed Disks akár 16 TB tárhelyet biztosíthat Kafka-közvetítőnként. A felügyelt lemezek konfigurálásáról a HDInsight Kafka segítségével az [Apache Kafka méretezhetőségének növelése a HDInsight-on](apache-kafka-scalability.md)című témakörben talál.

    További tudnivalók a felügyelt lemezekről: [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* A Kafkát egy egydimenziós állványnézettel tervezték. Az Azure az állványokat két dimenzióra osztja: a frissítési tartományokra (UD) és a tartalék tartományokra (FD). A Microsoft biztosítja azokat az eszközöket, amelyek kiegyensúlyozhatják a Kafka-partíciókat és -replikákat a frissítési és tartalék tartományok között.

    További információ: [Magas rendelkezésre állás az Apache Kafka a HDInsight.](apache-kafka-high-availability.md)

* A HDInsight lehetővé teszi a feldolgozó csomópontok (amelyeken a Kafka-közvetítő fut) számának a fürt létrehozását követő módosítását. A méretezés elvégezhető az Azure Portal, az Azure PowerShell vagy egyéb Azure-kezelőfelületek segítségével. A Kafka esetében érdemes kiegyenlíteni a partíciók replikáit a méretezési műveleteket követően. A partíciók kiegyenlítése lehetővé teszi, hogy a Kafka kihasználja a feldolgozó csomópontok új mennyiségét.

    További információ: [Magas rendelkezésre állás az Apache Kafka a HDInsight.](apache-kafka-high-availability.md)

* Az Azure Monitor naplók segítségével figyelheti a Kafka a HDInsight. Az Azure Monitor naplózza a virtuális gép szintű adatokat, például a lemez- és hálózati adapterek metrikákat, valamint a Kafka JMX-metrikákat.

    További információ: [A HDInsight-on az Apache Kafka naplóinak elemzése.](apache-kafka-log-analytics-operations-management.md)

## <a name="apache-kafka-on-hdinsight-architecture"></a>Apache Kafka a HDInsight architektúráról

Az alábbi ábra egy tipikus Kafka-konfigurációt mutat be, amely felhasználói csoportok, particionálás és replikálás használatával biztosítja az események párhuzamos olvasását hibatűréssel:

![Kafka-fürtkonfigurációs ábra](./media/apache-kafka-introduction/kafka-cluster-diagram.png)

A Kafka-fürt állapotát az Apache ZooKeeper felügyeli. A ZooKeeper egyidejű, rugalmas és kis késleltetésű tranzakciókhoz készült.

A Kafka a rekordokat (adatokat) **témakörökben** tárolja. A rekordokat **előállítók** hozzák létre, és **fogyasztók** használják fel. Az előállítók Kafka-**közvetítőknek** adják tovább a rekordokat. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő.

A témakörök particionálják a rekordokat a közvetítők között. A rekordok felhasználásakor partíciónként legfeljebb egy fogyasztó használható, az adatok párhuzamos feldolgozása érdekében.

A partíciók csomópontok közötti duplikálásához a rendszer replikációt alkalmaz, amely védelmet nyújt a csomópontok (közvetítők) leállásával szemben. Az ábrán *(L)* jelöléssel rendelkező partíció az adott partíció vezetője. Az előállítói forgalmat a csomópontok vezetőjéhez irányítja a rendszer a ZooKeeper által kezelt állapot segítségével.

## <a name="why-use-apache-kafka-on-hdinsight"></a>Miért érdemes az Apache Kafka-t használni a HDInsight-on?

Néhány gyakori feladat és minta, amelyek a HDInsight-alapú Kafkával elvégezhetők:

|Használat |Leírás |
|---|---|
|Apache Kafka adatok replikációja|A Kafka biztosítja a MirrorMaker segédprogramot, amely replikálja az adatokat a Kafka-fürtök között. A MirrorMaker használatával kapcsolatos további tudnivalókért olvassa el az [Apache Kafka-témakörök replikálása az Apache Kafka alkalmazással a HDInsight-on című témakört.](apache-kafka-mirroring.md)|
|Közzététel-előfizetési üzenetküldési minta|A Kafka produceri API-t biztosít a rekordok Kafka-témakörben való közzétételéhez. A fogyasztói API-ra a témakörökre való feliratkozáskor van szükség. További információ: [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Stream-feldolgozás|A Kafka gyakran használatos az Apache Storm vagy a Spark segítségével a valós idejű adatfolyam-feldolgozáshoz. A Kafka 0.10.0.0 (HDInsight 3.5-ös és 3.6-os verzió) egy olyan streamelési API-t vezetett be, amely lehetővé teszi a streammegoldások Storm vagy Spark nélküli fejlesztését. További információ: [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Horizontális skálázhatóság|A Kafka partíciók streamelik a HDInsight-fürt csomópontjait. A fogyasztói folyamatok társíthatók az egyes partíciókkal, így biztosítható a terheléselosztás a rekordok használatakor. További információ: [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Sorrendben történő kiszállítás|Az egyes partíciókon belül a rekordok a beérkezésük sorrendjében tárolódnak az adatfolyamban. Partíciónként egy fogyasztói folyamat társításával garantálhatja, hogy a rekordok feldolgozása érkezési sorrendben történjen. További információ: [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Üzenetkezelés|Mivel támogatja a közzététel-feliratkozási üzenet mintát, Kafka gyakran használják üzenetközvetítőként.|
|Tevékenység követés|Mivel a Kafka a rekordok sorrendben történő naplózását biztosítja, tevékenységek nyomon követésére és újbóli létrehozására használható. Ilyen tevékenységek például a felhasználók műveletei egy webhelyen vagy egy alkalmazásban.|
|Összesítés|Az adatfolyam-feldolgozás használatával összesítheti a különböző adatfolyamokból származó információkat az információk operatív adatokká történő egyesítéséhez és központosításához.|
|Átalakítás|Az adatfolyam-feldolgozás használatával több bemeneti témakör adatait egyesítheti és gazdagíthatja egy vagy több kimeneti témakörben.|

## <a name="next-steps"></a>További lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [Rövid útmutató: Apache Kafka létrehozása a HDInsighton](apache-kafka-get-started.md)

* [A Kafka használata REST proxyval](rest-proxy.md)

* [Oktatóanyag: Az Apache Spark használata az Apache Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)

* [Oktatóanyag: Az Apache Storm használata az Apache Kafka on HDInsighttal](../hdinsight-apache-storm-with-kafka.md)
