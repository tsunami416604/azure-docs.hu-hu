<properties 
    pageTitle="如何使用 CDN |Microsoft Azure" 
    description="了解如何使用 Azure 內容傳遞網路 (CDN) 來快取 Blob 和靜態內容，以傳遞高頻寬內容。" 
    services="cdn" 
    documentationCenter=".net" 
    authors="camsoper" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="cdn" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="casoper"/>


# 整合儲存體帳戶與 CDN

可以啟用 CDN，以從您的 Azure 儲存體快取內容。 它透過將計算執行個體 Blob 與靜態內容快取到位於美國、歐洲、亞洲、澳洲與南美洲的實體節點中，為開發人員提供一套傳遞高頻寬內容的全球解決方案。


## 步驟 1：建立儲存體帳戶

使用下列程序，為 Azure 訂閱建立新的儲存體帳戶
。 儲存體帳戶可存取 
Azure 儲存體服務。 儲存體帳戶代表命名空間的最高層級，
可存取每個 Azure 儲存體服務
元件: Blob 服務、 佇列服務和表格服務。 如需詳細資訊，請參閱 [Microsoft Azure 儲存體簡介](../storage-introduction.md)。

若要建立儲存體帳戶，您必須是服務
系統管理員或相關聯訂閱的共同管理員。

> [AZURE.NOTE] 有數種方法可用來建立儲存體帳戶，包括 Azure 網站和 Powershell。  針對本教學課程，我們將使用 Azure 入口網站。  

**為 Azure 訂閱建立儲存體帳戶**

1.  登入 [Azure 入口網站](https://portal.azure.com)。
2.  在左上角，選取 **新增**。 在 **新增** 對話方塊中，選取 **資料 + 儲存體**, ，然後按一下 [ **儲存體帳戶**。 保留 **傳統** 選取做為部署模型，然後按一下 [ **建立**。

     **儲存體帳戶** 刀鋒視窗隨即出現。

    ![建立儲存體帳戶][create-new-storage-account]

4. 在 **儲存體** 欄位中，輸入子網域名稱。 此項目可以包含 3 至 24 個小寫字母與數字。

    此值會變成 URI 內，用來
    定址訂閱的 Blob、佇列或對應表資源的主機名稱。 收件人
    定址 Blob 服務中的容器資源，您要使用
    URI 格式如下，其中 *& lt;StorageAccountLabel & gt;* 是指
    您輸入的值在 **輸入 URL**:

    http://*& l t;StorageAcountLabel & gt;*.blob.core.windows.net/*& lt; mycontainer & gt;*

    **重要事項:** URL 標籤會形成儲存體的子網域
    而且在 Azure 的所有託管服務中 
    Azure。

    此值也用作這個儲存體帳戶在入口網站中的名稱，或用於透過程式設計方式存取此帳戶時。

5.  選取 **定價層** 或使用預設值。  如需定價層的詳細資訊，請參閱 [Azure 儲存體定價](../../pricing/details/storage)。

6.  選取或建立 **資源群組**。  如需資源群組的詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview/#resource-groups)。 

7. 選取 **訂閱** 會搭配使用的儲存體帳戶。

8.  按一下 [ **建立**。 建立儲存體帳戶的程序可能需要幾分鐘才能完成。

9.  若要確認已成功建立儲存體帳戶，請確認帳戶出現在針對列出的項目 **儲存體** 狀態為 **線上**。


## 步驟 2：建立新的 CDN 設定檔

CDN 設定檔為 CDN 端點的集合。  每個設定檔皆包含一或多個 CDN 端點。  您可能會想要使用多個設定檔，依網際網路網域、Web 應用程式或其他準則來組織您的 CDN 端點。

> [AZURE.TIP] 如果您已經有您想要用於本教學課程的 CDN 設定檔，請移到 [步驟 3](#step-3-create-a-new-cdn-endpoint)。

**建立新的 CDN 設定檔**

1. 在 [Azure 管理入口網站](https://portal.azure.com), ，左上方，按一下 [ **新增**。  在 **新增** 分頁中，選取 **媒體 + CDN**, ，然後 **CDN**。

    此時會顯示新的 [CDN 設定檔] 刀鋒視窗。
    
    ![新增 CDN 設定檔][new-cdn-profile]

2. 輸入 CDN 設定檔的名稱。 

3. 選取 **定價層** 或使用預設值。

4. 選取或建立 **資源群組**。  此資源群組並不必然是與您儲存體帳戶相同的資源群組。

5. 選取 **訂閱** 此 CDN 設定檔。  基於本教學課程用途，此訂用帳戶必須為與儲存體帳戶相同的訂用帳戶。

6. 選取 **位置**。  此為儲存您 CDN 設定檔資訊的所在 Azure 位置。  其不會影響 CDN 端點位置。  此位置不一定是與儲存體帳戶相同的位置。

7. 按一下 [ **建立** ] 按鈕以建立新的設定檔。

## 步驟 3：建立新的 CDN 端點

**為儲存體帳戶建立新的 CDN 端點**

1. 在 [Azure 管理入口網站](https://portal.azure.com), ，瀏覽至您的 CDN 設定檔。  您可能已在先前步驟中將其釘選至儀表板。  如果您不是，您可以找到它，即可 **瀏覽**, ，然後 **CDN 設定檔**, ，然後按一下您要新增您的端點，在設定檔。

    此時會顯示 [CDN 設定檔] 刀鋒視窗。
    
    ![CDN 設定檔][cdn-profile-settings]
    
2. 按一下 [ **新增端點** ] 按鈕。

    ![[加入端點] 按鈕][cdn-new-endpoint-button]

     **將端點加入** 刀鋒視窗隨即出現。
    
    ![新增端點刀鋒視窗][cdn-add-endpoint]

3. 輸入 **名稱** 此 CDN 端點。  此名稱會用於存取位於網域 `<EndpointName>.azureedge.net` 的快取資源。

4. 在 **原始型別** 下拉式清單中，選取 *儲存體*。  

5. 在 **原始主機名稱** 下拉式清單中，選取儲存體帳戶。

6. 保留預設值為 **原始路徑**, ，**原始主機標頭**, ，和 **原始通訊協定/連接埠**。  您至少必須指定一個通訊協定 (HTTP 或 HTTPS)。 

    > [AZURE.NOTE] 此設定可讓您公開可見的容器，在 CDN 中快取儲存體帳戶中的所有。  如果您想要將範圍限制為單一容器，使用 **原始路徑**。  請注意，容器可見度必須設為公開。

7. 按一下 [ **新增** ] 按鈕以建立新的端點。

8. 端點建立完畢之後，即會出現在設定檔的端點清單中。 此清單檢視會顯示用來存取所快取內容的 URL 以及原始網域。

    ![CDN 端點][cdn-endpoint-success]

    > [AZURE.NOTE] 端點將無法立即可供使用。  註冊可能需要 90 分鐘的處理時間，以透過 CDN 網路傳播。 若使用者嘗試立即使用 CDN 網域名稱，則可能會顯示狀態碼 404，直到可透過 CDN 使用內容為止。


## 步驟 4：存取 CDN 內容

若要存取 CDN 上快取的內容，請使用入口網站中提供的 CDN URL。 所快取 Blob 的位址將類似如下：

http://<*EndpointName*\ >.azureedge.net/ <*myPublicContainer*\ > / <*BlobName*\ >

> [AZURE.NOTE] 之後您啟用 CDN 存取儲存體帳戶或託管服務時，所有公開可用的物件皆適用於 CDN 邊緣快取的。 如果您修改的物件目前是 CDN 中的快取物件，在快取內容的有效存留期已滿，且 CDN 重新整理內容之前，都無法透過 CDN 取得新的內容。

## 步驟 5：從 CDN 移除內容

如果您不想再快取 Azure 內容
傳遞網路 (CDN) 中的物件，可以採取下列其中一個步驟：

-   您可以將容器設為私人而非公用。 請參閱 [管理對容器和 blob 的匿名讀取權限](../storage-manage-access-to-resources/) 如需詳細資訊。
-   您可以使用管理入口網站來停用或刪除 CDN 端點。
-   您可以修改託管服務，使其不再回應物件的要求。

已在 CDN 中快取的物件會保持快取狀態，直到物件的有效存留期已過或端點已清除為止。 有效存留期間已滿時，CDN 將查看 CDN 端點是否仍然有效，以及物件是否仍可匿名存取。 如果不是的話，將不再快取物件。


## 其他資源

-   [如何將 CDN 對應至自訂網域](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[new-cdn-profile]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png


