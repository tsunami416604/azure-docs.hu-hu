<properties 
    pageTitle="將內部部署 AlwaysOn 可用性群組延伸至 Azure | Microsoft Azure"
    description="本教學課程使用隨傳統部署模型建立的資源，並說明如何在 SQL Server Management Studio (SSMS) 中使用 [加入複本精靈]，以在 Azure 中加入 AlwaysOn 可用性群組複本。"
    services="virtual-machines"
    documentationCenter="na"
    authors="rothja"
    manager="jeffreyg"
    editor="monicar"
    tags="azure-service-management"/>

<tags 
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="11/13/2015"
    ms.author="jroth" />

# 將內部部署 AlwaysOn 可用性群組延伸至 Azure

AlwaysOn 可用性群組可透過新增次要複本，為資料庫群組提供高可用性。 如果發生故障，這些複本便可容錯移轉資料庫。 此外，它們還可用來卸載讀取工作負載或備份工作。

您可使用 SQL Server 佈建一或多個 Azure VM，並將它們以複本形式新增至內部部署可用性群組，藉此將內部部署可用性群組延伸至 Microsoft Azure。

本教學課程假設您具有下列項目：

- 有效的 Azure 訂用帳戶。 您可以 [申請免費試用版](http://azure.microsoft.com/pricing/free-trial)。

- 現有的 AlwaysOn 可用性群組內部部署。 如需有關可用性群組的詳細資訊，請參閱 [AlwaysOn 可用性群組](https://msdn.microsoft.com/library/hh510230.aspx)。

- 內部部署網路與 Azure 虛擬網路之間的連線 如需建立這個虛擬網路的詳細資訊，請參閱 [Azure 傳統入口網站中設定站台對站 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。

## 加入 Azure 複本精靈

本節說明如何使用 **加入 Azure 複本精靈** 來擴充您的 AlwaysOn 可用性群組解決方案，以包含 Azure 的複本。

1. 從 SQL Server Management Studio 內展開 **AlwaysOn 高可用性** > **可用性群組** > **可用性群組名稱**。

1. 以滑鼠右鍵按一下 **可用性複本**, ，然後按一下 [ **將複本加入**。

1. 根據預設， **將複本加入至可用性群組精靈** 隨即出現。 按一下 [ **下一步**。  如果您已選取 **不要再顯示此頁面** 將不會顯示在頁面底部期間選項先前啟動此精靈，此畫面。

    ![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742861.png)

1. 您必須連線到所有現有的次要複本。 您可以按一下 **連接...** 旁邊每個複本，或者您可以按一下 **全部連接...** 螢幕的底部。 在驗證之後，按一下 [ **下一步** 前進到下一個畫面。

1. 在 **指定複本** ] 頁面上，多個索引標籤所列的上方: **複本**, ，**端點**, ，**備份喜好設定**, ，和 **接聽程式**。 從 **複本** 索引標籤上，按一下 [ **加入 Azure 複本...** 以啟動 [新增 Azure 複本精靈]。

    ![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742863.png)

1. 如果您先前已安裝 Azure 管理憑證，請從本機的 Windows 憑證存放區選取現有的 Azure 管理憑證。 如果您先前已使用過 Azure 訂用帳戶的識別碼，請選取或輸入該識別碼。 您可以按 [下載] 下載並安裝 Azure 管理憑證，然後使用 Azure 帳戶下載訂用帳戶清單。

    ![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742864.png)

1. 請在頁面中的每個欄位填入值，這些值將用來建立裝載複本的 Azure 虛擬機器 (VM)。

    |Setting|Description|
|---|---|
|**映像**|選取所需的作業系統和 SQL 伺服器 | 組合
|**VM 大小**|選取最適合您商務 needs| 的 VM 大小
|**VM 名稱**|指定新 VM 的唯一名稱。 名稱必須介於 3 到 15 個字元，可以包含字母、 數字和連字號和必須以字母開頭和結束以字母或數字。 |
|**VM 使用者名稱**|指定將成為 vm | 上的系統管理員帳戶的使用者名稱
|**VM 系統管理員密碼**|指定密碼給新的帳戶 |
|**確認密碼**|確認新的帳戶 | 密碼
|**虛擬網路**|指定新 VM 應使用的 Azure 虛擬網路。 如需虛擬網路的詳細資訊，請參閱 [虛擬網路概觀](..\virtual-network\virtual-networks-overview.md)。 |
|**虛擬網路子網路**|指定新的 VM 應該 use| 虛擬網路子網路
|**網域**|確認網域的預設的值是 correct|
|**網域使用者名稱**|指定的帳戶是在本機叢集 nodes| 的本機系統管理員群組中。
|**密碼**|指定網域使用者名稱 | 的密碼

1. 按一下 [ **確定** 驗證部署設定。

1. 接著會顯示法律條款。 閱讀，然後按一下 **確定** 如果您同意這些條款。

1.  **指定複本** 頁面會再次顯示。 確認新的 Azure 複本的設定上 **複本**, ，**端點**, ，和 **備份喜好設定** 索引標籤。 修改設定以符合您的業務需求。  如需有關這些索引標籤上所包含的參數的詳細資訊，請參閱 [指定複本頁面 (新增可用性群組精靈/加入複本精靈)](https://msdn.microsoft.com/library/hh213088.aspx)。請注意，無法使用包含 Azure 複本的可用性群組接聽程式] 索引標籤建立接聽程式。 此外，如果接聽程式已在精靈啟動之前建立，您將會收到一則訊息指出在 Azure 中不支援該精靈。 我們會介紹如何建立接聽程式在 **建立可用性群組接聽程式** 一節。

    ![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742865.png)

1. 按一下 [ **下一步**。

1. 選取您想要使用上的資料同步處理方法 **選取初始資料同步處理** 頁面上，按一下 [ **下一步**。 在大部分情況下，選取 **完整資料同步處理**。 如需有關資料同步處理方法的詳細資訊，請參閱 [選取初始資料同步處理] 頁面 (AlwaysOn 可用性群組精靈)](https://msdn.microsoft.com/library/hh231021.aspx)。

1. 檢閱結果上 **驗證** 頁面。 修正未解決的問題，並視需要重新執行驗證。 按一下 [ **下一步**。

    ![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742866.png)

1. 檢閱設定在 **摘要** 頁面上，然後按一下 [ **完成**。

1. 佈建程序隨即開始。 精靈成功完成時，按一下 [ **關閉** 結束精靈。

>[AZURE.NOTE] 加入 Azure 複本精靈建立的記錄檔中 <Users>\ < 使用者名稱 > server\addreplicawizard。 此記錄檔可用來疑難排解失敗的 Azure 複本部署。 如果精靈無法執行任何動作，則所有先前的作業皆會回復，包括刪除佈建的 VM。

## 建立可用性群組接聽程式

建立可用性群組後，您應該建立供用戶端連線到複本的接聽程式。 接聽程式會將連入連線導向主要或唯讀次要複本。 如需有關接聽程式的詳細資訊，請參閱 [在 Azure 中設定 AlwaysOn 可用性群組的 ILB 接聽](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)。

## 後續步驟

除了使用 **加入 Azure 複本精靈** 延伸至 Azure 的 AlwaysOn 可用性群組，您可能也會移動某些 SQL Server 工作負載完全至 Azure。 若要開始，請參閱 [佈建 Azure 上的 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)。

若要在 Azure Vm 中執行 SQL Server 相關之其他主題，請參閱 [Azure 虛擬機器上的 SQL Server](virtual-machines-sql-server-infrastructure-services.md)。

