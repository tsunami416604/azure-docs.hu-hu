---
title: 'Azure HDInsight: .NET-minták'
description: C# .NET példák keresése a GitHubon a HDInsight SDK for .NET használatával végzett gyakori feladatokhoz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 12/06/2019
ms.openlocfilehash: 097323b4566daeda737428121bee4d64e9f7248b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74951564"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight: .NET-minták

> [!div class="op_single_selector"]
> * [Példák .](hdinsight-sdk-dotnet-samples.md)
> * [Példák python](hdinsight-sdk-python-samples.md)
> * [Java-példák](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Ez a cikk a következő információkat tartalmazza:

* A fürtlétrehozási feladatok mintáira mutató hivatkozások.
* Egyéb felügyeleti feladatokhoz kapcsolódó tartalomra mutató hivatkozások.

[Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisite"></a>Előfeltétel

[.NET-hez készült Azure HDInsight SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Fürtkezelés - létrehozás

* [Kafka-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Spark-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Spark-fürt létrehozása az Azure Data Lake Storage Gen2 használatával](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Spark-fürt létrehozása vállalati biztonsági csomaggal (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Ezeket a mintákat a .NET a [hdinsight-dotnet-sdk-samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) GitHub-tárház klónozásával kaphatja le.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

A további SDK-funkciók kódrészlete megtalálható a [HDInsight SDK.NET referenciadokumentációban.](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)
