---
title: Az Azure HDInsight kibocsátási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 5d9ca8d0df3eb0186add5c40765c87a4409a5660
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926423"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében a nyílt forráskódú Apache Hadoop és az Azure-beli Apache Spark-elemzések terén.

## <a name="release-date-11072019"></a>Kiadás dátuma: 11/07/2019

Ez a kiadás a 3,6-es és a 4,0-es HDInsight is érvényes.

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információ: [HDInsight verziószámozási cikk](hdinsight-component-versioning.md).


## <a name="new-features"></a>ÚJ funkciók

### <a name="hdinsight-identity-broker-hib-preview"></a>HDInsight Identity Broker (HIB) (előzetes verzió)

A HDInsight Identity Broker (HIB) lehetővé teszi a felhasználók számára, hogy a többtényezős hitelesítés (MFA) használatával bejelentkezzenek az Apache Ambari, és lekérik a szükséges Kerberos-jegyeket anélkül, hogy jelszó-kivonatokat kellene Azure Active Directory Domain Services (HRE-DS). Jelenleg a HIB csak ARM-sablonnal üzembe helyezett fürtök esetében érhető el.

### <a name="kafka-rest-api-proxy-preview"></a>Kafka REST API-proxy (előzetes verzió)

A Kafka REST API proxy a biztonságos HRE-engedélyezési és OAuth protokollon keresztül egykattintásos üzembe helyezést biztosít a Kafka-fürtön keresztül. 

### <a name="auto-scale"></a>Automatikus méretezés

Az Azure HDInsight-ra való autoskálázás mostantól általánosan elérhető minden régióban Apache Spark és Hadoop. Ez a funkció lehetővé teszi a big data elemzési számítási feladatok költséghatékony és hatékonyabb kezelését. Most már optimalizálhatja a HDInsight-fürtök használatát, és csak azért kell fizetnie, amire szüksége van.

A követelményektől függően választhat a terhelésalapú és az ütemezésalapú automatikus skálázás között. A terhelésen alapuló automatikus méretezés a fürt méretét az aktuális erőforrás-igényeknek megfelelően méretezheti, míg az ütemezett automatikus skálázás a fürt méretét előre meghatározott ütemterv alapján módosíthatja. 

A HBase és a LLAP számítási feladatának funkciói a nyilvános előzetes verzióban is elérhetők. További információ: az [Azure HDInsight-fürtök automatikus méretezése](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters).

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>HDInsight gyorsított írások az Apache HBase 

A Gyorsított írási műveletek az Azure prémium szintű felügyelt SSD lemezeit használja az Apache HBase Előre írt napló (WAL) szolgáltatásához. További információ: [Azure HDInsight-beli gyorsított írási műveletek Apache HBase-hez](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes)

### <a name="custom-ambari-db"></a>Egyéni Ambari-adatbázis

A HDInsight mostantól új kapacitást kínál, amely lehetővé teszi, hogy az ügyfelek a saját SQL-ADATBÁZISát használják a Ambari. Mostantól az ügyfelek kiválaszthatják a megfelelő SQL-adatbázist a Ambari, és a saját üzleti növekedési követelményük alapján egyszerűen frissíthetik azt. Az üzembe helyezés egy Azure Resource Manager sablonnal történik. További információ: HDInsight- [fürtök beállítása egyéni Ambari-adatbázissal](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db).

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>Az F sorozatú virtuális gépek mostantól elérhetők a HDInsight

Az F-sorozatú virtuális gépek (VM-EK) jó választást kapnak a HDInsight és a könnyű feldolgozási követelményekkel való ismerkedéshez. Az alacsonyabb óránkénti listaáron elérhető F-sorozat képviseli a legjobb ár–teljesítmény arányt az Azure-portfólióban az egy vCPU-ra jutó Azure-alapú számítási egységek (Azure Compute Unit, ACU) alapján. További információ: [a megfelelő virtuálisgép-méret kiválasztása az Azure HDInsight-fürthöz](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size).

## <a name="deprecation"></a>Elavulás

### <a name="g-series-virtual-machine-deprecation"></a>A G sorozatú virtuális gép elavult
Ebből a kiadásból a G sorozatú virtuális gépeket már nem ajánljuk fel a HDInsight.

### <a name="dv1-virtual-machine-deprecation"></a>Dv1 virtuális gép elavult
Ettől a kiadástól kezdve a Dv1 virtuális gépek HDInsight-mel való használata elavult. A Dv1-hez tartozó összes ügyfél-kérelem automatikusan a Dv2 lesz kézbesítve. A Dv1 és a Dv2 virtuális gépek között nincs árkülönbség.

## <a name="behavior-changes"></a>Viselkedésbeli változások

### <a name="cluster-managed-disk-size-change"></a>Fürtözött felügyelt lemez méretének változása
A HDInsight felügyelt lemezterületet biztosít a fürt számára. Ebből a kiadásból az új létrehozott fürt egyes csomópontjainak felügyelt lemez mérete 128 GB-ra módosul.

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight mostantól Azure-beli virtuális gépeket használ a fürt kiépítéséhez. Decembertől kezdődően a HDInsight az Azure virtuálisgép-méretezési csoportokat fogja használni helyette. További információ az [Azure-beli virtuálisgép-méretezési csoportokról](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="hbase-20-to-21"></a>HBase 2,0 – 2,1
A közelgő HDInsight 4,0-es kiadásban a HBase verziója a 2,0-es verzióról 2,1-ra lesz frissítve.

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>Az a sorozatú virtuális gépek elavulása ESP-fürt esetén
Az A sorozatú virtuális gépek az ESP-fürtökkel kapcsolatos problémákat okozhatnak viszonylag alacsony CPU-és memória-kapacitás miatt. A következő kiadásban az A sorozatú virtuális gépek elavultak lesznek az új ESP-fürtök létrehozásához.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Ehhez a kiadáshoz nem módosítható összetevő-verzió. A HDInsight 4,0 és a HDInsight 3,6 aktuális összetevő-verzióit [itt](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)találja.
