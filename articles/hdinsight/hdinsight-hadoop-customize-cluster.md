<properties
    pageTitle="使用指令碼動作來自訂 HDInsight 叢集 | Microsoft Azure"
    description="深入了解使用指令碼動作來自訂 HDInsight 叢集。"
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/13/2015"
    ms.author="nitinme"/>

# 使用指令碼動作來自訂 HDInsight 叢集 (Windows)

[AZURE.INCLUDE [usescriptaction-selector](../../includes/hdinsight-selector-use-script-action.md)]

**指令碼動作** 可以用來叫用 [自訂指令碼](hdinsight-hadoop-script-actions.md) 
在叢集上安裝其他軟體在叢集建立程序。

本文的資訊是針對以 Windows 為基礎的 HDInsight 叢集。 使用索引標籤 
切換至本文的特定 linux 叢集的版本，請選取上方。

HDInsight 叢集可以在各種不同的其他方式，例如包括自訂 
其他的 Azure 儲存體帳戶、 變更 Hadoop 組態檔 (core-site.xml， 
hive-site.xml 等），或將共用程式庫 （例如 Hive、 Oozie） 加入至一般位置 
在叢集中。 這些自訂可以透過 Azure PowerShell，Azure 
HDInsight.NET SDK 或 Azure 入口網站。 如需詳細資訊，請參閱 
[在 HDInsight 中建立 Hadoop 叢集][hdinsight-provision-cluster]。

## 叢集建立程序中的指令碼動作

只有正在建立叢集時，才會使用指令碼動作。 下列 
圖說明當建立程序期間執行指令碼動作 ︰

![HDInsight 叢集自訂和叢集建立期間的階段][img-hdi-cluster-states]

當執行指令碼時，叢集會進入 **ClusterCustomization** 階段。 在此 
階段中，以平行方式上所有在系統管理員帳戶下執行指令碼指定 
叢集中的節點，並在節點上提供完整的系統管理員權限。

> [AZURE.NOTE] 因為叢集節點上擁有系統管理員權限 
**ClusterCustomization** 階段中，您可以使用指令碼執行作業，例如停止 
與啟動服務，包括 Hadoop 相關服務。 因此，在指令碼，您必須 
確定 Ambari 服務及其他 Hadoop 相關服務已啟動並執行之前 
指令碼完成執行。 這些服務必須成功地確定健全狀況 
和叢集在建立時的狀態。 如果您在變更任何設定 
叢集，這些服務的影響，您必須使用所提供的協助程式函式。 如需 
helper 函式的詳細資訊請參閱 [hdinsight 開發指令碼動作指令碼][hdinsight-write-script]。

輸出和錯誤記錄檔指令碼會儲存在預設儲存體帳戶 
指定給叢集。 記錄會儲存在資料表名稱 
**u < \cluster-name-fragment >< \time-stamp > u<\cluster-name-fragment><\time-stamp>setuplog**。 這些是來自指令碼彙總的記錄檔 
在所有節點 （前端節點和背景工作節點） 在叢集上執行。

每個叢集可接受多個指令碼動作叫用的順序，它們 
所指定。 指令碼可在前端節點、背景工作節點或同時在兩者執行。

HDInsight 提供數個指令碼在 HDInsight 叢集上安裝下列元件：

名稱 | 指令碼
----- | -----
**安裝 Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1。 請參閱 [HDInsight 上的 Spark 叢集的安裝和使用][hdinsight-install-spark]。
**安裝 R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1。 請參閱 [安裝和使用 R HDInsight 叢集上的][hdinsight-install-r]。
**安裝 Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1。 請參閱 [安裝和使用 HDInsight 叢集上 Solr](hdinsight-hadoop-solr-install.md)。
- **安裝 Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1。 請參閱 [Giraph 的 HDInsight 上的叢集安裝和使用](hdinsight-hadoop-giraph-install.md)。



## 使用 Azure 入口網站呼叫指令碼

**從 Azure 入口網站**

1. 開始建立叢集述 [建立 Hadoop 叢集的 HDInsight](hdinsight-provision-clusters.md#portal)。
2. 選用設定] 下的 **指令碼動作** 刀鋒視窗中，按一下 [ **加入指令碼動作** 提供有關指令碼動作，詳細資料，如下所示 ︰

    ![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Use Script Action to customize a cluster")

    <table border='1'>
        <tr><th>屬性</th><th>值</th></tr>
        <tr><td>名稱</td>
            <td>指定指令碼動作的名稱。</td></tr>
        <tr><td>指令碼 URI</td>
            <td>對自訂叢集所叫用的指令碼指定 URI。 s</td></tr>
        <tr><td>Head/Worker</td>
            <td>指定的節點 (**Head** 或 **工作者**) 上執行自訂指令碼。</b>。
        <tr><td>參數</td>
            <td>如果指令碼要求，請指定參數。</td></tr>
    </table>

    請按 ENTER 加入一個以上的指令碼動作，以在叢集上安裝多個元件。

3. 按一下 [ **選取** 儲存指令碼動作組態，然後繼續建立叢集。

## 使用 Azure PowerShell 呼叫指令碼

接下來的這個 PowerShell 指令碼示範如何在以 Windows 為基礎的 HDInsight 叢集上安裝 Spark。  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    
    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
    
    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "Pass@word111"
    
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
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
    $defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext
    
    #############################################################
    # Create cluster with Spark
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


若要安裝其他軟體，您必須取代指令碼中的指令碼檔案：


出現提示時，請輸入叢集的認證。 建立叢集可能需要幾分鐘的時間。

## 使用 .NET SDK 呼叫指令碼 

下列範例示範如何在以 Windows 為基礎的 HDInsight 叢集上安裝 Spark。 若要安裝其他軟體，您必須取代程式碼中的指令碼檔案。

**使用 Spark 建立 HDInsight 叢集** 

1. 在 Visual Studio 建立 C# 主控台應用程式。
2. 從 NuGet Package Manager Console 執行下列命令：

        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Common.Authentication -Pre

2. 在 Program.cs 檔案中使用下列 using 陳述式：

        using System;
        using System.Security;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;

3. 使用下列命令將程式碼放置在類別中：

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<YourAzureSubscriptionID>");
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumNodes = <NumberOfClusterNodes>;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();

        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }


        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }

4. 按下 **F5** 執行應用程式。


## 支援在 HDInsight 叢集上使用開放原始碼軟體
Microsoft Azure HDInsight 服務是彈性的平台，可讓您使用以 Hadoop 形成之開放原始碼技術的生態系統，在雲端中建置巨量資料應用程式。 Microsoft Azure 會提供對開放原始碼技術支援的一般層級中所述 **支援範圍** 區段 <a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure 支援常見問題集網站</a>。 HDInsight 服務對於某些元件提供額外層級的支援，如下所述。

HDInsight 服務中有兩種類型的開放原始碼元件可用：

- **內建元件** -這些元件預先安裝在 HDInsight 叢集上，並且提供叢集的核心功能。 例如，YARN ResourceManager、Hive 查詢語言 (HiveQL) 及 Mahout 程式庫都屬於這個類別。 叢集元件的完整清單位於 [的 HDInsight 所提供的 Hadoop 叢集版本的新功能？](hdinsight-component-versioning.md)</a>。
- **自訂元件** -您為叢集中，使用者可以安裝或使用您的工作負載在社群中可用或是您建立的任何元件。

內建元件受到完整支援，且 Microsoft 支援服務將會協助釐清與解決這些元件的相關問題。

> [AZURE.WARNING] 隨附於 HDInsight 叢集的元件受到完整支援，並以隔離並解決這些元件的相關問題協助 Microsoft 支援服務。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。 如此可能會進而解決問題，或要求您利用可用管道，以找出開放原始碼技術，從中了解該技術的深度專業知識。 例如，有許多社群網站可以使用，像是 ︰ [適用於 HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), ，[http://stackoverflow.com](http://stackoverflow.com)。 Apache 專案也有專案網站 [http://apache.org](http://apache.org), ，例如 ︰ [Hadoop](http://hadoop.apache.org/), ，[Spark](http://spark.apache.org/)。

HDInsight 服務提供數種方式以使用自訂元件。 無論元件如何使用或如何安裝在叢集上，都適用相同層級的支援。 以下是自訂元件可用於 HDInsight 叢集之最常見方式的清單：

1. 工作提交 - Hadoop 或其他類型的工作，執行或使用可以提交給叢集的自訂元件。
2. 叢集自訂 - 在叢集建立期間，您可以指定額外設定和將會安裝在叢集節點上的自訂元件。
3. 範例 - 對於熱門自訂元件，Microsoft 和其他提供者可能會提供如何在 HDInsight 叢集上使用這些元件的範例。 提供這些範例，但是沒有支援。

## 開發指令碼動作指令碼

請參閱 [hdinsight 開發指令碼動作指令碼][hdinsight-write-script]。


## 另請參閱

- [在 HDInsight 中建立 Hadoop 叢集][hdinsight-provision-cluster] 提供如何使用其他自訂選項建立 HDInsight 叢集的指示。
- [開發 HDInsight 的指令碼動作指令碼][hdinsight-write-script]
- [在 HDInsight 叢集上安裝及使用 Spark][hdinsight-install-spark]
- [在 HDInsight 叢集上安裝及使用 R][hdinsight-install-r]
- [安裝及使用 Solr HDInsight 叢集上](hdinsight-hadoop-solr-install.md)。
- [安裝及使用 Giraph HDInsight 叢集上](hdinsight-hadoop-giraph-install.md)。

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster creation"


