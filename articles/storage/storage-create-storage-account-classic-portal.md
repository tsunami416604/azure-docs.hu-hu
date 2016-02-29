<properties
    pageTitle="如何建立、管理或刪除儲存體帳戶 | Microsoft Azure"
    description="了解如何在 Azure 傳統入口網站中建立、管理或刪除儲存體帳戶。"
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="12/04/2015" 
    ms.author="robinsh"/>


# 關於 Azure 儲存體帳戶

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## 概觀

Azure 儲存體帳戶可讓您存取 Azure 儲存體中的 Azure Blob、佇列、資料表和檔案服務。 您的儲存體帳戶提供您 Azure 儲存體資料物件的唯一命名空間。 根據預設，您帳戶中的資料只有帳戶擁有者 (也就是您) 可以使用。

儲存體帳戶分為兩種類型：

- 標準儲存體帳戶包含 Blob、資料表、佇列和檔案儲存體。 
- 進階儲存體帳戶目前僅支援 Azure 虛擬機器磁碟。 請參閱 [高階儲存體: Azure 虛擬機器工作負載的高效能儲存體](storage-premium-storage-preview-portal.md) 的高階儲存體的深入概觀。

## 儲存體帳戶計費

我們會根據您的儲存體帳戶對 Azure 儲存體使用量計費。 儲存體成本以四項因素為基礎：儲存體容量、複寫配置、儲存體交易和資料輸出。

- 儲存體容量是指您用於儲存資料的儲存體帳戶配額。 若只是儲存資料，則成本是由您所儲存的資料量和複寫資料的方式來決定。
- 複寫會決定您的資料同時維護了多少複本，以及在哪些位置。
- 交易是指對 Azure 儲存體進行的所有讀取和寫入作業。
- 出口流量是指傳出 Azure 地區的資料。 當您儲存體帳戶中的資料受不同地區中執行的應用程式存取時，不論該應用程式是雲端服務還是其他某類應用程式，您都要負擔出口流量的費用。 (若為 Azure 服務，您可以採取步驟，將資料和服務群組在相同的資料中心，以減少或消除出口流量費用。)  

 [Azure 定價](http://azure.microsoft.com/pricing/details/#storage) 頁面會提供儲存體容量、 複寫和交易的詳細定價資訊。  [資料傳輸定價詳細資料](http://azure.microsoft.com/pricing/details/data-transfers/) 頁面會提供資料輸出的詳細定價資訊。

如需有關儲存體帳戶容量和效能目標的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/azure/dn249410.aspx)。

> [AZURE.NOTE] 當您建立 Azure 虛擬機器時，儲存體帳戶會為您自動建立部署位置中如果您還沒有儲存體帳戶在該位置。 因此，您無須依照下方的步驟為虛擬機器磁碟建立儲存體帳戶。 儲存體帳戶名稱將以虛擬機器名稱為基礎。 請參閱 [Azure 虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/) 如需詳細資訊。

## 建立儲存體帳戶

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。

2. 按一下 [ **新增** 頁面底部的工具列。 選擇 **Data Services** | **儲存體**, ，然後按一下 [ **快速建立**。

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. 在 **URL**, ，輸入您的儲存體帳戶的名稱。

    > [AZURE.NOTE] 儲存體帳戶名稱必須是長度為 3 到 24 個字元之間，而且可能包含數字和小寫字母只。
    >  
    > 儲存體帳戶名稱必須在 Azure 中是獨一無二的。 Azure 傳統入口網站會指出您選取的儲存體帳戶名稱是否已使用。

    請參閱 [儲存體帳戶端點](#storage-account-endpoints) 下面詳細瞭解儲存體帳戶名稱將會用來解析 Azure 儲存體中的物件。

4. 在 **位置/同質群組**, ，選取靠近您或您的客戶儲存體帳戶的位置。 如果您將會從其他 Azure 服務 (例如 Azure 虛擬機器或雲端服務) 存取儲存體帳戶，您可以從清單中選取同質群組，以將相同資料中心內的儲存體帳戶與您用來改善效能和降低成本的其他 Azure 服務分組在一起。

    請注意，您必須在儲存體帳戶建立時選取同質群組。 您無法將現有的帳戶移至同質群組。 如需有關同質群組的詳細資訊，請參閱 [服務與同質群組的共置](#service-co-location-with-an-affinity-group) 下方。

    >[AZURE.IMPORTANT] 若要判斷哪些位置可供您的訂閱，您可以呼叫 [清單提供者](https://msdn.microsoft.com/library/azure/dn790524.aspx) 作業。 清單提供者，從 PowerShell，呼叫 [Get-azurelocation](https://msdn.microsoft.com/library/azure/dn757693.aspx)。 從.NET 使用 [清單](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) ProviderOperationsExtensions 類別的方法。
    >
    >此外，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services) 如需有關哪些服務可用的區域中。


5. 如果您有多個 Azure 訂用帳戶，然後在 **訂閱** 欄位會顯示。 在 **訂閱**, ，輸入您要使用的儲存體帳戶的 Azure 訂閱。

6. 在 **複寫**, ，選取所需的層級的儲存體帳戶的複寫。 建議的複寫選項是 [地理備援] 複寫，這可為您的資料提供最大的耐用性。 如需 Azure 儲存體複寫選項的詳細資訊，請參閱 [Azure 儲存體複寫](storage-redundancy.md)。

6. 按一下 [ **建立儲存體帳戶**。

    建立儲存體帳戶可能需要花費數分鐘的時間。 若要檢查狀態，可以監視位於 Azure 傳統入口網站底部的通知。 建立儲存體帳戶之後，新的儲存體帳戶有 **線上** 狀態和可供使用。

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### 儲存體帳戶端點

每個儲存在 Azure 儲存體中的物件都有一個唯一 URL 位址。 儲存體帳戶名稱會構成該位址的子網域。 子網域和網域名稱，即每個服務的特定組合形成 *端點* 儲存體帳戶。

例如，如果您儲存體帳戶的名稱為 *mystorageaccount*, ，然後儲存體帳戶的預設端點包括:

- Blob 服務: http://*mystorageaccount*。 windows.net

- 表格服務: http://*mystorageaccount*。.table.core.windows.net

- 佇列服務: http://*mystorageaccount*。 queue.core.windows.net

- 檔案服務: http://*mystorageaccount*。 windows.net

您可以檢視的端點中的儲存體儀表板上的儲存體帳戶 [Azure 傳統入口網站](manage.windowsazure.com) 之後已建立的帳戶。

用以存取儲存體帳戶中某物件的 URL，可藉由在端點後附加該物件在儲存體帳戶中的位置來建置。 例如，blob 位址格式可能如下: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

您也可以設定與儲存體帳戶搭配使用的自訂網域名稱。 請參閱 [Azure 儲存體帳戶中設定自訂網域名稱的 blob 資料](storage-custom-domain-name.md) 如需詳細資訊。

### 使用同質群組讓服務位於相同位置

 *同質群組* 是您的 Azure 服務和 Vm 與 Azure 儲存體帳戶地理的群組。 同質群組會將電腦工作負載置於相同的資料中心內或目標使用者對象附近，因而能改善服務效能。 此外，當您儲存體帳戶中的資料是由同一同質群組中的服務存取時，出口流量並不會產生任何費用。

> [AZURE.NOTE]  若要建立同質群組，請開啟 <b>設定</b> 區域 [Azure 傳統入口網站](manage.windowsazure.com), ，按一下 <b>同質群組</b>, ，然後按一下 [ <b>新增同質群組</b> 或 <b>新增</b> ] 按鈕。 您也可以使用 Azure 服務管理 API 建立和管理同質群組。 如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">同質群組的相關作業</a>。

## 檢視、複製和重新產生儲存體存取金鑰

當您建立儲存體帳戶時，Azure 會產生兩個 512 位元的儲存體存取金鑰，作為存取儲存體帳戶時的驗證憑藉。 透過提供這兩個儲存體存取金鑰，Azure 讓您可重新產生金鑰，同時又不需中斷儲存體服務或對該服務的存取。

> [AZURE.NOTE] 我們建議您避免與其他人共用儲存體存取金鑰。 若要允許存取儲存體資源但不要公開您的存取金鑰，您可以使用 *共用的存取簽章*。 共用存取簽章可在您定義的間隔期間內，使用您所指定的權限，來存取帳戶中的資源。 請參閱 [共用的存取簽章教學課程](storage-dotnet-shared-access-signature-part-1.md) 如需詳細資訊。

在 [Azure 傳統入口網站](manage.windowsazure.com), ，使用 **管理金鑰** 儀表板上或 **儲存體** 頁面，即可檢視、 複製和重新產生用來存取 Blob、 資料表和佇列服務的儲存體存取金鑰。

### 複製儲存體存取金鑰  

您可以使用 **管理金鑰** 複製要在連接字串中使用的儲存體存取金鑰。 連接字串需有要在驗證中使用的儲存體帳戶名稱和金鑰。 設定連接字串以存取 Azure 儲存體服務的相關資訊，請參閱 [設定 Azure 連接字串](http://msdn.microsoft.com/library/azure/ee758697.aspx)。

1. 在 [Azure 傳統入口網站](manage.windowsazure.com), ，按一下 [ **儲存體**, ，然後按一下以開啟儀表板的儲存體帳戶名稱。

2. 按一下 [ **管理金鑰**。

    **管理存取金鑰** 隨即開啟。

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. 若要複製儲存體存取金鑰，請選取金鑰文字。 然後按一下滑鼠右鍵，然後按一下 [ **複製**。

### 重新產生儲存體存取金鑰
您應定期變更儲存體帳戶的存取金鑰，保護儲存體連線的安全。 指派了兩個存取金鑰，因此您可以在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持儲存體帳戶連線。

> [AZURE.WARNING] 重新產生存取金鑰會影響虛擬機器、 媒體服務，以及任何相依於儲存體帳戶的應用程式。 所有使用存取金鑰來存取儲存體帳戶的用戶端，都必須更新為使用新的金鑰。

**虛擬機器** -如果您的儲存體帳戶包含任何正在執行的虛擬機器，您必須在重新產生存取金鑰之後，重新部署的所有虛擬機器。 若要避免重新部署，請在重新產生存取金鑰之前關閉虛擬機器。

**媒體服務** -如果您有媒體服務相依於儲存體帳戶，您必須重新同步處理存取金鑰與媒體服務之後重新產生金鑰。

**應用程式** -如果您有 web 應用程式或使用儲存體帳戶的雲端服務，就會失去連線重新產生金鑰，除非您變換金鑰。 程序如下：

1. 更新應用程式程式碼中的連接字串，以參考儲存體帳戶的次要存取金鑰。

2. 重新產生儲存體帳戶的主要存取金鑰。 在 [Azure 傳統入口網站](manage.windowsazure.com), ，從儀表板或 **設定** 頁面上，按一下 **管理金鑰**。 按一下 [ **重新產生** 主要存取金鑰，然後按一下 [下 **是** 來確認您想要產生新的金鑰。

3. 更新程式碼中的連接字串，以參考新的主要存取金鑰。

4. 重新產生次要存取金鑰。

## 刪除儲存體帳戶

若要移除您不再使用的儲存體帳戶，請使用 **刪除** 儀表板上或 **設定** 頁面。 **刪除** 刪除整個儲存體帳戶，包括帳戶中的所有 blob、 資料表和佇列。

> [AZURE.WARNING] 您不可以還原已刪除的儲存體帳戶，或擷取任何刪除之前它所包含的內容。 請務必先備份您想要儲存的任何資料，再刪除帳戶。 這也適用於帳戶中的任何資源 - 一旦刪除 Blob、資料表、佇列或檔案，就是永久刪除。
>
> 如果儲存體帳戶包含 Azure 虛擬機器的 VHD 檔案，則必須先刪除使用這些 VHD 檔案的映像和磁碟，才能刪除儲存體帳戶。 首先，停止執行中的虛擬機器，然後予以刪除。 若要刪除磁碟，請瀏覽至 **磁碟** 標籤，然後刪除該處的所有磁碟。 若要刪除映像，瀏覽至 **映像** 標籤，然後刪除帳戶中儲存的任何映像。

1. 在 [Azure 傳統入口網站](manage.windowsazure.com), ，按一下 [ **儲存體**。

2. 按一下名稱以外的儲存體帳戶項目中的任何位置，然後按一下 **刪除**。

     -或-

    按一下以開啟儀表板，然後按一下 [儲存體帳戶名稱 **刪除**。

3. 按一下 [ **是** 來確認您想要刪除儲存體帳戶。

## 後續步驟

- 若要深入了解 Azure 儲存體，請參閱 Azure 儲存體文件 [Azure.com](http://azure.microsoft.com/documentation/services/storage/)。
- 請瀏覽 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)。
- [使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy)
