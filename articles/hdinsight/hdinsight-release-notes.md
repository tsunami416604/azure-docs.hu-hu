---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: fd0412459e7d6e51b6abdccbc8782d157acee6b9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89319797"
---
# <a name="azure-hdinsight-release-notes"></a>Az Azure HDInsight kibocsátási megjegyzései

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

## <a name="release-date-08092020"></a>Kiadás dátuma: 08/09/2020

Ez a kiadás csak a 4,0-es HDInsight vonatkozik. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várja meg, amíg a kiadás több napon belül élő marad a régióban.

## <a name="new-features"></a>Új funkciók
### <a name="support-for-sparkcruise"></a>A SparkCruise támogatása
A SparkCruise egy automatikus számítási rendszer a Spark számára. Kijelöli az általános alkifejezéseket, amelyeket a korábbi lekérdezési munkaterhelés alapján kell megvalósulni. A SparkCruise ezeket az alkifejezéseket a lekérdezés feldolgozásának részeként, a számítások újrafelhasználását pedig automatikusan alkalmazza a háttérben. A Spark-kód módosítása nélkül is élvezheti a SparkCruise.
 
### <a name="support-hive-view-for-hdinsight-40"></a>A HDInsight 4,0-es verziójának támogatási struktúra-nézete
Az Apache Ambari kaptár nézet segítségével a webböngésző használatával hozhat létre, optimalizálhat és futtathat kaptár-lekérdezéseket. A kaptár nézet natív módon támogatott a HDInsight 4,0-fürtök esetében, ettől a kiadástól kezdve. A meglévő fürtökre nem vonatkozik. El kell dobnia, majd újra létre kell hoznia a fürtöt a beépített kaptár nézet beszerzéséhez.
 
### <a name="support-tez-view-for-hdinsight-40"></a>A HDInsight 4,0 TEZ-nézetének támogatása
Az Apache TEZ View segítségével nyomon követheti és hibakeresést végezhet a kaptár TEZ-feladata végrehajtásában. A TEZ nézet natív módon támogatott a HDInsight 4,0-es verziójától kezdve. A meglévő fürtökre nem vonatkozik. El kell dobnia, majd újra létre kell hoznia a fürtöt a beépített TEZ nézet beszerzéséhez.

## <a name="deprecation"></a>Elavulás
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>A Spark 2.1 és 2.2 elavulása a HDInsight 3.6 Kafka-fürtjein
Július 1 2020-től kezdődően az ügyfelek nem hozhatnak létre új Spark-fürtöket a Spark 2,1 és 2,2 HDInsight 3,6-ben. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Vegye figyelembe, hogy a Spark 2,3-ra való áttérés a HDInsight 30 2020 3,6-re, a lehetséges rendszer-és támogatási megszakítás elkerülése érdekében.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>A Spark 2.3 elavulása a HDInsight 4.0 Kafka-fürtjein
Július 1 2020-től kezdődően az ügyfelek nem hozhatnak létre új Spark-fürtöket a Spark 2,3-mel a HDInsight 4,0-on. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Fontolja meg, hogy a HDInsight 4.0-ban átvált a Spark 2.4-re 2020. június 30-ig, a rendszerhasználat/támogatás esetleges megszakításának elkerülése érdekében.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>A Kafka 1.1 elavulása a HDInsight 4.0 Kafka-fürtjein
Július 1 2020-től kezdődően az ügyfelek nem tudnak új Kafka-fürtöket létrehozni a HDInsight 4,0-on futó Kafka 1,1-mel. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Fontolja meg, hogy a HDInsight 4.0-ban átvált a Kafka 2.1-re 2020. június 30-ig, a rendszerhasználat/támogatás esetleges megszakításának elkerülése érdekében.

## <a name="behavior-changes"></a>Viselkedési változások
### <a name="ambari-stack-version-change"></a>Ambari-verem verziójának módosítása
Ebben a kiadásban a Ambari verziója 2. x. x. x és 4,1 között változik. A Ambari: Ambari > felhasználói > verziójában ellenőrizheti a verem verzióját (HDInsight 4,1).

## <a name="upcoming-changes"></a>Közelgő változások
Nincs szükség a közelgő változtatásokra, amelyekre figyelnie kell.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

Az alábbi JIRAs a struktúra számára:
* [STRUKTÚRA – 23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [STRUKTÚRA – 21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [STRUKTÚRA – 22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [STRUKTÚRA – 22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [STRUKTÚRA – 22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [STRUKTÚRA – 18786](https://issues.apache.org/jira/browse/HIVE-18786)

Az alábbi JIRAs a HBase-re vannak portolva:
* [HBASE-21458](https://issues.apache.org/jira/browse/HBASE-21458)
* [HBASE-24208](https://issues.apache.org/jira/browse/HBASE-24208)
* [HBASE-24205](https://issues.apache.org/jira/browse/HBASE-24205)

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Ehhez a kiadáshoz nem módosult az összetevő verziószáma. A HDInsight 4,0 és a HDInsight 3,6 aktuális összetevő-verzióit ebben a [dokumentumban](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)találja.

## <a name="known-issues"></a>Ismert problémák

Javítva lett az Azure Portal azon hibája, amely miatt amikor a felhasználók Azure HDInsight-fürtöt hoztak létre SSH-hitelesítéses típusú nyilvános kulcs használatával, hibaüzenetet kaptak. Amikor a felhasználók rákattintottak a **Felülvizsgálat + létrehozás** gombra, „A jelszó nem tartalmazhatja az SSH-felhasználónév semmilyen egymást követő három karakterét” hibaüzenet jelent meg. Ezt a problémát megoldottuk, de előfordulhat, hogy a CTRL + F5 billentyűkombinációval frissítenie kell a böngésző gyorsítótárát a javított nézet betöltéséhez. A probléma megkerülő megoldása a fürt ARM-sablonnal történő létrehozása volt. 
