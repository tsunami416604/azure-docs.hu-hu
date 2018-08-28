---
title: A .NET SDK használatával – Azure HDInsight Hadoop-fürtök kezelése
description: Ismerje meg, hogyan hajthat végre felügyeleti feladatokat a HDInsight .NET SDK-val a HDInsight Hadoop-fürtök számára.
services: hdinsight
ms.reviewer: jasonh
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: 0d8238d5f1dca15aaba0f8f5a6580df97164006a
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43110799"
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>A HDInsight Hadoop-fürtök kezelése a .NET SDK-val
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Útmutató a HDInsight-fürtök használata kezelheti [HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).

**Előfeltételek**

A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Csatlakozás az Azure HDInsight

A következő NuGet-csomagok lesz szüksége:

```
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Az alábbi példakód bemutatja, hogyan kapcsolódhat az Azure HDInsight-fürtök felügyelete az Azure-előfizetéshez előtt.

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

Egy üzenet kell jelenik meg, amikor a program futtatása.  Ha nem szeretné a üzenet jelenik meg, tekintse meg [nem interaktív hitelesítéssel .NET HDInsight-alkalmazások létrehozása](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

## <a name="create-clusters"></a>Fürtök létrehozása
Lásd: [létrehozása Linux-alapú fürtök a HDInsight .NET SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>Fürtök listázása
A következő kódrészletet a fürtök és az egyes tulajdonságok sorolja fel:

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
Használja a következő kódrészlet törli egy fürt szinkron vagy aszinkron módon: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Fürtök méretezése
A fürtméretezés egy funkció lehetővé teszi, hogy a fürt újbóli létrehozása nélkül fut az Azure HDInsight-fürt által használt munkavégző csomópontok számának módosítását.

> [!NOTE]
> Csak 3.1.3 verziójú HDInsight-fürtök vagy újabb verziója támogatott. Ha biztos benne, hogy a fürt verziója, a Tulajdonságok lapon ellenőrizheti.  Lásd: [fürtök listázása és megjelenítése](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
> 
> 

A fürt a HDInsight által támogatott különböző típusú adatok csomópontok számának módosításával hatásai:

* Hadoop
  
    Zökkenőmentesen lehet növelni egy Hadoop-fürtöt, amely a folyamatban lévő vagy futó feladatok befolyásolása nélkül fut-e a munkavégző csomópontok számát. Új feladatok is lehet beküldeni, amíg a művelet folyamatban van. A skálázási művelet hibák szabályosan kezeli, úgy, hogy a fürt minden esetben működőképes állapotban marad.
  
    Ha egy Hadoop-fürtöt az adatcsomópontok száma csökkentésével vertikálisan leskálázni, a fürtben a szolgáltatások újra lesz indítva. Ennek hatására a összes futó és a függőben lévő feladatok meghiúsulhatnak, a skálázási művelet befejezése után. A feladatok újból elküldheti, azonban a művelet befejeződése után.
* HBase
  
    Zökkenőmentesen adja hozzá vagy távolíthat el csomópontokat a HBase-fürt futás közben. Regionális kiszolgáló automatikusan kiegyensúlyozott vannak a skálázási művelet befejezése néhány percen belül. Azonban, manuálisan is eloszthatja a regionális kiszolgálók jelentkezik be a fürt átjárócsomópontjával, és a egy parancssori ablakot a következő parancsokat futtatni:
  
    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```
* Storm
  
    Zökkenőmentesen adja hozzá vagy távolít el a Storm-fürt adatcsomópontok futás közben. Azonban a topológia újraegyensúlyozására kell a skálázási művelet a sikeres telepítést követően.
  
    Az újraegyensúlyozás két módon is elvégezhető:
  
  * A Storm webes felhasználói felületen
  * Parancssori felület (CLI) eszköz
    
    Tekintse meg a [Apache Storm-dokumentáció](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) további részletekért.
    
    A Storm webes felhasználói felületen a HDInsight-fürtön érhető el:
    
    ![HDInsight Storm méretezési újraegyensúlyozási](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Íme egy példa a Storm-topológia újraegyensúlyozására a CLI-parancs használatával:
    
    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

A következő kódrészletet bemutatja, hogyan méretezheti át egy fürt szinkron vagy aszinkron módon:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>GRANT/revoke-access
HDInsight-fürtök a következő HTTP webes szolgáltatások (ezen szolgáltatások mindegyikéhez kell RESTful végpontokon) rendelkezik:

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton eszközön keresztül végzett

Alapértelmezés szerint ezek a szolgáltatások hozzáférés kapnak. Akkor is visszavonása/biztosítása a hozzáférést. Visszavonásához:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Megadását:

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
> A hozzáférés biztosítása/visszavonása, visszaállítja, a fürthöz tartozó felhasználónevet és jelszót.
> 
> 

Ez a portálon keresztül is elvégezhető. Lásd: [HDInsight felügyelheti az Azure portal használatával][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>A HTTP felhasználói hitelesítő adatok frissítése
Célszerű ugyanezt az eljárást, mint [Grant/revoke HTTP access](#grant/revoke-access). Ha a fürt a HTTP-hozzáférést kapott, meg kell először visszavonják.  És adja meg a hozzáférés az új HTTP-felhasználónál használt.

## <a name="find-the-default-storage-account"></a>Keresse meg az alapértelmezett tárfiókot
A következő kódrészletet bemutatja, hogyan juthat az alapértelmezett tárfiók neve és a fürt alapértelmezett tárfiókkulcs.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Feladatok elküldése
**A MapReduce-feladatok elküldése**

Lásd: [futtassa a Hadoop MapReduce-minták a HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**A Hive-feladatok** 

Lásd: [futtatása Hive-lekérdezések a .NET SDK használatával](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**A Pig-feladatok elküldése**

Lásd: [.NET SDK használatával futtassa a Pig-feladatok](hadoop/apache-hadoop-use-pig-dotnet-sdk.md).

**A Sqoop-feladatok elküldése**

Lásd: [Sqoop használata a HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Az Oozie-feladatok elküldése**

Lásd: [megadásához és a munkafolyamat futtatása a HDInsight Hadoop-keretrendszerrel használható Oozie](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Az Azure Blob storage-adatok feltöltése
Lásd: [Adatok feltöltése a HDInsightba][hdinsight-upload-data].

## <a name="see-also"></a>Lásd még:
* [HDInsight .NET SDK dokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [HDInsight felügyelete az Azure portal használatával][hdinsight-admin-portal]
* [Felügyelheti a HDInsight egy parancssori felülettel][hdinsight-admin-cli]
* [HDInsight-fürtök létrehozása][hdinsight-provision]
* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [Azure HDInsight – első lépések][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


