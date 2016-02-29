<properties
    pageTitle="Azure 中的 SharePoint Server 2013 伺服器陣列 | Microsoft Azure"
    description="了解 Azure 中 SharePoint Server 2013 伺服器陣列的價值、設定測試環境，以及部署高可用性組態。"
    services="virtual-machines"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="Windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/29/2015"
    ms.author="josephd"/>

# Azure 基礎結構服務工作負載：內部網路 SharePoint 伺服器陣列

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員部署模型。

在 Microsoft Azure 中設定第一個或下一個 SharePoint 伺服器陣列，這樣您就可以利用簡化設定的優點，還能夠快速擴大伺服器陣列，使其增加新的容量或最佳化關鍵功能。 許多 SharePoint 伺服器陣列具備標準、高可用性，以及伺服器陣列的三層式組態，可能包含數十個或更多針對效能或不同角色進行最佳化的伺服器，例如分散式快取或搜尋。

您可以使用 Azure 基礎結構服務的虛擬機器和虛擬網路功能，以透明的方式快速部署和執行連接到內部部署網路的 SharePoint 伺服器陣列。 例如，您可以設定下列網路。

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

由於 Azure 虛擬網路是內部網路的延伸模組，並備妥所有正確命名與流量路由，您的使用者將以如同它位於內部部署資料中心的相同方式來進行存取。

這種組態可讓您透過新增新 Azure 虛擬機器輕鬆擴充 SharePoint 伺服陣列，其中投入的硬體和維護成本會低於您資料中心所執行的相同伺服陣列。

在 Azure 基礎結構服務中裝載內部網路 SharePoint 伺服陣列是企業營運應用程式的範例。 如需概觀，請參閱 [列的應用程式架構藍圖](http://msdn.microsoft.com/dn630664)。

下一步是設定裝載在 Azure 中的開發/測試內部網路 SharePoint 伺服器陣列。

> [AZURE.NOTE] Microsoft 已發行 SharePoint Server 2016 IT Preview。 若要輕鬆安裝和測試此預覽版，您可以搭配 SharePoint Server 2016 IT 預覽版和其預先安裝的必要元件來使用 Azure 虛擬機器資源庫映像。 如需詳細資訊，請參閱 [測試 Azure 中的 SharePoint Server 2016 IT Preview](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)。

## 建立裝載在 Azure 中的開發/測試內部網路 SharePoint 伺服器陣列。

您有兩個選擇可以針對裝載在 Azure 中的 SharePoint 伺服器陣列建立開發/測試環境：

- 純雲端虛擬網路
- 跨單位虛擬網路

您可以免費建立這些開發/測試環境，與您 [MSDN 訂閱](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) 或 [Azure 試用版訂閱](http://azure.microsoft.com/pricing/free-trial/)。

### 純雲端虛擬網路

純雲端虛擬網路並未連接至內部部署網路。 如果您只想快速建立基本或高可用性的 SharePoint 伺服器陣列，請參閱 [建立 SharePoint 伺服器陣列](virtual-machines-sharepoint-farm-azure-preview.md)。 下列範例會顯示基本的 SharePoint 伺服陣列組態。

![](./media/virtual-machines-workload-intranet-sharepoint-farm/Non-HAFarm.png)

### 跨單位虛擬網路

跨單位虛擬網路會透過站台對站台 VPN 或 ExpressRoute 連接到內部部署網路。 如果您想要建立開發/測試環境，以模擬最後組態和實驗存取 SharePoint 伺服器並執行遠端系統管理，透過 VPN 連線，請參閱 [設定用於測試的混合式雲端中的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)。

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)

下一步是在 Azure 中建立高可用性的內部網路 SharePoint 伺服器陣列。

## 部署裝載在 Azure 中的內部網路 SharePoint 伺服器陣列

根據下列範例中所顯示，基準線代表功能性、高可用性內部網路 SharePoint 伺服器陣列組態。

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

其中包括：

- 在 Web、應用程式和資料庫層具有兩部伺服器的內部網路 SharePoint 伺服陣列。
- 使用 SQL Server AlwaysOn 可用性群組組態的兩個 SQL 伺服器和叢集中多數節點電腦。
- 在虛擬網路中使用兩個複本網域控制站的 Azure Active Directory。

若要查看此組態資訊圖，請參閱 [使用 SQL Server AlwaysOn 的 SharePoint](http://go.microsoft.com/fwlink/?LinkId=394788)。

### 用料表

基準組態需要下列 Azure 服務和元件的設定組：

- 九部虛擬機器。
- 四個額外資料磁碟，適用於網域控制站和 SQL 伺服器。
- 三個雲端服務。
- 四個可用性集合。
- 一個跨單位虛擬網路。
- 一個儲存體帳戶。
- 一個 Azure 訂用帳戶。

以下是適用於此組態的虛擬機器及其預設大小。

項目 | 虛擬機器描述 | 資源庫映像 | 預設大小
--- | --- | --- | ---
1. | 第一網域控制站 | Windows Server 2012 R2 Datacenter | A2 (中型)
2. | 第二網域控制站 | Windows Server 2012 R2 Datacenter | A2 (中型)
3. | 第一資料庫伺服器 | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4. | 第二資料庫伺服器 | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5. | 叢集的多數節點 | Windows Server 2012 R2 Datacenter | A1 (小型)
6. | 第一 SharePoint 應用程式伺服器 | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4 (特大型)
7. | 第二 SharePoint 應用程式伺服器 | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4 (特大型)
8. | 第一 SharePoint Web 伺服器 | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4 (特大型)
9. | 第二 SharePoint Web 伺服器 | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4 (特大型)

若要計算估計的成本，這項設定，請參閱 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)。 

1. 在 **模組**, ，按一下 [ **計算**, ，然後按一下 [ **虛擬機器** 足夠時間，以建立九個虛擬機器的清單。
2. 針對每一個虛擬機器，選取：
    - 您想要的區域
    - **Windows** 類型
    - **標準** 定價層
    - 在上表或您想要的大小的預設大小 **執行個體大小**

> [AZURE.NOTE] Azure 定價計算機不包含執行 SQL Server 2014 Enterprise 的兩個虛擬機器的 SQL Server 授權的其他成本。 請參閱 [虛擬機器定價 SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql) 如需詳細資訊。

### 部署階段

若要部署此組態，請使用下列程序：

- 第 1 階段：設定 Azure。

    使用 Azure 傳統入口網站和 Azure PowerShell 建立儲存體帳戶、雲端服務和跨單位虛擬網路。 如需詳細的設定步驟，請參閱 [第 1 階段](virtual-machines-workload-intranet-sharepoint-phase1.md)。

- 第 2 階段：設定網域控制站。

    為虛擬網路設定兩個 Azure Active Directory 複本網域控制站和 DNS 設定。 如需詳細的設定步驟，請參閱 [第 2 階段](virtual-machines-workload-intranet-sharepoint-phase2.md)。

- 第 3 階段：設定 SQL Server 基礎結構。  

    準備 SQL Server 虛擬機器，以便與 SharePoint 搭配使用及建立 SQL Server 叢集。 如需詳細的設定步驟，請參閱 [第 3 階段](virtual-machines-workload-intranet-sharepoint-phase3.md)。

- 第 4 階段：設定 SharePoint 伺服器。

    針對新 SharePoint 伺服器陣列設定四部 SharePoint 虛擬機器。 如需詳細的設定，請參閱 [第 4 階段](virtual-machines-workload-intranet-sharepoint-phase4.md)。

- 第 5 階段：建立 AlwaysOn 可用性群組。

    準備 SharePoint 資料庫，並建立 AlwaysOn 可用性群組，然後將 SharePoint 資料庫新增至其中。 如需詳細的設定步驟，請參閱 [第 5 階段](virtual-machines-workload-intranet-sharepoint-phase5.md)。

一旦設定之後，您可以展開的指引，從這個 SharePoint 伺服陣列 [適用於 SharePoint 2013 的 Microsoft Azure 架構](http://technet.microsoft.com/library/dn635309.aspx)。

## 其他資源

[在 Azure 中使用 SQL Server AlwaysOn 可用性群組部署 SharePoint](virtual-machines-workload-deploy-spsqlao-overview.md)

[在混合式雲端中設定用於測試的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)

[包含 SQL Server AlwaysOn 的 SharePoint 資訊圖](http://go.microsoft.com/fwlink/?LinkId=394788)

[在 Azure 基礎結構服務中架設的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 基礎結構服務工作負載：高可用性企業營運應用程式](virtual-machines-workload-high-availability-lob-application.md)

