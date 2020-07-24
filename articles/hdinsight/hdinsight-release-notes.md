---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ef243d5b151f95a00e22ac7636a46b93090ccce3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006534"
---
# <a name="azure-hdinsight-release-notes"></a>Az Azure HDInsight kibocsátási megjegyzései

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összegzés

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

## <a name="release-date-07132020"></a>Kiadás dátuma: 07/13/2020

Ez a kiadás a 3,6-es és a 4,0-es HDInsight is érvényes. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várja meg, amíg a kiadás több napon belül élő marad a régióban.

## <a name="new-features"></a>Új funkciók
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>A Microsoft Azure Ügyfélszéf támogatása
Az Azure HDInsight mostantól támogatja az Azure Ügyfélszéf. Felületet biztosít az ügyfelek számára az ügyféladatok hozzáférési kéréseinek áttekintéséhez és jóváhagyásához vagy elutasításához. Akkor használatos, amikor a Microsoft mérnöknek egy támogatási kérelem során hozzá kell férnie az ügyféladatok eléréséhez. További információ: [Ügyfélszéf Microsoft Azurehoz](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview).

### <a name="service-endpoint-policies-for-storage"></a>Szolgáltatás-végponti házirendek tárolóhoz
Az ügyfelek mostantól a HDInsight-fürt alhálózatán is használhatnak szolgáltatási végponti házirendeket (SEP). További információ az [Azure-szolgáltatás végpont-házirendjéről](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="deprecation"></a>Elavulás
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>A Spark 2.1 és 2.2 elavulása a HDInsight 3.6 Kafka-fürtjein
Július 1 2020-től kezdődően az ügyfelek nem hozhatnak létre új Spark-fürtöket a Spark 2,1 és 2,2 HDInsight 3,6-ben. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Vegye figyelembe, hogy a Spark 2,3-ra való áttérés a HDInsight 30 2020 3,6-re, a lehetséges rendszer-és támogatási megszakítás elkerülése érdekében.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>A Spark 2.3 elavulása a HDInsight 4.0 Kafka-fürtjein
Július 1 2020-től kezdődően az ügyfelek nem hozhatnak létre új Spark-fürtöket a Spark 2,3-mel a HDInsight 4,0-on. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Fontolja meg, hogy a HDInsight 4.0-ban átvált a Spark 2.4-re 2020. június 30-ig, a rendszerhasználat/támogatás esetleges megszakításának elkerülése érdekében.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>A Kafka 1.1 elavulása a HDInsight 4.0 Kafka-fürtjein
Július 1 2020-től kezdődően az ügyfelek nem tudnak új Kafka-fürtöket létrehozni a HDInsight 4,0-on futó Kafka 1,1-mel. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Fontolja meg, hogy a HDInsight 4.0-ban átvált a Kafka 2.1-re 2020. június 30-ig, a rendszerhasználat/támogatás esetleges megszakításának elkerülése érdekében.

## <a name="behavior-changes"></a>Viselkedési változások
Nincsenek viselkedési változások, amelyekre figyelnie kell.

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Különböző Zookeeper SKU kiválasztásának lehetősége Spark, Hadoop és ML szolgáltatásokhoz
A HDInsight jelenleg nem támogatja a Spark, a Hadoop és a ML szolgáltatások Zookeeperének módosítását. A2_v2/a2 SKU-t használ a Zookeeper-csomópontokhoz, és az ügyfelek számára nem számítunk fel díjat. A következő kiadásban az ügyfelek igény szerint módosíthatják a Spark, a Hadoop és a ML szolgáltatások Zookeeper SKU-át. A A2_v2/a2 eltérő SKU-val rendelkező Zookeeper-csomópontokat a rendszer felszámítja. Az alapértelmezett SKU továbbra is A2_V2/a2 és díjmentes.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Hiba történt a méhkas-tárház-összekötő kijavításakor
Hiba történt a kaptár-összekötő az előző kiadásban való használhatósága során. A probléma kijavítva. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>Rögzített Zeppelin-jegyzetfüzet levágja a vezető nulla problémát
A Zeppelin helytelenül csonkítja a tábla kimenetének kezdő nulláit karakterlánc formátumra. Ebben a kiadásban kijavítottuk ezt a problémát.

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Ehhez a kiadáshoz nem módosult az összetevő verziószáma. A HDInsight 4,0 és a HDInsight 3,6 aktuális összetevő-verzióit ebben a [dokumentumban](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)találja.
