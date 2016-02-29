<properties
    pageTitle="使用 Powershell 建立並上傳 Windows Server VHD | Microsoft Azure"
    description="了解如何建立及上傳 Windows Server 的虛擬硬碟 (VHD) 使用傳統部署模型和 Azure Powershell。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/10/2015"
    ms.author="cynthn"/>

# 建立並上傳 Windows Server VHD 到 Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本文說明如何使用作業系統上傳虛擬硬碟 (VHD)，以便用它做為映像，根據該映像建立虛擬機器。 如需有關磁碟和 Microsoft Azure 中的 Vhd 的詳細資訊，請參閱 [關於磁碟和虛擬機器的 Vhd](virtual-machines-disks-vhds.md)。



## 先決條件

本文假設您已經：

1. **Azure 訂用帳戶** -如果您沒有帳戶，您可以 [免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F): 取得點數可用來試用付費 Azure 服務，並甚至用後可保留帳戶，並使用免費的 Azure 服務，例如網站。 除非您明確變更您的設定且同意付費，否則我們將不會從您的信用卡收取任何費用。 您也可以 [啟用 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): 您的 MSDN 訂閱提供您額度，您可以使用 Azure 付費服務的每個月。

2. **Microsoft Azure PowerShell** -您已安裝且設定為使用您的訂閱 Microsoft Azure PowerShell 模組。 若要下載此模組，請參閱 [Microsoft Azure 下載](http://azure.microsoft.com/downloads/)。 若要安裝和設定模組的教學課程 [這裡](../powershell-install-configure.md)。 您將使用 [Add-azurevhd](http://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet 來上傳 VHD。

3. **支援 Windows 作業系統並儲存在.vhd 檔案，並連接至虛擬機器** -有多項工具可用來建立.vhd 檔案。 例如，您可以使用 Hyper-V 建立虛擬機器，並安裝作業系統。 如需指示，請參閱 [安裝 HYPER-V 角色和設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。 如需作業系統的詳細資訊，請參閱 [Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](http://go.microsoft.com/fwlink/p/?LinkId=393550)。

> [AZURE.IMPORTANT] Microsoft Azure 不支援 VHDX 格式。 您可以將磁碟轉換為 VHD 格式使用 HYPER-V 管理員或 [CONVERT-VHD cmdlet](http://technet.microsoft.com/library/hh848454.aspx)。 如需詳細資訊，請參閱此 [blogpost](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)。

## 步驟 1：準備 VHD 

將 VHD 上傳至 Azure 之前，必須使用 Sysprep 工具來一般化。 這要準備 VHD 以做為映像。 如需有關 Sysprep 的詳細資訊，請參閱 [如何使用 Sysprep: 簡介](http://technet.microsoft.com/library/bb457073.aspx)。

從安裝作業系統的虛擬機器，完成下列程序：

1. 登入作業系統。

2. 以系統管理員身分開啟 [命令提示字元] 視窗。 切換至目錄 **%windir%\system32\sysprep**, ，然後執行 `sysprep.exe`。

    ![開啟 [命令提示字元] 視窗](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.   **系統準備工具** ] 對話方塊隨即出現。

    ![啟動 Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  在 **系統準備工具**, ，請選取 **輸入系統全新體驗 (OOBE)** 並確定 **一般化** 已核取。

5.  在 **關機選項**, ，請選取 **關機**。

6.  按一下 [ **確定**。

## 步驟 2：從您的 Azure 儲存體帳戶建立或取得資訊

您需要 Azure 中的儲存體帳戶，讓您有空間可上傳 .vhd 檔案。 此步驟說明如何建立帳戶，或從現有的帳戶取得您需要的資訊。

### 選項 1：建立儲存體帳戶

1. 登入 Azure 傳統入口網站。

2. 在命令列中，按一下 [ **新增**。

3. 按一下 [ **Data Services** > **儲存體** > **快速建立**。

    ![快速建立儲存體帳戶](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. 依照下列方式填入欄位：

 - 在 **URL**, ，輸入要在 URL 中使用的儲存體帳戶的子網域名稱。 此項目可以包含 3 至 24 個小寫字母與數字。 此名稱會成為 URL 的主機名稱，您將其用來存取訂用帳戶的 blob、佇列或資料表資源。
 - 選擇 **位置或同質群組** 儲存體帳戶。 同質群組可讓您將雲端服務和儲存體放在相同的資料中心。
 - 決定是否要使用 **地理區域複寫** 的儲存體帳戶。 依預設會開啟異地複寫。 此選項可讓您免費將資料複寫至次要位置，使您在主要位置發生重大錯誤時，可將儲存體容錯移轉至該位置。 次要位置會自動指派，且無法變更。 如果您因為法律規定或組織原則而需要更充分掌控您以雲端為基礎的儲存體所在的位置，您可以關閉地理複寫。 但是，如果您後續又開啟異地複寫，在您將現有的資料複寫至次要位置時，將會產生一次性的資料傳輸費用。 不含異地複寫的儲存服務會有相對的折扣。 如需詳細資訊，請參閱 [建立、 管理或刪除儲存體帳戶](../storage-create-storage-account/#replication-options)。

      ![輸入儲存體帳戶詳細資料](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

5. 按一下 [ **建立儲存體帳戶**。 帳戶現在會出現在 **儲存體**。

    ![已成功建立儲存體帳戶](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. 接下來，為您上傳的 VHD 建立容器。 按一下 [儲存體帳戶名稱，然後按一下 **容器**。

    ![儲存體帳戶詳細資訊](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. 按一下 [ **建立容器**。

    ![儲存體帳戶詳細資訊](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. 型別 a **名稱** 容器和選取 **存取** 原則。

    ![容器名稱](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

    > [AZURE.NOTE] 根據預設，容器屬私人性質，只能由帳戶擁有者可以存取。 若要讓容器，但不是容器屬性和中繼資料中的 blob 的公用讀取權限，請使用 **公用 Blob** 選項。 若要允許對容器及 blob 的完整公用讀取權限，請使用 **公用容器** 選項。

### 選項 2：取得儲存體帳戶資訊

1.  登入 Azure 傳統入口網站。

2.  從 [瀏覽] 窗格中，按一下 [ **儲存體**。

3.  按一下 [儲存體帳戶的名稱，然後按一下 **儀表板**。

4.  儀表板中，在 **服務**, ，將滑鼠停留在 Blob URL，按一下 [複製 URL，然後貼上並將它儲存至剪貼簿圖示。 您將在建置命令以上傳 VHD 時使用它。

## 步驟 3：從 Azure PowerShell 連接至您的訂用帳戶

您必須先在電腦與 Azure 中的訂用帳戶之間建立安全連線，才能上傳 .vhd 檔案。 您可以使用 Microsoft Azure Active Directory 方法或憑證方法來達到此目的。

> [AZURE.TIP] 若要開始使用 Azure PowerShell，請參閱 [如何安裝和設定 Microsoft Azure PowerShell](../install-configure-powershell.md)。 如需一般資訊，請參閱 [開始使用 Microsoft Azure Cmdlet。](https://msdn.microsoft.com/library/azure/jj554332.aspx)

### 選項 1：使用 Microsoft Azure AD

1. 開啟 Azure PowerShell 主控台。

2. 輸入：  
       `Add-AzureAccount`

3.  在登入視窗中，輸入工作或學校帳戶的使用者名稱和密碼。

4. Azure 會驗證並儲存認證資訊，然後關閉視窗。

### 選項 2：使用憑證

1. 開啟 Azure PowerShell 主控台。

2.  輸入：
          `Get-AzurePublishSettingsFile`。

3. 隨即開啟瀏覽器視窗，並提示您下載 .publishsettings 檔案。 它包含您 Microsoft Azure 訂閱的資訊和憑證。

    ![瀏覽器下載頁面](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. 儲存 .publishsettings 檔案。

4. 輸入：
       `Import-AzurePublishSettingsFile <PathToFile>`

    其中 `<PathToFile>` 是 .publishsettings 檔案的完整路徑。

## 步驟 4：上傳 .vhd 檔案

在上傳 .vhd 檔案時，您可以將 .vhd 檔案放在 Blob 儲存體中的任一處。

1. 從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入類似以下的命令：

    `Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

    其中：
    - **BlobStorageURL** 是儲存體帳戶的 URL
    - **YourImagesFolder** 是 blob 儲存體中的容器，您要儲存映像
    - **VHDName** 名稱來識別虛擬硬碟的顯示 Azure 傳統入口網站
    - **PathToVHDFile** 是完整路徑和名稱的.vhd 檔案

    ![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

如需 Add-azurevhd cmdlet 的詳細資訊，請參閱 [Add-azurevhd](http://msdn.microsoft.com/library/dn495173.aspx)。

## 步驟 5：將映像加入至您的自訂映像清單

> [AZURE.TIP] 若要新增映像，而不是 Azure 傳統入口網站使用 Azure PowerShell，使用 **Add-azurevmimage** 指令程式。 例如：

>   `Add-AzureVMImage -ImageName <ImageName> -MediaLocation <VHDLocation> -OS <OSType>`

1. 從 Azure 傳統入口網站，在 **的所有項目**, ，按一下 [ **虛擬機器**。

2. 在虛擬機器] 下按一下 **映像**。

3. 按一下 [ **建立映像**。

    ![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. 在 **從 VHD 建立映像** 視窗:

    - 指定 **名稱**。

    - 指定 **描述**。

    - 在 **VHD URL**, ，按一下資料夾按鈕以開啟 **Browse Cloud Storage** 視窗。 尋找.vhd 檔案，並按一下 **開啟**。

    ![選取 VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

5.  在 **從 VHD 建立映像** 視窗下方 **作業系統系列**, ，選取您的作業系統。 檢查 **我已經與此 VHD 相關聯的虛擬機器上執行 Sysprep** 來確認，您將作業系統一般化，然後按一下 **確定**。

    ![新增映像](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

6. 完成上述步驟之後，當您選擇，會列出新的映像 **映像** ] 索引標籤。

    ![自訂映像](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

    這個新映像現在是可在 **我的映像** 當您建立虛擬機器。 如需指示，請參閱 [如何建立自訂虛擬機器執行 Windows](virtual-machines-windows-create-custom.md)。

    ![從自訂映像建立 VM](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

    > [AZURE.TIP] 如果您收到錯誤，當您嘗試建立 VM、 錯誤訊息 「 VHD https://XXXXX...具有不支援的虛擬大小 YYYY 位元組。 大小必須是整數 (以 MB 為單位)」，這表示您的 VHD 不是整數 MB，且必須是固定大小的 VHD。 請嘗試使用 **Add-azurevmimage** PowerShell 指令程式，而不是 Azure 傳統入口網站中加入影像 (請參閱上面的步驟 5)。 Azure Cmdlet 可確保 VHD 會符合 Azure 需求。

## 後續步驟 ##

建立虛擬機器之後，嘗試建立 SQL Server 虛擬機器。 如需指示，請參閱 [佈建 Microsoft Azure 上的 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)。

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Create a storage account in Azure]: #createstorage
[Step 3: Prepare the connection to Azure]: #prepAzure
[Step 4: Upload the .vhd file]: #upload

