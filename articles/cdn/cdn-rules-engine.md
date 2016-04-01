<properties 
    pageTitle="CDN - 使用規則引擎覆寫預設的 HTTP 行為" 
    description="規則引擎可讓您自訂 HTTP 要求的處理方式，例如封鎖傳遞特定類型的內容、定義快取原則及修改 HTTP 標頭。" 
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

# 使用規則引擎覆寫預設的 HTTP 行為

## 概觀

規則引擎可讓您自訂 HTTP 要求的處理方式，例如封鎖傳遞特定類型的內容、定義快取原則及修改 HTTP 標頭。  本教學課程將示範如何建立用以變更 CDN 資產之快取行為的規則。

> [AZURE.NOTE] 規則引擎是高階 CDN 層的功能。  如需標準和高階 CDN 功能的比較，請參閱 [Azure CDN 概觀](cdn-overview.md)。

## 教學課程

1. 從 CDN 基本資料] 分頁中，按一下 [ **管理** ] 按鈕。

    ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-rules-engine/cdn-rules-manage-btn.png)
    
    隨即開啟 CDN 管理入口網站。
    
2. 按一下 [ **HTTP 大型** ] 索引標籤，後面接著 **規則引擎**。
    
    隨即顯示新規則的選項。
    
    ![CDN 新規則選項](./media/cdn-rules-engine/cdn-new-rule.png)

3. 中輸入名稱 **名稱 / 描述** 文字方塊。

4. 識別將套用此規則的要求類型。  根據預設， **永遠** 選取符合條件。  您將使用 **永遠** 本教學課程中，因此，不要選取。

    ![CDN 相符條件](./media/cdn-rules-engine/cdn-request-type.png)
    
    >[AZURE.TIP] 有許多類型的相符項目可用的下拉式清單中的條件。  按一下相符條件左側的藍色資訊圖示，即會詳細說明目前選取的條件。
    >
    >在詳細資料的比對條件的完整清單，請參閱 [規則引擎比對條件和功能的詳細資料](cdn-rules-engine-details.md#match-conditions)。
    
5.  按一下 [ **+** 旁 **功能** 加入新功能。  在左側下拉式清單中，選取 **Force 內部 Max-age**。  在出現的文字方塊中，輸入 **300**。  保留其餘預設值。

    ![CDN 功能](./media/cdn-rules-engine/cdn-new-feature.png)

    >[AZURE.NOTE] 為符合條件，按一下左邊的新功能的藍色資訊圖示會顯示此功能的詳細資訊。   **Force 內部 Max-age**, ，我們會覆寫的資產 **快取控制** 和 **到期** 標頭來控制當 CDN 邊緣節點將會重新整理原始的資產。  我們的範例為 300 秒，表示 CDN 邊緣節點會快取資產 5 分鐘，再從其原始來源重新整理資產。
    >
    >功能的詳細資料的完整清單，請參閱 [規則引擎比對條件和功能的詳細資料](cdn-rules-engine-details.md#features)。
    
6.  按一下 [ **新增** ] 按鈕以儲存新的規則。  新規則現在正在等待核准。 一旦核准之後，將會變更狀態 **暫止 XML** 至 **作用中的 XML**。

## 考量

- 多項規則的列出順序會影響規則的處理方式。 後一項規則可能會覆寫前一項規則所指定的動作。

## 另請參閱
* [規則引擎相符條件和功能詳細資料](cdn-rules-engine-details.md)
* [Azure CDN 概觀](cdn-overview.md)
* [Microsoft Azure CDN 中的即時統計資料](cdn-real-time-stats.md)
* [進階 HTTP 報告](cdn-advanced-http-reports.md)
* [分析邊緣效能](cdn-edge-performance.md)


    



