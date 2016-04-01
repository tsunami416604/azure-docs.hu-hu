<properties
    pageTitle="設定內部部署 VMM 站台之間的保護"
    description="Azure Site Recovery 可協調內部部署 VMM 網站之間的 Hyper-V 虛擬機器複寫、容錯移轉及復原作業。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2015"
    ms.author="raynew"/>

# 設定內部部署 VMM 站台之間的保護


## 概觀


Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 做出貢獻。 如需完整清單的部署案例，請參閱  [Azure Site Recovery 概觀](site-recovery-overview.md)。

本案例指南說明如何部署 Site Recovery，為執行於虛擬機器 (位於 VMM 私人雲端中的 Hyper-V 主機伺服器上) 的工作負載協調和自動化保護。 在這個案例中的虛擬機器會從主要 VMM 站台複寫到使用 Hyper-V 複本的次要 VMM 站台。

此指南包含案例的必要條件，並示範如何設定 Site Recovery 保存庫、取得安裝在來源和目標 VMM 伺服器的 Azure Site Recovery 提供者、在保存庫註冊伺服器、設定將套用到所有受保護虛擬機器之 VMM 雲端的保護設定、然後啟用那些虛擬機器的保護。 測試容錯移轉，確認一切如預期般運作以完成動作。

如果您遇到設定問題此案例中張貼您的問題上 [Azure 復原服務論壇](http://go.microsoft.com/fwlink/?LinkId=313628)。


## 開始之前
確認您已備妥這些必要條件：
### Azure 必要條件

- 您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。 如果您沒有帳戶，以啟動 [免費試用版](http://aka.ms/try-azure)。 此外，您可以參閱 [Azure Site Recovery 管理員價格](http://go.microsoft.com/fwlink/?LinkId=378268)。
- 若要了解如何使用資訊和資料，讀取 [Microsoft Azure 隱私權聲明](http://go.microsoft.com/fwlink/?LinkId=324899) 和其他 <a href="#privacy">Site Recovery 的隱私權資訊</a> 本主題的底部。

### VMM 先決條件
- 您至少需要一個 VMM 伺服器。
- VMM 伺服器應至少執行附帶最新累計更新的 System Center 2012 SP1。
- 任何包含您想要保護之虛擬機器的 VMM 伺服器都必須執行 Azure Site Recovery 提供者。 它會在部署 Azure Site Recovery 期間安裝。
- 如果您想要利用單一 VMM 伺服器設定保護，您必須在伺服器上設定至少兩個雲端。
- 如果您想要利用兩部 VMM 伺服器部署保護，每部伺服器都必須至少有一個雲端設定在您想要保護的主要 VMM 伺服器上，一個雲端要設定在您想要用於保護和復原的次要 VMM 伺服器上
- 所有的 VMM 雲端都必須設定 Hyper-V 容量設定檔。
- 您想要保護的來源雲端必須包含下列項目：
    - 一或多個 VMM 主機群組。
    - 每個主機群組中的一或多個 Hyper-V 主機伺服器。
    - 主機伺服器上的一或多個虛擬機器。
- 深入了解設定 VMM 雲端：
    - 深入了解私人 VMM 雲端 [的新功能使用 System Center 2012 R2 VMM 的私人雲端](http://go.microsoft.com/fwlink/?LinkId=324952) 和 [VMM 2012 和雲端](http://go.microsoft.com/fwlink/?LinkId=324956)。
    - 深入了解 [設定 VMM 雲端網狀架構](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
    - 深入了解建立私人雲端中的您的雲端網狀架構元素之後  [在 VMM 中建立私人雲端](http://go.microsoft.com/fwlink/?LinkId=324953) 和 [逐步解說 ︰ 使用 System Center 2012 SP1 VMM 建立私人雲端](http://go.microsoft.com/fwlink/?LinkId=324954)。

### Hyper-V 的必要條件

- 主機和目標 Hyper-V 伺服器必須至少執行 Hyper-V 角色的 Windows Server 2012，並且安裝最新更新。
- 如果您在叢集中執行 Hyper-V，請注意，如果您具有靜態 IP 位址叢集，並不會自動建立叢集代理。 您必須手動設定叢集代理。 如需指示，請參閱 [設定 HYPER-V 複本代理人](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。
- 您想要管理保護的任何 Hyper-V 主機伺服計或叢集都必須包含在 VMM 雲端中。

下圖顯示 Azure Site Recovery 為協調流程與複寫所使用的不同通訊通道與連接埠

![E2E 拓樸](./media/site-recovery-vmm-to-vmm/E2ETopology.png)


### 網路對應的必要條件
網路對應可確保在容錯移轉之後以最佳方式在 Hyper-V 主機伺服器上放置複本虛擬機器，而且他們可以連線到適當的 VM 網路。 如果您沒有設定網路對應，複本虛擬機器將不會在容錯移轉後連線到 VM 網路。 如果您想要部署網路對應，您需要下列項目：

- 您想要在來源 VMM 伺服器上保護的虛擬機器應該連線到 VM 網路。 該網路應該連結到與雲端相關聯的邏輯網路。
- 在您用於復原的次要 VMM 伺服器上的目標雲端應該設定相對應的 VM 網路，而該網路應該連結到與目標雲端相關聯的相對應邏輯網路。

深入了解網路對應：

- [在 VMM 中設定邏輯網路](http://go.microsoft.com/fwlink/?LinkId=386307)
- [在 VMM 中設定 VM 網路和閘道](http://go.microsoft.com/fwlink/?LinkId=386308)

### 儲存體對應的必要條件
根據預設，當您將來源 Hyper-V 主機伺服器上的虛擬機器複寫至目標 Hyper-V 主機伺服器時，複寫的資料會儲存在為 Hyper-V 管理員中之目標 Hyper-V 主機所指定的預設位置。 如果要進一步控制複寫資料的儲存位置，您可以設定儲存體對應。 若要這樣做，您必須在開始部署之前，在來源與目標 VMM 伺服器設定儲存體分類。
如需指示，請參閱 [如何在 VMM 中建立儲存體分類](http://go.microsoft.com/fwlink/?LinkId=400937)。


## 步驟 1：建立 Site Recovery 保存庫

1. 登入 [管理入口網站](https://portal.azure.com) 從您想要註冊的 VMM 伺服器。

2. 展開 **Data Services** > **復原服務** 按一下 **Site Recovery 保存庫**。


3. 按一下 [ **新建** > **快速建立**。

4. 在 **名稱**, ，輸入易記名稱來識別保存庫。

5. 在 **區域** 選取保存庫的地理區域。 若要檢查支援的地區，請參閱在上市 [Azure Site Recovery 定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=389880)。

6. 按一下 [ **建立保存庫**。

    ![[建立保存庫]](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CreateVault.png)

檢查狀態列，以確認是否已建立保存庫。 保存庫會被列為 **Active** 在主要復原服務頁面上。

## 步驟 2：產生保存庫註冊金鑰

在保存庫中產生註冊金鑰。 下載 Azure Site Recovery 提供者並將其安裝在 VMM 伺服器之後，您將使用此金鑰在保存庫中註冊 VMM 伺服器。

1. 在 [復原服務]**** 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。 您也可以使用圖示隨時開啟 [快速啟動]。

    ![快速啟動圖示](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_QuickStartIcon.png)

2. 在下拉式清單中，選取 **兩個內部部署 HYPER-V 站台間**。
3. 在 **準備 VMM 伺服器**, ，按一下 [ **產生註冊金鑰檔**。 該金鑰檔案會自動產生，並在產生後會維持 5 天有效。 如果您沒有從 VMM 伺服器存取 Azure 入口網站，您必須將這個檔案複製到伺服器。

    ![註冊金鑰](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_E2ERegisterKey.png)

## 步驟 3：安裝 Azure Site Recovery 提供者

4. 在 *快速入門* 頁面上，於 **準備 VMM 伺服器**, ，按一下 [ *下載 Microsoft Azure Site Recovery Provider 的 VMM 伺服器上安裝* 以取得最新版的提供者安裝檔案。

2. 在來源 VMM 伺服器上執行此檔案。 如果 VMM 部署在叢集中，且您是第一次安裝提供者，請將其安裝在作用中節點上，並完成安裝以在保存庫中註冊 VMM 伺服器。 然後在其他節點上安裝提供者。 請注意，如果您要升級提供者，您必須在所有節點上升級，因為它們都應該執行相同的提供者版本。


3. 安裝程式沒有少數 **必要條件檢查** ，並要求停止 VMM 服務以開始安裝 「 提供者的權限。 當安裝完成之後，VMM 服務將會自動重新啟動。 如果您安裝在 VMM 叢集上，您會收到停止叢集角色的提示。

4. 在 **Microsoft Update** 您可以選擇進行更新。 啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者更新。

    ![Microsoft Update](./media/site-recovery-vmm-to-vmm/VMMASRInstallMUScreen.png)


1.  安裝位置設為 **<SystemDrive>files\microsoft System Center 2012 R2\Virtual Machine Manager\bin**。 按一下 [安裝] 按鈕，開始安裝提供者。
    ![InstallLocation](./media/site-recovery-vmm-to-vmm/VMMASRInstallLocationScreen.png)



1. 提供者安裝之後，請按一下 [註冊] 按鈕，在保存庫中註冊伺服器。
    ![InstallComplete](./media/site-recovery-vmm-to-vmm/VMMASRInstallComplete.png)

5. 在 **網際網路連線** 指定 VMM 伺服器上執行的提供者連線到網際網路的方式。 選取 *使用預設系統 proxy 設定* 使用在伺服器上設定的預設網際網路連線設定。

    ![網際網路設定](./media/site-recovery-vmm-to-vmm/VMMASRRegisterProxyDetailsScreen.png)
    - 如果您想要使用自訂 proxy，您應該在安裝提供者之前進行設定。 當您進行自訂 proxy 設定時，會執行一項測試以檢查 proxy 連線。
    - 如果您使用自訂 proxy，或者您的預設 proxy 需要驗證，您必須輸入 proxy 詳細資料，包含 proxy 位址和連接埠。
    - 下列 URL 應可從 VMM 伺服器和 Hyper-V 主機存取
        - *.hypervrecoverymanager.windowsazure.com
        - *.accesscontrol.windows.net
        - *.backup.windowsazure.com
        - *.blob.core.windows.net
        - *.store.core.windows.net
    - 允許中所述的 IP 位址 [Azure Datacenter IP 範圍](http://go.microsoft.com/fwlink/?LinkId=511094) 和 HTTPS (443) 通訊協定。 您必須具有打算使用以及美國西部之 Azure 區域的白名單 IP 範圍。

    - 如果您使用的是自訂 proxy，則會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。 設定 proxy 伺服器，讓此帳戶可以成功進行驗證。 在 VMM 主控台中，可以修改 VMM RunAs 帳戶設定。 若要這樣做，請開啟 [設定] 工作區、展開 [安全性]、按一下 [執行身分帳戶]，然後修改 DRAProxyAccount 的密碼。 您必須重新啟動 VMM 服務，這項設定才會生效。

6. 在 **註冊金鑰**, ，選取您從 Azure Site Recovery 下載並複製到 VMM 伺服器。
7. 在 **保存庫名稱**, ，確認保存庫中註冊伺服器的名稱。 按一下 [ *下一步*。


    ![Server registration](./media/site-recovery-vmm-to-vmm/VMMASRRegisterVaultCreds.png)

9. 這項設定只適用於 Azure 案例的 VMM，如果您是 VMM 對 VMM 唯一使用者，則可以忽略這個畫面。

    ![伺服器註冊](./media/site-recovery-vmm-to-vmm/VMMASRRegisterEncryptionScreen.png)

8. 在 **伺服器名稱**, ，指定用來識別保存庫中的 VMM 伺服器的易記名稱。 在叢集設定中，指定 VMM 叢集角色名稱。

8. 在 **初始雲端中繼資料** 同步選取您是否想要在 VMM 伺服器上的所有雲端的中繼資料與保存庫同步。 這個動作只需要在每個伺服器上進行一次。 如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。
    ![伺服器註冊](./media/site-recovery-vmm-to-vmm/VMMASRRegisterFriendlyName.png)


8. 按一下 [ *下一步* 完成程序。 註冊後，Azure Site Recovery 即可從 VMM 伺服器擷取中繼資料。 伺服器會顯示在  *VMM 伺服器* 索引標籤上 **伺服器** 保存庫中的頁面。

>[AZURE.NOTE] Azure Site Recovery 提供者也可以使用下列命令列來安裝。 這個方法可以用來在適用於 Windows Server 2012 R2 的伺服器核心上安裝提供者

1. 將提供者安裝檔案和註冊金鑰下載至資料夾，例如 C:\ASR
1. 停止 System Center Virtual Machine Manager 服務
1. 擷取提供者安裝程式，藉由執行下列命令，從命令提示字元中使用 **管理員** 權限

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
1. 執行下列命令來安裝提供者

        C:\ASR> setupdr.exe /i
1. 執行下列命令來登錄提供者

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

    
#### 命令列安裝參數清單

 - **/ 認證** ︰ 指定註冊金鑰檔所在的位置的必要參數  
 - **/Friendlyname** ︰ 必要參數，會出現在 Azure Site Recovery 入口網站中的 HYPER-V 主機伺服器的名稱。
 - **/ EncryptionEnabled** ︰ 選擇性的參數，您需要使用只能在 VMM 至 Azure 案例中，如果您需要的虛擬機器在 Azure 中的靜止的加密。 請確認您提供的檔案名稱具有 **.pfx** 延伸模組。
 - **/proxyAddress** ︰ 選擇性參數，指定 proxy 伺服器的位址。
 - **/proxyport** ︰ 指定的連接埠的 proxy 伺服器的選擇性參數。
 - **/proxyUsername** ︰ 指定的 Proxy 使用者名稱 （如果 proxy 需要驗證） 的選擇性參數。
 - **/proxyPassword** ︰ 指定的密碼來驗證 proxy 伺服器 （如果 proxy 需要驗證） 的選擇性參數。  

## 步驟 4：設定雲端保護設定

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。 如果您啟用此選項 **雲端資料與保存庫同步** 當您安裝提供者 VMM 伺服器上的所有雲端都會出現在 **受保護項目** 保存庫中的索引標籤。 如果未指定您可以在 Azure Site recovery 同步處理特定定域機組 **一般** VMM 主控台中雲端屬性頁面] 索引標籤。

![發佈的雲端](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudsList.png)

1. 在 [快速入門] 頁面中，按一下 [ **設定 VMM 雲端的保護**。
2. 在 **VMM 定域機組** 索引標籤上，選取您想要設定，並移至雲端 **組態** ] 索引標籤。
3. 在 [目標]**** 中，選取 [VMM]****。
4. 在 [目標位置]**** 中，選取網站上負責對您要用於復原之雲端進行管理的 VMM 伺服器。
4. 在 [目標雲端]**** 中，選取要在來源雲端中用於虛擬機器容錯移轉的目標雲端。 請注意：
    - 建議您，選取的目標雲端要符合所要保護之虛擬機器的復原需求。
    - 雲端只能當成單一雲端組中的主要雲端或目標雲端。
6. 在 **複製頻率** 頻率指定資料應該在來源和目標位置之間同步處理。 請注意，這個設定只有在 Hyper-V 主機執行 Windows Server 2012 R2 時才有重要性。 對於其他伺服器，使用預設設定的 5 分鐘即可。
7. 在 **其他復原點** 指定您要建立其他復原點。預設為零值表示主要的虛擬機器只有最新復原點會儲存在複本主機伺服器上。 請注意，啟用多個復原點需要額外的儲存體給儲存在每個復原點的快照集。 根據預設，每個小時都會建立復原點，所以每個復原點都包含一小時有價值的資料。 您在 VMM 主控台中指派給虛擬機器的復原點值不得低於您在 Azure Site Recovery 主控台中所指派的值。
8. 在 **應用程式一致快照的頻率** 指定建立應用程式一致快照的頻率。 Hyper-V 使用兩種類型的快照，一個是標準快照，提供整個虛擬機器的增量快照，另一個是應用程式一致快照，會建立虛擬機器內應用程式資料的時間點快照。 應用程式一致快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。 請注意，如果您啟用應用程式一致快照，它會影響在來源虛擬機器上執行的應用程式效能。 確認您設定的值低於您設定的其他復原點數目。

    ![進行保護設定](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudSettings.png)

9. 在 **資料傳輸壓縮**, ，指定是否應該壓縮傳輸的複寫的資料。
10. 在 **驗證**, ，指定如何驗證主要和復原 HYPER-V 主機伺服器之間的流量。 除非您已設定可用的 Kerberos 環境，否則請選取 HTTPS。 Azure Site Recovery 會自動設定用於 HTTPS 驗證的憑證。 不需要進行任何手動設定。 如果您確實已選取 Kerberos，Kerberos 票證將會用於主機伺服器的相互驗證。 根據預設，連接埠 8083 和 8084 (用於憑證) 將在 Hyper-V 主機伺服器上的 Windows 防火牆中開啟。 請注意，這項設定只有在 Hyper-V 主機伺服器是執行於 Windows Server 2012 R2 時才有重要性。
11. 在 **連接埠**,，修改來源和目標主機電腦接聽複寫流量的連接埠號碼。 例如，如果您想要將服務品質 (QoS) 網路頻寬節流套用於複寫流量，您可能會修改設定。 檢查任何其他應用程式都沒有使用連接埠，且連接埠已在防火牆設定中開啟。
12. 在 **複寫方法**, ，指定如何從來源到目標位置的資料初始複寫處理，一般複寫開始之前。
    - **透過網路**— 透過網路複製資料可能既費時又需要大量資源。 如果雲端中的虛擬機器使用相當小的虛擬硬碟，而且主要網站透過寬頻連線來連接次要網站，則建議您使用這個選項。 您可以指定立即開始複製，或是選取開始複製的時間。 如果您使用網路複寫，建議您排在離峰時段進行。
    - **離線**— 這個方法指定，會使用外部媒體執行初始複寫。 如果您想要避免影響網路效能，或者複寫目的地位於很遙遠的地方，就適合使用這個方法。 若要使用這個方法，您需指定來源雲端上的匯出位置，以及目標雲端上的匯入位置。 對虛擬機器啟用保護時，虛擬硬碟會複製到指定的匯出位置。 您需將它送到目標站台，再將它複製到匯入位置。 系統會將匯入的資訊複製到複本虛擬機器。 如需離線複寫先決條件的完整清單，請參閱《部署指南》中的<a href="http://go.microsoft.com/fwlink/?LinkId=323469">步驟 3：設定 VMM 雲端的保護設定</a>。
13. 選取 **刪除複本虛擬機器** 來指定應該刪除複本虛擬機器，是否您停止保護虛擬機器選取 **刪除虛擬機器的保護** 雲端屬性的虛擬機器] 索引標籤上的選項。 如果啟用這項設定，當您停用保護時，便會從 Azure Site Recovery 中移除虛擬機器、在 VMM 主控台中移除虛擬機器的 Site Recovery 設定，並刪除複本。
    ![設定保護設定](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudSettingsRep.png)

<p>儲存設定之後，即會建立一個工作，並且可在 [工作]**** 索引標籤上進行監視。 將會對 VMM 雲端中的所有 Hyper-V 主機伺服器設定複寫。 上可修改雲端設定 **設定** ] 索引標籤。 如果您要修改目標位置或目標雲端，則必須移除雲端組態，然後重新設定雲端。</p>

### 準備進行離線初始複寫

您必須執行下列動作，以準備進行初始複寫離線：

- 在來源伺服器上，您會指定要從中匯出資料的路徑位置。 在匯出路徑上指派 NTFS 和共用權限的完整控制權給 VMM 服務。 在目標伺服器上，您會指定要從中匯入資料的路徑位置。 在這個匯入路徑上指派相同的權限。
- 如果共用匯入或匯出路徑，請在共用所在的遠端桌面上，指派系統管理員、進階使用者、列印操作員或伺服器操作員群組成員資格給 VMM 伺服器帳戶。
- 如果您使用任何執行身分帳戶加入主機，請在匯入和匯出路徑上指派讀取和寫入權限給 VMM 中的執行身分帳戶。
- 匯入和匯出共用不得位於任何做為 Hyper-V 主機伺服器的電腦，因為 Hyper-V 不支援回送設定。
- 在 Active Directory 的每部包含您想要保護之虛擬機器的 Hyper-V 主機伺服器上，啟用與設定限制委派，以信任匯入與匯出路徑所在的遠端電腦，如下所示：
    1. 在網域控制站上開啟 **Active Directory 使用者和電腦**。
    2. 在主控台樹狀目錄中按一下 [ **DomainName** > **電腦**。
    3. 以滑鼠右鍵按一下 HYPER-V 主機伺服器名稱 > **屬性**。
    4. 在 **委派** 索引標籤按一下 T**信任這台電腦所委派的指定服務台，但**。
    5. 按一下 [ **使用任何驗證通訊協定**。
    6. 按一下 [ **新增** > **使用者和電腦**。
    7. 輸入裝載匯出路徑的電腦名稱 > **確定**。從可用服務清單，請按住 CTRL 鍵，然後按一下 **cifs** > **確定**。 為裝載匯入路徑的電腦名稱重複動作。 視需要為其他 Hyper-V 主機伺服器重複動作。

## 步驟 5：設定網路對應
1. 在 [快速入門] 頁面中，按一下 [ **對應網路**。
2. 選取您想要從其中對應網路的來源 VMM 伺服器，然後選取對應網路的目標 VMM 伺服器。 隨即會顯示來源網路的清單和與其相關聯的目標網路。 目前尚未對應的網路會顯示空白值。
3. 中選取網路 **來源上的網路** > **對應**。 服務會偵測目標伺服器上的 VM 網路並加以顯示。 按一下來源和目標網路名稱旁邊的資訊圖示，以檢視每個網路的子網路。

    ![設定網路對應](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_NetworkMapping1.png)

4. 在對話方塊中，從目標 VMM 伺服器選取其中一個 VM 網路。

    ![選取目標網路](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_NetworkMapping2.png)

5. 當您選曲目標網路時，隨即會顯示使用來源網路的受保護雲端。 同時也會顯示與用於保護之雲端相關聯的可用目標網路。 建議您選取所有用於保護之雲端都可用的目標網路。 或者，您也可以移至 VMM 伺服器並修改雲端屬性，以新增想要選擇的 vm 網路所對應的邏輯網路。
6. 按一下打勾記號完成對應程序。 隨即有個工作啟動來追蹤對應程序。 您可以檢視上 **工作** ] 索引標籤。


## 步驟 6：設定儲存體對應
根據預設，當您將來源 Hyper-V 主機伺服器上的虛擬機器複寫至目標 Hyper-V 主機伺服器時，複寫的資料會儲存在為 Hyper-V 管理員中之目標 Hyper-V 主機所指定的預設位置。 如果要進一步控制複寫資料的儲存位置，您可以用以下方式設定儲存體對應：

- 在來源和目標 VMM 伺服器上定義儲存體分類。 如需指示，請參閱 [如何在 VMM 中建立儲存體分類](http://go.microsoft.com/fwlink/?LinkId=400937)。 分類必須可用於來源與目標雲端中的 Hyper-V 主機伺服器。 分類不需要具有相同類型的儲存體。 例如，您可以將包含 SMB 共用的來源分類對應至包含 CSV 的目標分類。
- 設定分類之後，您就可以建立對應。
1. 在 **快速入門** 頁面 > **對應儲存體**。
1. 按一下 [ **儲存體** ] 索引標籤 > **對應儲存體分類**。
1. 在 **對應儲存體分類** ] 索引標籤上，選取分類，在來源和目標 VMM 伺服器。 儲存您的設定。

    ![選取目標網路](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_StorageMapping1.png)


## 步驟 7：啟用虛擬機器保護
正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。

1. 在 **虛擬機器** 虛擬機器位於定域機組中索引標籤中，按一下 [ **啟用保護** > **新增虛擬機器**。
2. 從雲端中所有虛擬機器的清單，選取您要保護的虛擬機器。


![啟用虛擬機器保護](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_EnableProtectionVM.png)

追蹤在 「 啟用保護動作的進度 **工作** 索引標籤上，包括初始複寫。 執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。 啟用保護並複寫虛擬機器之後，您將能夠在 Azure 中檢視這些虛擬機器。

請注意，您也可以在 VMM 主控台中啟用虛擬機器的保護。 在虛擬機器屬性中的 [Azure Site Recovery] 索引標籤的工具列上按一下 [啟用保護]。


![虛擬機器保護工作](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_VMJobs.png)

### 建立現有的虛擬機器
如果您在使用 Hyper-V 複本複寫的 VMM 中具有現有的虛擬機器，您必須以下列方式為 Azure Site Recovery 保護建立虛擬機器：
1. 請確認您有主要和次要雲端。 請確認裝載現有虛擬機器的 Hyper-V 伺服器位於主要雲端中，且裝載複本虛擬機器的 Hyper-V 伺服器位於次要雲端中。 請確認您已為雲端進行保護設定。 這些設定應符合目前為 Hyper-V 複本所做的設定。 否則虛擬機器複寫可能無法如預期般運作。
2. 然後啟用主要虛擬機器的保護。 Azure Site Recovery 和 VMM 可確保偵測到相同的複本主機和虛擬機器，且 Azure Site Recovery 會在雲端設定期間使用這些設定，重複使用及重新建立複寫作業。


## 測試您的部署

若要測試部署，您可以對單一虛擬機器執行測試容錯移轉，或者建立包含多部虛擬機器的復原方案，再對這個方案執行測試容錯移轉。  測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。

### 建立復原計畫

1. 在 **復原計劃** 索引標籤上，按一下 [ **建立復原計畫**。
2. 指定復原方案的名稱，以及來源和目標 VMM 伺服器。 來源伺服器必須有已啟用容錯移轉和復原功能的虛擬機器。 選取 **HYPER-V** ，檢視為 HYPER-V 複寫設定的雲端。

    ![建立復原計畫](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_RP1.png)

3. 在 **選取虛擬機器**, ，選取複寫群組。 所有與複寫群組關聯的虛擬機器，將會被選取並新增至復原方案。 這些虛擬機器將新增到復原計畫的預設群組—群組 1。 您可以視需要新增更多的群組。 請注意，複寫之後，虛擬機器將會根據復原方案群組的順序來啟動。

    ![新增虛擬機器](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_RP2.png)

建立復原計畫之後，它會出現在清單上 **復原計劃** ] 索引標籤。

###執行測試容錯移轉

1. 在 **復原計劃** ] 索引標籤上，選取計畫，然後按一下 [ **測試容錯移轉**。
2. 在 **確認測試容錯移轉** 頁面上，選取 **無**。 請注意，啟用此選項時，容錯移轉複本虛擬機器將不會連線到任何網路。 這將會測試虛擬機器是否依照預期執行容錯移轉，但是不會測試您的複寫網路環境。 看看如何 [執行測試容錯移轉](site-recovery-failover.md#run-a-test-failover) 如需有關如何使用不同的網路功能選項的詳細資訊。

7. 測試虛擬機器將建立在複本虛擬機器所在的相同主機上。 它會新增至複本虛擬機器所在的相同雲端。

### 執行復原計畫
複製之後，複本虛擬機器的 IP 位址可能會與主要虛擬機器的 IP 位址相同。 虛擬機器在啟動後將更新他們正在使用的 DNS 伺服器。 您也可以加入指令碼以更新 DNS 伺服器，以確保及時更新。

#### 要擷取 IP 位址的指令碼
執行此範例指令碼以抓取 IP 位址。

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### 要更新 DNS 的指令碼
執行此範例指令碼來更新 DNS (使用上一個範例指令碼抓取到的 IP 位址)。

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



##<a name="privacy"></a>Site Recovery 的隱私權資訊

本節提供 Microsoft Azure Site Recovery 服務 (「服務」) 的其他隱私權資訊。 若要檢視 Microsoft Azure 服務的隱私權聲明，請參閱
[Microsoft Azure 隱私權聲明](http://go.microsoft.com/fwlink/?LinkId=324899)

**功能：註冊**

- **作用**︰ 向服務註冊伺服器，如此可以保護虛擬機器
- **收集的資訊**︰ 註冊這項服務會收集之後，處理及傳輸管理憑證資訊從 VMM 伺服器已指定提供災害復原 VMM 伺服器上使用 VMM 伺服器、 服務名稱以及虛擬機器的名稱。
- **資訊的用途**:
    - 管理憑證—用來協助識別及驗證已註冊的 VMM 伺服器，以便存取服務。 服務會使用憑證的公開金鑰部分來保護 token，只有已註冊的 VMM 伺服器可以獲得其存取權。 伺服器必須使用此 token 獲得服務功能的存取權。
    - VMM 伺服器的名稱—要識別雲端所在之適當 VMM 伺服器並與其通訊時，VMM 伺服器名稱是必要項目。
    - VMM 伺服器中的雲端名稱—使用如下所述之服務雲端配對/取消配對功能時，雲端名稱為必要項目。 當您決定配對您在主要資料中心的雲端與另一個在復原資料中心的雲端時，復原資料中心的所有雲端名稱都會出現。

- **選擇**︰ 這項資訊是服務註冊程序中不可或缺的一部分，因為它可協助您和服務識別您要提供 Azure Site Recovery 保護，也可以識別正確註冊的 VMM 伺服器的 VMM 伺服器。 如果您不想將此資訊傳送給服務，請勿使用此服務。 如果您已註冊您的伺服器，且稍後想要取消註冊，您可以從服務入口網站 (也就是 Azure 入口網站) 刪除 VMM 伺服器資訊，即可完成動作。

**功能：啟用 Azure Site Recovery 保護**

- **作用**: VMM 伺服器上安裝的 Azure Site Recovery Provider 是用來與服務通訊的管道。 提供者是裝載在 VMM 程序中的動態連結程式庫 (DLL)。 安裝提供者之後，“Datacenter Recovery” 功能會在 VMM 系統管理員主控台中啟用。 雲端中所有新的或現有的虛擬機器都可以啟用稱為 “Datacenter Recovery” 的屬性以協助保護虛擬機器。 一旦設定此屬性之後，提供者會將虛擬機器的名稱和識別碼傳送至服務。 虛擬保護是由 Windows Server 2012 或 Windows Server 2012 R2 Hyper-V 複寫技術啟用。 虛擬機器資料會從一個 Hyper-V 主機複寫至另一個 (通常位於不同的「復原」資料中心)。

- **收集的資訊**︰ 服務會收集、 處理及傳輸虛擬機器，其中包含名稱、 識別碼、 虛擬網路，雲端名稱的中繼資料
的名稱。

- **資訊的用途**︰ 服務會使用上述資訊填入服務入口網站上的虛擬機器資訊。

- **選擇**︰ 這是服務不可或缺的一部分，而且無法關閉。 如果您不想傳送這項資訊至服務，請勿啟用任何虛擬機器的 Azure Site Recovery 保護。 請注意，所有由提供者傳送給服務的資料都是透過 HTTPS 傳送。

**功能：復原計畫**

- **作用**︰ 這項功能可協助您建立 「 復原 」 資料中心的協調計畫。 您可以定義虛擬機器或虛擬機器群組應在復原站台啟動的順序。 您也可以在每個虛擬機器復原時指定任何要執行的自動化指令碼，或任何要採取的手動動作。 容錯移轉 (下一節所述) 通常會在協調復原的復原計畫層級觸發。

- **收集的資訊**︰ 服務會收集、 處理程序和傳輸復原計畫，包括虛擬機器中繼資料，以及任何自動化指令碼和手動動作備註的中繼資料的中繼資料。

- **資訊的用途**︰ 上述中繼資料用來建置您的服務入口網站中的復原計畫。

- **選擇**︰ 這是服務不可或缺的一部分，而且無法關閉。 如果您不想將此資訊傳送給服務，請勿在此服務中建置復原計畫。

**功能：網路對應**

- **作用**︰ 這項功能可讓您對應至復原資料中心的網路資訊從主要資料中心。 在復原站台上復原虛擬機器時，此對應可協助建立其網路連線。

- **收集的資訊**︰ 網路對應功能的一部分，服務會收集、 處理及傳輸每個站台 （主要與資料中心） 之邏輯網路的中繼資料。

- **資訊的用途**︰ 服務會使用中繼資料來填入服務入口網站，您可以將對應的網路資訊。

- **選擇**︰ 這是服務不可或缺的一部分，而且無法關閉。 如果您不想將此資訊傳送給服務，請勿使用網路對應功能。

**功能：容錯移轉 - 已規畫、未規劃、測試**

- **作用**︰ 這項功能有助於從一個 VMM 管理的資料中心的虛擬機器的容錯移轉至另一個 VMM 管理的資料中心。 容錯移轉動作會在其服務入口網站上由使用者觸發。 容錯移轉的可能原因包括 unplalled 事件 (例如自然災害 0 案例；規劃的事件 (例如資料中心負載平衡)，測試容錯移轉 (例如復原計畫排練)。

VMM 伺服器上的提供者會收到來自服務的事件通知，並在 Hyper-V 主機上透過 VMM 介面執行容錯移轉動作。 虛擬機器從一部 Hyper-V 主機實際容錯移轉至另一部 (通常在不同的「復原」資料中心中執行) 的動作會由 Windows Server 2012 或 Windows Server 2012 R2 Hyper-V 複寫技術處理。 完成容錯移轉之後，安裝在「復原」資料中心之 VMM 伺服器上的提供者會傳送成功資訊給服務。

- **收集的資訊**︰ 服務會使用上述資訊填入服務入口網站上的容錯移轉動作資訊的狀態。

- **資訊的用途**︰ 服務會使用上述資訊，如下所示 ︰

    - 管理憑證—用來協助識別及驗證已註冊的 VMM 伺服器，以便存取服務。 服務會使用憑證的公開金鑰部分來保護 token，只有已註冊的 VMM 伺服器可以獲得其存取權。 伺服器必須使用此 token 獲得服務功能的存取權。
    - VMM 伺服器的名稱—要識別雲端所在之適當 VMM 伺服器並與其通訊時，VMM 伺服器名稱是必要項目。
    - VMM 伺服器中的雲端名稱—使用如下所述之服務雲端配對/取消配對功能時，雲端名稱為必要項目。 當您決定配對您在主要資料中心的雲端與另一個在復原資料中心的雲端時，復原資料中心的所有雲端名稱都會出現。

- **選擇**︰ 這是服務不可或缺的一部分，而且無法關閉。 如果您不想將此資訊傳送給服務，請勿使用此服務。


