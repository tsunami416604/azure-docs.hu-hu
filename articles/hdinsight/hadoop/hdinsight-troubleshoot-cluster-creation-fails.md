---
title: Fürtlétrehozási hibák elhárítása az Azure HDInsight segítségével
description: Ismerje meg, hogyan háríthatja el az Apache-fürt létrehozási problémáit az Azure HDInsight hoz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.openlocfilehash: 1e13c7ef8eae81ef2a12a8761b0596f6329f94dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937907"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Fürtlétrehozási hibák elhárítása az Azure HDInsight segítségével

A fürtlétrehozási hibák leggyakoribb kiváltó okai a következő problémák:

- Engedélyekkel kapcsolatos problémák
- Erőforrás-házirendre vonatkozó korlátozások
- Tűzfalak
- Erőforrás-zárolások
- Nem támogatott összetevő-verziók
- Tárfiók névkorlátozásai
- Szolgáltatáskimaradások

## <a name="permissions-issues"></a>Engedélyekkel kapcsolatos problémák

Ha az Azure Data Lake Storage Gen2 ```AmbariClusterCreationFailedErrorCode```használatával ```Internal server error occurred while processing the request. Please retry the request or contact support.```jár, és a hibaüzenetet kapja, nyissa meg az Azure Portalt, nyissa meg a storage-fiókot, és a hozzáférés-vezérlés (IAM) területen győződjön meg arról, hogy a **Storage Blob Data Contributor** vagy a Storage Blob Data **Owner** szerepkör hozzárendelt hozzáféréssel rendelkezik az **előfizetéshez hozzárendelt felügyelt identitáshoz.** További információt [A felügyelt identitásra vonatkozó engedélyek beállítása a Data Lake Storage Gen2-fiókban](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) című szakaszban találhat.

Ha az Azure Data Lake Storage Gen1 szolgáltatást használja, [itt](../hdinsight-hadoop-use-data-lake-store.md)olvashat a beállítási és konfigurációs utasításokról. A Data Lake Storage Gen1 nem támogatott a HBase-fürtök számára, és a HDInsight 4.0-s verziója nem.

Az Azure Storage használata esetén győződjön meg arról, hogy a tárfiók neve érvényes a fürt létrehozása során.

## <a name="resource-policy-restrictions"></a>Erőforrás-házirendre vonatkozó korlátozások

Az előfizetés-alapú Azure-szabályzatok megtagadhatják a nyilvános IP-címek létrehozását. A HDInsight-fürt létrehozásához két nyilvános IP-cím szükséges.  

A következő házirendek általában hatással lehetnek a fürt létrehozására:

* Az IP-cím & terheléselosztók létrehozását megakadályozó házirendek az előfizetésen belül.
* A tárfiók létrehozását megakadályozó házirend.
* A hálózati erőforrások törlését megakadályozó házirend (IP-cím /terheléselosztók).

## <a name="firewalls"></a>Tűzfalak

A virtuális hálózaton vagy a tárfiókban lévő tűzfalak megtagadhatják a HDInsight-felügyeleti IP-címekkel való kommunikációt.

Engedélyezze az alábbi táblázatban szereplő IP-címekről érkező forgalmat.

| Forrás IP-címe | Cél | Irány |
|---|---|---|
| 168.61.49.99 | *:443 | Bejövő |
| 23.99.5.239 | *:443 | Bejövő |
| 168.61.48.131 | *:443 | Bejövő |
| 138.91.141.162 | *:443 | Bejövő |

Adja hozzá a fürt létrehozásának régiójára jellemző IP-címeket is. Tekintse meg [a HDInsight-kezelési IP-címeket](../hdinsight-management-ip-addresses.md) az egyes Azure-régiók címeinek listáját.

Ha expressz útvonalat vagy saját egyéni DNS-kiszolgálót használ, olvassa el a Virtuális hálózat megtervezése az [Azure HDInsight hoz – több hálózat csatlakoztatása](../hdinsight-plan-virtual-network-deployment.md#multinet)című témakört.

## <a name="resources-locks"></a>Erőforrások zárolása  

Győződjön meg arról, hogy nincsenek [zárolások a virtuális hálózaton és az erőforráscsoporton.](../../azure-resource-manager/management/lock-resources.md) A fürtök nem hozhatók létre és nem törölhetők, ha az erőforráscsoport zárolva van. 

## <a name="unsupported-component-versions"></a>Nem támogatott összetevő-verziók

Győződjön meg arról, hogy az [Azure HDInsight támogatott verzióját](../hdinsight-component-versioning.md) és a megoldásban lévő Apache [Hadoop-összetevőket](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) használja.  

## <a name="storage-account-name-restrictions"></a>Tárfiók névkorlátozásai

A tárfióknevek nem lehetnek 24 karakternél hosszabbak, és nem tartalmazhatnak különleges karaktert. Ezek a korlátozások az alapértelmezett tárolónévre is vonatkoznak a tárfiókban.

Más elnevezési korlátozások a fürt létrehozása korra is vonatkoznak. További információt [a Fürtnév-korlátozások](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)című témakörben talál.

## <a name="service-outages"></a>Szolgáltatáskimaradások

Ellenőrizze [az Azure állapotát](https://status.azure.com) az esetleges kimaradások vagy szolgáltatási problémák.

## <a name="next-steps"></a>További lépések

* [Azure HDInsight kiterjesztése Azure virtuális hálózat használatával](../hdinsight-plan-virtual-network-deployment.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Az Azure Storage és az Azure HDInsight-fürtök együttes használata](../hdinsight-hadoop-use-blob-storage.md)
* [Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával](../hdinsight-hadoop-provision-linux-clusters.md)
