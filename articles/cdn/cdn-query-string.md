<properties 
    pageTitle="CDN - 使用查詢字串控制 CDN 要求的快取行為" 
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

#使用查詢字串控制 CDN 要求的快取行為

> [AZURE.SELECTOR]
- [標準](cdn-query-string.md)
- [高級](cdn-query-string-premium.md)

##概觀

查詢字串快取可控制檔案內含查詢字串時的檔案快取方式。 

> [AZURE.NOTE] 標準和高階 CDN 層提供相同的查詢字串快取功能，但不同的使用者介面。  本文件說明 **標準** 層使用者介面。  「 高階 」 層，請參閱 [控制 CDN 快取行為要求查詢字串-高階](cdn-query-string-premium.md)。

提供三種可用模式：

- **略過查詢字串**: 這是預設模式。  CDN 邊緣節點會將要求者發出的查詢字串，傳遞至第一個要求的來源並快取資產。  快取的資產到期之前，所有從邊緣節點提供且針對該資產的後續查詢皆會忽略查詢字串。
- **略過快取 url 查詢字串**: 在此模式中，以查詢字串的要求不會快取 CDN 邊緣節點。  邊緣節點會直接從來源擷取資產，然後透過每個要求將其傳遞給要求者。
- **快取每個唯一的 URL**: 這個模式會包含查詢字串的每個要求視為具有自己的快取的唯一的資產。  例如，要求的原始回應 *foo.ashx?q=bar* 會快取在邊緣節點，並針對後續的快取以該相同的查詢字串傳回。  要求 *foo.ashx?q=somethingelse* 會以即時個別資產與它自己的時間來快取。
    
    >[AZURE.WARNING] 查詢字串包含每個要求，將會變更的參數，例如工作階段識別碼或使用者名稱，因為這會導致非常低的快取點擊率時，不應該使用此模式。

##變更查詢字串快取設定

1. 在 [CDN 設定檔] 刀鋒視窗中，按一下您要管理的 CDN 端點。
    
    ![[CDN 設定檔] 刀鋒視窗端點](./media/cdn-query-string/cdn-endpoints.png)

    隨即開啟 [CDN 端點] 刀鋒視窗。

2. 按一下 [ **設定** ] 按鈕。

    ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-query-string/cdn-config-btn.png)
    
    [CDN 組態] 刀鋒視窗隨即開啟。
    
3. 選取設定從 **查詢字串快取行為** 下拉式清單。
    
    ![CDN 查詢字串快取選項](./media/cdn-query-string/cdn-query-string.png)
    
4. 您的選取範圍之後，按一下 [ **儲存** ] 按鈕。




    


