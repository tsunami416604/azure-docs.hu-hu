---
title: Apache Kafka méretezés – Azure-HDInsight
description: Útmutató az Azure HDInsight-beli Apache Kafka-fürtök felügyelt lemezeinek a méretezhetőséget javító konfigurálásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 5ce8414376862b66314f754252aba3ab6afdaf25
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435329"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Tárhely és méretezhetőség konfigurálása HDInsight-beli Apache Kafka platformon

Megtudhatja, hogyan konfigurálhatja a [Apache Kafka](https://kafka.apache.org/) által használt felügyelt lemezek számát a HDInsight-on.

A HDInsight-beli Kafka a HDInsight-fürt virtuális gépeinek helyi lemezét használja. Mivel a Kafka nagy ki- és bemenő adatforgalmat kezel, az [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) szolgáltatás gondoskodik a magas átviteli sebességről és csomópontonként több tárhelyről. Ha a Kafka hagyományos virtuális merevlemezeket használna, akkor az egyes csomópontok korlátja 1 TB volna. Felügyelt lemezekkel több lemez is használható, és 16 TB is elérhető a fürt minden csomópontjában.

A következő ábra a felügyelt lemezek nélküli és a felügyelt lemezeket használó HDInsight-beli Kafka összehasonlítása:

![Kafka felügyelt lemezekkel architektúrával](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Felügyelt lemezek konfigurálása: Azure Portal

1. Kövesse a [HDInsight-fürt létrehozása](../hdinsight-hadoop-create-linux-clusters-portal.md) című cikkben leírtakat, hogy megismerje a fürt Portal segítségével történő létrehozásának szokásos lépéseit. Ne fejezze be a portál létrehozási folyamatát.

2. A **konfiguráció & díjszabása** szakaszban a __csomópontok száma__ mező segítségével konfigurálja a lemezek számát.

    > [!NOTE]  
    > A felügyelt lemez típusa __Standard__ (HDD) vagy __Prémium__ (SSD) lehet. Prémium lemezeket DS és GS sorozatbeli virtuális gépek használnak. Minden más virtuálisgép-típus standard lemezeket használ.

    ![fürt méret szakasza a lemezek száma munkavégző csomóponttal kiemelve](./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Felügyelt lemezek használata: Resource Manager-sablon

Egy Kafka-fürt egy feldolgozó csomópontjára jutó lenezek számának beállításához használja a sablon következő szakaszát:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

A felügyelt lemezek [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json)-on való konfigurálását bemutató teljes sablont talál.

## <a name="next-steps"></a>Következő lépések

A HDInsight-Apache Kafka használatáról a következő dokumentumokban talál további információt:

* [Apache Kafka replikájának létrehozása a MirrorMaker használatával a HDInsight](apache-kafka-mirroring.md)
* [Apache Storm használata a HDInsight Apache Kafka használatával](../hdinsight-apache-storm-with-kafka.md)
* [Apache Spark használata a HDInsight Apache Kafka használatával](../hdinsight-apache-spark-with-kafka.md)
* [Kapcsolódás Apache Kafka Azure-beli Virtual Network](apache-kafka-connect-vpn-gateway.md)

* [HDInsight blog a felügyelt lemezeken Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
