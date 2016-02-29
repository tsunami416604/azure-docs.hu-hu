<properties
    pageTitle="設定 AlwaysOn 可用性群組 (GUI) | Microsoft Azure"
    description="使用 Azure 虛擬機器建立 AlwaysOn 可用性群組。 本教學課程主要是透過使用者介面作業，而非編寫指令碼。"
    services="virtual-machines"
    documentationCenter="na"
    authors="rothja"
    manager="jeffreyg"
    editor="monicar"
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="12/04/2015"
    ms.author="jroth" />

# 在 Azure VM (GUI) 中設定 AlwaysOn 可用性群組

> [AZURE.SELECTOR]
- [Azure 傳統入口網站](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本端對端教學課程將示範如何透過在 Azure 虛擬機器上執行的 SQL Server AlwaysOn 實作可用性群組。

>[AZURE.NOTE] 在 Azure 管理入口網站中，沒有新的組件庫設定 AlwaysOn 可用性群組接聽程式。 這可自動設定 AlwaysOn 可用性群組所需的所有項目。 如需詳細資訊，請參閱 [提供 SQL Server AlwaysOn 在 Microsoft Azure 傳統入口網站組件庫](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)。 若要使用 PowerShell，請參閱本教學課程的相同案例的 [Azure PowerShell 中設定 AlwaysOn 可用性群組](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)。

在本教學課程結束時，您 Azure 中的 SQL Server AlwaysOn 解決方案將包含下列項目：

- 包含多個子網路 (前端和後端子網路) 的虛擬網路

- 具有 Active Directory (AD) 網域的網域控制站

- 兩個 SQL Server VM 已部署至後端子網路並加入 AD 網域

- 具有節點多數仲裁模型的 3 節點 WSFC 叢集

- 具有兩份可用性資料庫同步認可複本的可用性群組

下圖將解決方案以圖形呈現。

![在 Azure 中針對 AG 測試實驗室架構](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC791912.png)

請注意以下這是一個可用的組態。 比方說，您可以將雙複本可用性群組的 VM 數量減到最少，以縮短在 Azure 中的運算時數，方法是在 2 節點 WSFC 叢集中使用網域控制站作為仲裁檔案共用見證。 此方法可讓上述組態減少一個 VM。

本教學課程假設您已句備下列條件：

- 您已經有 Azure 帳戶。

- 您已經知道如何透過 GUI 佈建來自虛擬機器資源庫的 SQL Server VM。 如需詳細資訊，請參閱 [佈建 Azure 上的 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)

- 您已非常熟悉 AlwaysOn 可用性群組的功能。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。

>[AZURE.NOTE] 如果您有興趣在 SharePoint 上使用 AlwaysOn 可用性群組，也會看到 [設定 SQL Server 2012 AlwaysOn 可用性群組適用於 SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx)。

## 建立虛擬網路和網域控制站伺服器

一開始先建立新的 Azure 試用帳戶。 完成帳戶設定，系統會將您導向 Azure 傳統入口網站的主畫面。

1. 按一下 [ **新增** 按鈕頁面左下角，如下所示。

    ![在入口網站中按一下 [新增]](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665511.gif)

1. 按一下 [ **網路服務**, ，然後按一下 [ **虛擬網路，** 然後按一下 [ **自訂建立**, ，如下所示。

    ![建立虛擬網路](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665512.gif)

1. 在 **建立虛擬網路** ] 對話方塊中，逐步進行頁面的底下設定來建立新的虛擬網路。 

    |頁面Settings|
|---|---|
|虛擬網路詳細資料 |**名稱 = ContosoNET**<br/>**區域 = 美國西部**|
|DNS 伺服器和 VPN Connectivity|None|
|虛擬網路位址 Spaces|以下螢幕擷取畫面顯示設定: ![建立虛擬網路](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784620.png)|

1. 接下來，建立要作為網域控制站 (DC) 的 VM。 按一下 [ **新增** 一次，然後 **計算**, ，然後 **虛擬機器**, ，然後 **從組件庫**, ，如下所示。

    ![建立 VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784621.png)

1. 在 **建立虛擬機器** ] 對話方塊中，逐步進行頁面的底下設定來設定新的 VM。 

    |頁面Settings|
|---|---|
|選取虛擬機器作業系統 system|Windows Server 2012 R2 Datacenter|
|虛擬機器組態 |**版本發行日期** (最新版本) =<br/>**虛擬機器名稱** = ContosoDC<br/>**層** = STANDARD<br/>**大小** = A2 (2 核心)<br/>**新的使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso! 000<br/>**確認** = Contoso! 000|
|虛擬機器組態 |**雲端服務** = 建立新的雲端服務<br/>**CLOUD SERVICE DNS NAME** = 唯一的雲端服務名稱<br/>**DNS 名稱** = 唯一的名稱 (例如: ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = Back(10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = (無) |
|虛擬機器選項 |使用 defaults|

新的 VM 設定完畢後，請等候系統佈建 VM。 這個程序需要一些時間才能完成，而且如果您按一下以 **虛擬機器** ] 索引標籤在 Azure 傳統入口網站，請參閱從 ContosoDC 循環狀態 **啟動 (佈建)** 至 **已停止**, ，**起始**, ，**執行 (正在佈建)**, ，最後 **執行**。

現在已成功佈建 DC 伺服器。 接下來，您將在這個 DC 伺服器上設定 Active Directory 網域。

## 設定網域控制站。

下列步驟將帶您把 ContosoDC 電腦設定為 corp.contoso.com 的網域控制站。

1. 在入口網站中，選取 **ContosoDC** 機器。 在 **儀表板** 索引標籤上，按一下 [ **連接** 若要開啟 [遠端桌面存取的 RDP 檔案。

    ![連接至虛擬機器](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784622.png)

1. 您已設定的管理員帳戶登入 (**\AzureAdmin**) 和密碼 (**Contoso! 000**)。

1. 根據預設， **伺服器管理員** 儀表板應該會顯示。

1. 按一下 [ **新增角色及功能** 儀表板上的連結。

    ![透過 [伺服器總管] 新增角色](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784623.png)

1. 選取 **下一步** 直到您進入 **伺服器角色** 一節。

1. 選取 **Active Directory 網域服務** 和 **DNS 伺服器** 角色。 出現提示時，加入這些角色所需的所有其他功能。

    >[AZURE.NOTE] 您會收到警告，指出沒有靜態 IP 位址驗證。 如果您是要測試組態，請按一下 [繼續]。 若為生產案例 [使用 PowerShell 來設定靜態 IP 位址的網域控制站電腦](./virtual-network/virtual-networks-reserved-private-ip.md)。

    ![新增角色對話方塊](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784624.png)

1. 按一下 [ **下一步** 直到您到達 **確認** 一節。 選取 **需要時自動重新啟動目的地伺服器** 核取方塊。

1. 按一下 [ **安裝**。

1. 功能完成安裝之後，請回到 **伺服器管理員** 儀表板。

1. 選取新 **AD DS** 左側窗格上的選項。

1. 按一下 [ **詳細** 黃色警告列上的連結。

    ![在 DNS 伺服器 VM 上新增 AD DS 對話方塊](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784625.png)

1. 在 **動作** 資料行的 **所有伺服器的工作詳細資料** ] 對話方塊中，按一下 [ **此伺服器升級為網域控制站**。

1. 在 **Active Directory 網域服務設定精靈**, ，使用下列值:

    |頁面Setting|
|---|---|
|部署設定 |**加入新的樹系** = 已選取<br/>**根網域名稱** = corp.contoso.com|
|網域控制站選項 |**密碼** = Contoso! 000<br/>**確認密碼** = Contoso! 000|

1. 按一下 [ **下一步** 到精靈中的其他頁面。 在 **必要條件檢查** 頁面上，確認您看到下列訊息: **順利通過所有先決條件檢查**。 請注意，務必要檢閱所有適用您情況的警告訊息，但是仍可以繼續進行安裝。

1. 按一下 [ **安裝**。  **ContosoDC** 虛擬機器會自動重新開機。

## 設定網域帳戶

接下來的步驟，將帶您設定 Active Directory (AD) 帳戶，以供之後使用。

1. 登入 **ContosoDC** 機器。

1. 在 **伺服器管理員** 選取 **工具** 然後按一下 [ **Active Directory 管理中心**。

    ![Active Directory 管理中心](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784626.png)

1. 在 **Active Directory 管理中心** 選取 **corp (本機)** 從左窗格中。

1. 在右邊 **工作** 窗格中，選取 **新增** 然後按一下 [ **使用者**。 套用下列設定：

    |Setting|值 |
|---|---|
|**名字**|Install|
|**使用者 SamAccountName**|Install|
|**密碼**|Contoso! 000|
|**確認密碼**|Contoso! 000|
|**其他密碼選項**|Selected|
|**密碼永久有效**|Checked|

1. 按一下 [ **確定** 建立 **安裝** 使用者。 此帳戶將用來設定容錯移轉叢集和可用性群組。

1. 使用相同的步驟建立兩個額外的使用者: **CORP\SQLSvc1** 和 **CORP\SQLSvc2**。 這些帳戶將用於 SQL Server 執行個體。接下來，您必須提供 **CORP\Install** 設定 Windows Server 容錯移轉叢集 (WSFC) 的必要權限。

1. 在 **Active Directory 管理中心**, ，請選取 **corp (本機)** 的左窗格中。 然後在右邊 **工作** ] 窗格中，按一下 **屬性**。

    ![企業使用者內容](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784627.png)

1. 選取 **延伸**, ，然後按一下 [ **進階** 按鈕 **安全性** ] 索引標籤。

1. 在 **corp 的進階安全性設定** ] 對話方塊。 按一下 [ **新增**。

1. 按一下 [ **選取一個主體**。 然後搜尋 **CORP\Install**。 按一下 [ **確定**。

1. 選取 **讀取全部內容** 和 **建立電腦物件** 權限。

    ![企業使用者權限](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784628.png)

1. 按一下 [ **確定**, ，然後按一下 [ **確定** 一次。 關閉 [企業內容] 視窗。

現在 Active Directory 和使用者物件便已設定完畢，請建立三個 SQL Server VM，並將這些 VM 加入此網域。

## 建立 SQL Server VM

接下來，建立三個 VM，包括 WSFC 叢集節點和兩個 SQL Server VM。 若要建立每個 Vm，請回到 Azure 傳統入口網站中，按一下 **新增**, ，**計算**, ，**虛擬機器**, ，然後 **從組件庫**。 然後使用下表中的範本建立 VM。

|Page|VM1|VM2|VM3|
|---|---|---|---|
|選取虛擬機器作業系統|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|虛擬機器組態|**版本發行日期** (最新版本) =<br/>**虛擬機器名稱** = ContosoWSFCNode<br/>**層** = STANDARD<br/>**大小** = A2 (2 核心)<br/>**新的使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso! 000<br/>**確認** = Contoso! 000|**版本發行日期** (最新版本) =<br/>**虛擬機器名稱** = ContosoSQL1<br/>**層** = STANDARD<br/>**大小** = A3 (4 核心)<br/>**新的使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso! 000<br/>**確認** = Contoso! 000|**版本發行日期** (最新版本) =<br/>**虛擬機器名稱** = ContosoSQL2<br/>**層** = STANDARD<br/>**大小** = A3 (4 核心)<br/>**新的使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso! 000<br/>**確認** = Contoso! 000|
|虛擬機器組態|**雲端服務** = 先前建立唯一的雲端服務 DNS 名稱 (例如: ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = Back(10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = 建立可用性設定<br/>**可用性設定組名稱** = SQLHADR|**雲端服務** = 先前建立唯一的雲端服務 DNS 名稱 (例如: ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = Back(10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = 的 SQLHADR (您也可以設定可用性設定組機器建立之後。 三部虛擬機器都必須指派至 SQLHADR 可用性集合)。|**雲端服務** = 先前建立唯一的雲端服務 DNS 名稱 (例如: ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = Back(10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = 的 SQLHADR (您也可以設定可用性設定組機器建立之後。 三部虛擬機器都必須指派至 SQLHADR 可用性集合)。|
|虛擬機器選項|使用預設值|使用預設值|使用預設值|

<br/>

>[AZURE.NOTE] 先前的設定建議的標準層虛擬機器，因為基本層機器不支援更新版本建立可用性群組接聽程式所需的負載平衡端點。 此外，此處建議的機器大小是為了在 Azure VM 中測試可用性群組。 在生產環境工作負載達到最佳效能，請參閱 SQL Server 機器大小和組態中的建議 [的 Azure 虛擬機器中 SQL Server 效能最佳作法](virtual-machines-sql-server-performance-best-practices.md)。

之後的三個 Vm 完整佈建，您需要將它們加入 **corp.contoso.com** 網域和授與 CORP\Install 系統管理權限的機器。 若要這樣做，請針對每個 VM 執行下列步驟。

1. 首先，變更慣用的 DNS 伺服器位址。 每個 VM 的遠端桌面 (RDP) 檔案下載到本機目錄，在清單中選取 VM 並按一下開始 **連接** ] 按鈕。 若要選取 VM，請如下方所示，在資料列中第一個儲存格以外的任何地方按一下。

    ![下載 RDP 檔案](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664953.jpg)

1. 啟動您所下載的 RDP 檔案，並登入 VM，使用您已設定的管理員帳戶 (**{1>builtin\azureadmin**) 和密碼 (**Contoso! 000**)。

1. 一旦您登入時，您應該會看到 **伺服器管理員** 儀表板。 按一下 [ **本機伺服器** 的左窗格中。

1. 選取 **啟用 IPv6 dhcp 指派的 IPv4 位址** 連結。

1. 在 **網路連線** ] 視窗中，選取 [網路] 圖示。

    ![變更 VM 慣用的 DNS 伺服器](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784629.png)

1. 在命令列中，按一下 [ **變更此連線的設定** (根據您的視窗大小，您可能需要按一下雙向右箭號，以檢視此命令)。

1. 選取 **網際網路通訊協定第 4 版 (TCP/IPv4)** 並按一下 [內容]。

1. 選取 [使用下列的 DNS 伺服器位址並指定 **10.10.2.4** 中 **慣用 DNS 伺服器**。

1. 位址 **10.10.2.4** 位址指派給在 Azure 虛擬網路中的 10.10.2.0/24 子網路中的 VM 和 VM 是 **ContosoDC**。 若要確認 **ContosoDC**的 IP 位址、 使用 **nslookup contosodc** 在命令提示字元中，如下所示。

    ![使用 NSLOOKUP 尋找 DC 的 IP 位址](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664954.jpg)

1. 按一下 [O**K** 然後 **關閉** 以認可變更。 現在您就可以加入至 VM **corp.contoso.com**。

1. 回到 **本機伺服器** ] 視窗中，按一下 [ **群組** 連結。

1. 在 **電腦名稱** 區段中，按一下 **變更**。

1. 選取 **網域** 核取方塊，然後輸入 **corp.contoso.com** 在文字方塊中。 按一下 [ **確定**。

1. 在 **Windows 安全性** 快顯對話方塊中，指定預設網域系統管理員帳戶的認證 (**{1>corp\azureadmin**) 和密碼 (**Contoso! 000**)。

1. 當您看見 [歡迎使用 corp.contoso.com 網域] 時，按一下 [ **確定**。

1. 按一下 [ **關閉**, ，然後按一下 [ **立即重新啟動** 快顯對話方塊中。

### 將 Corp\Install 使用者新增為每個 VM 的系統管理員：

1. 等候 VM 重新啟動，然後啟動 RDP 檔案登入 VM 使用 **{1>builtin\azureadmin** 帳戶。

1. 在 **伺服器管理員** 選取 **工具**, ，然後按一下 [ **電腦管理**。

    ![電腦管理](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784630.png)

1. 在 **電腦管理** ] 視窗中，展開 **本機使用者和群組**, ，然後選取 **群組**。

1. 按兩下 **管理員** 群組。

1. 在 **系統管理員內容** ] 對話方塊中，按一下 [ **新增** ] 按鈕。

1. 輸入的使用者 **CORP\Install**, ，然後按一下 [ **確定**。 當提示您輸入認證，使用 **{1>azureadmin** 帳戶必須具有 **Contoso! 000** 密碼。

1. 按一下 [ **確定** 關閉 **系統管理員內容** ] 對話方塊。

### 新增 **容錯移轉叢集** 至每個 VM 的功能。

1. 在 **伺服器管理員** 儀表板，按一下 [ **新增角色及功能**。

1. 在 **新增角色及功能精靈**, ，按一下 [ **下一步** 直到您進入 **功能** 頁面。

1. 選取 **容錯移轉叢集**。 出現提示時，請新增所有其他相依功能。

    ![將 [容錯移轉叢集] 功能新增至 VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784631.png)

1. 按一下 [ **下一步**, ，然後按一下 [ **安裝** 上 **確認** 頁面。

1. 當 **容錯移轉叢集** 功能安裝完成時，按一下 [ **關閉**。

1. 登出 VM。

1. 針對所有三個伺服器重複本節中的步驟 **ContosoWSFCNode**, ，**ContosoSQL1**, ，和 **ContosoSQL2**。

現在 SQL Server VM 已完成佈建並執行中，但這些 VM 所安裝的是使用預設值的 SQL Server。

## 建立 WSFC 叢集

本節將帶您建立 WSFC 叢集；該叢集將裝載您稍後會建立的可用性群組。 到目前為止，您已經針對將用於 WSFC 叢集中的每個 VM，完成下列作業：

- 在 Azure 中完整佈建

- 將 VM 加入網域

- 加入 **CORP\Install** 本機系統管理員群組

- 新增 [容錯移轉叢集] 功能

以上都是將 VM 加入 WSFC 叢集前，每個 VM 所需完成的先決條件。

此外，請注意 Azure 虛擬網路的運作方式和其在內部部署網路中的運作方式不同。 您必須依照下列順序建立叢集：

1. 其中一個節點上建立單一節點叢集 (**ContosoSQL1**)。

1. 修改為未使用的 IP 位址的叢集 IP 位址 (**10.10.2.101**)。

1. 讓叢集名稱上線。

1. 加入其他節點 (**ContosoSQL2** 和 **ContosoWSFCNode**)。

請執行下列步驟以完成這些可完整設定叢集的工作。

1. 啟動 RDP 檔案，如 **ContosoSQL1** ，使用網域帳戶登入 **CORP\Install**。

1. 在 **伺服器管理員** 儀表板，選取 **工具**, ，然後按一下 [ **容錯移轉叢集管理員**。

1. 在左窗格中，以滑鼠右鍵按一下 **容錯移轉叢集管理員**, ，然後按一下 [ **建立叢集**, ，如下所示。

    ![建立叢集](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784632.png)

1. 在建立叢集精靈中，透過套用下方設定以通過精靈中的頁面，來建立單節點叢集：

    |頁面Settings|
|---|---|
|您 Begin| 之前使用 defaults|
|選取伺服器 |型別 **ContosoSQL1** 中 **輸入伺服器名稱** 按一下 **新增**|
|驗證 Warning|選取 **否。 我不需要此群集中，來自 Microsoft 的支援，因此不想執行驗證測試。按 [下一步] 後，繼續建立叢集**。 |
|管理叢集的存取點型別 **Cluster1** 中 **叢集名稱**|
|Confirmation|除非您使用儲存空間，請使用預設值。 請參閱本表格後的注意事項。 |

    >[AZURE.WARNING] 如果您使用 [儲存空間](https://technet.microsoft.com/library/hh831739), 、 哪些群組多個磁碟到存放集區，您必須取消核取 **所有合格的存放裝置新增到叢集** 上的核取方塊 **確認** 頁面。 如果不取消勾選此選項，在群集程序進行期間虛擬磁碟會中斷連結。 因此，虛擬磁碟不會顯示在 [磁碟管理員] 或 [總管] 中，直到您將儲存空間從叢集中移除，並使用 PowerShell 重新連接虛擬磁碟。

1. 在左窗格中，展開 **容錯移轉叢集管理員**, ，然後按一下 [ **[cluster1.corp.contoso.com]**。

1. 在中央窗格中，向下捲動至 **叢集核心資源** 區段，然後展開 **名稱: Clutser1** 詳細資料。 您應該會看到兩個 **名稱** 和 **IP 位址** 中的資源 **失敗** 狀態。 由於指派給叢集的 IP 位址與虛擬機器的的 IP 位址相同，是個重複的位址，因此無法讓該 IP 位址資源上線。

1. 以滑鼠右鍵按一下失敗 **IP 位址** 資源，然後再按一下 **屬性**。

    ![叢集屬性](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784633.png)

1. 選取 **靜態 IP 位址** ，並指定 **10.10.2.101** 的地址文字方塊中。 然後按一下 [ **確定**。

1. 在 **叢集核心資源** 區段中，以滑鼠右鍵按一下 **名稱: Cluster1** 按一下 **上線**。 然後等待兩個資源上線。 叢集名稱資源上線後，會以新的 AD 電腦帳戶更新 DC 伺服器。 此 AD 帳戶稍後將用來執行可用性群組叢集服務。

1. 最後，將剩餘的節點新增至叢集。 在瀏覽器樹狀目錄中，以滑鼠右鍵按一下 **[cluster1.corp.contoso.com]** 按一下 **加入節點**, ，如下所示。

    ![將節點新增至叢集](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784634.png)

1. 在 **新增節點精靈]**, ，按一下 [ **下一步**。 在 **選取伺服器** 頁面上，新增 **ContosoSQL2** 和 **ContosoWSFCNode** 至輸入中的伺服器名稱清單 **輸入伺服器名稱** ，然後按一下 [ **新增**。 當您完成之後時，按一下 [ **下一步**。

1. 在 **驗證警告** 頁面上，按一下 **否** (在實際執行案例中您應該執行驗證測試)。 然後按一下 [ **下一步**。

1. 在 **確認** 頁面上，按一下 **下一步** 新增節點。

    >[AZURE.WARNING] 如果您使用 [儲存空間](https://technet.microsoft.com/library/hh831739), 、 哪些群組多個磁碟到存放集區，您必須取消核取 **所有合格的存放裝置新增到叢集** 核取方塊。 如果不取消勾選此選項，在群集程序進行期間虛擬磁碟會中斷連結。 因此，虛擬磁碟不會顯示在 [磁碟管理員] 或 [總管] 中，直到您將儲存空間從叢集中移除，並使用 PowerShell 重新連接虛擬磁碟。

1. 當節點加入到叢集之後時，按一下 [ **完成**。 容錯移轉叢集管理員現在應該會顯示您的叢集有三個節點，並列出在 **節點** 容器。

1. 登出遠端桌面工作階段。

## 針對可用性群組準備 SQL Server 執行個體

本節中，您即可在兩者上 **ContosoSQL1** 和 **contosoSQL2**:

- 新增的登入 **NT AUTHORITY\System** 設為預設 SQL Server 執行個體的必要權限

- 新增 **CORP\Install** 為 sysadmin 角色的預設 SQL Server 執行個體

- 開啟防火牆以供 SQL Server 進行遠端存取

- 啟用 [AlwaysOn 可用性群組] 功能

- SQL Server 服務帳戶改成 **CORP\SQLSvc1** 和 **CORP\SQLSvc2**, 分別

執行這些動作沒有順序限制。 不過，下列步驟將依順序執行這些動作。 兩個步驟 **ContosoSQL1** 和 **ContosoSQL2**:

1. 如果您尚未將 VM 登出遠端桌面工作階段，請現在登出。

1. 啟動 RDP 檔案 **ContosoSQL1** 和 **ContosoSQL2** 身分登入和 **{1>builtin\azureadmin**。

1. 首先，加入 **NT AUTHORITY\System** 至 SQL Server 登入，並具備必要的權限。 啟動 **SQL Server Management Studio**。

1. 按一下 [ **連接** 連接到預設 SQL Server 執行個體。

1. 在 **物件總管] 中**, ，依序展開 **安全性**, ，然後展開 **登入**。

1. 以滑鼠右鍵按一下 **NT AUTHORITY\System** 登入，並按一下 **屬性**。

1. 在 **安全性實體** ] 頁面上，本機伺服器，選取 **授與** 下列權限，按一下 [ **確定**。

    - 更改所有可用性群組

    - 連接 SQL

    - 檢視伺服器狀態

1. 接下來，加入 **CORP\Install** 為 **sysadmin** 角色的預設 SQL Server 執行個體。 在 **物件總管] 中**, ，以滑鼠右鍵按一下 **登入** 按一下 **新登入**。

1. 型別 **CORP\Install** 中 **登入名稱**。

1. 在 **伺服器角色** 頁面上，選取 **sysadmin**。 然後按一下 [ **確定**。 一旦建立登入之後，您可以看到它展開 **登入** 中 **物件總管] 中**。

1. 接下來，建立 SQL Server 的防火牆規則。 從 **開始** 畫面上，啟動 **具有進階安全性的 Windows 防火牆**。

1. 在左窗格中，選取 **輸入規則**。 在右窗格中，按一下 [ **新規則**。

1. 在 **規則類型** 頁面上，選取 **程式**, ，然後按一下 [ **下一步**。

1. 在 **程式** 頁面上，選取 **這個程式路徑** 和型別 **%ProgramFiles%\Microsoft SQL Server\MSSQL12。MSSQLSERVER\MSSQL\Binn\sqlservr.exe** 在文字方塊中 (如果您要遵循這些指示，但使用 SQL Server 2012、 SQL Server 目錄是 **MSSQL11。MSSQLSERVER**)。 然後按一下 [ **下一步**。

1. 在 **動作** 頁面上，保留 **允許連線** 選取，然後按一下 [ **下一步**。

1. 在 **設定檔** 頁面上，接受預設設定，按一下 [ **下一步**。

1. 在 **名稱** 頁面上，指定規則的名稱，例如 **SQL Server (程式規則)** 中 **名稱** 文字] 方塊中，然後按一下 [ **完成**。

1. 接下來，您會啟用 **AlwaysOn 可用性群組** 功能。 從 **開始** 畫面上，啟動 **SQL Server 組態管理員**。

1. 在瀏覽器樹狀目錄中，按一下 [ **SQL Server 服務**, ，然後以滑鼠右鍵按一下 **SQL Server (MSSQLSERVER)** 服務，然後按一下 **屬性**。

1. 按一下 [ **AlwaysOn 高可用性** 索引標籤，然後選取 **啟用 AlwaysOn 可用性群組**, ，如下所示，然後按一下 [ **套用**。 按一下 [ **確定** 在快顯對話方塊中，但不關閉 [屬性] 視窗。 變更服務帳戶之後，將重新啟動 SQL Server 服務。

    ![啟用 AlwaysOn 可用性群組](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665520.gif)

1. 接下來，變更 SQL Server 服務帳戶。 按一下 **登入** 索引標籤，然後輸入 **CORP\SQLSvc1** (如 **ContosoSQL1**) 或 **CORP\SQLSvc2** (如 **ContosoSQL2**) 中 **帳戶名稱**, ，然後填入，並確認密碼，然後按一下 **確定**。

1. 在快顯視窗中，按一下 [ **是** 重新啟動 SQL Server 服務。 SQL Server 服務重新啟動之後，您在 [內容] 視窗中所進行的變更便會生效。

1. 登出所有 VM。

## 建立可用性群組。

現在已準備就緒，可以開始設定可用性群組。 以下列出需執行的動作：

- 建立新的資料庫 (**MyDB1**) 上 **ContosoSQL1**

- 建立資料庫的完整備份和交易記錄備份。

- 還原完整備份和記錄備份，以 **ContosoSQL2** 與 **NORECOVERY** 選項

- 建立可用性群組 (**AG1**) 同步認可、 自動容錯移轉，且可讀取次要複本

### 在 ContosoSQL1 上建立 MyDB1 資料庫：

1. 如果您有您尚未登出遠端桌面工作階段的 **ContosoSQL1** 和 **ContosoSQL2**, ，請立刻執行。

1. 啟動 RDP 檔案，如 **ContosoSQL1** 身分登入和 **CORP\Install**。

1. 在 **檔案總管**, 底下 **C:\**, ，建立名為 **備份**。 此目錄將用來備份和還原您的資料庫。

1. 以滑鼠右鍵按一下新的目錄，並指向 **分享**, ，然後按一下 [ **特定人員**, ，如下所示。

    ![建立備份資料夾](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665521.gif)

1. 新增 **CORP\SQLSvc1** ，並提供它 **讀/寫** 權限，然後新增 **CORP\SQLSvc2** ，並提供它 **讀取** 權限，如下所示，然後再按一下 **共用**。 檔案共用程序完成之後，按一下 [ **完成**。

    ![將權限授與備份資料夾](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665522.gif)

1. 接下來，建立資料庫。 從 **開始** ] 功能表中，啟動 **SQL Server Management Studio**, ，然後按一下 [ **連接** 連接到預設 SQL Server 執行個體。

1. 在 **物件總管] 中**, ，以滑鼠右鍵按一下 **資料庫** 按一下 **新的資料庫**。

1. 在 **資料庫名稱**, ，型別 **MyDB1**, ，然後按一下 [ **確定**。

### 建立 MyDB1 的完整備份，然後將其還原至 ContosoSQL2：

1. 接下來，備份資料庫的所有內容。 在 **物件總管] 中**, ，依序展開 **資料庫**, ，然後以滑鼠右鍵按一下 **MyDB1**, ，然後指向 **工作**, ，然後按一下 [ **備份**。

1. 在 **來源** 區段中，保留 **備份類型** 設 **完整**。 在 **目的地** 區段中，按一下 **移除** ，移除備份檔案的預設檔案路徑。

1. 在 **目的地** 區段中，按一下 **新增**。

1. 在 **檔案名稱** 文字方塊中，輸入 **\\ContosoSQL1\backup\MyDB1.bak**。 然後按一下 [ **確定**, ，然後按一下 [ **確定** 以備份資料庫。 備份作業完成後，按一下 [ **確定** ] 以關閉對話方塊。

1. 接下來，備份資料庫的交易記錄。 在 **物件總管] 中**, ，依序展開 **資料庫**, ，然後以滑鼠右鍵按一下 **MyDB1**, ，然後指向 **工作**, ，然後按一下 [ **備份**。

1. 在 **備份** 類型，選取 **交易記錄檔**。 保留 **目的地** 檔案設定為您稍早指定的路徑，然後按一下 **確定**。 備份作業完成之後，按一下 [ **確定** 一次。

1. 接下來，還原完整備份和交易記錄備份 **ContosoSQL2**。 啟動 RDP 檔案，如 **ContosoSQL2** 身分登入和 **CORP\Install**。 保留的遠端桌面工作階段 **ContosoSQL1** 開啟。

1. 從 **開始** ] 功能表中，啟動 **SQL Server Management Studio**, ，然後按一下 [ **連接** 連接到預設 SQL Server 執行個體。

1. 在 **物件總管] 中**, ，以滑鼠右鍵按一下 **資料庫** 按一下 **Restore Database**。

1. 在 **來源** 區段中，選取 **裝置**, ，然後按一下 **...** ] 按鈕。

1. 在 **選取備份裝置**, ，按一下 [ **新增**。

1. 在備份檔案的位置，輸入 \\ContosoSQL1\backup，然後按一下 [重新整理，然後選取 MyDB1.bak，按一下 [確定]，然後再按一次 [確定]。 現在 [還原] 窗格的備份集，應會顯示可以在完整備份和記錄備份。

1. 移至 [選項] 頁面中，在還原狀態中選取 [使用 NORECOVERY 還原]，然後按一下 [確定]，以還原資料庫。 還原作業完成後，按一下 [確定]。

### 建立可用性群組：

1. 返回至的遠端桌面工作階段 **ContosoSQL1**。 在 **物件總管] 中** 在 SSMS 中，以滑鼠右鍵按一下 **AlwaysOn 高可用性** 按一下 **新增可用性群組精靈**, ，如下所示。

    ![啟動新增可用性群組精靈](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665523.gif)

1. 在 **介紹** 頁面上，按一下 **下一步**。 在 **指定可用性群組名稱** 頁面上，輸入 **AG1** 中 **可用性群組名稱**, ，然後按一下 [ **下一步** 一次。

    ![新增 AG 精靈：指定 AG 名稱](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665524.gif)

1. 在 **選取資料庫** 頁面上，選取 **MyDB1** 按一下 **下一步**。 由於您已經為預定的主要複本建立了至少一份完整備份，所以現在這些資料庫已符合建立可用性群組的先決條件。

    ![新增 AG 精靈：選取資料庫](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665525.gif)

1. 在 **指定複本** 頁面上，按一下 **將複本加入**。

    ![新增 AG 精靈：指定複本](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665526.gif)

1.  **連接到伺服器** 的對話方塊。 型別 **ContosoSQL2** 中 **伺服器名稱**, ，然後按一下 [ **連接**。

    ![新增 AG 精靈：連接至伺服器](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665527.gif)

1. 回到 **指定複本** 頁面上，您現在應該會看到 **ContosoSQL2** 中列出 **可用複本**。 如下方所示，設定複本。 當您完成時，按一下 [ **下一步**。

    ![新增 AG 精靈：指定複本 (完成)](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665528.gif)

1. 在 **選取初始資料同步處理** 頁面上，選取 **僅聯結** 按一下 **下一步**。 您已經執行資料同步處理以手動方式當您在上進行完整備份和交易備份 **ContosoSQL1** 還原它們 **ContosoSQL2**。 您可以改為選擇不執行備份和還原資料庫，然後選取作業 **完整** ，讓您執行資料同步處理 [新增可用性群組精靈。 不過，不建議針對某些企業中的超大型資料庫採用這種同步處理方式。

    ![新增 AG 精靈：選取初始資料同步處理](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665529.gif)

1. 在 **驗證** 頁面上，按一下 **下一步**。 此頁面的外觀類似於下圖。 由於您尚未設定可用性群組接聽程式，所以出現了關於接聽程式組態的警告。 您可以忽略此警告，因為本教學課程不會示範設定接聽程式的步驟。 若要完成此教學課程之後設定接聽程式，請參閱 [在 Azure 中設定 AlwaysOn 可用性群組的 ILB 接聽](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)。

    ![新增 AG 精靈：驗證](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665530.gif)

1. 在 **摘要** 頁面上，按一下 **完成**, ，然後等候精靈設定新的可用性群組。 在 **進度** ] 頁面上，您可以按一下 **詳細** 若要檢視詳細的進度。 精靈完成後，檢查 **結果** 頁面，以確認是否已成功建立可用性群組，如下所示，然後按一下 [ **關閉** 以結束精靈。

    ![新增 AG 精靈：結果](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665531.gif)

1. 在 **物件總管] 中**, ，依序展開 **AlwaysOn 高可用性**, ，然後展開 **可用性群組**。 新的可用性群組應會顯示在此容器中。 以滑鼠右鍵按一下 **AG1 (主要)** 按一下 **顯示儀表板**。

    ![顯示 AG 儀表板](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665532.gif)

1. 您 **AlwaysOn 儀表板** 看起來應該類似下列所示。 當中會顯示複本、每個複本的容錯移轉模式和同步處理狀態。

    ![AG 儀表板](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665533.gif)

1. 返回 **伺服器管理員**, ，請選取 **工具**, ，然後啟動 **容錯移轉叢集管理員**。

1. 展開 **[cluster1.corp.contoso.com]**, ，然後展開 **服務和應用程式**。 選取 **角色** 和注意 **AG1** 已建立可用性群組的角色。 請注意，由於您未設定接聽程式，所以 AG1 不具備任何資料庫用戶端可用來連接至可用性群組的 IP 位址。 直接連接至主要節點，可進行讀寫作業，連接至次要節點，則可進行唯讀查詢。

    ![容錯移轉叢集管理員中的 AG](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665534.gif)

>[AZURE.WARNING] 請勿嘗試容錯移轉可用性群組容錯移轉叢集管理員。 所有容錯移轉作業應該執行從 **AlwaysOn 儀表板** 在 SSMS 中。 如需詳細資訊，請參閱 [限制在使用 WSFC 容錯移轉叢集管理員與可用性群組](https://msdn.microsoft.com/library/ff929171.aspx)。

## 後續步驟
現在，您已透過在 Azure 中建立可用性群組的方式，成功實作 SQL Server AlwaysOn。 若要設定這個可用性群組接聽程式，請參閱 [在 Azure 中設定 AlwaysOn 可用性群組的 ILB 接聽](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)。

如需在 Azure 中使用 SQL Server 的其他資訊，請參閱 [Azure 虛擬機器上的 SQL Server](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md)。

