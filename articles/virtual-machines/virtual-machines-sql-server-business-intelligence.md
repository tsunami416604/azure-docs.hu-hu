<properties 
    pageTitle="SQL Server Business Intelligence |Microsoft Azure"
    description="本主題使用隨傳統部署模型建立的資源，並描述 Azure 虛擬機器 (VM) 上執行的 SQL Server 提供的 Business Intelligence (BI) 功能。"
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
    ms.date="12/11/2015"
    ms.author="jroth" />

# Azure 虛擬機器中的 SQL Server Business Intelligence

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。
 
 
Microsoft Azure 虛擬機器資源庫含有包含 SQL Server 安裝的映像。 資源庫映像中支援的 SQL Server 版本與您可以在內部部署電腦與虛擬機器中安裝的安裝檔案相同。 本主題摘要說明映像上安裝的 SQL Server 商業智慧 (BI) 功能和佈建虛擬機器後所需的組態步驟。 本主題也描述 BI 功能支援的部署拓撲和最佳作法。

## 授權考量

有兩種方式可在 Microsoft Azure 虛擬機器中授權 SQL Server：

1. 屬於軟體保證的授權機動性優點。 如需詳細資訊，請參閱 [Azure 上透過軟體保證的授權機動性](http://azure.microsoft.com/pricing/license-mobility/)。

1. 支付安裝了 SQL Server 的 Azure 虛擬機器每小時的費用。 請參閱 < SQL Server > 一節 [虛擬機器定價](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql)。

如需授權及目前費率的詳細資訊，請參閱 [虛擬機器授權常見問題集](http://azure.microsoft.com/pricing/licensing-faq/%20)。

## Azure 虛擬機器資源庫中提供 SQL Server 映像

Microsoft Azure 虛擬機器資源庫涵蓋數個包含 Microsoft SQL Server 的映像。 虛擬機器映像上安裝的軟體因作業系統版本與 SQL Server 版本而異。 Azure 虛擬機器資源庫中提供的映像清單經常變更。

![Azure VM 資源庫中的 SQL 映像](./media/virtual-machines-sql-server-business-intelligence/IC741367.png)

![PowerShell](./media/virtual-machines-sql-server-business-intelligence/IC660119.gif) 下列 PowerShell 指令碼會傳回包含"Sql-server"，其中的 ImageName Azure 映像的清單 ︰

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.
    
    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert
    
    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID
    
    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description
    
    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2012"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2012*"} | select imagename,category, location, label, description

如需有關版本與 SQL Server 所支援的功能的詳細資訊，請參閱下列各項：

- [SQL Server 版本](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [SQL Server 2014 的版本所支援的功能](https://msdn.microsoft.com/library/cc645993.aspx)

### SQL Server 虛擬機器資源庫映像上安裝的 BI 功能

下表摘要說明常見的 Microsoft Azure 虛擬機器資源庫映像上所安裝 SQL Server 的商務智慧功能

- SQL Server 2014 RTM Enterprise

- SQL Server 2014 Standard

- SQL Server 2012 SP2 Enterprise

- SQL Server 2012 SP2 Standard

|SQL Server BI 功能|在資源庫映像上安裝|注意事項|
|---|---|---|
|**Reporting Services 原生模式**|是|已安裝但需要組態，包括報表管理員 URL。 請參閱章節 [設定 Reporting Services](#configure-reporting-services)。|
|**Reporting Services SharePoint 模式**|否|Microsoft Azure 虛擬機器資源庫映像庫不包含 SharePoint 或 SharePoint 安裝檔案。 <sup>1</sup>|
|**Analysis Services 多維度和資料採礦 (OLAP)**|是|已安裝並設定為預設的 Analysis Services 執行個體|
|**Analysis Services 表格式**|否|SQL Server 2012 和 2014 映像中支援，但預設不會安裝。 安裝另一個執行個體的 Analysis Services。 請參閱本主題中的＜安裝其他 SQL Server 服務和功能＞。|
|**適用於 SharePoint 的 Analysis Services Power Pivot**|否|Microsoft Azure 虛擬機器資源庫映像庫不包含 SharePoint 或 SharePoint 安裝檔案。 <sup>1</sup>|

<sup>1</sup> 如需有關 SharePoint 和 Azure 虛擬機器的詳細資訊，請參閱 [適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx) 和 [SharePoint 部署 Microsoft Azure 虛擬機器上](https://www.microsoft.com/download/details.aspx?id=34598)。

![PowerShell](./media/virtual-machines-sql-server-business-intelligence/IC660119.gif) 執行下列 PowerShell 命令來取得服務名稱中包含"SQL"的已安裝服務的清單。

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## 一般建議和最佳作法

- 最小的建議大小為虛擬機器 **A3** 使用 SQL Server Enterprise Edition 時。  **A4** 虛擬機器大小建議用於 SQL Server BI 部署的 Analysis Services 和 Reporting Services。

    如需目前 VM 大小，請參閱 [Azure 的虛擬機器大小](virtual-machines-size-specs.md)。

- 磁碟管理的最佳作法是儲存資料、 記錄及磁碟機上的備份檔案以外的其他 **C**︰ 和 **D**:。 例如，建立資料磁碟 **E**︰ 和 **F**:。

    - 快取原則的預設磁碟機的磁碟機 **C**︰ 不是最適合使用的資料。
    
    -  **D**︰ 磁碟機是主要用於分頁檔的暫存磁碟機。  **D**︰ 磁碟機不會保存，並不會儲存在 blob 儲存體。 管理工作，例如變更虛擬機器大小重設 **D**︰ 磁碟機。 建議您 **不** 使用 **D**︰ 資料庫檔案，包括 tempdb 磁碟機。

    如需有關建立及連接磁碟的詳細資訊，請參閱 [如何將資料磁碟連接至虛擬機器](storage-windows-attach-disk.md)。

- 停止或解除安裝您不打算使用的服務。 例如，如果虛擬機器僅用於 Reporting Services，請停止或解除安裝 Analysis Services 和 SQL Server Integration Services。 下圖是預設會啟動的服務的範例。

    ![SQL Server 服務](./media/virtual-machines-sql-server-business-intelligence/IC650107.gif)

    >[AZURE.NOTE] 支援的 BI 案例需要 SQL Server 資料庫引擎。 在單一伺服器 VM 拓撲中，需要資料庫引擎才能在相同的 VM 上執行。

    如需詳細資訊，請參閱下列 ︰ [解除安裝 Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) 和 [解除安裝 Analysis Services 的執行個體](https://msdn.microsoft.com/library/ms143687.aspx)。

- 檢查 **Windows Update** 的新 「 重要更新 」。 Microsoft Azure 虛擬機器映像經常在重新整理。不過，重要的更新無法成為可從 **Windows Update** 上次重新整理 VM 映像之後。

## 部署拓撲範例

下面是使用 Microsoft Azure 虛擬機器的部署範例。 這些圖表中的拓撲只是一些您可以搭配 SQL Server BI 功能與 Microsoft Azure 虛擬機器使用的可能拓撲。

### 單一虛擬機器

Analysis Services、Reporting Services、SQL Server Database Engine 和資料來源在單一虛擬機器上。

![具有 1 部虛擬機器的 BI IaaS 案例](./media/virtual-machines-sql-server-business-intelligence/IC650108.gif)

### 兩部虛擬機器

- Analysis Services、Reporting Services 和 SQL Server Database Engine 在單一虛擬機器上。 此部署包含報表伺服器資料庫。

- 資料來源在第二部 VM 上。 第二部 VM 包含 SQL Server Database Engine 當做資料來源。

![2 個虛擬機器的 bi iass 案例](./media/virtual-machines-sql-server-business-intelligence/IC650109.gif)

### 混合的 Azure - 資料在 Azure SQL 資料庫上

- Analysis Services、Reporting Services 和 SQL Server Database Engine 在單一虛擬機器上。 此部署包含報表伺服器資料庫。

- 資料來源是 Azure SQL 資料庫。

![BI IaaS 案例 VM 以及 AzureSQL 做為資料來源](./media/virtual-machines-sql-server-business-intelligence/IC650110.gif)

### 混合 - 資料內部部署

- 在此範例部署中，Analysis Services、Reporting Services 和 SQL Server Database Engine 是在單一虛擬機器上執行。 虛擬機器會裝載報表伺服器資料庫。 虛擬機器會透過 Azure 虛擬網路或一些其他 VPN 通道解決方案加入內部部署網域。

- 資料來源是在內部部署。

![BI IaaS 案例 VM 以及內部部署資料來源](./media/virtual-machines-sql-server-business-intelligence/IC654384.gif)

## Reporting Services 原生模式組態

SQL Server 的虛擬機器資源庫映像包含 Reporting Services 原生模式安裝，但是未設定報表伺服器。 本小節中的步驟會設定 Reporting Services 報表伺服器。 如需詳細設定 Reporting Services 原生模式的詳細資訊，請參閱 [安裝 Reporting Services 原生模式報表伺服器 (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx)。

>[AZURE.NOTE] 類似的內容來設定報表伺服器使用 Windows PowerShell 指令碼，請參閱 [使用 PowerShell 建立 Azure VM 具有原生模式報表伺服器](virtual-machines-sql-server-create-native-mode-report-server-powershell.md)。

### 連接到虛擬機器並啟動 Reporting Services 組態管理員

連接到 Azure 虛擬機器有兩個常見的工作流程：

- 若要在中，按一下 [連接虛擬機器的名稱，然後按一下 **連接**。 遠端桌面連線會開啟，並自動填入電腦名稱。

    ![連接至 Azure 虛擬機器](./media/virtual-machines-sql-server-business-intelligence/IC650112.gif)

- 透過 Windows 遠端桌面連線連接到虛擬機器。 在遠端桌面的使用者介面中：

    1. 型別 **雲端服務名稱** 作為電腦名稱。
    
    1. 輸入冒號 (:) 以及針對 TCP 遠端桌面端點設定的公用連接埠號碼。
        
        Myservice.cloudapp.net:63133
        
        如需詳細資訊，請參閱 [什麼是雲端服務？](http://www.windowsazure.com/manage/services/cloud-services/what-is-a-cloud-service/)。

**啟動 Reporting Services 組態管理員。**

1. 在 **Windows Server 2012**:

1. 從 **開始** 畫面上，輸入 **Reporting Services** 查看應用程式清單。

1. 以滑鼠右鍵按一下 **Reporting Services 組態管理員** 按一下 **系統管理員身分執行**。

1. 在 **Windows Server 2008 R2**:

1. 按一下 [ **啟動**, ，然後按一下 [ **所有程式**。

1. 按一下 [ **Microsoft SQL Server 2012**。

1. 按一下 [ **組態工具**。

1. 以滑鼠右鍵按一下 **Reporting Services 組態管理員** 按一下 **系統管理員身分執行**。

或

1. 按一下 [ **啟動**。

1. 在 **搜尋程式及檔案** 對話方塊類型 **reporting services**。 如果 VM 執行 Windows Server 2012 輸入 **reporting services** 在 Windows Server 2012 [開始] 畫面上。

1. 以滑鼠右鍵按一下 **Reporting Services 組態管理員** 按一下 **系統管理員身分執行**。

    ![搜尋 ssrs 組態管理員](./media/virtual-machines-sql-server-business-intelligence/IC650113.gif)

### 設定 Reporting Services

**服務帳戶及 Web 服務 URL：**

1. 確認 **伺服器名稱** 是本機伺服器名稱，然後按一下 **連接**。

1. 請注意空白 **報表伺服器資料庫名稱**。 組態完成時會建立資料庫。

1. 確認 **報表伺服器狀態** 是 **已啟動**。 如果您想要確認服務在 Windows 伺服器管理員，服務會先 **SQL Server Reporting Services** Windows 服務。

1. 按一下 [ **服務帳戶** 並視需要變更該帳戶。 如果虛擬機器環境中使用非網域聯結，內建 **ReportServer** 帳戶即足夠。 如需有關服務帳戶的詳細資訊，請參閱 [服務帳戶](https://msdn.microsoft.com/library/ms189964.aspx)。

1. 按一下 [ **Web 服務 URL** 的左窗格中。

1. 按一下 [ **套用** 設定的預設值。

1. 請注意 **報表伺服器 Web 服務 Url**。 請注意，預設 TCP 連接埠是 80 且屬於 URL。 在稍後步驟中，您可以為連接埠建立 Microsoft Azure 虛擬機器端點。

1. 在 **結果** ] 窗格中，確認已順利完成的動作。

**資料庫：**

1. 按一下 [ **資料庫** 的左窗格中。

1. 按一下 [ **資料庫變更**。

1. 確認 **建立新的報表伺服器資料庫** 已選取，然後按一下 [下一步]。

1. 確認 **伺服器名稱** 按一下 **測試連接**。

1. 如果結果為 **連接測試成功**, ，按一下 [ **確定** 然後按一下 [ **下一步**。

1. 請注意，資料庫名稱 **ReportServer** 和 **報表伺服器模式** 是 **原生** 然後按一下 [ **下一步**。

1. 按一下 [ **下一步** 上 **認證** 頁面。

1. 按一下 [ **下一步** 上 **摘要** 頁面。

1. 按一下 [ **下一步** 上 **進度和完成** 頁面。

**報告管理員 URL：**

1. 按一下 [ **報表管理員 URL** 的左窗格中。

1. 按一下 [ **套用**。

1. 在 **結果** ] 窗格中，確認已順利完成的動作。

1. 按一下 [ **結束**。

報表伺服器權限的資訊，請參閱 [原生模式報表伺服器上授與的權限](https://msdn.microsoft.com/library/ms156014.aspx)。

### 瀏覽至本機報表管理員

若要驗證組態，瀏覽至 VM 上的報表管理員。

1. 在 VM 上，以系統管理員權限啟動 Internet Explorer。

1. 瀏覽至 VM 上 http://localhost/reports。

### 連接到遠端報表管理員

如果您想要從遠端電腦連接到虛擬機器上的報表管理員，請建立新的虛擬機器 TCP 端點。 根據預設，報表伺服器會接聽 HTTP 要求上 **連接埠 80**。 如果您將報表伺服器 URL 設定為使用不同的連接埠，您必須在下列指示中指定該連接埠編號。

1. 為虛擬機器的 TCP 連接埠 80 建立端點。 如需詳細資訊，請參閱、 [虛擬機器端點和防火牆連接埠](#virtual-machine-endpoints-and-firewall-ports) 這份文件中的一節。

1. 在虛擬機器防火牆中開啟連接埠 80。

1. 瀏覽至報表管理員使用 Azure 虛擬機器 **DNS 名稱** 做為伺服器名稱，在 URL 中。 例如： 

    **報表管理員**: http://uebi.cloudapp.net/reportserver
    **報表伺服器**: http://uebi.cloudapp.net/reports

    [為報表伺服器存取設定防火牆](https://technet.microsoft.com/library/bb934283.aspx)

### 建立報表並發佈至 Azure 虛擬機器

下表摘要列出一些選項，可將現有報表從內部部署電腦發佈至 Microsoft Azure 虛擬機器上託管的報表伺服器：

- **報表產生器**︰ 虛擬機器包含-click-once 版本的 Microsoft SQL Server 報表產生器。 若要在虛擬機器上首次啟動報表產生器：
                                            
    1. 以管理權限啟動瀏覽器。
    
    1. 瀏覽至報表管理員虛擬機器上，按一下 **報表產生器** 功能區中。
    
    如需詳細資訊，請參閱 [安裝、 解除安裝，並支援報表產生器](https://technet.microsoft.com/library/dd207038.aspx)。

- **SQL Server Data Tools**: VM: SQL Server 資料工具安裝在虛擬機器上，而且可用來建立 **報表伺服器專案** 和虛擬機器上的報表。 SQL Server Data Tools 可將報表發佈至虛擬機器上的報表伺服器。

- **SQL Server Data Tools ︰ 遠端**︰ 您本機電腦上，建立 Reporting Services 專案包含 Reporting Services 報告的 SQL Server 資料工具中。 設定專案以連接至 Web 服務 URL。

    ![SSRS 專案的 SSDT 專案屬性](./media/virtual-machines-sql-server-business-intelligence/IC650114.gif)

- 建立包含報表的 .VHD 硬碟機，然後上傳並連接磁碟機。

    1. 在您的本機電腦上建立包含報表的 .VHD 硬碟機。
    
    1. 建立及安裝管理憑證。
    
    1. 將 VHD 檔案上傳至 Azure 中使用 Add-azurevhd cmdlet [建立並上傳 Windows Server VHD 到 Azure](virtual-machines-create-upload-vhd-windows-server.md)。
    
    1. 將磁碟連接至虛擬機器。

## 安裝其他 SQL Server 服務和功能

若要在表格式模式中安裝其他 SQL Server 服務 (例如 Analysis Services)，請執行 SQL Server 安裝精靈。 安裝程式檔案是在虛擬機器的本機磁碟上。

1. 按一下 [ **啟動** 然後按一下 [ **所有程式**。

1. 按一下 [ **Microsoft SQL Server 2014** 或 **Microsoft SQL Server 2012** 然後按一下 [ **組態工具**。

1. 按一下 [ **SQL Server 安裝中心**。

或執行 C:\SQLServer_12.0_full\setup.exe 或 C:\SQLServer_11.0_full\setup.exe

>[AZURE.NOTE] 第一次執行 SQL Server 安裝程式，更多安裝檔可以下載，而且需要重新啟動虛擬機器，並重新啟動 SQL Server 安裝程式。
>
>如果您需要重複自訂從 Microsoft Azure 虛擬機器選取的映像，請考慮建立您自己的 SQL Server 映像。 Analysis Services SysPrep 功能是隨著 SQL Server 2012 SP1 CU2 啟用。 如需詳細資訊，請參閱 [使用 SysPrep 安裝 SQL Server 的考量](https://msdn.microsoft.com/library/ee210754.aspx)。

### 安裝 Analysis Services 表格式模式

本章節中的步驟 **摘要** Analysis Services 表格式模式安裝。 如需詳細資訊，請參閱下列主題：

- [以表格式模式安裝 Analysis Services](https://msdn.microsoft.com/library/hh231722.aspx)

- [表格式模型化 (Adventure Works 教學課程)](https://technet.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**若要安裝 Analysis Services 表格式模式：**

1. 在 SQL Server 安裝精靈中，按一下 [ **安裝** 在左的窗格，然後按一下 [ **新的 SQL server 獨立安裝或將功能加入到現有安裝**。

    - 如果您看到 **瀏覽資料夾**, ，瀏覽至 c:\SQLServer_12.0_full 或 c:\SQLServer_11.0_full，然後按一下 **確定**。

1. 按一下 [ **下一步** 產品更新] 頁面上。

1. 在 **安裝類型** 頁面上，選取 **執行新安裝的 SQL Server** 按一下 **下一步**。

1. 在 **安裝程式角色** 頁面上，按一下 **SQL Server 功能安裝**。

1. 在 **特徵選取** 頁面上，按一下 **Analysis Services**。

1. 在 **執行個體組態** 頁面上，輸入描述性名稱，例如 **表格式** 到 **具名執行個體** 和 **執行個體識別碼** 文字方塊。

1. 在 **Analysis Services 組態** 頁面上，選取 **表格式模式**。 將目前使用者加入至管理權限清單。

1. 完成並關閉 SQL Server 安裝精靈。

## Analysis Services 組態

### 遠端存取 Analysis Services 伺服器

Analysis Services 伺服器僅支援 Windows 驗證。 若要從用戶端應用程式 (例如 SQL Server Management Studio 或 SQL Server Data Tools) 遠端存取 Analysis Services，虛擬機器必須使用 Azure 虛擬網路加入至您的本機網域。 如需詳細資訊，請參閱 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。

A **預設執行個體** Analysis services 會接聽 TCP 連接埠 **2383年**。 在虛擬機器防火牆中開啟連接埠。 叢集具名執行個體的 Analysis Services 也會接聽連接埠上的 **2383年**。

如 **具名執行個體** Analysis Services 的 SQL Server Browser 服務才能管理連接埠的存取。 SQL Server Browser 預設組態是連接埠 **2382年**。

在虛擬機器防火牆中，開啟 [連接埠 **2382年** ，並建立靜態的 Analysis Services 具名執行個體的通訊埠。

1. 若要確認 VM 上已在使用的連接埠，以及正在使用連接埠的處理序，請以管理權限執行下列命令：

        netstat /ao

1. 使用 SQL Server Management Studio，藉由更新表格式 AS 執行個體一般屬性中的「連接埠」值來建立靜態的 Analysis Services 具名執行個體連接埠。 如需詳細資訊，請參閱的 「 使用固定通訊埠的預設或具名執行個體 」 中 [設定 Windows 防火牆以允許 Analysis Services 存取](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed)。

1. 重新啟動 Analysis Services 服務的表格式執行個體。

如需詳細資訊，請參閱、 **虛擬機器端點和防火牆連接埠** 這份文件中的一節。

## 虛擬機器端點和防火牆連接埠

本節摘要說明要建立的 Microsoft Azure 虛擬機器端點，以在虛擬機器防火牆中開啟的連接埠。 下表摘要說明 **TCP** 建立端點的連接埠和虛擬機器防火牆中開啟的連接埠。

- 如果您使用單一 VM，而下列兩項條件成立，您不需要建立 VM 端點，並且不需要在 VM 上的防火牆開啟連接埠。

    - 您未從遠端存取連接到 VM 上的 SQL Server 功能。 建立對 VM 的遠端桌面連線並存取 VM 本機上的 SQL Server 功能，不會被視為對 SQL Server 功能的遠端連線。
    
    - 您未透過 Azure 虛擬網路或其他 VPN 通道解決方案將 VM 加入內部部署網域。

- 如果虛擬機器未加入網域，但您想要從遠端連接到 VM 上的 SQL Server 功能： 

    - 在 VM 上開啟防火牆的連接埠。
    
    - 為標示的連接埠 (*) 建立虛擬機器端點。

- 如果虛擬機器是使用 VPN 通道 (例如 Azure 虛擬網路) 加入網域，那麼並不需要端點。 不過，請在 VM 上的防火牆中開啟連接埠。

  	|Port|Type|Description|
|---|---|---|
|**80**|TCP|報表伺服器的遠端存取 (*)。 |
|**1433**|TCP|SQL Server Management Studio (*)。 |
|**1434**|UDP|SQL Server 瀏覽器。 中的 VM 加入網域時才需要。 |
|**2382**|TCP|SQL Server Browser。 |
|**2383**|TCP|SQL Server Analysis Services 預設執行個體與叢集具名執行個體。 |
|**使用者定義**|TCP|建立靜態的 Analysis Services 具名執行個體通訊埠，您選擇，連接埠號碼，然後解除封鎖防火牆的通訊埠編號。 |

如需有關如何建立端點的詳細資訊，請參閱下列各項：

- 建立端點 ︰[如何設定虛擬機器的端點](virtual-machines-set-up-endpoints.md)。

- SQL Server ︰ 請參閱 「 完整的設定步驟來連接到虛擬機器使用 SQL Server Management Studio 」 一節 [佈建 Azure 上的 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)。

下圖說明要在 VM 防火牆中開啟，以允許遠端存取 VM 上的功能和元件的連接埠。

![要在 Azure VM 中為 BI 應用程式開啟的連接埠](./media/virtual-machines-sql-server-business-intelligence/IC654385.gif)

## 資源

- 檢閱 Azure 虛擬機器環境中使用的 Microsoft 伺服器軟體的支援原則。 下列主題摘要說明 BitLocker、容錯移轉叢集和網路負載平衡等功能的支援。 [Microsoft server software 支援 Azure 虛擬機器的](http://support.microsoft.com/kb/2721672)。

- [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-sql-server-infrastructure-services.md)

- [虛擬機器](http://azure.microsoft.com/documentation/services/virtual-machines/)

- [在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)

- [如何將資料磁碟附加至虛擬機器](storage-windows-attach-disk.md)

- [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-migrate-onpremises-database.md)

- [判斷 Analysis Services 執行個體的伺服器模式](https://msdn.microsoft.com/library/gg471594.aspx)

- [多維度模型化 (Adventure Works 教學課程)](https://technet.microsoft.com/library/ms170208.aspx)

- [Azure 文件中心](http://azure.microsoft.com/documentation/)

- [在混合式環境中使用 Power BI](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [透過 Microsoft SQL Server Connect 提交意見和連絡資訊](https://connect.microsoft.com/SQLServer/Feedback)

### 社群內容

- [使用 PowerShell 管理 Azure SQL Database](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)


