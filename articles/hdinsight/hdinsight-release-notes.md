---
title: Az Azure HDInsight kibocsátási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435412"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében a nyílt forráskódú Apache Hadoop és az Azure-beli Apache Spark-elemzések terén.

## <a name="release-date-12172019"></a>Kiadás dátuma: 12/17/2019

Ez a kiadás a 3,6-es és a 4,0-es HDInsight is érvényes.

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információ: [HDInsight verziószámozási cikk](hdinsight-component-versioning.md).


## <a name="new-features"></a>ÚJ funkciók

### <a name="service-tags"></a>Szolgáltatáscímkék
A szolgáltatási címkék egyszerűbbé teszik az Azure-beli virtuális gépek és az Azure-beli virtuális hálózatok biztonságát azáltal, hogy könnyedén korlátozzák az Azure-szolgáltatásokhoz való hálózati hozzáférést. A hálózati biztonsági csoport (NSG) szabályaiban található szolgáltatási címkék használatával engedélyezheti vagy megtagadhatja a forgalmat egy adott Azure-szolgáltatás számára globálisan vagy Azure-régión keresztül. Az Azure biztosítja az egyes címkéken alapuló IP-címek karbantartását. A hálózati biztonsági csoportokhoz (NSG) tartozó HDInsight-szolgáltatási címkék az állapot-és felügyeleti szolgáltatások IP-címeinek csoportjai. Ezek a csoportok segítenek a biztonsági szabályok létrehozásának összetettségének minimalizálásában. A HDInsight-ügyfelek az Azure Portal, a PowerShell és a REST API használatával engedélyezhetik a szolgáltatás címkéit. További információ: [hálózati biztonsági csoport (NSG) szolgáltatás címkéi az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags).

### <a name="custom-ambari-db"></a>Egyéni Ambari-adatbázis
A HDInsight mostantól lehetővé teszi saját SQL-adatbázis használatát az Apache Ambari. Ezt az egyéni Ambari-adatbázist a Azure Portal vagy a Resource Manager-sablon használatával konfigurálhatja.  Ez a funkció lehetővé teszi a megfelelő SQL-adatbázis kiválasztását a feldolgozási és a kapacitási igényekhez. Az üzleti növekedési követelményeknek való megfelelést is könnyedén frissítheti. További információ: HDInsight- [fürtök beállítása egyéni Ambari-adatbázissal](hdinsight-custom-ambari-db.md).

![Egyéni Ambari-adatbázis](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>Elavulás
Nincs elavultság ehhez a kiadáshoz. A közelgő elavulás elkezdéséhez tekintse meg a [közelgő változásokat](#upcoming-changes).

## <a name="behavior-changes"></a>Viselkedésbeli változások
Ebben a kiadásban nem változik a viselkedés. A közelgő viselkedés változásairól a [közelgő változások](#upcoming-changes)című szakaszban olvashat.

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni. 

### <a name="transport-layer-security-tls-12-enforcement"></a>Transport Layer Security (TLS) 1,2 kényszerítés
A Transport Layer Security (TLS) és a SSL (SSL) olyan titkosítási protokollok, amelyek kommunikációs biztonságot biztosítanak a számítógép hálózatán. További információ: [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). Míg az Azure HDInsight-fürtök elfogadják a TLS 1,2-kapcsolatokat a nyilvános HTTPS-végpontokon, a TLS 1,1 továbbra is támogatott a régebbi ügyfelekkel való visszamenőleges kompatibilitás érdekében.

A következő kiadástól kezdve lehetőség van arra, hogy bekapcsolja és konfigurálja az új HDInsight-fürtöket, hogy csak a TLS 1,2-kapcsolatokat fogadja el. 

Az év későbbi részében az Azure HDInsight a 6/30/2020-es verziótól kezdődően a TLS 1,2-es vagy újabb verzióját fogja érvényesíteni az összes HTTPS-kapcsolathoz. Javasoljuk, hogy minden ügyfél készen álljon a TLS 1,2-es vagy újabb verzióinak kezelésére.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight mostantól Azure-beli virtuális gépeket használ a fürt kiépítéséhez. A Feburary 2020-től kezdődően (a pontos dátumot később közli), a HDInsight az Azure virtuálisgép-méretezési csoportokat fogja használni helyette. További információ az [Azure-beli virtuálisgép-méretezési csoportokról](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark-fürt csomópontjának méretének változása 
A következő kiadásban:
- Az ESP Spark-fürt minimálisan engedélyezett csomópontjának mérete Standard_D13_V2ra lesz módosítva. 
- Az "a" sorozatú virtuális gépek elavultak lesznek az új ESP-fürtök létrehozásához, mivel A-sorozatú virtuális gépek az ESP-fürtöket a viszonylag alacsony CPU-és memória-kapacitás miatt okozhatják.

### <a name="hbase-20-to-21"></a>HBase 2,0 – 2,1
A közelgő HDInsight 4,0-es kiadásban a HBase verziója a 2,0-es verzióról 2,1-ra lesz frissítve.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Bővítettük a HDInsight 3,6 támogatást 2020. december 31-ig. További részleteket a [támogatott HDInsight-verziókban](hdinsight-component-versioning.md#supported-hdinsight-versions)találhat.

Az HDInsight 4,0-es verziójának összetevő-verziója nem módosult.

Apache Zeppelin a HDInsight 3,6:0.7.0--> 0.7.3. 

[Ebből a dokumentumból](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)megtekintheti a legfrissebb összetevő-verziókat.

## <a name="new-regions"></a>Új régiók

### <a name="uae-north"></a>Egyesült Arab Emírségek északi régiója
Az Észak-UAE felügyeleti IP-címei a következők: `65.52.252.96` és `65.52.252.97`.
