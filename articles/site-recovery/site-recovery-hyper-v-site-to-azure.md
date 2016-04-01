<properties
    pageTitle="使用 Site Recovery 在內部部署 Hyper-V 虛擬機器與 Azure (沒有 VMM) 之間複寫 | Microsoft Azure"
    description="Azure Site Recovery 可將內部部署伺服器上的虛擬機器複寫、容錯移轉，以及將位於內部部署 Hyper-V 伺服器上的虛擬機器復原到 Azure。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="12/10/2015"
    ms.author="raynew"/>


# 使用 Azure Site Recovery 在內部部署 Hyper-V 虛擬機器與 Azure (沒有 VMM) 之間複寫

Azure Site Recovery 可藉由協調複寫、 容錯移轉及復原的虛擬機器和實體伺服器，在一些部署案例中的為您的商務持續性和災害復原 (BCDR) 策略。 [閱讀更多](site-recovery-overview.md) 有關站台復原。

## 概觀

本文說明如何部署 Site Recovery，在執行 Windows Server 2012 R2 的 Hyper-V 主機未在 System Center Virtual Machine Manager (VMM) 雲端中受管理時，複寫 Hyper-V 虛擬機器。 

本文將摘要說明部署的必要條件，可協助您設定複寫設定，並啟用虛擬機器的保護。 最後它會測試容錯移轉，藉此確定一切如預期般運作。 如果您遇到的問題張貼至您 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## 開始之前

開始之前，請確認您已經做好一切準備工作。

### Azure 必要條件

- 您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。 您可以開始 [免費試用版](pricing/free-trial/)。
- 您需要 Azure 儲存體帳戶來儲存複寫的資料。 此帳戶必須啟用異地複寫。 它應該與 Azure Site Recovery 保存庫位於相同的區域，且和同一個訂用帳戶產生關聯。 [深入了解 Azure 儲存體](../storage/storage-introduction.md)。
- 您將需要 Azure 虛擬網路，如此一來，當您從主要網站容錯移轉時，Azure 虛擬機器就會連接至網路。

### Hyper-V 的必要條件

- 在來源內部部署網站中，您將至少要有一部執行已安裝 Hyper-V 角色之 Windows Server 2012 R2 的伺服器。 此伺服器應該：
- 包含一或多部虛擬機器。
- 直接或透過 Proxy 連接到網際網路。
- 執行以 kb 為單位所述的修正程式 [2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977")。

### 虛擬機器先決條件

您想要保護的虛擬機器應該要符合 [虛擬機器必要條件](site-recovery-best-practices.md#virtual-machines)。

### 提供者和代理程式的必要條件

在 Azure Site Recovery 部署期間，您將在每部 Hyper-V 伺服器上，安裝 Azure Site Recovery Provider 和 Azure 復原服務代理程式。 請注意：

- 建議您一律執行最新版本的提供者和代理程式。 這些程式可以在 Site Recovery 入口網站中取得。
- 保存庫中的所有 Hyper-V 伺服器應該有相同版本的提供者和代理程式。
- 伺服器上執行的提供者會透過網際網路連接到 Site Recovery。 您不需要使用 Poxy 就能選擇執行這個動作，方法是使用目前設定於 Hyper-V 伺服器上的 Poxy 設定，或使用您在提供者安裝期間所設定的自訂 Poxy 設定。 您必須確定您想要使用的 Proxy 伺服器可以存取這些 URL 以連接到 Azure：
    - *.hypervrecoverymanager.windowsazure.com
    - *.accesscontrol.windows.net
    - *.backup.windowsazure.com     
    - *.blob.core.windows.net
    - *.store.core.windows.net
    
- 除了允許中所述的 IP 位址 [Azure Datacenter IP 範圍](https://www.microsoft.com/en-us/download/details.aspx?id=41653) 和 HTTPS (443) 通訊協定。 您必須具有打算使用以及美國西部之 Azure 區域的白名單 IP 範圍。


下圖顯示 Site Recovery 為協調流程與複寫所使用的不同通訊通道與連接埠

![B2A 拓樸](./media/site-recovery-hyper-v-site-to-azure/B2ATopology.png)


## 步驟 1：建立保存庫

1. 登入 [管理入口網站](https://portal.azure.com)。


2. 展開 **Data Services** > **復原服務** 按一下 **Site Recovery 保存庫**。


3. 按一下 [ **新建** > **快速建立**。

4. 在 **名稱**, ，輸入易記名稱來識別保存庫。

5. 在 **區域**, ，選取保存庫的地理區域。 若要檢查支援的地區，請參閱在上市 [Azure Site Recovery 定價詳細資料](pricing/details/site-recovery/)。

6. 按一下 [ **建立保存庫**。

    ![新增保存庫](./media/site-recovery-hyper-v-site-to-azure/SR_HvVault.png)

檢查狀態列，以確認是否順利建立保存庫。 保存庫會被列為 **Active** 在主要復原服務頁面上。


## 步驟 2：建立 Hyper-V 站台

1. 在 [復原服務] 頁面中，按一下保存庫以開啟 [快速入門] 頁面。 您也可以使用圖示隨時開啟 [快速啟動]。

    ![快速啟動](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_QuickStartIcon.png)

2. 在下拉式清單中，選取 **內部部署 HYPER-V 站台和 Azure 之間**。

    ![Hyper-V 站台案例](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectScenario.png)

3. 在 **建立 HYPER-V 站台** 按一下 **建立 HYPER-V 站台**。 指定站台名稱並儲存。

    ![Hyper-V 站台](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateSite2.png)


## 步驟 3：安裝提供者和代理程式
在具有您想要保護的 VM 的每部 Hyper-V 伺服器上，安裝提供者和代理程式。

如果您正在 Hyper-V 叢集上進行安裝，請在容錯移轉叢集中的每個節點上執行步驟 5-11 。 在註冊所有節點並啟用保護之後，虛擬機器將會受到保護，即使它們在叢集中的節點間進行移轉也一樣。

1. 在 **準備 HYPER-V 伺服器**, ，按一下 [ **下載註冊金鑰** 檔案。
2. 在 **下載註冊金鑰** 頁面上，按一下 **下載** 站台旁邊。 將金鑰下載到 Hyper-V 伺服器容易存取的安全位置。 該金鑰在產生後會維持 5 天有效。

    ![註冊金鑰](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_DownloadKey2.png)

4. 按一下 [ **下載提供者** 以取得最新版本。
5. 在您想要於保存庫中註冊的每一部 Hyper-V 伺服器上執行該檔案。 該檔案會安裝兩個元件：
    - **Azure Site Recovery Provider**— 處理 HYPER-V 伺服器與 Azure Site Recovery 入口網站之間的協調流程的通訊和。
    - **Azure 復原服務代理程式**— 處理在來源 HYPER-V 伺服器和 Azure 儲存體上執行的虛擬機器之間的資料傳輸。
6. 在 **Microsoft Update** 您可以選擇進行更新。 啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者和代理程式更新。

    ![Microsoft Update](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider1.png)

7. 在 **安裝** 指定您要在 HYPER-V 伺服器上安裝提供者和代理程式。

    ![安裝位置](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider2.png)

8. 安裝完成之後，請繼續設定以在保存庫中註冊伺服器。

    ![安裝完成](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider3.png)


9. 在 **網際網路連線** 頁面上，您指定的提供者連線至 Azure Site Recovery 的方式。 選取 **使用預設系統 proxy 設定** 使用在伺服器上設定的預設網際網路連線設定。 如果未指定值，將使用預設設定。

    ![網際網路設定](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider4.png)

9. 在 **保存庫設定** 頁面上，按一下 **瀏覽** 以選取金鑰檔。 指定 Azure Site Recovery 訂用帳戶、保存庫名稱，以及 Hyper-V 伺服器所屬的 Hyper-V 站台。

    ![伺服器註冊](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectKey.png)


11. 註冊作業會開始在保存庫中註冊伺服器。

    ![伺服器註冊](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider6.png)

11. 註冊完成中繼資料之後由 Azure Site Recovery 伺服器便會抓取來自 HYPER-V，而伺服器會顯示在 **HYPER-V 站台** 索引標籤上 **伺服器** 保存庫中的頁面。

    ![伺服器註冊](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider7.png)


### 從命令列安裝提供者

或者，您可以從命令列安裝 Azure Site Recovery 提供者。 如果您想要在執行 Windows Server Core 2012 R2 的電腦上安裝提供者，您應該使用這個方法。 從命令列執行，如下所示：

1. 將提供者安裝檔案和註冊金鑰下載至資料夾。 例如 C:\ASR。
2. 以系統管理員身分開啟 [命令提示字元] 並輸入：

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

3. 然後執行下列命令以安裝提供者：

        C:\ASR> setupdr.exe /i

4. 執行下列命令以完成註冊：

        CD C:\Program Files\Microsoft Azure Site Recovery Provider
        C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

參數包括：

- **/ 認證**︰ 指定您下載註冊金鑰的位置。
- **/Friendlyname**︰ 指定識別 HYPER-V 主機伺服器的名稱。 這個名稱會出現在入口網站中。
- **/ EncryptionEnabled**︰ 選擇性。 指定是否要在 Azure 中加密複本虛擬機器 (靜態加密)。
- **/proxyAddress**; **/proxyport**; **/proxyUsername**; **/proxyPassword**︰ 選擇性。 如果您想要使用自訂 proxy，或您現有的 proxy 需要驗證，請指定 proxy 參數。


## 步驟 4：建立 Azure 儲存體帳戶 

1. 在 **準備資源** 選取 **建立儲存體帳戶**  來建立 Azure 儲存體帳戶，如果您沒有此帳戶。 此帳戶應啟用異地複寫。 它應該與 Azure Site Recovery 保存庫位於相同的區域，且和同一個訂用帳戶產生關聯。

    ![建立儲存體帳戶](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateResources1.png)

## 步驟 5：建立和設定保護群組

保護群組是虛擬機器的邏輯群組，您想要使用相同的保護設定來保護這類群組。 您只要將保護設定套用至保護群組，這些設定就會套用至您新增至該群組中的所有虛擬機器。

1. 在 **建立和設定保護群組** 按一下 **建立保護群組**。 如果有任何必要條件不符就會發出訊息，而且您可以按一下 **檢視詳細資料** 如需詳細資訊。

2. 在 **保護群組** 索引標籤上，新增保護群組。 指定的名稱、 來源 HYPER-V 站台、 目標 **Azure**, ，您的 Azure Site Recovery 訂用帳戶名稱和 Azure 儲存體帳戶。

    ![保護群組](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroupCreate3.png)


2. 在 **複寫設定** 設定 **複製頻率** 來指定頻率應該在來源和目標之間同步資料差異。 您可以設定為 30 秒、5 分鐘或 15 分鐘。
3. 在 **保留復原點** 指定應該儲存多少個小時復原歷程記錄。
4. 在 **應用程式一致快照的頻率** 您可以指定是否要建立快照，使用磁碟區陰影複製服務 (VSS) 來確保建立快照時，應用程式是處於一致的狀態。 預設不會建立這些快照。 請確定已將此值設定為小於您設定的其他復原點數目。 只有當虛擬機器正在執行 Windows 作業系統時，才支援此功能。
5. 在 **初始複寫開始時間** 指定當保護群組中的虛擬機器的初始複寫傳送到 Azure。

    ![保護群組](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroup4.png)


## 步驟 6：啟用虛擬機器保護


將虛擬機器新增到保護群組，為其啟用保護。

>[AZURE.NOTE] 不支援保護執行 Linux 的靜態 IP 位址和 Vm。 

1. 在 **機器** 的保護群組] 索引標籤，按一下 [新增虛擬機器至保護群組以啟用保護]。
2. 在 **啟用虛擬機器保護** 頁面上，選取您想要保護的虛擬機器。

    ![啟用虛擬機器保護](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_AddVM3.png)

    「啟用保護」工作隨即開始。 您可以追蹤進度 **工作** ] 索引標籤。 執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。
3. 設定保護之後，您可以：

    - 檢視中的虛擬機器 **受保護項目** > **保護群組** > *protectiongroup_name* > **虛擬機器** 您可以向下切入到機器詳細資料中 **屬性** ] 索引標籤。
    - 設定中的虛擬機器的容錯移轉屬性 **受保護項目** > **保護群組** > *protectiongroup_name* > **虛擬機器** *virtual_machine_name* > **設定**。 您可以設定：
        - **名稱**: Azure 中的虛擬機器的名稱。
        - **大小**︰ 容錯移轉的虛擬機器的目標大小。

        ![設定虛擬機器屬性](./media/site-recovery-hyper-v-site-to-azure/VMProperties.png)
    - 設定中的其他虛擬機器設定 *受保護項目** > **保護群組** > *protectiongroup_name* > **虛擬機器** *virtual_machine_name* > **設定**, ，包括 ︰

        - **網路介面卡**︰ 網路介面卡數目取決於所指定目標虛擬機器的大小。 檢查 [虛擬機器大小規格](../virtual-machines/virtual-machines-size-specs.md#size-tables) nic 的虛擬機器大小所支援的數目。


            When you modify the size for a virtual machine and save the settings, the number of network adapter will change when you open **Configure** page the next time. The number of network adapters of target virtual machines is minimum of the number of network adapters on source virtual machine and maximum number of network adapters supported by the size of the virtual machine chosen. It is explained below:


            - If the number of network adapters on the source machine is less than or equal to the number of adapters allowed for the target machine size, then the target will have the same number of adapters as the source.
            - If the number of adapters for the source virtual machine exceeds the number allowed for the target size then the target size maximum will be used.
            - For example if a source machine has two network adapters and the target machine size supports four, the target machine will have two adapters. If the source machine has two adapters but the supported target size only supports one then the target machine will have only one adapter.     
        - **Azure network**: Specify the network to which the virtual machine should fail over. If the virtual machine has multiple network adapters all adapters should connected to the same Azure network.
        - **Subnet** For each network adapter on the virtual machine, select the subnet in the Azure network to which the machine should connect after failover.
        - **Target IP address**: If the network adapter of source virtual machine is configured to use static a IP address then you can specify the IP address for the target virtual machine to ensure that the machine has the same IP address after failover.  If you don't specify an IP address then any available address will be assigned at the time of failover. If you specify an address that's in use then failover will fail.

        ![Configure virtual machine properties](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_VMMultipleNic.png)




## 步驟 7：建立復原計畫

為測試部署，您可以針對單一虛擬機器執行測試容錯移轉，或執行包含一或多部虛擬機器的復原計劃。 [了解詳細](site-recovery-create-recovery-plans.md) 需建立復原計畫。

## 步驟 8：測試部署

有兩種方式可以測試容錯移轉至 Azure。

- **測試容錯移轉不會在 Azure 網路**— 這種類型的測試容錯移轉會檢查該虛擬機器正確地出現在 Azure 中。 在容錯移轉之後，虛擬機器不會連線到任何 Azure 網路。
- **測試容錯移轉與 Azure 網路**— 這種類型的容錯移轉檢查整體複寫環境是否如預期般出現並容錯移轉虛擬機器連接到指定的目標 Azure 網路。 請注意，針對測試容錯移轉，可根據複本虛擬機器的子網路得知測試虛擬機器的子網路。 這和一般的複寫不同，一般複寫的複本虛擬機器子網路是根據來源虛擬機器的子網路得知。

如果您想要執行測試容錯轉移，卻不想指定 Azure 網路，您不需要作任何準備。 

若要以目標 Azure 網路執行測試容錯移轉，您必須建立與您的 Azure 正式作業網路 (當您在 Azure 中建立新網路時的預設行為) 分隔的新的 Azure 網路。 讀取 [執行測試容錯移轉](site-recovery-failover.md#run-a-test-failover) 如需詳細資訊。


若要完整測試您的複寫和網路部署，您必須針對複寫虛擬機器設定基礎結構，以如預期般運作。 其中一個方式是將虛擬機器設定為具有 DNS 的網域控制站，並且使用 Site Recovery 將其複寫至 Azure，以藉由執行測試容錯移轉在測試網路中建立它。  [採取更多有關](site-recovery-active-directory.md#considerations-for-test-failover) 測試 Active Directory 的容錯移轉考量。

執行測試容錯移轉，如下所示：

1. 在 **復原計劃** ] 索引標籤上，選取計畫，然後按一下 [ **測試容錯移轉**。
2. 在 **確認測試容錯移轉** 頁面上，選取 **無** 或特定的 Azure 網路。  請注意，如果您選取 **無** 測試容錯移轉會檢查虛擬機器正確複寫至 Azure，但並不會檢查您的複寫網路組態。

    ![測試容錯移轉](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_TestFailoverNoNetwork.png)

3. 在 **工作** ] 索引標籤，您可以追蹤容錯移轉進度。 您應該可以在 Azure 入口網站中看到該虛擬機器測試複本。 如果您設定從內部部署網路存取虛擬機器，您可以初始化虛擬機器的「遠端桌面」連線。
4. 當容錯移轉到達 **完成測試** 階段時，請按一下 [ **完成測試** 以完成測試容錯移轉。 您可以向下切入至 **工作** 索引標籤，來追蹤容錯移轉進度和狀態，並執行任何所需的動作。
5. 容錯移轉之後您便可以看到 Azure 入口網站中的虛擬機器測試複本。 如果您設定從內部部署網路存取虛擬機器，您可以初始化虛擬機器的「遠端桌面」連線。

    1. 確認虛擬機器成功啟動。
    2. 如果您在容錯移轉之後要使用遠端桌面連接到 Azure 中的虛擬機器，請先在虛擬機器上啟用遠端桌面連線，再執行測試容錯移轉。 您也必須在虛擬機器上新增 RDP 端點。 您可以利用 [Azure 自動化 Runbook](site-recovery-runbook-automation.md) 若要這麼做。
    3. 容錯移轉之後，如果您使用公用 IP 位址，利用 [遠端桌面] 連接到 Azure 中的虛擬機器，請確定您沒有任何網域原則會阻止您使用公用位址連接到虛擬機器。

6. 測試完成之後，請執行下列動作：

    - 按一下 [ **測試容錯移轉已完成**。 清除測試環境，以自動關閉電源及刪除測試虛擬機器。
    - 按一下 [ **備忘稿** 記錄並儲存關於測試容錯移轉相關聯的任何觀察。
7. 當容錯移轉到達 **完成測試** 階段完成驗證，如下所示 ︰
    1. 在 Azure 入口網站中檢視複本虛擬機器。 確認虛擬機器成功啟動。
    2. 如果您設定從內部部署網路存取虛擬機器，您可以初始化虛擬機器的「遠端桌面」連線。
    3. 按一下 [ **完成測試** 來完成它。
    4. 按一下 [ **備忘稿** 記錄並儲存關於測試容錯移轉相關聯的任何觀察。
    5.  按一下 [ **測試容錯移轉已完成**。 清除測試環境，以自動關閉電源及刪除測試虛擬機器。

## 後續步驟

您的部署設定完成並開始執行之後 [深入](site-recovery-failover.md) 有關容錯移轉。


