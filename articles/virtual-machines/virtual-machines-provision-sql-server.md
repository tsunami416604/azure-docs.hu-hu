<properties 
    pageTitle="佈建 SQL Server 虛擬機器 | Microsoft Azure" 
    description="本教學課程會教您如何在 Azure 上建立及設定 SQL Server VM。" 
    services="virtual-machines" 
    documentationCenter="" 
    authors="rothja" 
    manager="jeffreyg" 
    editor="monicar"
    tags="azure-service-management"
    />

<tags 
    ms.service="virtual-machines" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows-sql-server" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2015" 
    ms.author="jroth"/>


# 在 Azure 中佈建 SQL Server 虛擬機器

> [AZURE.SELECTOR]
- [Azure classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)


## 概觀

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。
。

Azure 虛擬機器組件庫涵蓋數個包含 Microsoft SQL Server 的映像。 您可以從組件庫選取任一個虛擬機器映像，只要點擊幾下便可以將虛擬機器佈建至 Azure 環境。

在本教學課程中，您將：

* [連接 Azure 傳統入口網站並佈建虛擬機器從組件庫](#Provision)
* [開啟虛擬機器使用遠端桌面並完成設定](#RemoteDesktop)
* [完成設定步驟，連接到另一部電腦上使用 SQL Server Management Studio 的虛擬機器](#SSMS)
* [後續步驟](#Optional)

>[AZURE.NOTE] 本文說明如何使用現有的入口網站佈建 SQL Server VM。 不過，您也可建立和管理 SQL Server Vm 中 [新入口網站](https://manage.windowsazure.com)。 新的入口網站有一些優點，例如預設使用進階儲存體，以及其他選項，例如自動修補、自動備份及 AlwaysOn 組態。 後續內容會提供逐步指示。

## <a id="Provision">佈建 SQL Server 虛擬機器從組件庫</a>

1. 登入 [Azure 傳統入口網站](http://manage.windowsazure.com) 使用您的帳戶。 如果您沒有 Azure 帳戶，請造訪 [Azure 免費試用版](http://azure.microsoft.com/pricing/free-trial/)。

2. 在 Azure 傳統入口網站中，依序按一下網頁左下角的 [+新增]****、[計算]****、[虛擬機器]**** 和 [從資源庫]****。

3. 在 [選擇映像]**** 頁面上，按一下 [SQL SERVER]****。 然後選取 SQL Server 映像。 按一下頁面右下方的 [下一步] 箭頭。

    ![選擇映像](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

如需支援的 SQL Server 映像，在 Azure 上最新的資訊，請參閱 [SQL Server 的 Azure 虛擬機器總覽](virtual-machines-sql-server-infrastructure-services.md)。
>[AZURE.NOTE] 如果您擁有以 SQL Server Evaluation Edition 平台映像建立的虛擬機器，該虛擬機器無法升級為組件庫內以分鐘計費之版本的映像。 您可以在以下兩個選項中選擇其中一項：
>
> -您可以從資源庫使用的每分鐘計費的 SQL Server 版本來建立新的虛擬機器並將資料庫檔案移轉到這部新虛擬機器所遵循的步驟，在 [資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-migrate-onpremises-database)
> -或者，您可以將現有的執行個體的 SQL Server 評估版升級至不同版本的 SQL Server 下 [Azure 上透過軟體保證的授權機動性](http://azure.microsoft.com/pricing/license-mobility/) 協議所遵循的步驟 [升級至 SQL Server 的不同版本](https://msdn.microsoft.com/library/cc707783.aspx)。 如需如何購買授權的版本之 SQL Server 的資訊，請參閱 [如何購買 SQL Server](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx)。

4. 在第一個 [**虛擬機器組態**] 頁面，請提供下列資訊：
    - **版本發行日期**。 如果有多個映像，請選取最新映像。
    - 唯一的 [虛擬機器名稱]****。
    - 在 [新的使用者名稱]**** 方塊中，輸入此機器之本機系統管理員帳戶的唯一使用者名稱。
    - 在 [新增密碼]**** 方塊中，輸入增強式密碼。
    - 在 [確認密碼]**** 方塊中，重新輸入密碼。
    - 從 [**大小**] 下拉式清單中選取適當的大小。

    ![VM 組態](./media/virtual-machines-provision-sql-server/4VM-Config.png)
    >[AZURE.NOTE] 虛擬機器的大小可在佈建期間指定：
    >
    > -若為生產工作負載，建議您使用高階儲存體具有下列最低的建議大小: **DS3** 適用於 SQL Server Enterprise edition 和 **DS2** 適用於 SQL Server Standard edition。 如需詳細資訊，請參閱 [的 Azure 虛擬機器中 SQL Server 效能最佳作法](virtual-machines-sql-server-performance-best-practices.md)。
    > -選取的大小會限制您可以設定的資料磁碟數目。 最新可用的虛擬機器大小和可連接至虛擬機器的資料磁碟數目的詳細資訊，請參閱 [Azure 的虛擬機器大小](virtual-machines-size-specs.md)。

5. 輸入 VM 組態的詳細資料後，請按一下右下角的下一步箭頭以繼續。

5. 在第二個 [虛擬機器組態]**** 頁面上，請設定網路、儲存體和可用性的資源：
    - 在 [雲端服務]**** 方塊中，選擇 [Create a new cloud service]****。
    - 在 [Cloud Service DNS Name]**** 方塊中，提供選擇之 DNS 名稱的第一個部分，使其形成 **TESTNAME.cloudapp.net** 格式的名稱
    - 如果您有多個訂用帳戶可供選擇，請選取 [訂用帳戶]****。 選擇會決定哪一個 ** 儲存體帳戶 **可用。
    - 在 [REGION/AFFINITY GROUP/VIRTUAL NETWORK]**** 方塊中，選取代管這個虛擬映像的所在區域。
    - 在 [儲存體帳戶]**** 中，可以自動產生帳戶，或從清單中選取一個帳戶。 變更 [訂用帳戶]**** 以查看更多帳戶。
    - 在 [**可用性設定組**] 方塊中，選取 [**(無)**]。
    - 閱讀及接受法律條款。

6. 按一下 [下一步] 箭頭以繼續。

7. 按一下右下角的核取記號以繼續操作。

8. 等待 Azure 準備好您的虛擬機器。 虛擬機器應該會依序經歷以下狀態變更：

    - **啟動中 (佈建中)**
    - **已停止**
    - **啟動中 (佈建中)**
    - **執行中 (佈建中)**
    - **執行中**


## <a id="RemoteDesktop">開啟 VM 以完成設定使用遠端桌面</a>

1. 佈建完成時，請按一下虛擬機器的名稱以前往 [儀表板] 頁面。 按一下頁面底部的 [**連接**]。

2. 按一下 [開啟]**** 按鈕。

    ![按一下 ](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. 在 [Windows 安全性]**** 對話方塊中，按一下 [使用其他帳戶]****。

    ![按一下 ](./media/virtual-machines-provision-sql-server/credentials.png)

4. 使用的電腦名稱做為網域名稱，後面加上您的系統管理員名稱格式如下: `machinename\username`。 輸入您的密碼並連線到虛擬機器。

4. 當您第一次登入時，需要完成數個程序，包括設定桌面、Windows Update，以及完成 Windows 初始組態工作 (sysprep)。 待 Windows sysprep 完成之後，SQL Server 安裝程式會完成組態工作。 這些工作完成時，可能會導致幾分鐘的延遲。 `選取 @@SERVERNAME` 直到 SQL Server 安裝程式完成時，SQL Server Management Studio 才可能會出現在 [開始] 頁面上，則不可能會傳回正確的名稱。

使用 Windows 遠端桌面連接到虛擬機器之後，虛擬機器的運作方式與任何其他電腦很像。 請依照正常方法使用 SQL Server Management Studio (於虛擬機器上運作) 連接 SQL Server 的預設執行個體。

## <a id="SSMS">從 SSMS 連接到 SQL Server VM 執行個體上另一部電腦</a>

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

## <a id="cdea">從您的應用程式連接到 Database Engine</a>

如果您可以使用 Management Studio 連接於 Azure 虛擬機器上運作的 SQL Server 執行個體，應該能使用與下文相似的連線字串進行連接。

    connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

如需詳細資訊，請參閱 [如何疑難排解連線到 SQL Server Database Engine](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)。

## <a id="Optional">後續步驟</a>

您已了解如何使用平台映像來建立及設定 Azure 虛擬機器上的 SQL Server。 在許多情況下下，下一個步驟是將資料庫移轉到這個新的 SQL Server VM。 資料庫移轉指引，請參閱 [資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-migrate-onpremises-database.md)。

下列清單提供 Azure 虛擬機器中 SQL Server 的其他資源。

### 針對 Azure VM 上的 SQL Server 的建議參考資源：

- [SQL Server 的 Azure 虛擬機器概觀](virtual-machines-sql-server-infrastructure-services.md)

- [連接到 Azure 上的 SQL Server 虛擬機器](virtual-machines-sql-server-connectivity.md)

- [Azure 虛擬機器中的 SQL Server 效能最佳作法](virtual-machines-sql-server-performance-best-practices.md)

- [Azure 虛擬機器中的 SQL Server 的安全性考量](virtual-machines-sql-server-security-considerations.md)

### 高可用性和災害復原：

- [高可用性和 Azure 虛擬機器中的 SQL Server 的嚴重損壞修復](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)

- [Azure 虛擬機器中的 SQL Server 的備份和還原](virtual-machines-sql-server-backup-and-restore.md)

### Azure 中的 SQL Server 工作負載：

- [Azure 虛擬機器中的 SQL Server 商業智慧](virtual-machines-sql-server-business-intelligence.md)

### 白皮書：

- [了解 Azure SQL Database 和 Azure 虛擬機器中的 SQL Server](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [應用程式模式和 Azure 虛擬機器中的 SQL Server 的開發策略](virtual-machines-sql-server-application-patterns-and-development-strategies.md)





