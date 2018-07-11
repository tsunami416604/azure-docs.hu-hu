---
title: Parancsfájlműveletekkel – Azure HDInsight-fürtök testre szabása |} A Microsoft Docs
description: Ismerje meg, hogyan Szkriptműveletek használatával HDInsight-fürtök testre szabása.
services: hdinsight
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3a63e216-4163-40c1-aa04-6b42fd0162ad
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 10/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 50ef40b3ea3bc8c768e8b4266ef50ad02e02f026
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950614"
---
# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Szkriptműveletek használatával Windows-alapú HDInsight-fürtök testre szabása
**Parancsfájl-művelet** hívja használhatók [egyéni parancsfájlok](hdinsight-hadoop-script-actions.md) a további szoftverek telepítése egy fürtre a fürt létrehozása során.

Ebben a cikkben található információk csak a Windows-alapú HDInsight-fürtök. A Linux-alapú fürtök esetén lásd: [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

HDInsight-fürtök számos más módon is testre szabható, többek között további Azure Storage-fiókokat, például a Hadoop módosítása konfigurációs fájlok (core-site.xml, hive-site.xml stb.) vagy kódtárakat (pl. a Hive, az Oozie) való hozzáadása megosztott általánosan használt helyet ismertet a fürtben. Ezek a testreszabások végezhető el az Azure PowerShell, az Azure HDInsight .NET SDK vagy az Azure Portalon. További információkért lásd: [Hadoop-fürtök létrehozása a HDInsight][hdinsight-provision-cluster].

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>A fürtlétrehozás a parancsfájlművelet
Parancsfájlművelet csak akkor használható, amíg a fürt létrehozása folyamatban van. A következő ábra szemlélteti a parancsprogram-művelet végrehajtásakor a létrehozási folyamat során:

![HDInsight-fürt testreszabása és -szakaszok a fürt létrehozásakor][img-hdi-cluster-states]

A parancsprogram futtatásakor kerül, a fürt a **ClusterCustomization** szakaszban. Ezen a ponton a parancsfájl a rendszer rendszergazdai fiók a megadott csomópontok a fürtben, párhuzamos alatt fut, és teljes körű rendszergazdai jogosultságokat biztosít a csomópontokon.

> [!NOTE]
> Mivel a rendszergazdai jogosultságokkal rendelkezik a fürtcsomópontokon során a **ClusterCustomization** fázis, a parancsfájl használatával hajtsa végre a műveletek, mint a szolgáltatások, beleértve a Hadoop-hez kapcsolódó szolgáltatások indítása és leállítása. Tehát, a parancsfájl bizonyosodjon meg, hogy az Ambari-szolgáltatások és egyéb Hadoop-hez kapcsolódó szolgáltatások és a parancsfájl futtatásának befejezése előtt. Ezek a szolgáltatások szükségesek sikeresen listájáért állapotát és a fürt állapotát, amíg létrehozása folyamatban van. Ha módosítja a fürtön, amely érinti ezeket a szolgáltatásokat semmilyen konfigurálást, a segédfüggvények biztosított kell használnia. Segédfüggvények kapcsolatos további információkért lásd: [Szkriptművelet fejlesztése HDInsight-parancsfájlok][hdinsight-write-script].
>
>

A kimenet és a hibanaplókat, a parancsfájl az alapértelmezett tárfiókot, a fürt megadott vannak tárolva. A naplók vannak tárolva egy tábla nevét **u < \cluster-name-fragment >< \time-stamp > setuplog**. Ezek azok a parancsfájl a csomópontokon futtatott összes (fő csomópontot és a feldolgozó csomópontok) a fürtben aggregált naplóinak.

Minden egyes fürt fogadhat több parancsfájl-műveletek kerül meghívásra, amely a megadott sorrendben. A fő csomópontot és a feldolgozó csomópontok tartozhatnak parancsfájlt is futtatunk.

HDInsight számos szkript a következő összetevők telepítése a HDInsight-fürtökön biztosítja:

| Name (Név) | Szkript |
| --- | --- |
| **A Spark telepítése** | `https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1`. Lásd: [telepítése és használata a Spark on HDInsight-fürtök][hdinsight-install-spark]. |
| **Az R telepítése** | `https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1`. Lásd: [telepítése és az R használata a HDInsight-fürtökön](r-server/r-server-hdinsight-manage.md#install-additional-r-packages-on-the-cluster). |
| **A Solr telepítése** | `https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1`. Lásd: [telepítése és használata a Solr a HDInsight-fürtök](hdinsight-hadoop-solr-install.md). |
| **A Giraph telepítése** | `https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1`. Lásd: [telepítése és használata a Giraph a HDInsight-fürtök](hdinsight-hadoop-giraph-install.md). |
| **Hive-kódtárak előzetes betöltése** | `https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1`. Lásd: [adja hozzá a Hive-kódtárak a HDInsight-fürtök](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="call-scripts-using-the-azure-portal"></a>Hívás parancsfájlok az Azure portal használatával
**Az Azure Portalról**

1. Indítsa el a fürt létrehozása ismertetett módon [Hadoop-fürtök létrehozása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
2. Választható konfiguráció mellett a a **Parancsfájlműveletek** panelen kattintson a **parancsfájlművelet hozzáadása** , adja meg a parancsfájlművelet adatait, ahogy az alábbi:

    ![Fürt testreszabása Szkriptműveletek használatával](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Szkriptműveleteket használata a fürt testreszabása")

    <table border='1'>
        <tr><th>Tulajdonság</th><th>Érték</th></tr>
        <tr><td>Name (Név)</td>
            <td>Adja meg a parancsfájlművelet nevét.</td></tr>
        <tr><td>Szkript URI-ja</td>
            <td>Adja meg az URI-t a parancsfájl, amely a fürt testreszabásához meghívott. s</td></tr>
        <tr><td>A fő/feldolgozó</td>
            <td>Adja meg a csomópontok (**Head** vagy **munkavégző**) a testreszabási parancsfájl futtatása a</b>.
        <tr><td>Paraméterek</td>
            <td>Adja meg a paramétereket, ha a parancsfájl által igényelt.</td></tr>
    </table>

    Nyomja le az ENTER billentyűt, hogy több összetevő telepítése a fürtön egynél több parancsprogram-művelet hozzáadása.
3. Kattintson a **kiválasztása** mentse a parancsfájlt művelet konfigurációját, és folytassa a fürt létrehozása.

## <a name="call-scripts-using-azure-powershell"></a>Azure PowerShell-lel szkriptek meghívása
A következő PowerShell-parancsprogram bemutatja, hogyan Spark telepítése Windows-alapú HDInsight-fürt.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Connect-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.

    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"

    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    #############################################################
    # Connect to Azure
    #############################################################

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Connect-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #############################################################
    # Prepare the dependent components
    #############################################################

    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext

    #############################################################
    # Create cluster with ApacheSpark
    #############################################################

    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey


    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `

    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Más szoftver telepítéséhez szüksége cserélje le a parancsfájlt a parancsfájlt:

Amikor a rendszer kéri, adja meg a hitelesítő adatokat a fürtön. Mielőtt a fürt létrehozása több percet is igénybe vehet.

## <a name="call-scripts-using-net-sdk"></a>.NET SDK használatával szkriptek meghívása
A következő minta bemutatja, hogyan Spark telepítése Windows-alapú HDInsight-fürt. Más szoftverek telepítése, szüksége lesz cserélje le a parancsfájlt a kódban.

**A Spark egy HDInsight-fürt létrehozása**

1. Hozzon létre egy C# konzolalkalmazást a Visual Studióban.
2. A Nuget-Csomagkezelő konzolról futtassa a következő parancsot.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight
3. Használja a következő using utasításokat a Program.cs fájlban:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
4. Helyezze a kódot az osztály a következő:

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId;
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,
                DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingContainer),
                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
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
5. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftverek támogatása
A Microsoft Azure HDInsight szolgáltatást az rugalmas platform, amely lehetővé teszi a felhőbeli big-data alkalmazások kiépítését az ökoszisztéma formátumú körül Hadoop nyílt forráskódú technológiák használatával. Microsoft Azure nyílt forráskódú technológiák, egy általános szintű támogatást kínál a leírt módon a **-támogatás hatóköre** szakaszában a <a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure támogatási gyakori Kérdéseinek webhelyre</a>. A HDInsight szolgáltatás egy további szintű egyes összetevői, támogatást biztosít, az alább ismertetett.

A HDInsight szolgáltatásban elérhető nyílt forráskódú összetevőket két típusa van:

* **Beépített összetevők** – ezek az összetevők a HDInsight-fürtök előre telepítve vannak, és adja meg a fürt fő funkciói. Ha például YARN ResourceManager, a Hive-lekérdezés nyelv (HiveQL) és a Mahout kódtár tartoznak ebbe a kategóriába. Kiszolgálófürt-összetevők teljes listája megtalálható [a HDInsight által biztosított Hadoop-fürtverziók újdonságai?](hdinsight-component-versioning.md) </a>.
* **Egyéni összetevők** -, a fürt felhasználói telepítése vagy használata az alkalmazások és szolgáltatások valamelyik összetevő a Közösségben elérhető vagy Ön által létrehozott.

Beépített összetevők teljes mértékben támogatottak, és a Microsoft Support fog help elkülönítésére, és ezeket az összetevőket kapcsolatos problémák megoldásához.

> [!WARNING]
> A HDInsight-fürthöz megadott összetevők teljes mértékben támogatottak, és a Microsoft Support fog help elkülönítésére, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők annak érdekében, hogy a probléma további hibaelhárításához üzletileg ésszerű támogatást kapnak. Emiatt előfordulhat, hogy a probléma megoldásához vagy rákérdez arra, hogy a nyílt forráskódú technológiák, ahol található részletes szakértelmét, hogy a technológiát a rendelkezésre álló csatorna léphet. Számos, használható, például közösségi helyek vannak, például: [HDInsight az MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Is Apache projektek rendelkeznek projekt helyek [ http://apache.org ](http://apache.org), például: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).
>
>

A HDInsight szolgáltatás használatához az egyéni összetevők több módot nyújt. Függetlenül attól, milyen egy összetevő használt vagy a fürtön telepíteni azonos szintű támogatási vonatkozik. Alább felsoroljuk a leggyakoribb módja, hogy egyéni összetevők a HDInsight-fürtökön is használható:

1. Feladat küldése – a Hadoop és egyéb feladatok végrehajtása, vagy használjon egyéni összetevők küldheti el a fürtöt.
2. Fürt testreszabása – fürt létrehozása során, megadhat további beállításokat és egyéni, a fürtcsomópontokon telepített összetevőket.
3. Minták – a népszerű egyéni összetevők, a Microsoft és mások rendelkezhetnek mintákat ezeket az összetevőket hogyan használható a HDInsight-fürtökön. Ezek a minták támogatás nélkül állnak rendelkezésre.

## <a name="develop-script-action-scripts"></a>Parancsfájlművelet-parancsfájlok fejlesztése
Lásd: [Szkriptművelet fejlesztése HDInsight-parancsfájlok][hdinsight-write-script].

## <a name="see-also"></a>Lásd még
* [A HDInsight Hadoop-fürtök létrehozása] [ hdinsight-provision-cluster] útmutatás egy HDInsight-fürt létrehozása más egyéni beállításai használatával.
* [Parancsfájlművelet-parancsfájlok fejlesztése a HDInsight][hdinsight-write-script]
* [Spark telepítése és használata HDInsight-fürtökön][hdinsight-install-spark]
* [Soir telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-solr-install.md).
* [Giraph telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fürt létrehozása során szakaszai"
