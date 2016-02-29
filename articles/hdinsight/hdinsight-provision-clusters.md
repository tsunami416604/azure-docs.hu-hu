<properties
   pageTitle="在 HDInsight 中建立 Hadoop 叢集 | Microsoft Azure"
    description="瞭解如何使用 Azure 入口網站、Azure PowerShell、命令列或 .NET SDK 來建立 Azure HDInsight 的叢集。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/11/2015"
   ms.author="jgao"/>

# 在 HDInsight 中建立 Hadoop 叢集

了解如何規劃 HDInsight 叢集建立作業。


###必要條件：

開始執行本文中的指示之前，您必須擁有以下項目：

- Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。


## 基本組態選項

下列是關於建立 HDInsight 叢集的基本組態選項。

- **叢集名稱**

    叢集名稱用來識別叢集。 叢集名稱必須遵循下列方針：

    - 欄位必須是包含 3 到 63 個字元的字串。
    - 欄位只能包含字母、數字和連字號。

- **訂用帳戶名稱**

    一個 HDInsight 叢集與一個 Azure 訂用帳戶綁定。

- **資源群組名稱**

    Azure 資源管理員 (ARM) 可讓您能夠使用您的應用程式群組中的資源 
    稱為 Azure 資源群組。 您可以部署、 更新、 監視或刪除的所有資源 
    應用程式在單一、 協調作業。 如需詳細資訊， 
    請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。  
    
- **作業系統**

    您可以建立 HDInsight 叢集上的下列兩個作業系統其中一個:
    - **Windows (Windows Server 2012 R2 Datacenter) 上的 HDInsight**:
    - **Linux (Ubuntu 12.04 LTS 適用於 Linux) 上的 HDInsight**: HDInsight 提供在 Azure 上設定 Linux 叢集的選項。 如果您熟悉 Linux 或 Unix、要從現有的 Linux Hadoop 方案進行移轉，或想輕鬆整合針對 Linux 所建置的 Hadoop 生態系統元件，請設定 Linux 叢集。 如需詳細資訊，請參閱 [開始在 HDInsight 中的 Linux 上使用 Hadoop](hdinsight-hadoop-linux-get-started.md)。

- **叢集類型** 和 **叢集大小 (亦稱為資料節點)**

    HDInsight 可讓客戶部署各種不同的叢集類型，用於不同的資料分析工作負載。 目前提供的叢集類型包括：

    - Hadoop 叢集：適用於查詢和分析工作負載
    - HBase 叢集: NoSQL 工作負載
    - Storm 叢集：適用於即時事件的處理工作負載
    - Spark 叢集 (預覽)：適用於記憶體內部處理、互動式查詢、資料流和機器學習工作負載。

    ![HDInsight 叢集](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

    > [AZURE.NOTE] *Azure HDInsight 叢集* 也稱為 *HDInsight 中的 Hadoop 叢集*, ，或 *hdinsight*。 某些情況下，它搭配交替 *Hadoop 叢集*。 它們都代表裝載於 Microsoft Azure 環境中的 Hadoop 叢集。

    在特定叢集類型中，各節點有不同的角色，可讓客戶針對特定角色，根據適合其工作負載的詳細資料來調整節點的大小。 舉例來說，若執行的分析作業類型會耗用大量記憶體，Hadoop 叢集將可使用大量記憶體來建立背景工作節點。

    ![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

    HDInsight 的 Hadoop 叢集利用兩個角色部署：

    - 前端節點 (2 個節點)
    - 資料節點 (至少 1 個節點)

    ![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

    Hdinsight 的 HBase 叢集利用三個角色部署：
    - 前端伺服器 (2 個節點)
    - 區域伺服器 (至少 1 個節點)
    - 主要/Zookeeper 節點 (3 個節點)

    ![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

    HDInsight 的 Storm 叢集利用三個角色部署：
    - Nimbus 節點 (2 個節點)
    - 監督員伺服器 (至少 1 個節點)
    - Zookeeper 節點 (3 個節點)


    ![HDInsight Hadoop cluster roles](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

    Spark clusters for HDInsight are deployed with three roles:
    - Head node (2 nodes)
    - Worker node (at least 1 node)
    - Zookeeper nodes (3 nodes) (Free for A1 Zookeepers)

    Customers are billed for the usage of those nodes for the duration of the cluster’s life. Billing starts once a cluster is created and stops when the cluster is deleted (clusters can’t be de-allocated or put on hold). The cluster size affects the cluster price. For learning purposes, it is recommended to use 1 data node. For more information about HDInsight pricing, see [HDInsight pricing](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


    >[AZURE.NOTE] The cluster size limit varies among Azure subscriptions. Contact billing support to increase the limit.

- **HDInsight 版本**

    這可用來決定要用於此叢集的 HDInsight 版本。 如需詳細資訊，請參閱 [Hadoop 叢集版本和在 HDInsight 中的元件](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)


- **位置 (區域)**

    HDInsight 叢集與其預設儲存體帳戶必須並存於相同的 Azure 位置。
    
    ![Azure 區域](./media/hdinsight-provision-clusters/Azure.regions.png)

    如需支援的地區清單，按一下 [ **區域** 下拉式清單上的 [HDInsight 定價](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)。

- **節點大小**

    ![hdinsight vm 節點大小](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

    選取 VM 的節點大小。 如需詳細資訊，請參閱 [雲端服務的大小](cloud-services-sizes-specs.md)

    根據選擇的 VM ，您的成本可能會有所不同。 HDInsight 針對叢集節點會使用所有標準層 VM。 如需 VM 大小對您價格的影響，請參閱 <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 定價</a>.


- **HDInsight 使用者**

    HDInsight 叢集可讓您在佈建期間設定兩個使用者帳戶：

    - HTTP 使用者。 預設使用者名稱是在 Azure 入口網站上使用基本組態的 admin。
    - RDP 使用者 (Windows 叢集)：用來連線到使用 RDP 的叢集。 當您建立帳戶時，必須將到期日設為從今天算起的 90 天內。
    - SSH 使用者 (Linux 叢集)：用來連線到使用 SSH 的叢集。 依照步驟建立叢集之後，您可以建立其他 SSH 使用者帳戶 [使用 SSH 與以 Linux 為基礎的 Hadoop，從 Linux、 Unix 或 OS X 在 HDInsight 上](hdinsight-hadoop-linux-use-ssh-unix.md)。



- **Azure 儲存體帳戶**

    原始的 HDFS 會使用叢集上的多個本機磁碟。 HDInsight 則會使用 Azure Blob 儲存體來儲存資料。 Azure Blob 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。 透過 Hadoop 分散式檔案系統 (HDFS) 介面，HDInsight 中的完整元件集可直接處理 Blob 儲存體中的結構化或非結構化資料。 將資料儲存在 Blob 儲存體中，您便可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。

    在設定期間，您必須指定 Azure 儲存體帳戶，並在該 Azure 儲存體帳戶中指定 Azure Blob 儲存體容器。 某些建立程序會要求您事先建立 Azure 儲存體帳戶和 Blob 儲存體容器。  叢集會以該 Blob 儲存體容器做為預設儲存位置。 您也可以選擇指定叢集可存取的其他 Azure 儲存體帳戶 (連結的儲存體)。 此外，叢集也可以存取任何設有完整公用讀取權限或僅限對 blob 之公用讀取權的 Blob 容器。  如需有關限制存取的詳細資訊，請參閱 [管理 Azure 儲存體資源的存取](storage-manage-access-to-resources.md)。

    ![HDInsight 儲存體](./media/hdinsight-provision-clusters/HDInsight.storage.png)

    >[AZURE.NOTE] Blob 儲存體容器提供一組 blob 的群組，如圖所示:

    ![Azure Blob 儲存體](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


    >[AZURE.WARNING] Don't share one Blob storage container for multiple clusters. This is not supported.

    For more information on using secondary Blob stores, see [Using Azure Blob Storage with HDInsight](hdinsight-use-blob-storage.md).

- **Hive/Oozie 中繼存放區**

    中繼存放區包含 Hive 和 Oozie 中繼資料，例如 Hive 資料表、資料分割、結構描述和資料行。 使用中繼存放區能協助您保留 Hive 和 Oozie 中繼資料，因此在建立新叢集時，您便無需重建 Hive 資料表或 Oozie 工作。 根據預設，Hive 會使用內嵌的 Azure SQL 資料庫來儲存此資訊。 刪除叢集時，嵌入的資料庫將無法保留中繼資料。 舉例來說，您使用 Hive 中繼存放區來建立叢集。 您已建立一些 Hive 資料表。 當您刪除叢集並透過相同的 Hive 中繼存放區重建叢集之後，便可以看到您在原始的叢集中建立的 Hive 資料表。
    
    > [AZURE.NOTE] 中繼存放區組態不適用於 HBase 叢集類型。

## 進階組態選項

### 使用 HDInsight 叢集自訂功能來自訂叢集

有時候，您可能需要設定組態檔：

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

叢集無法保留重新製作映像所造成的變更。 如需詳細資訊， 
請參閱 [角色執行個體重新啟動因作業系統升級而](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)。 
若要在叢集存留期間保留變更，您可以在建立程序期間使用 HDInsight 叢集自訂。

以下是自訂 Hive 組態的 Azure PowerShell 指令碼範例：

    # hive-site.xml configuration
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
    
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 
    
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $existingResourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -Config $config 

以下是更多自訂其他組態檔的範例：

    # hdfs-site.xml configuration
    $HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

    # core-site.xml configuration
    $CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

    # mapred-site.xml configuration
    $MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

    # oozie-site.xml configuration
    $OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

如需詳細資訊，請參閱標題為 Azim (英文) 部落格 [自訂 HDInsight 叢集 creationg](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx)。




### 使用指令碼動作來自訂叢集

您可以在建立期間使用指令碼來安裝其他元件或自訂組態。 這類指令碼會透過叫用 **指令碼動作**, ，這是組態選項，可用來從入口網站中，HDInsight Windows PowerShell cmdlet 或 HDInsight.NET SDK。 如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。


### 使用 Azure 虛擬網路

[Azure 虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/) 可讓您建立安全、 持續的網路，上面有您解決方案所需的資源。 虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。

    ![diagram of cloud-only configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

    站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接至 Azure 虛擬網路。

    ![diagram of site-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

    點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。

    ![diagram of point-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

如需有關虛擬網路功能、 優點和功能的詳細資訊，請參閱 [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

> [AZURE.NOTE] 佈建 HDInsight 叢集之前，您必須建立 Azure 虛擬網路。 如需詳細資訊，請參閱 [建立 Hadoop 叢集的虛擬網路到](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network)。
>
> Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。 使用 Azure PowerShell Cmdlet Get-AzureVNetConfig 來檢查現有的 Azure 虛擬網路是否以位置為基礎。 如果您的虛擬網路並非以位置為基礎，您會有下列選項：
>
> - 將現有的虛擬網路組態匯出，然後建立新的虛擬網路。 所有新的虛擬網路是以預設的位置。
> - 移轉到以位置為基礎的虛擬網路。  請參閱 [現有服務移轉到區域範圍](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)。
>
> 強烈建議您一個叢集只指定一個子網路。

## 使用入口網站

您可以參考 [基本組態選項](#basic-configuration-options), ，而 [進階組態選項的](#advanced-configuration-options) 欄位的說明。

**建立 HDInsight 叢集**

1. 登入 [Azure 入口網站][azure-preview-portal]。
2. 按一下 [ **新增**, ，按一下 [ **資料分析**, ，然後按一下 [ **HDInsight**。

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creating a new cluster in the Azure Portal")

3. 輸入或選取下列值：

  * **叢集名稱**: 輸入叢集的名稱。 如果該叢集名稱可使用，則名稱旁邊將會出現綠色的核取記號。
  * **叢集類型**: 選取 **Hadoop**。
  * **叢集作業系統**: 選取 **Windows Server 2012 R2 Datacenter**。
  * **訂閱**: 選取將用來建立此叢集的 Azure 訂閱。
  * **資源群組**: 選取現有或建立新的資源群組。 如果有可用的資源群組，則此項目會預設為現有資源群組的其中一個群組。
  * **認證**: 設定使用者名稱和 Hadoop 使用者 (HTTP 使用者) 的密碼。 如果您啟用該叢集的遠端桌面，您必須設定遠端桌面使用者的使用者名稱、密碼和帳戶到期日。 按一下 [ **選取** 底部以儲存變更。

        ![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **資料來源**: 建立新的或選取現有的 Azure 儲存體帳戶作為叢集預設檔案系統。

        ![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

        * **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
        * **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
        * **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
        * **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
    
  * **節點定價層**: 設定叢集所需的背景工作節點數目。 該叢集的預估成本將會顯示在此刀鋒視窗內。
  

        ![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **選擇性設定** 選取叢集的版本，以及設定其他選擇性設定，例如聯結 **虛擬網路**, 、 設定 **外部中繼存放區** 若要保留 Hive 和 Oozie 的資料，使用指令碼動作自訂叢集來安裝自訂元件，或使用其他儲存體帳戶與叢集。

        * **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
        * **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

            ![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

            >[AZURE.NOTE] Windows based HDInsight cluster can only be placed into a classical virtual network.
        
        * **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

            ![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")

            For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.

            >[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.
        
        * **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.

            ![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")

        * **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.

            ![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")

4. 按一下 [ **建立**。 選取 **釘選到開始面板** 會將叢集的磚加入至您的入口網站的儀表板。 該圖示可表示該叢集正在建立，並將在建立完成後變更為 HDInsight 圖示。


    | While creating | Creationg complete |
    | ------------------ | --------------------- |
    | ![Provisioning indicator on startboard](./media/hdinsight-provision-clusters/provisioning.png) | ![Provisioned cluster tile](./media/hdinsight-provision-clusters/provisioned.png) |


    
    > [AZURE.NOTE] It will take some time for the cluster to be created, usually around 15 minutes. Use the tile on the Startboard, or the **Notifications** entry on the left of the page to check on the provisioning process.
    

5. 建立完成後，在「開始面板」按一下該叢集磚，以啟動叢集刀鋒視窗。 此叢集刀鋒視窗提供該叢集的基本資訊，如名稱、其所屬的資源群組、位置、作業系統、叢集儀表板 URL 等。


    ![Cluster blade](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Cluster properties")


    Use the following to understand the icons at the top of this blade, and in the **Essentials** section:


    * **Settings** and **All Settings**: Displays the **Settings** blade for the cluster, which allows you to access detailed configuration information for the cluster.
    * **Dashboard**, **Cluster Dashboard**, and **URL**: These are all ways to access the cluster dashboard, which is a Web portal to run jobs on the cluster.
    * **Remote Desktop**: Enables you to enable/disable remote desktop on the cluster nodes.
    * **Scale Cluster**: Allows you to change the number of worker nodes for this cluster.
    * **Delete**: Deletes the HDInsight cluster.
    * **Quickstart** (![cloud and thunderbolt icon = quickstart](./media/hdinsight-provision-clusters/quickstart.png)): Displays information that will help you get started using HDInsight.
    * **Users** (![users icon](./media/hdinsight-provision-clusters/users.png)): Allows you to set permissions for _portal management_ of this cluster for other users on your Azure subscription.
    

        > [AZURE.IMPORTANT] This _only_ affects access and permissions to this cluster in the Portal, and has no effect on who can connect to or submit jobs to the HDInsight cluster.
        
    * **Tags** (![tag icon](./media/hdinsight-provision-clusters/tags.png)): Tags allows you to set key/value pairs to define a custom taxonomy of your cloud services. For example, you may create a key named __project__, and then use a common value for all services associated with a specific project.

## 使用 ARM 範本建立

Azure 資源管理員 (ARM) 範本可讓您更容易部署和重新部署叢集。 以下程序會建立以 Linux 為基礎的 HDInsight 叢集。

**使用 ARM 範本部署叢集**

1. 儲存在 json 檔案 [附錄 A](#appendix-a---arm-template) 到您的工作站。
2. 視需要製作參數。
3. 使用下列 PowerShell 指令碼執行範本：

        $subscriptionId = "<Azure Subscription ID"
        
        $newResourceGroupName = "<Azure Resource Group Name>"
        $Location = "EAST US 2" # for creating ARM group
                
        $armDeploymentName = "New-HDInsigt-Cluster-" + (Get-Date -Format MMdd)
        $newClusterName = "<HDInsight Cluster Name>"
        $clusterStorageAccountName = "<Default Storage Account Name>"
                
        # Connect to Azure
        #Login-AzureRmAccount
        #Select-AzureRmSubscription -SubscriptionId $subscriptionId
                
        # Create a resource group
        New-AzureRmResourceGroup -Name $newResourceGroupName -Location $Location
                
        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$newClusterName";clusterStorageAccountName="$clusterStorageAccountName"}
                
        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $newResourceGroupName `
            -TemplateFile E:\HDITutorials-ARM\Create-clusters\hdinsight-arm-template.json `
            -TemplateParameterObject $parameters
                
        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $newResourceGroupName -ClusterName $newClusterName 

    PowerShell 指令碼只會設定叢集名稱和儲存體帳戶名稱。  您可以在 ARM 範本中設定其他值。 
    
部署 ARM 範本使用其他方法，請參閱 [應用程式使用 Azure 資源管理員範本部署](resource-group-template-deploy.md)。


## 使用 Azure PowerShell 建立
Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。 本節提供如何使用 Azure PowerShell 佈建 HDInsight 叢集的指示。 如需設定工作站以執行 HDInsight Windows PowerShell cmdlet 的詳細資訊，請參閱 [安裝和設定 Azure PowerShell](../install-configure-powershell.md)。 如需有關如何使用 Azure PowerShell 與 HDInsight 的詳細資訊，請參閱 [使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。 如需 HDInsight Windows PowerShell cmdlet 的清單，請參閱 [HDInsight cmdlet 參考文件](https://msdn.microsoft.com/library/azure/dn858087.aspx)。


以下是使用 Azure PowerShell 建立 HDInsight 叢集時所需執行的程序：

- 建立 Azure 資源群組
- 建立 Azure 儲存體帳戶
- 建立 Azure Blob 容器
- 建立 HDInsight 叢集


    $subscriptionId = "<Azure Subscription ID>"
    
    $newResourceGroupName = "<Azure Resource Group Name>"
    $location = "<Azure Location>" # for example, "East US 2"
    $newDefaultStorageAccountName = "<Azure Storage Account Name>"
    $newClusterName = "<Azure HDInsight Cluster Name>"
    $clusterSizeInNodes = 1
    
    ###########################################
    # login Azure
    ###########################################
    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $newRresourceGroupName -Location $location
    
    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount -ResourceGroupName $newResourceGroupName -Name $newDefaultStorageAccountName -Location $location
    
    $defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $newResourceGroupName -Name $newDefaultStorageAccountName |  %{ $_.Key1 }
    $defaultStorageContext = New-AzureStorageContext -StorageAccountName $newDefaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $newClusterName -Context $defaultStorageContext #use the cluster name as the container name
        
    ###########################################
    # Create the cluster
    ###########################################
    $httpCredential =Get-Credential -Message "Enter the HTTP account credential:"
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $newResourceGroupName `
        -ClusterName $newClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential 



## 使用 HDInsight .NET SDK 建立
HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您能夠輕鬆地從 .NET Framework 應用程式使用 HDInsight。 請遵循下列指示建立 Visual Studio 主控台應用程式，並貼上建立叢集的程式碼。

**建立 Visual Studio 主控台應用程式**

1. 在 Visual Studio 中，建立新的 C# 主控台應用程式。
2. 在 Nuget 封裝管理主控台中執行下列的 Nuget 命令。

        Install-Package Microsoft.Azure.Common.Authentication -pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre

6. 從 [方案總管] 中，按兩下 **Program.cs** 來開啟它，請貼上下列程式碼，並提供變數的值:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        
        namespace CreateHDInsightCluster
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
        
                private static Guid SubscriptionId = new Guid("<Azure Subscription ID");
                private const string ExistingResourceGroupName = "<Azure Resource Group Name>";
                private const string ExistingStorageName = "<Default Storage Account Name>.blob.core.windows.net";
                private const string ExistingStorageKey = "<Default Storage Account Key>";
                private const string ExistingBlobContainer = "<Default Blob Container Name>";
                private const string NewClusterName = "<HDInsight Cluster Name>";
                private const int NewClusterNumNodes = 1;
                private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
                private const OSType NewClusterOsType = OSType.Windows;
                private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
                private const string NewClusterVersion = "3.2";
                private const string NewClusterUsername = "admin";
                private const string NewClusterPassword = "<HTTP User password";
        
                static void Main(string[] args)
                {
                    System.Console.WriteLine("Running");
        
                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
                
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        UserName = NewClusterUsername,
                        Password = NewClusterPassword,
                        Location = NewClusterLocation,
                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingBlobContainer,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOsType
                    };
        
                    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
                }
                private static void CreateCluster()
                {
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        UserName = NewClusterUsername,
                        Password = NewClusterPassword,
                        Location = NewClusterLocation,
                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingBlobContainer,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOsType
                    };
        
                    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
                }
        
                public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
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
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), creds.Token);
        
                }
            }
        }

7. 按下 **F5** 執行應用程式。 主控台視窗會開啟並顯示應用程式的狀態。 系統也會提示您輸入 Azure 帳戶認證。 建立 HDInsight 叢集可能需要幾分鐘的時間。

## 使用內部部署 SQL Server Integration Services 建立

您也可以使用 SQL Server Integration Services (SSIS) 來建立或刪除 HDInsight 叢集。 適用於 SSIS 的 Azure Feature Pack 中提供下列元件可搭配 HDInsight 叢集使用。


- [Azure HDInsight 建立叢集工作][ssisclustercreate]
- [Azure HDInsight 刪除叢集工作][ssisclusterdelete]
- [Azure 訂用帳戶連接管理員][connectionmanager]

深入了解 Azure Feature Pack ssis [這裡][ssispack]。


##後續步驟
在本文中，您學到幾種建立 HDInsight 叢集的方法。 若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight](hdinsight-get-started.md) -了解如何開始使用 HDInsight 叢集
* [搭配 HDInsight 使用 Sqoop](hdinsight-use-sqoop.md) -了解如何在 HDInsight 與 SQL Database 或 SQL Server 之間複製資料
* [使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md) -了解如何搭配 HDInsight 使用 Azure PowerShell
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md) -了解如何以程式設計方式提交工作至 HDInsight
* [Azure HDInsight SDK 文件] [hdinsight-sdk-documentation] -探索 HDInsight SDK



##附錄 A - ARM 範本

下列 Azure 資源管理員範本會建立 Hadoop 叢集與相依的 Azure 儲存體帳戶。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "North Europe",
          "allowedValues": [
            "North Europe"
          ],
          "metadata": {
            "description": "The location where all azure resources will be deployed."
          }
        },
        "clusterName": {
          "type": "string",
          "metadata": {
            "description": "The name of the HDInsight cluster to create."
          }
        },
        "clusterLoginUserName": {
          "type": "string",
          "defaultValue": "admin",
          "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
          }
        },
        "clusterLoginPassword": {
          "type": "securestring",
          "metadata": {
            "description": "The password for the cluster login."
          }
        },
        "sshUserName": {
          "type": "string",
          "defaultValue": "username",
          "metadata": {
            "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
          }
        },
        "sshPassword": {
          "type": "securestring",
          "metadata": {
            "description": "The password for the ssh user."
          }
        },
        "clusterStorageAccountName": {
          "type": "string",
          "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
          }
        },
        "clusterStorageType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
          ]
        },
        "clusterWorkerNodeCount": {
          "type": "int",
          "defaultValue": 4,
          "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
          }
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[parameters('clusterStorageAccountName')]",
          "type": "Microsoft.Storage/storageAccounts",
          "location": "[parameters('location')]",
          "apiVersion": "2015-05-01-preview",
          "dependsOn": [],
          "tags": {},
          "properties": {
            "accountType": "[parameters('clusterStorageType')]"
          }
        },
        {
          "name": "[parameters('clusterName')]",
          "type": "Microsoft.HDInsight/clusters",
          "location": "[parameters('location')]",
          "apiVersion": "2015-03-01-preview",
          "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
          ],
          "tags": {},
          "properties": {
            "clusterVersion": "3.2",
            "osType": "Linux",
            "clusterDefinition": {
              "kind": "hadoop",
              "configurations": {
                "gateway": {
                  "restAuthCredential.isEnabled": true,
                  "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                  "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                }
              }
            },
            "storageProfile": {
              "storageaccounts": [
                {
                  "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                  "isDefault": true,
                  "container": "[parameters('clusterName')]",
                  "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                }
              ]
            },
            "computeProfile": {
              "roles": [
                {
                  "name": "headnode",
                  "targetInstanceCount": "1",
                  "hardwareProfile": {
                    "vmSize": "Large"
                  },
                  "osProfile": {
                    "linuxOperatingSystemProfile": {
                      "username": "[parameters('sshUserName')]",
                      "password": "[parameters('sshPassword')]"
                    }
                  }
                },
                {
                  "name": "workernode",
                  "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                  "hardwareProfile": {
                    "vmSize": "Large"
                  },
                  "osProfile": {
                    "linuxOperatingSystemProfile": {
                      "username": "[parameters('sshUserName')]",
                      "password": "[parameters('sshPassword')]"
                    }
                  }
                }
              ]
            }
          }
        }
      ],
      "outputs": {
        "cluster": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
        }
      }
    }


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx

