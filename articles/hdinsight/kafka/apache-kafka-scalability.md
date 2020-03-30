---
title: Az Apache Kafka növeli a méretezést – Azure HDInsight
description: Útmutató az Azure HDInsight-beli Apache Kafka-fürtök felügyelt lemezeinek a méretezhetőséget javító konfigurálásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 56c25b7c77809a5cb7f4e539cff8e1815cd9976f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031705"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Tárhely és méretezhetőség konfigurálása HDInsight-beli Apache Kafka platformon

Ismerje meg, hogyan konfigurálhatja az [Apache Kafka](https://kafka.apache.org/) által a HDInsighton használt felügyelt lemezek számát.

A HDInsight-beli Kafka a HDInsight-fürt virtuális gépeinek helyi lemezét használja. Mivel a Kafka nagy ki- és bemenő adatforgalmat kezel, az [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) szolgáltatás gondoskodik a magas átviteli sebességről és csomópontonként több tárhelyről. Ha a Kafka hagyományos virtuális merevlemezeket használna, akkor az egyes csomópontok korlátja 1 TB volna. Felügyelt lemezekkel több lemez is használható, és 16 TB is elérhető a fürt minden csomópontjában.

A következő ábra a felügyelt lemezek nélküli és a felügyelt lemezeket használó HDInsight-beli Kafka összehasonlítása:

![kafka felügyelt lemezek architektúrával](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Felügyelt lemezek konfigurálása: Azure Portal

1. Kövesse a [HDInsight-fürt létrehozása](../hdinsight-hadoop-create-linux-clusters-portal.md) című cikkben leírtakat, hogy megismerje a fürt Portal segítségével történő létrehozásának szokásos lépéseit. Ne fejezze be a portál létrehozási folyamatát.

2. A **Konfiguráció & díjszabása** szakaszban a __Csomópontok száma__ mezőben konfigurálhatja a lemezek számát.

    > [!NOTE]  
    > A felügyelt lemez típusa __Standard__ (HDD) vagy __Prémium__ (SSD) lehet. Prémium lemezeket DS és GS sorozatbeli virtuális gépek használnak. Minden más virtuálisgép-típus standard lemezeket használ.

    ![fürtméret szakasz a munkavégző csomópontonkénti lemezekkel kiemelve](./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Felügyelt lemezek használata: Resource Manager-sablon

Egy Kafka-fürt egy feldolgozó csomópontjára jutó lenezek számának beállításához használja a sablon következő szakaszát:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

A rendszer teljes sablont talál, amely bemutatja [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json)a felügyelt lemezek konfigurálását a rendszeren.

## <a name="next-steps"></a>További lépések

Az Apache Kafka HDInsight-on végzett együttműködésével kapcsolatos további tudnivalókért tekintse meg az alábbi dokumentumokat:

* [A MirrorMaker segítségével létrehozhatja az Apache Kafka másolatát a HDInsight-on](apache-kafka-mirroring.md)
* [Az Apache Storm használata az Apache Kafkával a HDInsighton](../hdinsight-apache-storm-with-kafka.md)
* [Az Apache Spark használata az Apache Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)
* [Csatlakozás az Apache Kafkához egy Azure virtuális hálózaton keresztül](apache-kafka-connect-vpn-gateway.md)

* [HDInsight blog felügyelt lemezeken az Apache Kafka segítségével](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
