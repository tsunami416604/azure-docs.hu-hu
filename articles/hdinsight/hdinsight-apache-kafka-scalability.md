---
title: "Az Apache Kafka platform kiterjesztése – Azure HDInsight | Microsoft Docs"
description: "Útmutató az Azure HDInsight-beli Apache Kafka-fürtök felügyelt lemezeinek a méretezhetőséget javító konfigurálásához."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/14/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 84c7048bbd608b0b99215d74f71960dc9d1158ef
ms.contentlocale: hu-hu
ms.lasthandoff: 06/16/2017

---

<a id="configure-storage-and-scalability-for-apache-kafka-on-hdinsight" class="xliff"></a>

# Tárhely és méretezhetőség konfigurálása HDInsight-beli Apache Kafka platformon

Útmutató a HDInsight-beli Apache Kafka által használt felügyelt lemezek számának konfigurálásához.

A HDInsight-beli Kafka a HDInsight-fürt virtuális gépeinek helyi lemezét használja. Mivel a Kafka nagy ki- és bemenő adatforgalmat kezel, az [Azure Managed Disks](../storage/storage-managed-disks-overview.md) szolgáltatás gondoskodik a magas átviteli sebességről és csomópontonként több tárhelyről. Ha a Kafka hagyományos virtuális merevlemezeket használna, akkor az egyes csomópontok korlátja 1 TB volna. Felügyelt lemezekkel több lemez is használható, és 16 TB is elérhető a fürt minden csomópontjában.

A következő ábra a felügyelt lemezek nélküli és a felügyelt lemezeket használó HDInsight-beli Kafka összehasonlítása:

![Ábra a HDInsight-beli Kafka platformról virtuális gépenként egy virtuális merevlemezzel és virtuális gépenként több felügyelt lemezzel](./media/hdinsight-apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

<a id="configure-managed-disks-azure-portal" class="xliff"></a>

## Felügyelt lemezek konfigurálása: Azure Portal

1. Kövesse a [HDInsight-fürt létrehozása](hdinsight-hadoop-create-linux-clusters-portal.md) című cikkben leírtakat, hogy megismerje a fürt Portal segítségével történő létrehozásának szokásos lépéseit. Ne fejezze be a létrehozást a Portalon.

2. A lemezek száma a __Fürt mérete__ panelen a __Lemezek száma feldolgozó csomópontonként__ mezőben állítható be.

    > [!NOTE]
    > A felügyelt lemez típusa __Standard__ (HDD) vagy __Prémium__ (SSD) lehet. Prémium lemezeket DS és GS sorozatbeli virtuális gépek használnak. Minden más virtuálisgép-típus standard lemezeket használ.

    ![Ábra a fürtméret-panelről a feldolgozó csomópontonkénti lemezszám kiemelésével](./media/hdinsight-apache-kafka-scalability/set-managed-disks-portal.png)

<a id="configure-managed-disks-resource-manager-template" class="xliff"></a>

## Felügyelt lemezek használata: Resource Manager-sablon

Egy Kafka-fürt egy feldolgozó csomópontjára jutó lenezek számának beállításához használja a sablon következő szakaszát:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

A felügyelt lemezek konfigurálását bemutató teljes sablon megtalálható a [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json) URL-címen.

<a id="next-steps" class="xliff"></a>

## Következő lépések

A HDInsight-beli Kafka használatával kapcsolatos további információk a következő dokumentumokban találhatók:

* [A MirrorMaker használata a Kafka replikájának HDInsighton való létrehozásához](hdinsight-apache-kafka-mirroring.md)
* [Az Apache Storm használata a HDInsighton futó Kafkával](hdinsight-apache-storm-with-kafka.md)
* [Az Apache Spark használata a Kafkával a HDInsighton](hdinsight-apache-spark-with-kafka.md)
* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](hdinsight-apache-kafka-connect-vpn-gateway.md)

* [HDInsight blog a felügyelt lemezek Kafka platformmal való használatáról](https://azure.microsoft.com/blog/announcing-hdinsight-kafka-public-preview-with-azure-managed-disks)
