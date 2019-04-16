---
title: 'Az Azure HDInsight: .NET-minták'
description: Keresés C# gyakori feladatok a HDInsight SDK-val .NET-hez készült .NET-példáit a Githubon.
author: hrasheed-msft
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.author: hrasheed
ms.openlocfilehash: 94e2f007f70d55387b641b3d9166fa84f26f16ba
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59580290"
---
# <a name="azure-hdinsight-net-samples"></a>Az Azure HDInsight: .NET-minták

> [!div class="op_single_selector"]
> * [.NET-példák](hdinsight-sdk-dotnet-samples.md)
> * [Python-példák](hdinsight-sdk-python-samples.md)
> * [Java-példák](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Ez a cikk a következő információkat tartalmazza:

* Szereplő hivatkozások a fürt létrehozási feladatok.
* Segédanyagok a többi felügyeleti feladat mutató hivatkozásokat tartalmaz.

Is [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetésében kreditek minden hónapban biztosít Önnek, hogy a fizetős Azure-szolgáltatásokat is használhat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Az Azure HDInsight SDK for .NET csomaggal](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Fürtkezelés - létrehozás

* [Kafka-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Spark-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Spark-fürt létrehozása az Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Spark-fürt létrehozása a vállalati biztonsági csomag (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Klónozásával a .NET-hez ezeket a mintákat is kap a a [hdinsight-dotnet-sdk-minták](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) GitHub-adattárban.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Kódrészletek a tekintheti meg a további SDK-funkciók esetében a [HDInsight SDK for .NET-referenciák dokumentációiba](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).