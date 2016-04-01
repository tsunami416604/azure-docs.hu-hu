<properties 
     pageTitle="如何啟用 Azure 內容傳遞網路 (CDN)" 
     description="本主題說明如何啟用 Azure 內容傳遞網路 (CDN)。" 
     services="cdn" 
     documentationCenter="" 
     authors="camsoper" 
     manager="dwrede" 
     editor=""/>
<tags 
     ms.service="cdn" 
     ms.workload="media" 
     ms.tgt_pltfrm="na" 
     ms.devlang="na" 
     ms.topic="article" 
     ms.date="12/02/2015" 
     ms.author="casoper"/>



#如何啟用 Azure 內容傳遞網路 (CDN)  

您可透過 Azure 管理入口網站針對您的來源啟用 CDN。 目前支援數種整合式 Azure 原始來源類型，包括 Web Apps、Blob 儲存體和雲端服務。 您也可以針對您的 Azure 媒體服務啟用 CDN Azure 媒體服務串流端點。  如果您的原始來源不是其中一項 Azure 服務，或是裝載於 Azure 之外的其他位置，您也可以建立自訂原始來源。  針對您的來源啟用 CDN 端點之後，所有可公開取得的物件皆能進行 CDN 邊緣快取。

## 建立新的 CDN 設定檔

CDN 設定檔就是 CDN 端點的集合。  每個設定檔皆包含一或多個 CDN 端點。  您可能會想要使用多個設定檔，依網際網路網域、Web 應用程式或其他準則來組織您的 CDN 端點。

> [AZURE.NOTE] 單一的 Azure 訂用帳戶僅限於四個 CDN 設定檔。 而每個 CDN 設定檔則只能擁有四個 CDN 端點。
>
> CDN 定價是根據 CDN 設定檔層級來套用的。 如果您想要混合使用標準和進階的 CDN 功能，就需要擁有多個 CDN 設定檔。


**建立新的 CDN 設定檔**

1. 在 [Azure 管理入口網站](https://portal.azure.com), ，左上方，按一下 [ **新增**。  在 **新增** 分頁中，選取 **媒體 + CDN**, ，然後 **CDN**。

    此時會顯示新的 [CDN 設定檔] 刀鋒視窗。
    
    ![新增 CDN 設定檔][new-cdn-profile]

2. 輸入 CDN 設定檔的名稱。 

3. 選取 **定價層** 或使用預設值。

4. 選取或建立 **資源群組**。  如需資源群組的詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview/#resource-groups)。 

5. 選取 **訂閱** 此 CDN 設定檔。

6. 選取 **位置**。  此為儲存您 CDN 設定檔資訊的所在 Azure 位置。  其不會影響 CDN 端點位置。  此位置不一定是與儲存體帳戶相同的位置。

7. 按一下 [ **建立** ] 按鈕以建立新的設定檔。

## 建立新的 CDN 端點

**為儲存體帳戶建立新的 CDN 端點**

1. 在 [Azure 管理入口網站](https://portal.azure.com), ，瀏覽至您的 CDN 設定檔。  您可能已在先前步驟中將其釘選至儀表板。  如果您不是，您可以找到它，即可 **瀏覽**, ，然後 **CDN 設定檔**, ，然後按一下您要新增您的端點，在設定檔。

    此時會顯示 [CDN 設定檔] 刀鋒視窗。
    
    ![CDN 設定檔][cdn-profile-settings]
    
2. 按一下 [ **新增端點** ] 按鈕。

    ![[加入端點] 按鈕][cdn-new-endpoint-button]

     **將端點加入** 刀鋒視窗隨即出現。
    
    ![新增端點刀鋒視窗][cdn-add-endpoint]

3. 輸入 **名稱** 此 CDN 端點。  此名稱會用於存取位於網域 `<EndpointName>.azureedge.net` 的快取資源。

4. 在 **原始型別** 下拉式清單中，選取您的原始型別。
    
    ![CDN 原始來源類型](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. 在 **原始主機名稱** 下拉式清單中，選取或輸入您的原始網域。  下拉式清單會列出您在步驟 4 中指定之類型的所有可用原始來源。  如果您選取 *自訂原始* 做為您 **原始型別**, ，您會輸入自訂的來源網域中。

6. 在 **原始路徑** 文字方塊中，輸入您想要快取，或保留空白，允許您在步驟 5 中指定網域的任何資源的快取的資源的路徑。

7. 在 **原始主機標頭**, ，輸入您想要與每個要求一起傳送 CDN 主機標頭，或保留預設值。

8. 如 **通訊協定** 和 **原始連接埠**, ，指定通訊協定與連接埠用來存取您的資源，原始位置。  當您的用戶端存取 CDN 上的資源時，將會繼續使用這些相同的通訊協定和連接埠。  至少必須選取一個通訊協定 (HTTP 或 HTTPS)。

9. 按一下 [ **新增** ] 按鈕以建立新的端點。

10. 端點建立完畢之後，即會出現在設定檔的端點清單中。 此清單檢視會顯示用來存取所快取內容的 URL 以及原始網域。

    ![CDN 端點][cdn-endpoint-success]

    > [AZURE.NOTE] 端點將無法立即可供使用。  註冊可能需要 90 分鐘的處理時間，以透過 CDN 網路傳播。 若使用者嘗試立即使用 CDN 網域名稱，則可能會顯示接收狀態碼 404，直到可透過 CDN 使用內容為止。

##另請參閱
- [如何將內容傳遞網路 (CDN) 內容對應至自訂網域](cdn-map-content-to-custom-domain.md)
- [清除 Azure CDN 端點](cdn-purge-endpoint.md)

[new-cdn-profile]: ./media/cdn-create-new-endpoint/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png 
 

