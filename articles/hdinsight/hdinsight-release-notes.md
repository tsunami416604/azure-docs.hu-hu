---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: b7489c49b7469feacfd5b982615419741d286998
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849705"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

## <a name="release-date-01092020"></a>Kiadás dátuma: 01/09/2020

Ez a kiadás a 3,6-es és a 4,0-es HDInsight is érvényes. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várjon, amíg a kiadás több napon belül élő marad a régióban.

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információ: [HDInsight verziószámozási cikk](hdinsight-component-versioning.md).

## <a name="new-features"></a>Új funkciók
### <a name="tls-12-enforcement"></a>A TLS 1.2 kényszerítése
A Transport Layer Security (TLS) és a SSL (SSL) olyan titkosítási protokollok, amelyek kommunikációs biztonságot biztosítanak a számítógép hálózatán. További információ a [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)-ről. A HDInsight TLS 1,2-t használ a nyilvános HTTPs-végpontokon, de a TLS 1,1 továbbra is támogatott a visszamenőleges kompatibilitás érdekében. 

Ebben a kiadásban az ügyfelek csak a nyilvános fürt végpontján keresztül csatlakozhatnak a TLS 1,2-es kapcsolatokhoz. Ennek támogatásához a **minSupportedTlsVersion** új tulajdonsága be van vezetve, és a fürt létrehozása során megadható. Ha a tulajdonság nincs beállítva, a fürt továbbra is támogatja a TLS 1,0, 1,1 és 1,2 protokollt, amely megegyezik a mai viselkedésével. Az ügyfelek a tulajdonság értékét "1,2" értékre állíthatja, ami azt jelenti, hogy a fürt csak a TLS 1,2-es vagy újabb verzióját támogatja. További információ: [Transport Layer Security](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Saját kulcs használata a lemezes titkosításhoz
A HDInsight összes felügyelt lemeze az Azure Storage Service Encryption (SSE) védelemmel van ellátva. A lemezeken tárolt adatforgalom alapértelmezés szerint a Microsoft által felügyelt kulcsokkal van titkosítva. Ettől a kiadástól kezdve a lemezes titkosításhoz Bring Your Own Key (BYOK) és a Azure Key Vault használatával felügyelheti. A BYOK-titkosítás egy egylépéses konfiguráció a fürt létrehozása során, többletköltség nélkül. Csak regisztrálja a HDInsight felügyelt identitásként Azure Key Vault és adja hozzá a titkosítási kulcsot a fürt létrehozásakor. További információ: [ügyfél által felügyelt kulcs lemezének titkosítása](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Elavulás
Nincs elavultság ehhez a kiadáshoz. A közelgő elavulás elkezdéséhez tekintse meg a [közelgő változásokat](#upcoming-changes).

## <a name="behavior-changes"></a>Viselkedési változások
Ebben a kiadásban nem változik a viselkedés. A közelgő változásokról a [közelgő változások](#upcoming-changes)című szakaszban olvashat.

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni. 

### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>A Spark 2,1-es és 2,2-es verziójának elavult változata a HDInsight 3,6 Spark-fürtben
Július 1 2020-től kezdődően az ügyfelek nem tudnak új Spark-fürtöket létrehozni a Spark 2,1 és a 2,2 használatával a HDInsight 3,6-ben. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Vegye figyelembe, hogy a Spark 2,3-ra való áttérés a HDInsight 30 2020 3,6-re, a lehetséges rendszer-és támogatási megszakítás elkerülése érdekében.

### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>A Spark 2,3 elavult változata a HDInsight 4,0 Spark-fürtben
Július 1 2020-től kezdve az ügyfelek nem tudnak új Spark-fürtöket létrehozni a Spark 2,3-mel a HDInsight 4,0-on. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Vegye fontolóra a Spark 2,4-ra való áttérést a HDInsight 30 2020 4,0-re a lehetséges rendszer/támogatás megszakadásának elkerülése érdekében.

### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Kafka 1,1 elavult változata a HDInsight 4,0 Kafka-fürtben
Július 1 2020-től kezdődően az ügyfelek nem tudnak új Kafka-fürtöket létrehozni a HDInsight 4,0-on futó Kafka 1,1-mel. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. A potenciális rendszer/támogatás megszakításának elkerülése érdekében érdemes áthelyezni a Kafka 2,1-et a HDInsight 30 2020 4,0-re.

### <a name="hbase-20-to-216"></a>HBase 2,0 – 2.1.6
A közelgő HDInsight 4,0-es kiadásban a HBase verziója a 2,0-es verzióról a 2.1.6-re lesz frissítve.

### <a name="spark-240-to-244"></a>Spark 2.4.0 – 2.4.4
A közelgő HDInsight 4,0-es kiadásban a Spark verziója a 2.4.0 verzióról a 2.4.4-re lesz frissítve.

### <a name="kafka-210-to-211"></a>Kafka 2.1.0 – 2.1.1
A közelgő HDInsight 4,0-es kiadásban a Kafka verziója a 2.1.0 verzióról 2.1.1-re lesz frissítve.

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>A fő csomóponthoz legalább 4 magos virtuális gépnek kell tartoznia 
A HDInsight-fürtök magas rendelkezésre állásának és megbízhatóságának biztosítása érdekében legalább 4 magos virtuális gépnek kell lennie a fő csomópont számára. Április 6 2020-től kezdődően az ügyfelek csak 4 magos vagy annál újabb virtuális gépet választhatnak az új HDInsight-fürtök fő csomópontjának kiválasztásával. A meglévő fürtök továbbra is a várt módon fognak futni. 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark-fürt csomópontjának méretének változása 
A következő kiadásban az ESP Spark-fürt minimálisan megengedett csomópontjának mérete Standard_D13_V2re változik. Az A sorozatú virtuális gépek az ESP-fürtöket a viszonylag alacsony CPU-és memória-kapacitás miatt okozhatják. Az "a" sorozatú virtuális gépek elavulttá válik az új ESP-fürtök létrehozásához.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight mostantól Azure-beli virtuális gépeket használ a fürt kiépítéséhez. A következő kiadásban a HDInsight az Azure virtuálisgép-méretezési csoportokat fogja használni helyette. További információ az Azure-beli virtuálisgép-méretezési csoportokról.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Ehhez a kiadáshoz nem módosult az összetevő verziószáma. A HDInsight 4,0 ad HDInsight 3,6 aktuális összetevő-verzióit itt találja.

