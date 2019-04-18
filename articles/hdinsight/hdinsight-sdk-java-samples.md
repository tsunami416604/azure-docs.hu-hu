---
title: 'Azure HDInsight: Java-példák'
description: Java-példák a Githubon található a HDInsight SDK használata Javához készült gyakori feladatok.
author: hrasheed-msft
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.author: hrasheed
ms.openlocfilehash: 971af370425f733649f0b8d0079baaf93cc72129
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681539"
---
# <a name="azure-hdinsight-java-samples"></a>Azure HDInsight: Java-példák

> [!div class="op_single_selector"]
> * [Java-példák](hdinsight-sdk-java-samples.md)
> * [.NET-példák](hdinsight-sdk-dotnet-samples.md)
> * [Python-példák](hdinsight-sdk-python-samples.md)
<!-- * [Go Examples](hdinsight-sdk-dotnet-samples.md)-->

Ez a cikk a következő információkat tartalmazza:

* Szereplő hivatkozások a fürt létrehozási feladatok.
* Segédanyagok a többi felügyeleti feladat mutató hivatkozásokat tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [A Javához készült Azure HDInsight SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Fürtkezelés - létrehozás

* [Kafka-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateKafkaClusterSample.java)
* [Spark-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateSparkClusterSample.java)
* [Spark-fürt létrehozása az Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateHadoopClusterWithAdlsGen2Sample.java)
* [Spark-fürt létrehozása a vállalati biztonsági csomag (ESP)](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateEspClusterSample.java)

Klónozásával Java ezeket a mintákat is kap a [hdinsight-java-sdk-minták](https://github.com/Azure-Samples/hdinsight-java-sdk-samples) GitHub-adattárban.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Kódrészletek a tekintheti meg a további SDK-funkciók esetében a [HDInsight SDK for Java dokumentáció](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-preview).