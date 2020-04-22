---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kiadási feljegyzései. Fejlesztési tippeket és részleteket kaphat a Hadoophoz, a Sparkhoz, az R Serverhez, a Hive-hoz és egyebekhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: cdb31f1a46d7f46b69e9e0ad47a77ba6b32a50a0
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770831"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

Ez a cikk az Azure HDInsight **legújabb** kiadásfrissítéseivel kapcsolatos információkat tartalmazza. A korábbi kiadásokról a [HDInsight Release Notes Archive című témakörben](hdinsight-release-notes-archive.md)talál további információt.

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

## <a name="release-date-01092020"></a>Megjelenési dátum: 01/09/2020

Ez a kiadás a HDInsight 3.6-os és a 4.0-s felbontásra egyaránt vonatkozik. A HDInsight-kiadás több napon keresztül minden régió számára elérhetővé válik. A kiadás dátuma itt az első régió kiadási dátumát jelzi. Ha az alábbi módosításoknem láthatók, kérjük, várjon, amíg a kiadás néhány napon belül megjelenik a régióban.

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információt a [HDInsight verziószámozási cikkében talál.](hdinsight-component-versioning.md)

## <a name="new-features"></a>Új funkciók
### <a name="tls-12-enforcement"></a>A TLS 1.2 kényszerítése
A Transport Layer Security (TLS) és a Secure Sockets Layer (SSL) olyan kriptográfiai protokollok, amelyek számítógépes hálózaton keresztül biztosítják a kommunikáció biztonságát. További információ a [TLS-ről.](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0) A HDInsight a TLS 1.2-t használja nyilvános HTTPs-végpontokon, de a TLS 1.1 továbbra is támogatott a visszamenőleges kompatibilitás érdekében. 

Ezzel a kiadással az ügyfelek csak a nyilvános fürtvégponton keresztül i kapcsolatot engedélyezhetik a TLS 1.2-es szintre. Ennek támogatása érdekében az új tulajdonság **minSupportedTlsVersion** kerül bevezetésre, és meg adható a fürt létrehozása során. Ha a tulajdonság nincs beállítva, a fürt továbbra is támogatja a TLS 1.0, 1.1 és 1.2, amely megegyezik a mai viselkedését. Az ügyfelek beállíthatják a tulajdonság értékét "1.2", ami azt jelenti, hogy a fürt csak támogatja a TLS 1.2 és újabb. További információ: [Transport Layer Security](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Saját kulcs hozása a lemeztitkosításhoz
A HDInsight összes felügyelt lemeze az Azure Storage Service Encryption (SSE) szolgáltatással védett. Az ezeken a lemezeken lévő adatokat alapértelmezés szerint a Microsoft által kezelt kulcsok titkosítják. Ebből a kiadásból kiindulva saját kulcsot (BYOK) hozhat létre a lemeztitkosításhoz, és kezelheti azt az Azure Key Vault használatával. A BYOK-titkosítás egy egylépéses konfiguráció a fürt létrehozása során, további költségek nélkül. Csak regisztrálja a HDInsight-ot felügyelt identitásként az Azure Key Vaultban, és adja hozzá a titkosítási kulcsot a fürt létrehozásakor. További információt az [Ügyfél által kezelt kulcslemez-titkosítás](https://docs.microsoft.com/azure/hdinsight/disk-encryption)című témakörben talál.

## <a name="deprecation"></a>Elavulás
Ehhez a kiadáshoz nincsek eedek. A közelgő eprekationokra való felkészülésről a [Közelgő módosítások (Közelgő módosítások) témakörben kaphatunk felkészülést.](#upcoming-changes)

## <a name="behavior-changes"></a>Viselkedésbeli változások
Ebben a kiadásban nincs változás a viselkedésben. A közelgő változásokra való felkészülésről a Közelgő módosítások (Közelgő módosítások) témakörben [kaphatunk felkészülést.](#upcoming-changes)

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a következő kiadásokban fognak bekövetkezni. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>A head node-hoz legalább 4 magos virtuális gép szükséges 
A head-node-hoz a HDInsight-fürtök magas rendelkezésre állásának és megbízhatóságának biztosításához legalább 4 magos virtuális gép szükséges. 2020. április 6-tól kezdődően az ügyfelek csak 4 magos vagy annál magasabb virtuális gép et választhatnak az új HDInsight-fürtök főcsomópontjaként. A meglévő fürtök továbbra is a várt módon futnak. 

### <a name="esp-spark-cluster-node-size-change"></a>AZ ESP Spark-fürtcsomópont méretének változása 
A következő kiadásban az ESP Spark-fürt minimálisan engedélyezett csomópontmérete Standard_D13_V2. Az A sorozatú virtuális gépek a viszonylag alacsony processzor- és memóriakapacitás miatt esp-fürtproblémákat okozhatnak. Az A sorozatú virtuális gépek elavultak lesznek az új ESP-fürtök létrehozásához.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés az Azure virtuálisgép-méretezési csoportjaira
A HDInsight mostantól azure-beli virtuális gépeket használ a fürt kiépítéséhez. A következő kiadásban a HDInsight az Azure virtuálisgép-méretezési készleteket fogja használni. További információk az Azure virtuálisgép-méretezési csoportjairól.

### <a name="hbase-20-to-21"></a>HBase 2.0-2.1
A hdinsight 4.0-s verziójában a HBase verziója a 2.0-s verzióról a 2.1-es verzióra frissül.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is javítja a fürt megbízhatóságát és teljesítményét. 

## <a name="component-version-change"></a>Az összetevő verziómódosítása
Ehhez a kiadáshoz nincs összetevőverzió-változás. A HDInsight 4.0-s hdinsight 3.6-os verziójának jelenlegi összetevő-verzióit itt találja.

## <a name="known-issues"></a>Ismert problémák

2020. március 18-tól néhány Nyugat-Európában vagy Észak-Európában lévő Azure HDInsight-ügyfél hibaüzenetet kapott a HDInsight-fürtök létrehozásakor vagy méretezésekor ezekben a régiókban. A problémával kapcsolatos hibák a következők:

- Belső kiszolgálóhiba történt a kérelem feldolgozása közben. Próbálkozzon újra a kéréssel, vagy forduljon az ügyfélszolgálathoz.
- Legalább egy erőforrás-telepítési művelet sikertelen volt. A részleteket a központi telepítési műveletek listája tartalmazza. Kérjük, olvassa el https://aka.ms/DeployOperations a használati adatokat
- A User SubscriptionId\<\>' Subscription ID ' nem\<rendelkezik a "fürtnév>" erőforrás létrehozásához megmaradt magokkal. Kötelező: \<\>X , Elérhető: 0.

A mérnökök tisztában vannak ezzel a problémával, és aktívan vizsgálják.

További segítségért hozzon létre [egy támogatási kérelmet.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
