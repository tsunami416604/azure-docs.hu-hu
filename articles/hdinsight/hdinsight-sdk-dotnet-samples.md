---
title: 'Azure HDInsight: .NET-minták'
description: C# .Net-példák a githubon gyakori feladatokhoz a HDInsight SDK for .NET használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.openlocfilehash: c1365b39385a3dfb684628214765269ea3a225e9
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076878"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight: .NET-minták

> [!div class="op_single_selector"]
> * [.NET-példák](hdinsight-sdk-dotnet-samples.md)
> * [Python-példák](hdinsight-sdk-python-samples.md)
> * [Java-példák](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Ez a cikk a következő információkat tartalmazza:

* A fürtök létrehozására szolgáló feladatokhoz kapcsolódó mintákra mutató hivatkozások.
* Hivatkozások a más felügyeleti feladatokra vonatkozó hivatkozási tartalomra.

Aktiválhatja a [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)előfizetői előnyeit: A Visual Studio-előfizetése minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Azure HDInsight SDK a .NET-hez](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Fürt kezelése – létrehozás

* [Kafka-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Spark-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Spark-fürt létrehozása Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Spark-fürt létrehozása Enterprise Security Package (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Ezeket a mintákat a .NET-hez a [hdinsight-DotNet-SDK-Samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) GitHub-tárház klónozásával érheti el.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Ehhez a további SDK-funkciókhoz tartozó kódrészletek a [HDINSIGHT SDK for .net dokumentációjában](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)találhatók.
