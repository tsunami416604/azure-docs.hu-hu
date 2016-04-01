<properties 
    pageTitle="CDN - 使用查詢字串控制 CDN 要求的快取行為 - 高階" 
    description="CDN 查詢字串快取可控制檔案內含查詢字串時的檔案快取方式。" 
    services="cdn" 
    documentationCenter=".NET" 
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

#使用查詢字串控制 CDN 要求的快取行為 - 高階

> [AZURE.SELECTOR]
- [標準](cdn-query-string.md)
- [高級](cdn-query-string-premium.md)

##概觀

查詢字串快取可控制檔案內含查詢字串時的檔案快取方式。 

> [AZURE.NOTE] 標準和高階 CDN 層提供相同的查詢字串快取功能，但不同的使用者介面。  本文件說明 **高階** 層使用者介面。  標準層，請參閱 [以查詢字串的 CDN 快取行為的控制要求](cdn-query-string.md)。

提供三種可用模式：

- **標準快取**︰ 這是預設模式。  CDN 邊緣節點會將要求者發出的查詢字串，傳遞至第一個要求的來源並快取資產。  快取的資產到期之前，所有從邊緣節點提供且針對該資產的後續查詢皆會忽略查詢字串。 
- **無快取**︰ 在此模式中，以查詢字串的要求不會快取 CDN 邊緣節點。  邊緣節點會直接從來源擷取資產，然後透過每個要求將其傳遞給要求者。 
- **唯一快取**︰ 這個模式會包含查詢字串的每個要求視為具有自己的快取的唯一的資產。  例如，要求的原始回應 *foo.ashx?q=bar* 會快取在邊緣節點，並針對後續的快取以該相同的查詢字串傳回。  要求 *foo.ashx?q=somethingelse* 會以即時個別資產與它自己的時間來快取。
    
    >[AZURE.WARNING] 查詢字串包含每個要求，將會變更的參數，例如工作階段識別碼或使用者名稱，因為這會導致非常低的快取點擊率時，不應該使用此模式。

##變更查詢字串快取設定

1. 從 CDN 基本資料] 分頁中，按一下 [ **管理** ] 按鈕。

    ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-query-string-premium/cdn-manage-btn.png)
    
    隨即開啟 CDN 管理入口網站。
    
2. 將滑鼠停留在 **HTTP 大型** 索引標籤，然後將滑鼠停留在 **快取設定** 飛出視窗。  按一下 [ **查詢字串快取**。
    
    查詢字串快取選項隨即顯示。
    
    ![CDN 查詢字串快取選項](./media/cdn-query-string-premium/cdn-query-string.png)
    
3. 您的選取範圍之後，按一下 [ **更新** ] 按鈕。




    



