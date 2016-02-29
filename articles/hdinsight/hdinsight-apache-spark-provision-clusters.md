<properties
   pageTitle="在 HDInsight 中佈建 Apache Spark 叢集 | Microsoft Azure"
   description="了解如何使用 Azure 傳統入口網站、Azure PowerShell、命令列或 HDInsight .NET SDK 為 Azure HDInsight 佈建 Spark 叢集。"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="11/07/2015"
    ms.author="nitinme"/>

# 使用自訂選項在 HDInsight 上佈建 Apache Spark 叢集

在大部分情況下，您可以佈建的 Spark 叢集使用快速建立方法中所述 [開始使用 HDInsight 上的 Apache Spark](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)。 在某些情況下，您可能想要佈建自訂叢集。 比方說，您可能會想要連接外部中繼資料存放區，讓 Hive 中繼資料的持續性超越叢集存留期，或是可能會想要使用額外的儲存體來搭配叢集。

對於這類案例及其他案例，本文提供如何使用 Azure 傳統入口網站、Azure PowerShell 或 HDInsight .NET SDK，在 HDInsight 上佈建自訂 Spark 叢集的指示。  


**必要條件：**

開始執行本文中的指示之前，您必須擁有 Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

##<a id="configuration"></a>不同的組態選項有哪些?

###其他儲存體

在設定期間，您必須指定 Azure Blob 儲存體帳戶和預設容器。 叢集以此做為預設儲存位置。 您可以選擇性地指定將與叢集相關聯的其他 Azure 儲存體帳戶。

>[AZURE.NOTE] 不會共用一個 Blob 儲存體容器，讓多個叢集。 不支援此做法。

如需有關使用次要 Blob 存放區的詳細資訊，請參閱 [使用 Azure Blob 儲存體與 HDInsight](hdinsight-use-blob-storage.md)。

###Metastore

Spark 可讓您透過原始資料定義結構描述和 Hive 資料表。 您可以將這些結構描述和資料表中繼資料儲存在外部 metastore。 使用中繼存放區有助於保留 Hive 中繼資料，因此在佈建新叢集時，您便無需重建 Hive 資料表。 依預設，Hive 會使用內嵌資料庫來儲存此資訊。 刪除叢集時，嵌入的資料庫將無法保留中繼資料。

如需有關如何在 Azure 中建立 SQL 資料庫的指示，請參閱 [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。

### 叢集自訂

您可以在佈建期間使用指令碼來安裝其他元件或自訂組態。 這類指令碼會透過叫用 **指令碼動作**, ，這是組態選項，可用來從 Azure 傳統入口網站、 HDInsight Windows PowerShell cmdlet 或 HDInsight.NET SDK。 如需詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集][hdinsight-customize-cluster]。


###虛擬網路

[Azure 虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/) 可讓您建立安全、 持續的網路，上面有您解決方案所需的資源。 虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。

    ![diagram of cloud-only configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

    站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接至 Azure 虛擬網路。

    ![diagram of site-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

    點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。

    ![diagram of point-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

如需有關虛擬網路功能、 優點和功能的詳細資訊，請參閱 [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

> [AZURE.NOTE] 您必須建立 Azure 虛擬網路，再佈建叢集。 如需詳細資訊，請參閱 [如何建立虛擬網路](virtual-networks-create-vnet.md)。
>
> Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。
>
> 強烈建議您一個叢集只指定一個子網路。

##使用 Azure 入口網站

HDInsight 上的 Spark 叢集會使用 Azure Blob 儲存容器作為預設檔案系統。 相同的資料中心上必須要有 Azure 儲存體帳戶，才能夠建立 HDInsight 叢集。 如需詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。 如需建立 Azure 儲存體帳戶的詳細資訊，請參閱 [如何建立儲存體帳戶][azure-create-storageaccount]。

**使用自訂建立選項建立 HDInsight 叢集**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [ **新增**, ，按一下 [ **資料分析**, ，然後按一下 [ **HDInsight**。

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.1.png "Creating a new cluster in the Azure Portal")

3. 輸入 **叢集名稱**, ，請選取 **Spark** 的 **叢集類型**, ，以及從 **叢集作業系統** 下拉式清單中，選取 **Windows Server 2012 R2 Datacenter**。 如果該叢集可用，其名稱旁會出現綠色核取記號。

    ![輸入叢集名稱和類型](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.2.png "Enter cluster name and type")

4. 如果您有一個以上的訂閱，請按一下 [ **訂閱** 選取將用於叢集的 Azure 訂閱的項目。

5. 按一下 [ **資源群組** ，查看現有的資源群組的清單，然後選取 [建立叢集的一個。 或者，您可以按一下 **新建** ，然後輸入新的資源群組的名稱。 出現綠色核取記號即表示新群組的名稱可用。

    > [AZURE.NOTE] 如果有的話，這個項目將會預設為其中一個現有的資源群組。

6. 按一下 [ **認證**, ，然後輸入 **叢集登入使用者名稱** 和 **叢集登入密碼**。 如果您想要啟用遠端桌面的叢集節點上， **啟用遠端桌面**, ，按一下 [ **是**。 如果叢集的遠端桌面存取過期，請選取日期並提供遠端桌面使用者的使用者名稱/密碼。 按一下 [ **選取** 底部要儲存認證的設定。

    ![提供叢集認證](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

7. 按一下 [ **資料來源** 選擇現有的資料來源的叢集，或建立一個新。

    ![資料來源刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

    目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。 了解這些項目上使用下列 **資料來源** 刀鋒視窗。

    - **選取方法**: 設為 **從所有的訂閱** 才能瀏覽儲存體帳戶從您的訂閱。 請設為 **便捷鍵** 如果您想要輸入 **儲存體名稱** 和 **便捷鍵** 現有的儲存體帳戶。

    - **選取儲存體帳戶建立新的 /**: 按一下 **選取儲存體帳戶** 瀏覽並選取您要與叢集相關聯的現有儲存體帳戶。 或者，按一下 [ **新建** 來建立新的儲存體帳戶。 使用出現的欄位輸入儲存體帳戶名稱。 如果該名稱可用，將會出現綠色核取記號。

    - **選擇預設容器**: 使用此選項可輸入要用於叢集的預設容器名稱。 雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。

    - **位置**: 儲存體帳戶，或將中建立的地理區域。

        > [AZURE.IMPORTANT] 選取的預設資料來源的位置也會設定 HDInsight 叢集的位置。 叢集和預設資料來源必須位於相同區域中。

    按一下 [ **選取** 儲存資料來源設定。

8. 按一下 [ **節點定價層** 顯示將建立此叢集節點的相關資訊。 設定該叢集所需的背景工作角色節點數目。 該叢集的預估成本將會顯示在此刀鋒視窗內。

    ![節點定價層刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")

    按一下 [ **選取** 儲存定價組態的節點。

9. 按一下 [ **選擇性組態** 選取叢集的版本，以及設定其他選擇性設定，例如聯結 **虛擬網路**, 、 設定 **外部中繼存放區** 若要保留 Hive 和 Oozie 的資料，使用指令碼動作自訂叢集來安裝自訂元件，或使用其他儲存體帳戶與叢集。

    * 按一下 [ **HDInsight 版本** 下拉式清單並選取您想要在叢集中使用的版本。 如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。

    * 按一下 [ **虛擬網路** 提供組態詳細資料，來設定叢集的虛擬網路的一部分。 在 **虛擬網路** 刀鋒視窗中，按一下 [ **虛擬網路** 然後按一下您想要使用的網路。 同樣地，選取 **子網路** 做網路，然後按一下 [ **選取** 儲存虛擬網路設定。

        ![虛擬網路刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

    * 按一下 [ **外部中繼存放區** 來指定您想要用來儲存與叢集相關聯的 Hive 和 Oozie 中繼資料的 SQL 資料庫。

        ![自訂中繼存放區刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")

        如 **hive 使用現有的 SQL DB** 中繼資料中，按一下 **是**, 選取 SQL 資料庫，然後提供資料庫的使用者名稱/密碼。 如果您想要重複這些步驟 **針對 Oozie 中繼資料使用現有的 SQL DB**。 按一下 [ **選取** 直到您會回到 **選擇性組態** 刀鋒視窗。

        >[AZURE.NOTE] 用於 metastore 的 Azure SQL database 必須能夠連線至其他 Azure 服務，包括 Azure HDInsight。 在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。 這是指執行 SQL Database 執行個體的伺服器。 一旦進入伺服器檢視，按一下 [ **設定**, ，然後 **Azure 服務**, ，按一下 [ **是**, ，然後按一下 [ **儲存**。

    * 按一下 [ **指令碼動作** 如果您想要使用自訂指令碼以自訂叢集，與叢集正在建立。 如需指令碼動作的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。 請在 [指令碼動作] 刀鋒視窗上提供如螢幕擷取畫面所示的詳細資料。

        ![指令碼動作刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.8.png "Specify script action")

        按一下 [ **選取** 儲存指令碼動作組態變更。

    * 按一下 [ **Azure 儲存體金鑰** 來指定與叢集相關聯的其他儲存體帳戶。 在 **Azure 儲存體金鑰** 刀鋒視窗中，按一下 [ **新增儲存體金鑰**, ，然後選取現有的儲存體帳戶，或建立新的帳戶。

        ![其他儲存體刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")

        按一下 [ **選取** 直到您會回到 **新的 HDInsight 叢集** 刀鋒視窗。

10. 在 **新的 HDInsight 叢集** 刀鋒視窗中，確定 **釘選到開始面板** 已選取，然後按一下 [ **建立**。 這會建立叢集，並將該叢集磚加入您 Azure 傳統入口網站的「開始面板」中。 該圖示可表示該叢集正在佈建，並將在佈建完成後變更為 HDInsight 圖示。

    | 佈建期間 | 佈建完成 |
    | ------------------ | --------------------- |
    | ![「開始面板」上的佈建指示器](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

    > [AZURE.NOTE] 需要花一些時間，叢集才會建立，通常約 15 分鐘的時間。 在開始面板中，使用 [] 磚或 **通知** 佈建程序檢查頁面左邊的項目。

11. 佈建完成後，在「開始面板」按一下該叢集磚，以啟動叢集刀鋒視窗。 此叢集刀鋒視窗提供該叢集的基本資訊，如名稱、其所屬的資源群組、位置、作業系統、叢集儀表板 URL 等。

    ![叢集刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/HDI.Cluster.Blade.png "Cluster properties")

    使用下列命令以了解在此刀鋒視窗，並在最上方圖示 **Essentials** 和 **快速連結** 區段:

    * **設定** 和 **所有設定**: 顯示 **設定** 叢集中，可讓您存取叢集的詳細的組態資訊的分頁。

    * **儀表板** 和 **URL**: 這些是所有的方式來存取叢集儀表板，這是入口網站，以在叢集上執行工作。

    * **遠端桌面**: 可讓您在叢集節點上啟用/停用遠端桌面。

    * **調整叢集**: 可讓您變更此叢集的背景工作節點數。

    * **刪除**: 刪除 HDInsight 叢集。

    * **快速入門** (![定域機組和雷電圖示 = 快速入門](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)): 顯示資訊可協助您開始使用 HDInsight。

    * **使用者** (![使用者圖示](./media/hdinsight-apache-spark-provision-clusters/users.png)): 可讓您設定的權限 _入口網站的管理_ 的其他使用者此叢集上您的 Azure 訂閱。

        > [AZURE.IMPORTANT] 這 _只_ 影響存取，以及在 Azure 入口網站中，此叢集的權限，以及誰可以連線到，或將工作提交至 HDInsight 叢集上沒有作用。

    * **標記** (![標籤圖示](./media/hdinsight-apache-spark-provision-clusters/tags.png)): 標記可讓您設定來定義自訂分類您的雲端服務的索引鍵/值組。 例如，您可以建立名為 __專案__, ，然後使用共通的值與特定專案相關聯的所有服務。

    * **叢集儀表板**: 啟動 [叢集儀表板] 分頁中，您可以啟動叢集儀表板本身，或啟動 Zeppelin 和 Jupyter notebook。


## 使用 Azure PowerShell

請參閱 [建立 HDInsight 叢集](hdinsight-provision-clusters.md#create-using-azure-powershell)。

在 New-AzureRmHDInsightCluster Cmdlet 中指定 Spark 叢集類型轉換：

    -ClusterType Spark

## 使用搭載 ARM 的 HDInsight .NET SDK

請參閱 [建立 HDInsight 叢集](hdinsight-provision-clusters.md#create-using-the-hdinsight-net-sdk)。

指定 Spark 叢集類型：

    private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;


## 後續步驟

* 請參閱 [執行互動式資料分析 HDInsight 中使用 Spark 與 BI 工具](hdinsight-apache-spark-use-bi-tools.md) 以了解如何在 HDInsight 上使用 Apache Spark Power BI 和 Tableau 等 BI 工具。
* 請參閱 [HDInsight 建置機器學習應用程式中使用 Spark](hdinsight-apache-spark-ipython-notebook-machine-learning.md) 以了解如何建置在 HDInsight 上使用 Apache Spark 機器學習應用程式。
* 請參閱 [HDInsight 建置即時串流應用程式中使用 Spark](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) 以了解如何建置在 HDInsight 上使用 Apache Spark 串流應用程式。
* 請參閱 [管理 Azure HDInsight 中的 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md) 以了解如何使用資源管理員來管理資源配置給 Spark 叢集。


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/


[azure-preview-portal]: https://portal.azure.com

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

