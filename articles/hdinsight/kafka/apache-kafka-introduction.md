---
title: A HDInsight-alapú Apache Kafka bemutatása – Azure
description: 'Ismerje meg az Apache Kafka on HDInsight: Leírja, mi ez, mire szolgál, illetve hol találhat példákat és információt a használatának első lépéseiről.'
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 04/11/2018
ms.openlocfilehash: 4d054542d600f2569170f40b8f6c053e005fc8af
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889188"
---
# <a name="what-is-apache-kafka-on-hdinsight"></a>Mi az a HDInsight-alapú Apache Kafka?

Az [Apache Kafka](https://kafka.apache.org) egy nyílt forráskódú elosztott streamelési platform streamadatfolyamatok és -alkalmazások létrehozásához. A Kafka az üzenetsorokhoz hasonló üzenetközvetítő funkciót is biztosít, amellyel adatstreameket tehet közzé, illetve feliratkozhat rájuk. 

A HDInsight-alapú Kafka jellegzetességei:

* Egy felügyelt szolgáltatás, amely egyszerűsített konfigurációs folyamatot biztosít. Ennek eredménye egy, a Microsoft által tesztelt és támogatott konfiguráció.

* A Microsoft 99,9%-os szolgáltatói szerződést (SLA) nyújt a Kafka üzemidejével kapcsolatban. További információkért lásd [a HDInsight szolgáltatói szerződésével kapcsolatos információkat ismertető](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) dokumentumot.

* A Kafka az Azure Managed Disks szolgáltatást használja háttértárként. A Managed Disks akár 16 TB tárhelyet biztosíthat Kafka-közvetítőnként. Információk a HDInsight alatt futó Kafka felügyelt lemezek konfigurálása: [jobb méretezhetőség érdekében az Apache Kafka on HDInsight](apache-kafka-scalability.md).

    További tudnivalók a felügyelt lemezekről: [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* A Kafkát egy egydimenziós állványnézettel tervezték. Az Azure az állványokat két dimenzióra osztja: a frissítési tartományokra (UD) és a tartalék tartományokra (FD). A Microsoft biztosítja azokat az eszközöket, amelyek kiegyensúlyozhatják a Kafka-partíciókat és -replikákat a frissítési és tartalék tartományok között. 

    További információkért lásd: [magas rendelkezésre állás az Apache Kafka on HDInsight](apache-kafka-high-availability.md).

* A HDInsight lehetővé teszi a feldolgozó csomópontok (amelyeken a Kafka-közvetítő fut) számának a fürt létrehozását követő módosítását. A méretezés elvégezhető az Azure Portal, az Azure PowerShell vagy egyéb Azure-kezelőfelületek segítségével. A Kafka esetében érdemes kiegyenlíteni a partíciók replikáit a méretezési műveleteket követően. A partíciók kiegyenlítése lehetővé teszi, hogy a Kafka kihasználja a feldolgozó csomópontok új mennyiségét.

    További információkért lásd: [magas rendelkezésre állás az Apache Kafka on HDInsight](apache-kafka-high-availability.md).

* Az Azure Monitor naplóira a HDInsight alatt futó Kafka figyelésére használható. Az Azure Monitor a Kafkából naplózza a Surface-eszközök virtuális gépek szintadatait, például a lemez és a hálózati adapterek metrikáit és a JMX-metrikáit.

    További információkért lásd: [-naplók elemzése a HDInsight-beli Apache kafka](apache-kafka-log-analytics-operations-management.md).

### <a name="apache-kafka-on-hdinsight-architecture"></a>Az Apache Kafka on HDInsight-architektúra

Az alábbi ábra egy tipikus Kafka-konfigurációt mutat be, amely felhasználói csoportok, particionálás és replikálás használatával biztosítja az események párhuzamos olvasását hibatűréssel:

![Kafka-fürtkonfigurációs ábra](./media/apache-kafka-introduction/kafka-cluster.png)

A Kafka-fürt állapotát az Apache ZooKeeper felügyeli. A ZooKeeper egyidejű, rugalmas és kis késleltetésű tranzakciókhoz készült. 

A Kafka a rekordokat (adatokat) **témakörökben** tárolja. A rekordokat **előállítók** hozzák létre, és **fogyasztók** használják fel. Az előállítók Kafka-**közvetítőknek** adják tovább a rekordokat. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő. 

A témakörök particionálják a rekordokat a közvetítők között. A rekordok felhasználásakor partíciónként legfeljebb egy fogyasztó használható, az adatok párhuzamos feldolgozása érdekében.

A partíciók csomópontok közötti duplikálásához a rendszer replikációt alkalmaz, amely védelmet nyújt a csomópontok (közvetítők) leállásával szemben. Az ábrán *(L)* jelöléssel rendelkező partíció az adott partíció vezetője. Az előállítói forgalmat a csomópontok vezetőjéhez irányítja a rendszer a ZooKeeper által kezelt állapot segítségével.

## <a name="why-use-apache-kafka-on-hdinsight"></a>Miért érdemes a HDInsight Apache Kafka használatának?

Néhány gyakori feladat és minta, amelyek a HDInsight-alapú Kafkával elvégezhetők:

* **Az Apache Kafka-adatok replikálása**: A Kafka biztosítja a MirrorMaker eszközt, amely replikálja az adatokat a Kafka-fürtök között.

    A MirrorMaker használatával további információkért lásd: [Apache Kafka-témakörök replikálása Apache Kafka on HDInsight](apache-kafka-mirroring.md).

* **Közzétételi-feliratkozási üzenetkezelési minta**: A Kafka egy előállítói API-t biztosít a közzétételi rekordok Kafka-témakörökbe. A fogyasztói API-ra a témakörökre való feliratkozáskor van szükség.

    További információkért lásd: [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md).

* **Stream-feldolgozás**: A Kafka gyakran használják valós idejű streamfeldolgozásra az Apache Stormmal vagy Sparkkal. A Kafka 0.10.0.0 (HDInsight 3.5-ös és 3.6-os verzió) egy olyan streamelési API-t vezetett be, amely lehetővé teszi a streammegoldások Storm vagy Spark nélküli fejlesztését.

    További információkért lásd: [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md).

* **Horizontális skálázás**: A Kafka szétosztja a streameket a HDInsight-fürt csomópontjai között. A fogyasztói folyamatok társíthatók az egyes partíciókkal, így biztosítható a terheléselosztás a rekordok használatakor.

    További információkért lásd: [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md).

* **Érkezési sorrendben történő kézbesítés**: Minden egyes partíción belül rögzíti az adatfolyamot érkezési sorrendben sorrendben vannak tárolva. Partíciónként egy fogyasztói folyamat társításával garantálhatja, hogy a rekordok feldolgozása érkezési sorrendben történjen.

    További információkért lásd: [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md).

## <a name="use-cases"></a>Használati esetek

* **Üzenetküldési**: Mivel támogatja a közzétételi-feliratkozási, a Kafkát gyakran használják üzenetközvetítőként.

* **Tevékenységkövetés**: A Kafka érkezési sorrendben naplózás a rekordok kínál, mivel a használat nyomon követése, és hozza létre a tevékenységek. Ilyen tevékenységek például a felhasználók műveletei egy webhelyen vagy egy alkalmazásban.

* **Összesítés**: Adatfolyam-feldolgozó, összesítheti a egyesítse és központosítsa az operatív adatokat nyerhet ki adatokat a különböző Streamek információit.

* **Átalakítás**: Streamfeldolgozás használatával egyesítheti és bővítheti az adatokat több bemeneti témakörből egy vagy több kimeneti témakörbe.

## <a name="next-steps"></a>További lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [Rövid útmutató: A HDInsight az Apache Kafka létrehozása](apache-kafka-get-started.md)

* [Oktatóanyag: Az Apache Kafka on HDInsight az Apache Spark használata](../hdinsight-apache-spark-with-kafka.md)

* [Oktatóanyag: Az Apache Kafka on HDInsight az Apache Storm használata](../hdinsight-apache-storm-with-kafka.md)
