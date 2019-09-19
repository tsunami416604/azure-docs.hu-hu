---
title: Apache Kafka méretezés – Azure-HDInsight
description: Útmutató az Azure HDInsight-beli Apache Kafka-fürtök felügyelt lemezeinek a méretezhetőséget javító konfigurálásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: ef32a5d3a1439cded6cd092f076e7b26c178cb7a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122111"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Tárhely és méretezhetőség konfigurálása HDInsight-beli Apache Kafka platformon

Megtudhatja, hogyan konfigurálhatja a [Apache Kafka](https://kafka.apache.org/) által használt felügyelt lemezek számát a HDInsight-on.

A HDInsight-beli Kafka a HDInsight-fürt virtuális gépeinek helyi lemezét használja. Mivel a Kafka nagy ki- és bemenő adatforgalmat kezel, az [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) szolgáltatás gondoskodik a magas átviteli sebességről és csomópontonként több tárhelyről. Ha a Kafka hagyományos virtuális merevlemezeket használna, akkor az egyes csomópontok korlátja 1 TB volna. Felügyelt lemezekkel több lemez is használható, és 16 TB is elérhető a fürt minden csomópontjában.

A következő ábra a felügyelt lemezek nélküli és a felügyelt lemezeket használó HDInsight-beli Kafka összehasonlítása:

![Kafka felügyelt lemezekkel architektúrával](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Felügyelt lemezek konfigurálása: Azure Portal

1. Kövesse a [HDInsight-fürt létrehozása](../hdinsight-hadoop-create-linux-clusters-portal.md) című cikkben leírtakat, hogy megismerje a fürt Portal segítségével történő létrehozásának szokásos lépéseit. Ne fejezze be a létrehozást a Portalon.

2. A __Fürtméret__ szakaszban a __Lemezek száma feldolgozó csomópontonként__ mezőben konfigurálja a lemezek számát.

    > [!NOTE]  
    > A felügyelt lemez típusa __Standard__ (HDD) vagy __Prémium__ (SSD) lehet. Prémium lemezeket DS és GS sorozatbeli virtuális gépek használnak. Minden más virtuálisgép-típus standard lemezeket használ.

    ![fürt méret szakasza a lemezek száma munkavégző csomóponttal kiemelve](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Felügyelt lemezek konfigurálása: Resource Manager-sablon

Egy Kafka-fürt egy feldolgozó csomópontjára jutó lenezek számának beállításához használja a sablon következő szakaszát:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Megtalálhatja a teljes sablont, amely bemutatja, hogyan konfigurálhatja a felügyelt lemezeket a [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json)szolgáltatásban.

## <a name="next-steps"></a>További lépések

A HDInsight-Apache Kafka használatáról a következő dokumentumokban talál további információt:

* [Apache Kafka replikájának létrehozása a MirrorMaker használatával a HDInsight](apache-kafka-mirroring.md)
* [Apache Storm használata a HDInsight Apache Kafka használatával](../hdinsight-apache-storm-with-kafka.md)
* [Apache Spark használata a HDInsight Apache Kafka használatával](../hdinsight-apache-spark-with-kafka.md)
* [Kapcsolódás Apache Kafka Azure-beli Virtual Network](apache-kafka-connect-vpn-gateway.md)

* [HDInsight blog a felügyelt lemezeken Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
