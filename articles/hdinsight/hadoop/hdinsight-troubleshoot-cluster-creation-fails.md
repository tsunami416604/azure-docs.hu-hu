---
title: Fürtök létrehozásával kapcsolatos hibák elhárítása az Azure HDInsight
description: Ismerje meg, hogyan lehet elhárítani az Azure HDInsight-fürtök létrehozásával kapcsolatos problémákat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/06/2019
ms.openlocfilehash: c7092b2cbcef01ef71261b6f5498cde56a40c358
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857247"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Fürtök létrehozásával kapcsolatos hibák elhárítása az Azure HDInsight

A fürtök létrehozásával kapcsolatos hibák leggyakoribb kiváltó okai a következők:

- Engedélyekkel kapcsolatos problémák
- Erőforrás-házirendre vonatkozó korlátozások
- Tűzfalak
- Erőforrás-zárolások
- Nem támogatott összetevő-verziók
- Storage-fiók nevének korlátozásai
- Szolgáltatások kimaradásai

## <a name="permissions-issues"></a>Engedélyekkel kapcsolatos problémák

Ha Data Lake Storage Gen 2-t használja, ügyeljen arra, hogy a HDInsight-fürthöz rendelt, felhasználó által hozzárendelt felügyelt identitás a **Storage blob adatközreműködői** szerepkörben vagy a **Storage blob-adatok tulajdonosi szerepkörében**legyen. Lásd: a [Azure Data Lake Storage Gen2 használata az Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) -fürtökkel a telepítési utasítások végrehajtásához.

Ha az 1. generációs Data Lake Storage használja, tekintse meg a telepítési és konfigurációs utasításokat [itt](../hdinsight-hadoop-use-data-lake-store.md). A Data Lake Storage Gen 1 nem támogatja a HBase-fürtöket, és a HDInsight 4,0-es verziójában nem támogatott.

Ha az Azure Storage-t használja, győződjön meg arról, hogy a Storage-fiók neve érvényes a fürt létrehozásakor.

## <a name="resource-policy-restrictions"></a>Erőforrás-házirendre vonatkozó korlátozások

Az előfizetés-alapú Azure-szabályzatok nem tagadhatják meg a nyilvános IP-címek létrehozását. A HDInsight-fürt létrehozásához két nyilvános IP-cím szükséges.  

Az alábbi házirendek általánosságban befolyásolhatják a fürtök létrehozását:

* A házirendek meggátolják az IP-címek & terheléselosztó létrehozását az előfizetésen belül.
* Házirend a Storage-fiók létrehozásának megakadályozása.
* A hálózati erőforrások törlését megakadályozó szabályzat (IP-/Load Balancer).

## <a name="firewalls"></a>Tűzfalak

A virtuális hálózati vagy a Storage-fiókhoz tartozó tűzfalak megtagadhatják a HDInsight-felügyeleti IP-címekkel folytatott kommunikációt.

Az alábbi táblázatban lévő IP-címekről érkező forgalom engedélyezése.

| Forrás IP-címe | Cél | Direction |
|---|---|---|
| 168.61.49.99 | *: 443 | Bejövő |
| 23.99.5.239 | *: 443 | Bejövő |
| 168.61.48.131 | *: 443 | Bejövő |
| 138.91.141.162 | *: 443 | Bejövő |

Adja hozzá a fürthöz tartozó régióhoz tartozó IP-címeket is. Az egyes Azure-régiók címeinek listáját a [HDInsight-felügyeleti IP-címek](../hdinsight-management-ip-addresses.md) című részben tekintheti meg.

Ha expressz útvonalat vagy saját egyéni DNS-kiszolgálót használ, tekintse meg [a virtuális hálózat megtervezése az Azure HDInsight – több hálózat csatlakoztatása](../hdinsight-plan-virtual-network-deployment.md#multinet)című témakört.

## <a name="resources-locks"></a>Erőforrások zárolása  

Győződjön meg arról, hogy a [virtuális hálózat és az erőforráscsoport](../../azure-resource-manager/resource-group-lock-resources.md)nem zárolható.  

## <a name="unsupported-component-versions"></a>Nem támogatott összetevő-verziók

Győződjön meg arról, hogy az [Azure HDInsight támogatott verzióját](../hdinsight-component-versioning.md) és a megoldás bármely [Apache Hadoop összetevőjét](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) használja.  

## <a name="storage-account-name-restrictions"></a>Storage-fiók nevének korlátozásai

A Storage-fiókok neve nem lehet hosszabb 24 karakternél, és nem tartalmazhat speciális karaktert. Ezek a korlátozások az alapértelmezett tárolónévre is vonatkoznak a tárfiókban.

## <a name="service-outages"></a>Szolgáltatások kimaradásai

Győződjön meg arról, hogy az [Azure állapota](https://status.azure.com/status) esetleges kimaradások vagy szolgáltatási problémák esetén is fennáll.

## <a name="next-steps"></a>További lépések

* [Azure virtuális hálózat használatával Azure HDInsight kiterjesztése](../hdinsight-plan-virtual-network-deployment.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Az Azure Storage és az Azure HDInsight-fürtök együttes használata](../hdinsight-hadoop-use-blob-storage.md)
* [Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával](../hdinsight-hadoop-provision-linux-clusters.md)
