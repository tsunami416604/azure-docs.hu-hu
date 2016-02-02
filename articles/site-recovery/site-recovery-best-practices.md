<properties
    pageTitle="準備 Site Recovery 部署 | Microsoft Azure"
    description="Azure Site Recovery 可將內部部署伺服器上虛擬機器和實體伺服器的複寫、容錯移轉及復原協調至 Azure 或次要資料中心。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="12/07/2015"
    ms.author="raynew"/>


# 準備 Azure Site Recovery 部署

## 本文內容

本文說明如何準備 Azure Site Recovery 部署。 如果您有任何問題之後閱讀這篇文章將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## 保護 Hyper-V 虛擬機器

您有一些保護 Hyper-V 虛擬機器的部署選項。 您可以在 Azure 或次要資料中心複寫內部部署 Hyper-v Vm。 每個部署有不同的需求。

 **需求**| **複寫至 Azure (具有 VMM)**| **將 Hyper-V VM 複寫至 Azure (沒有 VMM)**| **將 Hyper-V VM 複寫至次要網站 (具有 VMM)**| **詳細資料**
---|---|---|---|---
 **VMM**| System Center 2012 R2 上執行的 VMM <br/><br/>至少一個 VMM 定域機組，其中包含一或多個 VMM 主機群組。| NA| 至少在具有最新更新的 System Center 2012 SP1 上執行的主要和次要網站中的 VMM 伺服器。<br/><br/> 每個 VMM 伺服器上至少一個定域機組。定域機組應有設定 HYPER-V 容量設定檔。<br/><br/>來源雲端應該有至少一個 VMM 主機群組。| 選用。您不需要部署 System Center VMM，即可將 Hyper-V 虛擬機器複寫至 Azure，但是如果必須部署，請務必確定已正確設定 VMM 伺服器。這包括確認您正在執行正確的 VMM 版本，且已設定雲端。
 **Hyper-V**| 內部部署資料中心中，至少有一部 Hyper-V 主機伺服器執行 Windows Server 2012 R2| 至少一部 HYPER-V 伺服器，來源和目標站台中至少執行 Windows Server 2012 R2，並連線到網際網路。<br/><br/>Hyper-V 伺服器必須位於 VMM 雲端中的主機群組。| 至少一部 HYPER-V 伺服器，來源和目標站台中至少執行含有最新更新的 Windows Server 2012 安裝，然後連線到網際網路。<br/><br/>Hyper-V 伺服器必須位於 VMM 雲端中的主機群組。|
 **虛擬機器**| 來源 Hyper-V 主機伺服器上至少有一個 VM| 來源 VMM 雲端中的 Hyper-V 主機伺服器上至少有一個 VM| 來源 VMM 雲端中的 Hyper-V 主機伺服器上至少有一個 VM。| 複寫至 Azure 的 Vm 必須符合 [Azure 虛擬機器必要條件](site-recovery-best-practices.md/#virtual-machines)
 **Azure 帳戶**| 您將需要 [Azure](http://azure.microsoft.com/) 帳戶和站台復原服務訂閱。| 您將需要 [Azure](http://azure.microsoft.com/) 帳戶和站台復原服務訂閱。| NA| 如果您沒有帳戶，以啟動 [免費試用版](pricing/free-trial/)。閱讀有關 [定價](pricing/details/site-recovery/) 服務。
 **Azure 儲存體**| 您必須有已啟用異地複寫的 Azure 儲存體帳戶的訂用帳戶。| 您必須有已啟用異地複寫的 Azure 儲存體帳戶的訂用帳戶。| NA| 此帳戶應該與 Azure Site Recovery 保存庫位於相同的區域，且和同一個訂用帳戶產生關聯。[深入了解儲存體](../storage/storage-introduction.md)。
 **儲存體對應**| NA| NA| 您可以選擇性設定儲存體對應，以確保虛擬機器可在容錯移轉之後，以最佳方式連接至儲存體。當您在兩個內部部署 VMM 網站之間執行複寫，則根據預設，會將複本虛擬機器儲存在目標 Hyper-V 主機伺服器的指定位置上。您可以在來源與目標 VMM 伺服器上，設定 VMM 儲存體分類之間的對應。| 若要使用此功能，您必須在開始部署之前，確定已先設定好儲存體分類。[Read more](site-recovery-storage-mapping.md).
 **SAN 複寫**| NA| NA| 如果要使用 SAN 複寫節點，在兩個內部部署 VMM 網站之間執行複寫，您可以使用現有的 SAN 環境。| 您將需要 [支援 SAN 陣列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx), ，且必須探索並在 VMM 中分類 SAN 存放裝置。<br/><br/>如果您目前沒有複寫，將需要建立 Lun 並配置存放在 VMM 主控台中。如果已經複寫，可以略過此步驟。
 **網路功能**| 設定網路對應，以確定無論使用哪個復原計劃，在相同 Azure 網路上容錯移轉的所有機器都能彼此連接。此外，如果目標 Azure 網路上已設定網路閘道，則虛擬機器可以連接到其他內部部署虛擬機器。如果您未設定網路對應，則只能連接在相同復原方案中容錯移轉的機器。| NA| <br/><br/>設定網路對應，以確保虛擬機器會連接到適當的網路，容錯移轉之後，而且複本虛擬機器以最佳方式置於 HYPER-V 主機伺服器。如果您沒有設定網路對應，則複寫的機器將不會在容錯移轉之後連接到任何 VM 網路。| [閱讀更多](site-recovery-network-mapping) 解網路對應。<br/><br/> 若要設定網路對應，與 VMM 必須先確定該 VMM 邏輯和 VM 網路已正確設定。[Learn more](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) and [VM networks](https://technet.microsoft.com/library/jj721575.aspx).此外，參閱 [網路考量 VMM](site-recovery-network-design.md/#vmm-design)。
 **提供者和代理程式**| 在部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery 提供者。您會在 VMM 雲端的 Hyper-V 伺服器上安裝 Azure 復原服務代理程式。| 在部署期間，您會在 Hyper-V 主機伺服器或叢集上，安裝 Azure Site Recovery 提供者和 Azure 復原服務代理程式。| 在部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery 提供者。您會在 VMM 雲端的 Hyper-V 伺服器上安裝 Azure 復原服務代理程式。| 提供者和代理程式會使用加密的 HTTPS 連線，透過網際網路連接至 Site Recovery。您不需要加入防火牆例外狀況或建立特定的 proxy，提供者連接，但如果想使用自訂的 proxy，您應該允許這些 Url 通過防火牆開始部署之前: <br/><br/>  *。.hypervrecoverymanager.windowsazure.com <br/><br/> *。 accesscontrol.windows.net<br/><br/> *。 backup.windowsazure.com <br/><br/> *。 windows.net <br/><br/>*。 store.core.windows.net
 **網際網路連線**| 只有 VMM 伺服器需要網際網路連線| 只有在 HYPER-V 主機伺服器需要網際網路連線| 只有 VMM 伺服器需要網際網路連線| 虛擬機器不需要安裝任何項目，並不直接連線到網際網路。



## 保護 VMware 虛擬機器或實體伺服器

您有一些保護 VMware 虛擬機器或 Windows/Linux 實體伺服器的部署選項。 您可以複寫其 Azure 或次要資料中心。 每個部署有不同的需求。

 **需求**| **將 VMware VM/實體伺服器複寫至 Azure)**| **將 VMware VM/實體伺服器複寫至次要網站**
---|---|---
 **主要網站**| **處理序伺服器**：專用的 Windows 伺服器 (實體或虛擬)| **處理序伺服器**: 專用的 Windows 伺服器 (實體或 VMware 虛擬機器<br/><br/>
 **次要內部部署網站**| NA| **組態伺服器**: 專用的 Windows 伺服器 (實體或虛擬) <br/><br/> **主要目標伺服器**: (實體或虛擬) 的專用的伺服器。以 Windows 設定來保護 Windows 機器，或以 Linux 設定來保護 Linux 機器。
 **Azure**| **訂用帳戶**：您需要 Site Recovery 服務的訂用帳戶。閱讀有關 [定價](pricing/details/site-recovery/) <br/><br/> **儲存體帳戶**: 您必須啟用異地備援儲存體帳戶。此帳戶應該與 Site Recovery 保存庫位於相同的區域，且和同一個訂用帳戶產生關聯。[Read more](../storage/storage-introduction.md).<br/><br/>**組態伺服器**: 您必須將組態伺服器做為 Azure VM 設定 <br/><br/> **主要目標伺服器**: 您必須設定主要目標伺服器做為 Azure VM <br/><br/> 以保護 Windows 機器的 Windows 或 Linux，以保護 Linux 的設定。<br/><br/>**Azure 虛擬網路**: 您必須在其設定伺服器與主要目標伺服器將會部署 Azure 虛擬網路。需與 Azure Site Recovery 保存庫位於相同的訂用帳戶與區域中| NA
 **虛擬機器/實體伺服器**| 至少一個 VMware 虛擬機器或實體 Windows/Linux 伺服器。<br/><br/>期間部署的行動服務將會安裝在每部機器上| 至少一個 VMware VM 或實體 Windows/Linux 伺服器。<br/><br/>在部署期間於每部機器上安裝整合代理程式。




## Azure 虛擬機器需求

您可以部署 Site Recovery 以複寫執行受 Azure 支援的任何作業系統的虛擬機器和實體伺服器。 這包括大部分的 Windows 和 Linux 版本。 您必須確認想要保護的內部部署虛擬機器均符合 Azure 要求。


 **功能**| **支援**| **詳細資料**
---|---|---
 Hyper-V 主機作業系統| Windows Server 2012 R2| 如果不支援，則先決條件檢查會失敗
 VMware hypervisor 作業系統| 執行受支援的作業系統| [詳細資料](site-recovery-vmware-to-azure.md/#before-you-start)
 客體作業系統| 複寫站台復原至 Azure 的 hyper-v 支援所有的作業系統， [Azure 所支援的](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)。<br/><br/> VMware 和實體伺服器複寫] 核取 Windows 和 Linux [必要條件](site-recovery-vmware-to-azure.md/#before-you-start)| 如果不支援，則先決條件檢查會失敗。
 客體作業系統架構| 64 位元| 如果不支援，則先決條件檢查會失敗
 作業系統磁碟大小| 最多 1023 GB| 如果不支援，則先決條件檢查會失敗
 作業系統磁碟計數| 1| 如果不支援，則先決條件檢查會失敗。
 資料磁碟計數| 16 或更少 (最大值是所建立之虛擬機器大小的函數。16 = XL)| 如果不支援，則先決條件檢查會失敗
 資料磁碟 VHD 大小| 最大 1023 GB| 如果不支援，則先決條件檢查會失敗
 網路介面卡| 支援多個介面卡|
 靜態 IP 位址| 支援| 如果主要虛擬機器使用靜態 IP 位址，您可以指定會在 Azure 中建立的虛擬機器靜態 IP 位址
 iSCSI 磁碟| 不支援| 如果不支援，則先決條件檢查會失敗
 共用 VHD| 不支援| 如果不支援，則先決條件檢查會失敗
 FC 磁碟| 不支援| 如果不支援，則先決條件檢查會失敗
 硬碟格式| VHD <br/><br/> VHDX| 雖然 Azure 目前不支援 VHDX，但 Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
 虛擬機器名稱| 介於 1 到 63 個字元。只能使用字母、數字和連字號。應該以字母或數字為開頭和結尾| 更新 Site Recovery 中虛擬機器內容的值
 虛擬機器類型| <p>層代 1</p> <p>第 2 代-Windows</p>| 支援第 2 代虛擬機器：具備包含 1 或 2 個資料磁碟區、磁碟格式為 VHDX、磁碟大小小於 300GB 之基本磁碟的作業系統磁碟。不支援 Linux 第 2 代虛擬機器。[了解詳細資訊](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## 最佳化您的部署

使用下列秘訣可協助您最佳化並調整您的部署。

- **作業系統磁碟區大小**：當您將虛擬機器複寫到 Azure 時，作業系統磁碟區必須小於 1TB。 如果磁碟區大小超過此值，您可以在開始部署之前，手動將磁碟區移動至不同的磁碟。
- ****資料磁碟大小：如果複寫至 Azure，您可以在虛擬機器上擁有最多 32 個資料磁碟，每個資料磁碟的上限為 1 TB。 您可以有效地複寫及容錯移轉最多 32 TB 的虛擬機器。
- ****復原方案限制：Site Recovery 可擴充至數千個虛擬機器。 復原方案是設計做為應用程式的模型，而由於應用程式應該一起進行容錯移轉，因此我們將一個復原方案中的機器數目限制為 50。
- ****Azure 服務限制：每個 Azure 訂用帳戶均隨附一組對核心、雲端服務等的預設限制。我們建議您執行容錯移轉測試，以驗證您訂用帳戶中的資源可用性。 您可以透過 Azure 支援修改這些限制。
- **容量規劃**: 讀取 [容量規劃工具](http://www.microsoft.com/download/details.aspx?id=39057) 如果您要複寫的 HYPER-V Vm。
- ****複寫頻寬：如果您的複寫頻寬不足，請注意：
    - **ExpressRoute**：Site Recovery 可搭配 Azure ExpressRoute 和 WAN 最佳化工具，例如 Riverbed。 [閱讀更多](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) 有關 ExpressRoute。
    - ****複寫流量：Site Recovery 只會使用資料區塊 (而非整個 VHD) 來執行智慧型初始複寫。 只會在複寫進行期間複寫變更。
    - **網路流量**: 您可以控制用於複寫所設定的網路流量 [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) 與原則，根據目的地 IP 位址和連接埠。 此外，如果您使用 Azure 備份代理程式複寫至 Azure Site Recovery， 則您可以設定該代理程式的節流。 [Read more](https://support.microsoft.com/kb/3056159).
- **RTO**：如果您想要測量可以預期的 Site Recovery 復原時間目標 (RTO)，我們建議您執行容錯移轉測試並檢視 Site Recovery 工作，來分析需要多少時間才能完成作業。 如果您容錯移轉至 Azure，我們建議您藉由整合 Azure 自動化和復原方案，將所有的手動操作自動化，來取得最佳的 RTO。
- **RPO**：當您複寫至 Azure 時，Site Recovery 支援幾乎同步的復原點目標 (RPO)。 這是假設您的資料中心與 Azure 之間有足夠的頻寬。





## 後續步驟

檢閱這些最佳作法後，就可以開始部署 Site Recovery：

- [設定內部部署 VMM 站台和 Azure 之間的保護](site-recovery-vmm-to-azure.md)
- [設定內部部署 HYPER-V 站台和 Azure 之間的保護](site-recovery-hyper-v-site-to-azure.md)
- [設定兩個內部部署 VMM 站台之間的保護](site-recovery-vmm-to-vmm.md)
- [使用 SAN 的兩個內部部署 VMM 站台間設定保護](site-recovery-vmm-san.md)
- [利用單一 VMM 伺服器設定保護](site-recovery-single-vmm.md)






