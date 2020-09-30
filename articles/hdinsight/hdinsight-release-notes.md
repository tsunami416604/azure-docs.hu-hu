---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535610"
---
# <a name="azure-hdinsight-release-notes"></a>Az Azure HDInsight kibocsátási megjegyzései

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

## <a name="release-date-09282020"></a>Kiadás dátuma: 09/28/2020

Ez a kiadás a 3,6-es és a HDInsight 4,0-es HDInsight egyaránt érvényes. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várja meg, amíg a kiadás több napon belül élő marad a régióban.

## <a name="new-features"></a>Új funkciók
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>Mostantól általánosan elérhető az interaktív lekérdezés az HDInsight 4,0-mel.
Az interaktív lekérdezési fürt típusának automatikus méretezése már általánosan elérhető (GA) a HDInsight 4,0-es verzióhoz. Az 4,0.2020 augusztus 27. után létrehozott összes interaktív lekérdezési fürt automatikusan támogatja az automatikus skálázást.

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>A HBase-fürt támogatja a prémium szintű ADLS Gen2
A HDInsight mostantól támogatja a prémium szintű ADLS Gen2 elsődleges HDInsight a HBase 3,6 és 4,0 fürtök esetében. A [gyorsított írásokkal](./hbase/apache-hbase-accelerated-writes.md)együtt jobb teljesítményt érhet el a HBase-fürtökhöz.

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Kafka-partíciók eloszlása az Azure tartalék tartományokban
A tartalék tartomány az alapul szolgáló hardver logikai csoportosítása egy Azure-adatközpontban. Mindegyik tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkezik. A HDInsight Kafka a partíció replikáit ugyanabban a tartalék tartományban tárolja. Ettől a kiadástól kezdve a HDInsight mostantól támogatja a Kafka-partíciók automatikus terjesztését az Azure tartalék tartományok alapján. 

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Az ügyfelek engedélyezhetik a fürt csomópontjai közötti átvitelt az IPSec-titkosítással a platform által felügyelt kulcsokkal. Ez a beállítás a fürt létrehozási idején engedélyezhető. További információ a [titkosítás engedélyezéséről az átvitel során](./domain-joined/encryption-in-transit.md).

### <a name="encryption-at-host"></a>Titkosítás a gazdagépen
Amikor engedélyezi a titkosítást a gazdagépen, a virtuálisgép-gazdagépen tárolt adatok titkosítva maradnak a tárolási szolgáltatásba titkosított adatforgalomban. Ebben a kiadásban **engedélyezheti a titkosítást a gazdagépen az ideiglenes adatlemezen** a fürt létrehozásakor. A gazdagépen lévő titkosítás csak [bizonyos, korlátozott régiókban lévő virtuális gépek esetében](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal)támogatott. A HDInsight a [következő csomópont-konfigurációt és SKU-](./hdinsight-supported-node-configuration.md)t támogatja. További információ a [titkosítás engedélyezéséről a gazdagépen](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight mostantól Azure-beli virtuális gépeket használ a fürt kiépítéséhez. Ettől a kiadástól kezdve a szolgáltatás fokozatosan migrálva lesz az [Azure virtuálisgép-méretezési csoportokra](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). A teljes folyamat hónapokat is igénybe vehet. A régiók és az előfizetések migrálása után az újonnan létrehozott HDInsight-fürtök felhasználói műveletek nélkül futnak a virtuálisgép-méretezési csoportokban. A rendszer nem várt változást.

## <a name="deprecation"></a>Elavulás
Nincs elavulás ehhez a kiadáshoz.

## <a name="behavior-changes"></a>Viselkedési változások
Ebben a kiadásban nem változik a viselkedés.

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni.

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Különböző Zookeeper SKU kiválasztásának lehetősége Spark, Hadoop és ML szolgáltatásokhoz
A HDInsight jelenleg nem támogatja a Spark, a Hadoop és a ML szolgáltatások Zookeeperének módosítását. A2_v2/a2 SKU-t használ a Zookeeper-csomópontokhoz, és az ügyfelek számára nem számítunk fel díjat. A következő kiadásban az ügyfelek igény szerint módosíthatják a Spark, a Hadoop és a ML szolgáltatásokhoz tartozó Zookeeper SKU-t. A A2_v2/a2 eltérő SKU-val rendelkező Zookeeper-csomópontokat a rendszer felszámítja. Az alapértelmezett SKU továbbra is A2_V2/a2 és díjmentes.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Ehhez a kiadáshoz nem módosult az összetevő verziószáma. A HDInsight 4,0 és a HDInsight 3,6 aktuális összetevő-verzióit ebben a [dokumentumban](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)találja.