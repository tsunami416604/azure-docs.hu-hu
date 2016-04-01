<properties
    pageTitle="使用 Azure 入口網站中的 HDInsight 管理以 Linux 為基礎的 Handooop 叢集 | Microsoft Azure"
    description="了解如何使用 Azure 入口網站來監視和管理以 Linux 為基礎的 HDInsight 叢集。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/19/2015"
    ms.author="larryfr"/>

# 使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]


使用 [Azure 入口網站][preview-portal], ，您可以佈建和管理 Azure HDInsight 的 Linux 為基礎的 Hadoop 叢集。

> [AZURE.NOTE] 這份文件中的步驟專屬於使用以 Linux 為基礎的 Hadoop 叢集。 如需使用 windows 叢集的詳細資訊，請參閱 [透過 Azure 入口網站在 HDInsight 中管理 Hadoop 叢集](hdinsight-administer-use-management-portal.md)


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## 用來管理 HDInsight 的其他工具
除了 Azure 入口網站之外，還有其他工具可以用來管理 HDInsight。

- [管理 HDInsight 使用 Azure CLI](hdinsight-administer-use-command-line.md): Azure CLI 是一種跨平台命令列工具，可讓您管理 Azure 服務

- [管理使用 Azure PowerShell HDInsight](hdinsight-administer-use-powershell.md): Azure PowerShell 提供 PowerShell cmdlet 來管理 Azure 服務

## 必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用版](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## 佈建 HDInsight 叢集

您可以使用下列步驟，從 Azure 入口網站佈建 HDInsight 叢集：

1. 登入 [Azure 入口網站][preview-portal]。

2. 選取 **新增**, ，請選取 __資料分析__, ，然後選取 __HDInsight__

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. 輸入 __叢集名稱__, ，然後選取 __叢集類型__ 您想要建立。 旁邊就會出現綠色的核取 __叢集名稱__ 是否可用。

    ![叢集名稱、叢集類型及 OS 類型](./media/hdinsight-administer-use-portal-linux/clustername.png)

4. 如果您有一個以上的訂閱，請選取 __訂閱__ 選取將用於叢集的 Azure 訂閱的項目。

5. 如 __資源群組__, ，您可以選取的項目，查看現有的資源群組的清單，然後選取 [建立叢集的一個。 您可以選取或者 __新建__ ，然後輸入新的資源群組的名稱。 出現綠色核取記號即表示新群組的名稱可用。

    > [AZURE.NOTE] 如果有的話，這個項目將會預設為其中一個現有的資源群組。

6. 選取 __認證__, ，然後輸入 __叢集登入密碼__ 的 __叢集登入使用者名稱__。 您也必須輸入 __SSH 使用者名稱__ 和 __密碼__ 或 __公開金鑰__, ，這將用來驗證 SSH 使用者。 最後，使用 __選取__ 按鈕，以設定認證。 本文件中不會使用遠端桌面，所以您可以將其停用。

    ![叢集認證刀鋒視窗](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)
    
    > [AZURE.NOTE] SSH 用來從遠端存取 HDInsight 叢集使用的命令列。 您在此使用的使用者名稱和密碼或公開金鑰將會在透過 SSH 連接到叢集時使用。

    如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

    * [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

7. 如 __資料來源__, ，您可以選取的項目，選擇現有的資料來源，或建立一個新。

    ![資料來源刀鋒視窗](./media/hdinsight-administer-use-portal-linux/datasource.png)

    目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。 了解這些項目上使用下列 __資料來源__ 刀鋒視窗。

    - __選取方法__︰ 設為 __從所有的訂閱__ 才能瀏覽儲存體帳戶，您的訂閱。 設定為 __便捷鍵__ 如果您想要輸入 __儲存體名稱__ 和 __便捷鍵__ 的現有儲存體帳戶。

    - __建立新__︰ 使用此選項可建立新的儲存體帳戶。 使用出現的欄位輸入儲存體帳戶名稱。 如果該名稱可用，將會出現綠色核取記號。

    - __選擇預設容器__︰ 使用此選項可輸入要用於叢集的預設容器名稱。 雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。

    - __位置__︰ 儲存體帳戶的地理區域中，或將中建立。

        > [AZURE.IMPORTANT] 選取的預設資料來源的位置也會設定 HDInsight 叢集的位置。 叢集和預設資料來源必須位於相同區域中。

    - __選取__︰ 使用此選項可將儲存的資料來源的設定。

    
8. 選取 __節點定價層__ 顯示將建立此叢集節點的相關資訊。 根據預設，背景工作節點數會設定為 __4__。 


    The estimated cost of the cluster will be shown at the bottom of this blade.

    ![Node pricing tiers blade](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)

    Use the __Select__ button to save the __Node Pricing Tiers__ information.

9. 選取 __選擇性組態__。 此刀鋒視窗可讓您設定下列項目：

    * __HDInsight 版本__: HDInsight 叢集使用的版本。 如需有關 HDInsight 版本設定的詳細資訊，請參閱 [HDInsight 元件版本設定](hdinsight-component-versioning.md)

    * __外部中繼存放區__︰ 這可讓您選取 SQL 資料庫，將會用來儲存 Oozie 與 Hive 組態資訊。 這可讓您在刪除並重新建立叢集時重複使用組態，而不必每次都重新建立 Hive 和 Oozie 的組態。

    * __虛擬網路__︰ 這可讓您將 HDInsight 叢集放在相同的虛擬網路與其他資源，例如 SQL Database 或 Azure 虛擬機器上。 將資源放在虛擬網路上可讓彼此直接通訊，並略過可處理來自網際網路之連入流量的公用閘道器。 如需有關 HDInsight 如何受惠於 Azure 虛擬網路的詳細資訊，請參閱 [使用 Azure 虛擬網路延伸 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。

        > [AZURE.IMPORTANT] 因為您無法從 HDInsight 組態來建立新的網路，您必須建立 HDInsight 叢集之前先建立 Azure 虛擬網路。
        >
        > 目前 (8/25/2015) 有一項限制，只有一個以 Linux 為基礎的 HDInsight 叢集可存在於 Azure 虛擬網路。 
        >
        > 您不能在 Linux 的 HDInsight 使用 v1 (傳統) Azure 虛擬網路。 虛擬網路必須是 v2 (Azure 資源管理員)，才能在 Azure 入口網站中的 HDInsight 叢集建立程序期間列出來做為選項，或者在以 Azure CLI 或 Azure PowerShell 建立叢集時使用。
        >
        > 如果您有 v1 網路上的資源，而且您想要讓 HDInsight 直接存取這些資源，透過虛擬網路，請參閱 [傳統 Vnet 連接到新的 Vnet](../virtual-network/virtual-networks-arm-asm-s2s.md) 如需如何將 v2 虛擬網路連線至 v1 虛擬網路。 一旦建立此連線之後，您便可以在 v2 虛擬網路中建立 HDInsight 叢集。

    * __編寫指令碼動作__︰ 這可讓您指定自訂 HDInsight 叢集佈建期間的 Bash 指令碼。 例如，沒有 [安裝色調指令碼](hdinsight-hadoop-hue-linux.md) （圖形用戶端與 Hadoop 搭配使用。）如需有關指令碼動作的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

    * __Azure 儲存體金鑰__︰ 這可讓您將其他儲存體帳戶與 HDInsight 伺服器產生關聯。

        > [AZURE.NOTE] HDInsight 只能存取 Azure 儲存體帳戶做為預設資料存放區新增到這個組態區段，或可公開存取。

    ![選用設定刀鋒視窗](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

10. 請確認 __釘選到開始面板__ 已選取，然後選取 __建立__。 這將會建立叢集，並將該叢集磚加入到您 Azure 入口網站的「開始面板」。 該圖示可表示該叢集正在佈建，並將在佈建完成後變更為 HDInsight 圖示。

  	| 佈建期間 | 佈建完成 |
  	| ------------------ | --------------------- |
  	| ![「開始面板」上的佈建指示器](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

    > [AZURE.NOTE] 需要花一些時間，叢集才會建立，通常約 15 分鐘的時間。 在開始面板中，使用 [] 磚或 __通知__ 佈建程序檢查頁面左邊的項目。

## 管理叢集

從 Azure 入口網站中選取叢集將會顯示叢集的基本資訊，例如名稱、資源群組、作業系統和叢集儀表板 URL (用來存取適用於 Linux 叢集的 Ambari Web )。

![叢集詳細資料](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

使用下列命令以了解在此刀鋒視窗，並在最上方圖示 __Essentials__ 和 __快速連結__ 區段 ︰

* __設定__ 和 __所有設定__︰ 顯示 __設定__ 叢集中，可讓您存取叢集的詳細的組態資訊的分頁。

* __儀表板__, ，__叢集儀表板__, ，和 __URL__︰ 這些是所有的方式來存取叢集儀表板，其為 Linux 型叢集的 Ambari Web。

* __安全殼層__︰ 存取使用 SSH 的叢集所需的資訊。

* __調整叢集__︰ 可讓您變更此叢集的背景工作節點數。

* __刪除__︰ 刪除 HDInsight 叢集。

* __快速入門 (![定域機組和雷電圖示 = 快速入門](./media/hdinsight-administer-use-portal-linux/quickstart.png))__︰ 顯示資訊可協助您開始使用 HDInsight。

* __使用者 (![使用者圖示](./media/hdinsight-administer-use-portal-linux/users.png))__︰ 可讓您設定的權限 _入口網站的管理_ 的其他使用者此叢集上您的 Azure 訂閱。

    > [AZURE.IMPORTANT] 這 _只_ 影響存取，以及在 Azure 入口網站中，此叢集的權限，以及誰可以連線到，或將工作提交至 HDInsight 叢集上沒有作用。

* __標記 (![標籤圖示](./media/hdinsight-administer-use-portal-linux/tags.png))__︰ 標記可讓您設定來定義自訂分類您的雲端服務的索引鍵/值組。 例如，您可以建立名為 __專案__, ，然後使用共通的值與特定專案相關聯的所有服務。

* __文件__: Azure hdinsight 的文件的連結。

> [AZURE.IMPORTANT] 若要管理的 HDInsight 叢集所提供的服務，您必須使用 Ambari Web 或 Ambari REST API。 如需有關如何使用 Ambari 的詳細資訊，請參閱 [使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

### <a name="scaling"></a>調整

調整叢集使用入口網站，選取您的 HDInsight 叢集，然後選取 __延展叢集__。 輸入 __背景工作節點數__ 您想要設定叢集，然後按一下 [ __儲存__。

![調整 ui 的影像](./media/hdinsight-administer-use-portal-linux/scaling.png)

如需有關調整作業的詳細資訊，請參閱 [資訊在 Linux 上使用 HDInsight](hdinsight-hadoop-linux-information.md#scaling)。

## 監視叢集

 __使用量__ 區段的 HDInsight 叢集] 刀鋒視窗中顯示與 HDInsight 搭配使用的訂閱可用的核心數目，以及配置給此叢集和此叢集中節點的配置方式的核心數目的相關資訊。

![使用量資訊](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT] 若要監視 HDInsight 叢集所提供的服務，您必須使用 Ambari Web 或 Ambari REST API。 如需有關如何使用 Ambari 的詳細資訊，請參閱 [使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)

## 後續步驟
在本文中，您已了解如何使用 Azure 入口網站建立 HDInsight 叢集，以及如何開啟 Hadoop 命令列工具。 若要深入了解，請參閱下列文章：

* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)
* [佈建 HDInsight 叢集](hdinsight-provision-clusters.md)
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight 使用者入門](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight 提供 Hadoop 的什麼版本？](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com


