---
title: Apache Hadoop-fürtök kezelése a HDInsightban a .NET SDK - Azure segítségével
description: Ismerje meg, hogyan hajthat végre felügyeleti feladatokat az Apache Hadoop-fürtökhöz a HDInsight ban a HDInsight .NET SDK használatával.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 36a77d49b507d3d0158d1b4b492d0141350de50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240638"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Apache Hadoop-fürtök kezelése a HDInsight rendszerében a .NET SDK segítségével

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Ismerje meg, hogyan kezelheti a HDInsight-fürtöket [HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)használatával.

**Előfeltételek**

A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Csatlakozás az Azure HDInsighthoz

A következő NuGet csomagokra van szüksége:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Az alábbi kódminta bemutatja, hogyan csatlakozhat az Azure-hoz, mielőtt felügyelné a HDInsight-fürtöket az Azure-előfizetésében.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

A program futtatásakor egy üzenet jelenik meg.  Ha nem szeretné látni a kérdést, olvassa el a [Nem interaktív hitelesítés .NET HDInsight-alkalmazások létrehozása című témakört.](hdinsight-create-non-interactive-authentication-dotnet-applications.md)


## <a name="list-clusters"></a>Fürtök listázása

A következő kódrészlet a fürtöket és néhány tulajdonságot sorolja fel:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Fürtök törlése

A fürt szinkron vagy aszinkron törléséhez használja a következő kódrészletet: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Fürtök méretezése

A fürtméretezési szolgáltatás lehetővé teszi, hogy módosítsa az Azure HDInsightban futó fürt által használt munkavégző csomópontok számát anélkül, hogy újra létre kellene hoznia a fürtöt.

> [!NOTE]  
> Csak a HDInsight 3.1.3-as vagy újabb verziójával rendelkező fürtök támogatottak. Ha nem biztos a fürt verziójában, ellenőrizheti a Tulajdonságok lapot.  Lásd: [Fürtök listája és megjelenítése](hdinsight-administer-use-portal-linux.md#showClusters).

A HDInsight által támogatott fürttípusok adatcsomópontjaiszámának módosításának hatása:

* Apache Hadoop
  
    Zökkenőmentesen növelheti a munkavégző csomópontok számát egy hadoop-fürtben, amely anélkül fut, hogy befolyásolna a függőben lévő vagy futó feladatokat. A művelet közben új feladatok is elküldhetők. A skálázási művelet hibáit a program szabályosan kezeli, így a fürt mindig működőképes állapotban marad.
  
    Ha egy Hadoop-fürt az adatcsomópontok számának csökkentésével csökken, a fürt egyes szolgáltatásai újraindulnak. Ez azt eredményezi, hogy az összes futó és függőben lévő feladat sikertelen lesz a skálázási művelet befejezésekor. A művelet befejezése után azonban újra elküldheti a feladatokat.
* Apache HBase
  
    Zökkenőmentesen hozzáadhat vagy eltávolíthat csomópontokat a HBase-fürthöz futás közben. A regionális kiszolgálók automatikusan kivannak egyensúlyban a skálázási művelet befejezését követő néhány percen belül. A regionális kiszolgálókat azonban manuálisan is kiegyensúlyozhatja, ha bejelentkezik a fürt csomópontjába, és parancssori ablakból futtatja a következő parancsokat:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Zökkenőmentesen hozzáadhat vagy eltávolíthat adatcsomópontokat a Storm-fürthöz futás közben. De a skálázási művelet sikeres befejezése után újra egyensúlyba kell hoznia a topológiát.
  
    Az egyensúly helyreállítása kétféleképpen valósítható meg:
  
  * Storm web felhasználói felülete
  * Parancssori interfész (CLI) eszköz
    
    További részletekért olvassa el az [Apache Storm dokumentációját.](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)
    
    A Storm webes felhasználói felülete a HDInsight-fürtön érhető el:
    
    ![HDInsight Storm-skála egyensúlyának helyreállítása](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Íme egy példa arra, hogyan használhatja a CLI parancsot a Storm topológia egyensúlyának helyreállítására:
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

A következő kódrészlet bemutatja, hogyan lehet a fürtöt szinkron vagy aszinkron módon átméretezni:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Hozzáférés megadása/visszavonása

A HDInsight-fürtök a következő HTTP-webszolgáltatásokkal rendelkeznek (ezek a szolgáltatások RESTful végpontokkal rendelkeznek):

* ODBC
* JDBC
* Az Ambari
* Apacs Oozie
* Apacs Templeton között

Alapértelmezés szerint ezek a szolgáltatások hozzáférési feltételeket kapnak. Visszavonhatja/megadhatja a hozzáférést. Visszavonás:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

A következők megadása:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> A hozzáférés megadásával/visszavonásával alaphelyzetbe állíthatja a fürt felhasználónevét és jelszavát.

Ez a portálon keresztül is elvégezhető. Lásd: [Apache Hadoop-fürtök kezelése a HDInsightban az Azure Portalhasználatával.](hdinsight-administer-use-portal-linux.md)

## <a name="update-http-user-credentials"></a>HTTP-felhasználói hitelesítő adatok frissítése

Ez ugyanaz az eljárás, mint a HTTP-hozzáférés megadása/visszavonása.  Ha a fürt megkapta a HTTP-hozzáférést, először vissza kell vonnia azt.  Majd adja meg a hozzáférést az új HTTP felhasználói hitelesítő adatokkal.

## <a name="find-the-default-storage-account"></a>Az alapértelmezett tárfiók megkeresése

A következő kódrészlet bemutatja, hogyan szerezheti be a fürt alapértelmezett tárfióknevét és alapértelmezett tárfiókkulcsát.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Feladatok beküldése

**MapReduce feladatok elküldése**

Lásd: [MapReduce minták futtatása a HDInsightban.](hadoop/apache-hadoop-run-samples-linux.md)

**Apache Hive-feladatok beküldése** 

Lásd: [Apache Hive-lekérdezések futtatása a .NET SDK használatával.](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)

**Apache Sqoop-feladatok beküldése**

Lásd: [Az Apache Sqoop használata a HDInsight segítségével.](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md)

**Apache Oozie-feladatok beküldése**

A HDInsight ban az Apache Oozie használata a [Hadoop segítségével definiálhatja és futtathatja a munkafolyamatot.](hdinsight-use-oozie-linux-mac.md)

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure Blob storage-ba

Lásd: [Adatok feltöltése a HDInsightba][hdinsight-upload-data].

## <a name="see-also"></a>Lásd még:

* [A HDInsight .NET SDK referenciadokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Apache Hadoop-fürtök kezelése a HDInsightban az Azure Portal használatával](hdinsight-administer-use-portal-linux.md)
* [A HDInsight felügyelete parancssori felületen keresztül][hdinsight-admin-cli]
* [HDInsight-fürtök létrehozása][hdinsight-provision]
* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [Ismerkedés az Azure HDInsight-mal][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
