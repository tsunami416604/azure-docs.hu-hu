<properties
   pageTitle="Microsoft Azure IaaS 上軟體更新的最佳作法 | Microsoft Azure"
   description="本文針對 Microsoft Azure IaaS 環境中的軟體更新提供最佳作法集合。  它的適用對象為 IT 專業人員和安全性分析師，他們每天都在處理變更控制、軟體更新和資產管理，包括那些負責其組織安全性和法規工作的人員。"
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriD"
   manager="swadhwa"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="yurid"/>

#Microsoft Azure IaaS 上軟體更新的最佳作法

在探究 Azure IaaS 環境的任何最佳作法討論之前，請務必了解可讓您管理軟體更新的案例。 下圖應有所幫助：

![雲端模型和職責](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack.png)

在整個基礎結構位於內部部署的傳統資料中心模型中，您需負起全責來管理作業系統、應用程式、網路裝置 (路由器、交換器等) 和硬體 (韌體) 的更新。 在 IaaS 案例中，您仍然必須管理作業系統和應用程式的更新。不過，作業系統和應用程式之下的整個基礎結構是由 Microsoft 管理。 在所有這些模型中，客戶仍是其資料的擁有者且仍必須負責在端點層級保護資料。

在 PaaS 案例中，您對軟體更新所負的責任比較少，因為作業系統的更新是 Microsoft 的責任。 在 SaaS 案例中，整個堆疊的軟體更新責任是由 Microsoft 所承擔。

這些相同的原則會套用在混合式案例中，其中貴公司使用 Azure IaaS VM 與內部部署資源通訊，如下圖所示。

![Microsoft Azure 的典型混合式案例](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## 初步評估

即使您的公司已採用更新管理系統，而且您已採用軟體更新原則，請務必經常檢閱先前的原則評估並根據目前的需求加以更新。 這表示您需要熟悉您的公司中資源的目前狀態。 若要達到此狀態，您必須了解：

-   您企業中的實體和虛擬電腦。

-   這些實體和虛擬電腦所執行的作業系統和版本。

-   每部電腦目前安裝的軟體更新 (Service pack 版本、軟體更新和其他修改)。

-   每部電腦在您的企業中執行的功能。

-   在每部電腦上執行的應用程式和程式。

-   每部電腦的擁有權和連絡人資訊。

-   您的環境中出現的資產，以及用來判斷哪些區域需要最多注意和保護的相對值。

-   已知的安全性問題以及您的企業在安全性層級用來識別新安全性問題或變更的程序。

-   為了保護您的環境所部署的安全措施。

您應該定期更新此資訊，而此資訊應該可隨時提供給那些參與軟體更新管理程序的人員使用。

## 建立基準

軟體更新管理程序中的重要部分就是為您企業中的電腦建立作業系統版本、應用程式和硬體的初始標準安裝；這些稱之為基準。 基準是在特定時間點建立的產品或系統的組態。 例如，應用程式或作業系統基準可供將電腦或服務重建至特定狀態。

基準可提供尋找及修正潛在問題的基礎並簡化軟體更新管理程序，其作法是減少您必須在企業中部署的軟體更新數目，以及增強您監視相符性的能力。

在執行您企業的初始稽核之後，您應該使用取自稽核的資訊來定義您的生產環境中 IT 元件的作業基準。 視生產環境中部署的各類硬體和軟體而定，可能一些需要基準。

例如，某些伺服器需要軟體更新，以免在執行 Windows Server 2012 的情況下進入關機程序時懸置。 這些伺服器的基準應該包含此軟體更新。

在大型組織中，這通常有助於將您企業中的電腦分成各種資產類別，並使用相同版本的軟體和軟體更新讓每個類別保持在標準基準。 然後，您可以在設定軟體更新散發的優先順序時使用這些資產類別。

## 訂閱適當的軟體更新通知服務

在您執行企業中所用軟體的初始稽核之後，您應該針對每個軟體產品和版本決定用於接收新軟體更新通知的最佳方法。 視軟體產品而定，最佳的通知方法可能是電子郵件通知、網站或電腦發佈。

例如，Microsoft 安全性回應中心 (MSRC) 會回應 Microsoft 產品的所有安全性相關疑慮，並提供 Microsoft 安全性公告服務、新發現弱點的免費電子郵件通知，以及為了解決這些弱點所發行的軟體更新。 您可以訂閱此服務在 http://www.microsoft.com/technet/security/bulletin/notify.mspx。

## 軟體更新考量

在您執行企業中所用軟體的初始稽核之後，您應該決定設定軟體更新管理系統的需求，這取決於您使用的軟體更新管理系統。 Wsus 讀取 [最佳作法與 Windows Server Update Services](https://technet.microsoft.com/library/Cc708536), ，如 System Center，請參閱 [規劃 Configuration Manager 中的軟體更新](https://technet.microsoft.com/library/gg712696)。

不過，不論您正在使用的解決方案為何，您可以套用一些一般考量和最佳作法，如後續各節所示。

### 設定環境

在打算設定軟體更新管理環境時，請考量下列作法：

-   **建立穩定的準則為基礎的軟體更新集合生產**︰ 一般情況下，建立您的軟體更新清查和發佈集合使用穩定的準則是為了簡化軟體更新管理程序的所有階段。 穩定準則可以包含已安裝的用戶端作業系統版本和 Service Pack 層級、系統角色或目標組織。

-   **建立包含參照電腦進入生產階段前集合**︰ 進入生產階段前集合應該包含代表性的組態的作業系統版本，一行商業軟體，執行您的企業中的其他軟體。

您也應該考慮軟體更新伺服器將位於何處︰位於雲端的 Azure IaaS 基礎結構中或在內部部署中。 這是一項重要決策，因為您需要評估內部部署資源與 Azure 基礎結構之間的流量。 讀取 [在內部網路連線到 Microsoft Azure 虛擬網路](https://technet.microsoft.com/library/Dn786406.aspx) 如需有關如何將內部部署基礎結構連接至 Azure。

根據您目前的基礎結構以及您目前使用的軟體更新系統而定，用來決定更新伺服器將位於何處的設計選項也會有所不同。 Wsus 讀取 [在組織中部署 Windows Server Update Services](https://technet.microsoft.com/library/hh852340.aspx) 和 System Center Configuration Manager 讀取 [規劃站台和階層中 Configuration Manager](https://technet.microsoft.com/library/Gg712681.aspx)。

### 備份

定期備份很重要，不只是對軟體更新管理平台本身而言，對即將更新的伺服器而言也很重要。 組織 [變更管理流程](https://technet.microsoft.com/library/cc543216.aspx) 就地需要 IT 證明的原因為何需要更新伺服器、 預估的停機時間和可能的影響。 為了確保在更新失敗時能採用回復組態，請務必定期備份系統。

Azure IaaS 的某些備份選項包括：

-   [使用 Data Protection Manager 的 Azure IaaS 工作負載保護](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [備份 Azure 虛擬機器](../backup/backup-azure-vms.md)

### 監視

您應該執行定期報告，以針對每個已獲授權的軟體更新，監視遺漏或已安裝的更新數目，或狀態不完整的更新。 同樣地，報告未獲授權的軟體更新可讓部署決策變得更容易。

您也應考量下列工作：

-   進行公司內所有電腦的適用和已安裝安全性更新的稽核。

-   授權更新並部署到適當的電腦。

-   追蹤公司內所有電腦的清查和更新安裝狀態和進度。

除了本文中說明的一般考量以外，您也應該考慮每項產品的最佳作法，例如︰如果您在採用 SQL Server 的 Azure 中有 VM，請確保遵循該產品的軟體更新建議。

## 後續步驟

使用本文所述的指導方針，可協助您判斷適用於 Azure IaaS 中虛擬機器軟體更新的最佳選項。 傳統資料中心與 Azure IaaS 之間的軟體更新最佳作法有許多相似之處，因此建議您評估目前的軟體更新原則，以包含 Azure VM 並將本文中的相關最佳作法章納入整體軟體更新程序中。


