---
title: A Parancsfájlműveletek - Azure HDInsight-fürtök testreszabása |} Microsoft Docs
description: Ismerje meg a parancsfájlművelet HDInsight-fürtök testreszabása.
services: hdinsight
documentationcenter: ''
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
ms.openlocfilehash: f5785fd6dc9b2c66913f7226a9c75246382c0485
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Parancsfájlművelet Windows-alapú HDInsight-fürtök testreszabása
**Parancsfájl-művelet** alkalmazásához használt [egyéni parancsfájlok](hdinsight-hadoop-script-actions.md) a fürt további szoftver telepítése a fürt létrehozása során.

Ebben a cikkben szereplő információk csak a Windows-alapú HDInsight-fürtök az elő. Linux-alapú fürtök esetében lásd: [testreszabása Linux-alapú HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster-linux.md).

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

További Azure Storage-fiókok ideértve például a HDInsight-fürtök testre szabható más módszerekkel is, számos, a Hadoop módosítása konfigurációs fájljai (core-site.xml, hive-site.xml stb.), vagy a megosztott függvénytárak (például a Hive, az Oozie) hozzáadása a fürt közös helyen való. Ezek a testreszabások végezhető el az Azure PowerShell, az Azure HDInsight .NET SDK vagy az Azure-portálon. További információkért lásd: [Hadoop létrehozása a HDInsight-fürtök][hdinsight-provision-cluster].

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>A Fürtlétrehozási folyamat parancsfájlművelet
Parancsfájl művelet csak akkor használatos, miközben a fürt létrehozása folyamatban van. A következő ábra szemlélteti a parancsfájl művelet végrehajtásakor a létrehozási folyamat során:

![A HDInsight fürt testreszabási és fürt létrehozása során szakaszai][img-hdi-cluster-states]

A parancsprogram futtatásakor kerül, a fürt a **ClusterCustomization** szakaszban. Ebben a szakaszban a parancsfájlt a fürt összes az adott csomóponton párhuzamosan a rendszer rendszergazdai fiók alatt fut, és teljes rendszergazdai jogosultságokat biztosít a csomópontokon.

> [!NOTE]
> Mivel a rendszergazdai jogosultságokkal rendelkezik a fürtcsomópontokon során a **ClusterCustomization** szakasza alatt a parancsfájlt használhatja például a szolgáltatások, beleértve a Hadoop-kapcsolatos szolgáltatások indítása és leállítása műveletek végrehajtásához. Igen, a parancsfájl gondoskodnia kell arról, hogy az Ambari és egyéb Hadoop-kapcsolódó szolgáltatás megfelelően működik, mielőtt a parancsfájl befejezése után történik. Ezek a szolgáltatások szükségesek állapotát és a fürt állapotának sikeresen megállapítása közben létrehozása folyamatban van. Ha módosítja a fürtön, ezeket a szolgáltatásokat érintő beállításra, a súgófunkciókat által biztosított kell használnia. Súgófunkciókat kapcsolatos további információkért lásd: [parancsfájlművelet fejlesztése parancsfájlok a HDInsight][hdinsight-write-script].
>
>

A kimeneti és a hibanaplókat a parancsfájl az alapértelmezett tárfiókot, a fürt számára megadott vannak tárolva. A naplók tárolódnak a nevű tábla **u < \cluster-name-fragment >< \time-stamp > setuplog**. Ezek a összesített napló, a parancsfájl futhat az összes (átjárócsomópont és feldolgozó csomópontokat) a fürt csomópontja.

Az egyes fürtökön fogadhatnak kerül meghívásra, amely a megadott sorrendben több parancsfájl-műveletek. Egy parancsfájlt is futott, az átjárócsomópont, a munkavégző csomópontokhoz vagy mindkettőt.

HDInsight több parancsprogramokat a HDInsight-fürtök az alábbi összetevők telepítése itt:

| Name (Név) | Szkript |
| --- | --- |
| **Spark telepítése** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Lásd: [telepítése és használata a HDInsight Spark-fürtök][hdinsight-install-spark]. |
| **R telepítéséhez** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Lásd: [telepítése és használata R HDInsight-fürtök][hdinsight-install-r]. |
| **Solr telepítése** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Lásd: [telepítése és használata Solr a HDInsight-fürtök](hdinsight-hadoop-solr-install.md). |
| - **Giraph telepítése** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Lásd: [telepítése és használata Giraph a HDInsight-fürtök](hdinsight-hadoop-giraph-install.md). |
| **Hive-könyvtárakhoz előzetes betöltése** |https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1. Lásd: [kódtárak hozzáadása Hive HDInsight-fürtök](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="call-scripts-using-the-azure-portal"></a>Az Azure portál használatával hívás parancsfájlok
**Az Azure-portálon**

1. Indítsa el a fürt létrehozása a részben ismertetett módon [Hadoop létrehozása a HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md).
2. Választható konfiguráció alatt az a **Parancsfájlműveletek** panelen kattintson **parancsfájlművelet hozzáadása** adni a parancsfájlművelet alább látható módon:

    ![Parancsfájlművelet használni ahhoz, hogy a fürt](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "parancsfájlművelet használja a fürt testreszabása")

    <table border='1'>
        <tr><th>Tulajdonság</th><th>Érték</th></tr>
        <tr><td>Name (Név)</td>
            <td>Adja meg a parancsfájlművelet nevét.</td></tr>
        <tr><td>A parancsfájl URI azonosítója</td>
            <td>Adja meg az URI-t a parancsfájlt, amelyet a fürt testreszabásához. s</td></tr>
        <tr><td>HEAD/munkavégző</td>
            <td>Adja meg a csomópontok (**Head** vagy **munkavégző**) a testreszabási parancsfájl futtatása a</b>.
        <tr><td>Paraméterek</td>
            <td>Adja meg a paraméterek, ha a parancsfájl által igényelt.</td></tr>
    </table>

    Nyomja le az ENTER billentyűt több összetevőinek telepítése a fürt több parancsfájlművelet hozzáadása.
3. Kattintson a **válasszon** a parancsfájl művelet konfiguráció mentéséhez, majd folytassa a fürt létrehozása.

## <a name="call-scripts-using-azure-powershell"></a>Hívja meg az Azure PowerShell parancsfájlok
A következő PowerShell-parancsfájl bemutatja, hogyan kell külső telepítse a Windows-alapú HDInsight-fürthöz.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

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
        Login-AzureRmAccount
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


Más szoftverek telepítése, akkor cserélje le a parancsfájlt a parancsfájl:

Amikor a rendszer kéri, adja meg a hitelesítő adatok a fürt. A fürt létrehozása előtt több percet is igénybe vehet.

## <a name="call-scripts-using-net-sdk"></a>Hívás parancsfájlok .NET SDK használatával
A következő példa bemutatja, hogyan Spark telepítse a Windows-alapú HDInsight-fürthöz. Más szoftver telepítéséhez, akkor cserélje le a parancsfájlt a kódban.

**A Spark HDInsight-fürtök létrehozása**

1. Hozzon létre egy C# konzolalkalmazást a Visual Studióban.
2. A Nuget-Csomagkezelő konzolról a következő parancsot.

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

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftvereket támogatása
A Microsoft Azure HDInsight-szolgáltatás rugalmas platform, amely lehetővé teszi a felhőalapú big data-alkalmazások összeállítását az ökoszisztéma formátumú-e körül Hadoop nyílt forráskódú technológiák használatával. A Microsoft Azure biztosít, általános támogatási nyílt forráskódú technológiák leírtaknak megfelelően a **támogatja a hatókör** szakasza a <a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure támogatási gyakori Kérdéseinek webhelyre</a>. A HDInsight-szolgáltatás egy további szintű egyes összetevői, támogatást biztosít, az alább ismertetett.

A HDInsight szolgáltatásban elérhető nyílt forráskódú összetevőinek két típusa van:

* **A beépített összetevők** -ezeket az összetevőket a HDInsight-fürtök előre telepített, és adja meg a fürt alapvető funkcióit. Például YARN erőforrás-kezelő, a Hive lekérdezési nyelv (HiveQL) és a Mahout könyvtárban a kategóriába tartoznak. A kiszolgálófürt-összetevők teljes listáját a érhető [What's new in HDInsight által biztosított Hadoop-fürt verziók?](hdinsight-component-versioning.md) </a>.
* **Egyéni összetevők** -Ön, mint a felhasználó a fürt telepítése vagy használata előtt a munkaterhelésnek valamelyik összetevő a közösségi érhető el, vagy Ön által létrehozott.

A beépített összetevők teljes mértékben támogatottak, és a Microsoft Support fog help elkülönítésére, és ezeket az összetevőket kapcsolatos problémák megoldásához.

> [!WARNING]
> A HDInsight-fürt összetevői teljes mértékben támogatottak, és a Microsoft Support fog help elkülönítésére, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők kapnak minden üzleti szempontból ésszerű támogatási segítséget nyújtanak a probléma további hibaelhárításához. A probléma megoldását, vagy kéri fel, a nyílt forráskódú technológiák, ahol a részletes segítséget, hogy a technológiát található elérhető csatorna végezhetnek eredményezhet. Például nincsenek sok közösségi webhelyek használható, például: [MSDN fórum hdinsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Is Apache projektek rendelkezik projekt helyek [ http://apache.org ](http://apache.org), például: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).
>
>

A HDInsight-szolgáltatás többféleképpen is egyéni összetevőket használnak. Függetlenül attól, milyen összetevőt használja vagy a fürt telepíteni az azonos szintű támogatást vonatkozik. Alább felsoroljuk, hogy használható-e az egyéni összetevőkben HDInsight-fürtök leggyakoribb módja:

1. Feladat elküldése - Hadoop és egyéb feladatok végrehajtása, vagy használjon egyéni összetevők küldheti el a fürtöt.
2. Fürt testreszabási - fürt létrehozása során megadhatja további beállításokat és a fürtcsomópontokon telepített egyéni összetevők.
3. Minták – a népszerű egyéni összetevők, a Microsoft és a mások által biztosított mintákat ezeket az összetevőket hogyan használható a HDInsight-fürtök. Ezeket a mintákat támogatás nélkül van megadva.

## <a name="develop-script-action-scripts"></a>Parancsfájlművelet-parancsfájlok fejlesztése
Lásd: [parancsfájlművelet fejlesztése parancsfájlok a HDInsight][hdinsight-write-script].

## <a name="see-also"></a>Lásd még
* [Hdinsight Hadoop-fürtök létrehozása] [ hdinsight-provision-cluster] HDInsight-fürtök létrehozása más egyéni beállítások használatával kapcsolatos utasításokat tartalmazza.
* [A HDInsight parancsfájlművelet-parancsfájlok fejlesztése][hdinsight-write-script]
* [Telepítse, és válassza a Spark on HDInsight-fürtök][hdinsight-install-spark]
* [Telepítheti és használhatja a HDInsight-fürtök R][hdinsight-install-r]
* [Telepítheti és használhatja a HDInsight-fürtök Solr](hdinsight-hadoop-solr-install.md).
* [Telepítheti és használhatja a HDInsight-fürtök Giraph](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fürt létrehozása során szakaszból"
