<properties 
    pageTitle="SQL Server 效能最佳作法 | Microsoft Azure"
    description="提供將 Microsoft Azure VM 中 SQL Server 效能最佳化的最佳作法。"
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
    ms.date="11/13/2015"
    ms.author="jroth" />

# Azure 虛擬機器中的 SQL Server 效能最佳作法

## 概觀

本主題提供將 Microsoft Azure 虛擬機器中 SQL Server 的效能最佳化的最佳作法。 在 Azure 虛擬機器中執行 SQL Server 時，我們建議您繼續使用相同的資料庫效能微調選項，這些選項適用於內部部署伺服器環境中的 SQL Server。 不過，公用雲端中關聯式資料庫的效能優劣取決於許多因素，例如虛擬機器的大小和資料磁碟的組態。

建立 SQL Server 映像時可考慮使用 Azure 入口網站，以善用各項功能 (例如預設使用進階儲存體) 以及其他選項 (例如自動修補、自動備份和 AlwaysOn 組態)。

本文的主題為如何讓 Azure VM 上的 SQL Server 達到最佳效能。 如果您的工作負載需求較低，可能就不需要採用下列每一項最佳化條件。 評估以下建議時，請考慮您的效能需求和工作負載模式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 快速檢查清單

下列快速檢查清單能協助您讓 Azure 虛擬機器上的 SQL Server 達到最佳效能：

|領域|最佳化|
|---|---|
|**VM 大小**|[DS3](virtual-machines-size-specs.md#standard-tier-ds-series) 或更高版本的 SQL Enterprise edition。<br/><br/>[DS2](virtual-machines-size-specs.md#standard-tier-ds-series) 或更高版本的 SQL Standard 以及 Web 版本。|
|**儲存體**|使用 [高階儲存體](../storage/storage-premium-storage-preview-portal.md)。<br/><br/>保留 [儲存體帳戶](../storage/storage-create-storage-account.md) 和 SQL Server VM 位於相同區域中。<br/><br/>停用 Azure [地理區域備援儲存體](../storage/storage-redundancy.md) （異地複寫） 上的儲存體帳戶。|
|**磁碟**|使用最少 2 [P30 磁碟](../storage/storage-premium-storage-preview-portal.md#scalability-and-performance-targets-when-using-premium-storage) （記錄檔 1; 1 表示資料檔和 TempDB）。<br/><br/>避免使用作業系統或暫存磁碟作為資料儲存體或進行記錄。<br/><br/>啟用裝載資料檔案和 TempDB 的磁碟上的 [讀取快取] 功能。<br/><br/>不要啟用裝載記錄檔案的磁碟上的 [快取] 功能。<br/><br/>分割多個 Azure 資料磁碟，以提高 IO 輸送量。<br/><br/>以文件上記載的配置大小格式化。|
|**I/O**|啟用 [資料庫頁面壓縮] 功能　。<br/><br/>針對資料檔案，啟用 [立即檔案初始化] 功能。<br/><br/>限制資料庫上的 [自動成長] 功能，或停用。<br/><br/>停用資料庫上的 [自動壓縮] 功能。<br/><br/>將所有的資料庫 (包括系統資料庫) 移到資料磁碟。<br/><br/>將 SQL Server 的錯誤記錄檔和追蹤檔案目錄移至資料磁碟。<br/><br/>設定預設的備份和資料庫檔案的位置。<br/><br/>啟用鎖定的頁面。<br/><br/>套用 SQL Server 效能修正程式。|
|**特定功能**|直接備份至 Blob 儲存體。|

如需詳細資訊，請參閱下列小節中的指導方針。

## 虛擬機器大小與儲存體帳戶的考量

對於需要高效能的應用程式，建議您採用下列虛擬機器大小：

- **SQL Server Enterprise Edition**: DS3 或更高版本

- **SQL Server Standard 和 Web Edition**: DS2 或更高版本

更新支援的虛擬機器大小的詳細資訊，請參閱 [虛擬機器的大小](virtual-machines-size-specs.md)。

此外，我們建議您在存放 SQL Server 虛擬機器的同一個資料中心內建立 Azure 儲存體帳戶，以減少傳輸延遲的狀況。 建立儲存體帳戶時，請停用 [異地複寫] 功能，因為跨多個磁碟時無法保證寫入順序一致。 請考慮另一個方法，在兩個 Azure 資料中心之間設定 SQL Server 災害復原技術。 如需詳細資訊，請參閱 [高可用性和嚴重損壞修復的 SQL Server Azure 虛擬機器中](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)。

## 磁碟和效能考量

建立 Azure 虛擬機器時，該平台會至少將一個磁碟連接至 VM 作為作業系統磁碟。 此磁碟是以分頁 Blob 的形式儲存於儲存體的 VHD。 您也可以將其他磁碟連接至虛擬機器作為資料磁碟，這些磁碟將會以分頁 Blob 形式儲存於儲存體。 Azure 虛擬機器中還有另一個磁碟，稱為暫存磁碟。 此磁碟位於可用於塗銷空間的節點上。

### 作業系統磁碟

作業系統磁碟是開機並掛接當做作業系統執行版本的 VHD，並標示為 **C** 磁碟機。

快取原則作業系統磁碟上的預設值是 **讀/寫**。 對於需要高效能的應用程式，我們建議您使用資料磁碟取代作業系統磁碟。 請參閱下面的＜資料磁碟＞一節。

### 暫存磁碟

暫存磁碟機，標示為 **D**︰ 磁碟機，不會保存到 Azure blob 儲存體。 要將您的資料或記錄檔儲存在 **D**︰ 磁碟機。

只有儲存在 TempDB 和 （或） 緩衝區集區延伸模組 **D** 使用 D 系列或 G 系列虛擬機器 (Vm) 時，磁碟機。 不同於其他 VM 系列 **D** 磁碟機 D 系列和 G 系列 Vm 是以 SSD 為基礎。 這可改善大量使用暫存物件，或工作集超過記憶體容量的工作負載的效能。 如需詳細資訊，請參閱 [使用 SSDs 來儲存 SQL Server TempDB 和緩衝區集區擴充功能的 Azure Vm 中](http://blogs.technet.com/b/dataplatforminsider/archive/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions.aspx)。

### 資料磁碟

- **資料磁碟數目的資料和記錄檔**︰ 最少使用 2 [P30 磁碟](../storage/storage-premium-storage-preview-portal.md#scalability-and-performance-targets-when-using-premium-storage) 其中一個磁碟含有記錄檔，另一個包含資料檔和 TempDB。 若要提高輸送量，您可能需要額外的資料磁碟。 若要判斷需要的資料磁碟數量，您需要分析資料和記錄磁碟可用的 IOPS 數量。 此資訊，請參閱每個 IOPS 上資料表 [VM 大小](virtual-machines-size-specs.md) 和磁碟大小，在下列文章 ︰ [磁碟使用 Premium 儲存體](../storage/storage-premium-storage-preview-portal.md)。 如果您需要更多的頻寬，可以附加額外磁碟，並使用磁碟分割。 如果您不使用高階儲存體，建議您新增的支援的資料磁碟數目上限您 [VM 大小](virtual-machines-size-specs.md) ，並使用磁碟條狀配置。 如需有關磁碟分割的詳細資訊，請參閱以下相關章節。

- **快取原則**︰ 啟用讀取快取只裝載您的資料檔和 TempDB 資料磁碟上。 如果您並非使用進階儲存體，請勿啟用任何資料磁碟上的任何快取功能。 如需設定磁碟快取的指示，請參閱下列主題 ︰ [Set-azureosdisk](https://msdn.microsoft.com/library/azure/jj152847) 和 [-Hostcaching](https://msdn.microsoft.com/library/azure/jj152851.aspx)。

- **NTFS 配置單位大小**︰ 在格式化資料磁碟，建議您針對資料和記錄檔以及 TempDB 使用 64 KB 配置單位大小。

- **磁碟條狀配置**︰ 我們建議您遵循這些指導方針 ︰ 

    - 適用於 Windows 8/Windows Server 2012 或更新版本，使用 [儲存空間](https://technet.microsoft.com/library/hh831739.aspx)。 將 OLTP 工作負載的等量磁碟區大小設為 64 KB，資料倉儲的工作負載則設為 256 KB，以避免分割對齊錯誤影響效能。 此外，設定資料行計數 = 實體磁碟數量。 若要設定磁碟超過 8 個的儲存空間，您必須使用 PowerShell (非伺服器管理員 UI)，以明確地將資料行計數設定為符合磁碟的數量。 如需有關如何設定 [儲存空間](https://technet.microsoft.com/library/hh831739.aspx), ，請參閱 [Windows PowerShell 中的儲存空間 Cmdlet](https://technet.microsoft.com/library/jj851254.aspx)
    
    - 對於 Windows 2008 R2 之前的版本，可以使用動態磁碟 (OS 分割的磁碟區)，且等量磁碟區的大小一律為 64 KB。 請注意，Windows 8/Windows Server 2012 已不再提供此選項。 詳細資訊，請參閱支援陳述式在 [虛擬磁碟服務正在轉換至 Windows 存放管理 API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)。
    
    - 如果您的工作負載不需大量記錄及，且不需專屬於 IOP，您可以只設定一個儲存體集區。 否則，請建立兩個儲存體集區，一個用於儲存記錄檔案，另一個用於儲存資料檔案和存放 TempDB。 請根據您預期的負載量，決定與每個儲存體集區相關聯的磁碟數量。 請注意，各 VM 大小所允許連接的資料磁碟數量皆不同。 如需詳細資訊，請參閱 [虛擬機器的大小](virtual-machines-size-specs.md)。

## I/O 效能考量

- 平行處理應用程式和要求時，進階儲存體可以達到最佳效能。 進階儲存體是專為 IO 佇列深度大於 1 的案例所設計，所以您會發現單一執行緒的序列要求 (即使其為儲存密集型) 的效能只有微幅提升，或沒有提升。 舉例來說，這可能會影響效能分析工具 (如 SQLIO) 的單一執行緒測試結果。

- 請考慮使用 [資料庫頁面壓縮](https://msdn.microsoft.com/library/cc280449.aspx) ，其有助於改善 I/O 密集型工作負載的效能。 不過，資料壓縮可能會增加資料庫伺服器的 CPU 使用量。

- 將資料檔案傳輸至 Azure，或從 Azure 往外傳輸時，請考慮先壓縮所有資料檔案。

- 請考慮啟用 [立即檔案初始化] 功能，以減少配置初始檔案所需的時間。 為了利用立即檔案初始化，方法，您可以授與 SQL Server (MSSQLSERVER) 服務帳戶的 SE_MANAGE_VOLUME_NAME，並將它加入至 **執行磁碟區維護工作** 安全性原則。 如果您使用 azure 的 SQL Server 平台映像，預設服務帳戶 (NT Service\MSSQLSERVER) 不會新增到 **執行磁碟區維護工作** 安全性原則。 也就是說，SQL Server Azure 平台映像未啟用 [立即檔案初始化] 功能。 加入 SQL Server 服務帳戶之後 **執行磁碟區維護工作** 安全性原則，重新啟動 SQL Server 服務。 使用此功能時，可能有安全性考量。 如需詳細資訊，請參閱 [資料庫檔案初始化](https://msdn.microsoft.com/library/ms175935.aspx)。

- **自動成長** 會被視為只是預期成長的偶發事件。 請勿每天使用「自動成長」功能，管理資料和記錄成長。 若已使用「自動成長」功能，請透過 大小參數預先放大檔案。

- 請確定 **autoshrink** 停用，以避免不必要的額外負荷，可以對效能造成負面的影響。

- 如果您執行的是 SQL Server 2012，請安裝 Service Pack 1 累計更新 10。 此更新包含一個修正程式，可修正在 SQL Server 2012 中執行 select into 暫存資料表陳述式時，I/O 效能不佳的狀況。 如需資訊，請參閱此 [知識庫文件](http://support.microsoft.com/kb/2958012)。

- 將 SQL Server 的系統資料庫 (例如 msdb 和 TempDB)、備份，以及預設資料和記錄目錄移至非快取的資料磁碟，以改善效能。 然後，執行下列動作：

    - 修改 XEvent 和追蹤檔案的路徑。
    
    - 修改 SQL 錯誤記錄檔的路徑。
    
    - 修改預設備份的路徑。
    
    - 修改預設資料庫的位置。

- 建立鎖定的頁面，以減少 IO 和任何分頁活動。

## 功能特定的效能考量

有些部署作業可能會使用更進階的組態技術，提供額外的效能優點。 下列清單特別強調了一些可協助您達到更佳效能的 SQL Server 功能：

- **備份至 Azure 儲存體**︰ 在 Azure 虛擬機器中執行的 SQL server 執行備份，您可以使用 [SQL Server 備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx)。 此功能從 SQL Server 2012 SP1 CU2 開始提供，建議在備份至連接的資料磁碟時使用。 當您備份/還原至/從 Azure 儲存體，請遵循所提供的建議 [SQL Server 備份至 URL 的最佳作法和疑難排解及還原的備份儲存在 Azure 儲存體](https://msdn.microsoft.com/library/jj919149.aspx)。 您也可以自動化使用這些備份 [Azure 虛擬機器中 SQL Server 的自動化備份](virtual-machines-sql-server-automated-backup.md)。

    您可以使用 SQL Server 2012 之前, [SQL Server 備份至 Azure 工具](https://www.microsoft.com/download/details.aspx?id=40740)。 這項工具可以透過多個備份等量磁碟區目標協助您提高備份的輸送量。

- **在 Azure 中的 SQL Server 資料檔案**︰ 這項新功能， [在 Azure 中的 SQL Server 資料檔案](https://msdn.microsoft.com/library/dn385720.aspx), ，從 SQL Server 2014 開始提供。 在 Azure 中執行具有資料檔案的 SQL Server 的效能與使用 Azure 資料磁碟的效能特性相當。

## 後續步驟

如果您有興趣深入探索 SQL Server 和高階儲存體，請參閱文章 [使用 Azure 高階儲存體和虛擬機器上的 SQL Server](virtual-machines-sql-server-use-premium-storage.md)。

安全性最佳作法，請參閱 [Azure 虛擬機器中 SQL Server 的安全性考量](virtual-machines-sql-server-security-considerations.md)。

檢閱其他 SQL Server 虛擬機器主題 [SQL Server 的 Azure 虛擬機器總覽](virtual-machines-sql-server-infrastructure-services.md)。


