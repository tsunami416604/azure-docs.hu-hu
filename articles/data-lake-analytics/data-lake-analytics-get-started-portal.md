<properties 
   pageTitle="使用 Azure 入口網站開始使用 Azure 資料湖分析 | Azure" 
   description="了解如何使用 Azure 入口網站建立資料湖分析帳戶、使用 U-SQL 建立資料湖分析工作，以及提交工作。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/22/2015"
   ms.author="jgao"/>

# 教學課程：使用 Azure 入口網站開始使用 Azure 資料湖分析

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 Azure 入口網站來建立 Azure 資料湖分析帳戶、 定義資料湖分析
中的工作 [U SQL](data-lake-analytics-u-sql-get-started.md), ，將作業提交至資料湖分析帳戶。 如需 
資料湖分析的相關資訊，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

在本教學課程中，您將開發工作讀取定位點分隔值 (TSV) 檔案，並將它轉換成逗號 
分隔的值 (CSV) 檔案。 若要使用其他支援的工具進行同一個教學課程，請按一下此區段最上方的索引標籤。 當第一個工作成功完成時，您便可開始使用 U SQL 撰寫更複雜的資料轉換。

**基本資料湖分析程序：**

![Azure 資料湖分析程序流程圖](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. 建立資料湖分析帳戶。
2. 準備來源資料。 資料湖分析工作可從 Azure 資料湖存放區帳戶或 Azure Blob 儲存體帳戶讀取資料。 在本範例中，我們將從 Azure 資料湖存放區進行讀取。  
3. 開發 U SQL 指令碼。
4. 將工作 (U-SQL 指令碼) 提交至資料湖分析帳戶。 作業會讀取來源資料，處理資料的指示 
U SQL 指令碼，然後再儲存輸出資料湖存放區帳戶或 Blob 儲存體帳戶。

**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

##建立資料湖分析帳戶

您必須擁有資料湖分析帳戶，才能執行任何工作。

每個資料湖分析帳戶都 [Azure 資料湖市集]() 帳戶相依性。  此帳戶被指
做為預設資料湖存放區帳戶。  事先或在建立時，您可以建立資料湖存放區帳戶 
您的資料湖分析帳戶。 在本教學課程中，您將建立資料湖存放區帳戶使用資料湖分析 
。

**建立資料湖分析帳戶**

1. 登入新 [Azure 傳統入口網站](https://portal.azure.com)。
2. 按一下 [ **新增**, ，按一下 [ **資料 + 分析**, ，然後按一下 [ **資料湖分析**。
6. 輸入或選取下列項目：

    ![Azure 資料湖分析入口網站刀鋒視窗](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **名稱**︰ 分析帳戶的名稱。
    - **資料湖存放區**︰ 每個資料湖分析帳戶有相依的資料湖存放區帳戶。 資料湖分析帳戶和相依的資料湖存放區帳戶必須位於同一個 Azure 資料中心。 請依照指示來建立新的資料湖存放區帳戶，或選取現有的帳戶。
    - **訂閱**︰ 選擇用於分析帳戶的 Azure 訂閱。
    - **資源群組**。 選取現有的 Azure 資源群組，或建立一個新的群組。 Azure 資源管理員 (ARM) 可讓您將應用程式中的資源作為群組使用。 如需詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。 
    - **位置**。 為資料湖分析帳戶選取 Azure 資料中心。 
7. 選取 **釘選到開始面板**。 這是遵循本教學課程的必要步驟。
8. 按一下 [ **建立**。 它會帶領您前往入口網站「開始面板」。 新的磚會新增至「開始面板」，且具有顯示 [部署 Azure 資料湖分析] 的標籤。 建立資料湖分析帳戶需要幾分鐘的時間。 當帳戶建立時，入口網站就會在其中的新刀鋒視窗上開啟帳戶。

    ![Azure 資料湖分析入口網站刀鋒視窗](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)


建立資料湖分析帳戶之後，您可以新增其他資料湖存放區帳戶和 Azure 儲存體 
。 如需指示，請參閱 [管理資料湖分析帳戶資料來源](data-lake-analytics-manage-use-portal.md#manage-account-data-sources)。

##準備來源資料

在本教學課程中，您將會處理一些搜尋記錄檔。  搜尋記錄檔可以儲存在資料湖存放區或 Azure Blob 儲存體中。 

Azure 入口網站會提供使用者介面，可將範例資料檔案複製到預設的資料湖帳戶，其中包括搜尋記錄檔案。

**複製範例資料檔案**

1. 從 Azure 入口網站中，按一下 [ **Microsoft Azure** 左上角。
2. 按一下具有您資料湖分析帳戶名稱的磚。  該建立帳戶時它便已釘選在此處。
如果帳戶不固定的發生，請參閱 
[從入口網站中開啟資料湖分析帳戶](data-lake-analytics-manage-use-portal.md#access-adla-account) 開啟
。
3. 展開 **Essentials** ] 窗格中，然後再按一下 **瀏覽範例工作**。 它會開啟另一個名為的分頁 **範例
工作**。
4. 按一下 [ **複製範例資料**, ，然後按一下 [ **確定** 確認。
5. 按一下 [ **通知** 即形狀的鈴鐺圖示。 您應該會看到記錄檔指出 **更新已完成的範例資料**。 按一下 [通知] 窗格外的任一位置即可關閉。
7. 從 [資料湖分析帳戶] 分頁中，按一下 [ **資料總管** 上面。 

    ![Azure 資料湖分析資料總管按鈕](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    這會開啟兩個刀鋒視窗。 其中一個是 **資料總管**, ，而另一個預設資料湖存放區帳戶。
8. 在 [預設資料湖存放區帳戶] 分頁中，按一下 [ **範例** 展開資料夾，然後按一下 **資料** 展開資料夾。 您會看見下列檔案和資料夾：

    - AmbulanceData /
    - AdsLog.tsv
    - SearchLog.tsv
    - version.txt
    - WebLog.log
    
    在本教學課程中，您將使用 SearchLog.tsv。

在實務上，您可編寫應用程式以將資料寫入至連結的儲存體帳戶，或是上傳資料。 上傳檔案，請參閱 
[將資料上傳至資料湖存放區](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) 或 [資料上傳至 Blob 儲存體](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb)。

##建立並提交資料湖分析工作

在您備妥來源資料後，您可以開始開發 U SQL 指令碼。  

**提交工作**

1. 從入口網站上的資料湖分析帳戶分頁中按一下 [ **新工作**。 

    ![Azure 資料湖分析新工作按鈕](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    如果您沒有看到 [] 分頁中，請參閱 [從入口網站中開啟資料湖分析帳戶](data-lake-analytics-manage-use-portal.md#access-adla-account)。
4. 輸入 **工作名稱**, ，和下列 U SQL 指令碼 ︰

    ![建立 Azure 資料湖分析 U-SQL 工作](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    這個 U SQL 指令碼會讀取來源資料檔使用 **Extractors.Tsv()**, ，然後建立 csv 檔案使用 **Outputters.Csv()**。 
    
    除非您將來源檔案複製到其他位置，否則請勿修改這兩個路徑。  資料湖分析將建立輸出資料夾 (若尚未建立)。  在此情況中，我們會使用簡單且相對的路徑。  
    
    使用儲存在預設資料湖帳戶中檔案的相對路徑，是比較容易的方法。 您也可以使用絕對路徑。  例如 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
      

    如需詳細資訊 U SQL，請參閱 [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md) 和 [U SQL 語言參考](http://go.microsoft.com/fwlink/?LinkId=691348)。
     
5. 按一下 [ **提交工作** 頂端。 新的 [工作詳細資料] 窗格隨即開啟。 窗格的標題列會顯示工作狀態。   
6. 等候作業的狀態變更為 **成功**。 當工作完成時，入口網站會在新的刀鋒視窗中開啟工作詳細資料：

    ![Azure 資料湖分析工作詳細資料](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-completed.png)

    您可以從上一個螢幕擷取畫面中看見工作需要花上大約 1.5 分鐘，才能完成從 [已提交] 至 [已結束] 狀態。
    
    作業失敗，請參閱 [監視和疑難排解資料湖分析工作](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorials.md)。

7. 在底部 **工作詳細資料** 刀鋒視窗中，按一下工作名稱在 **SearchLog 從資料 Lake.csv**。 您可以預覽、下載、重新命名和刪除輸出檔案。

    ![Azure 資料湖分析工作輸出檔案屬性](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)
8. 按一下 [ **預覽** 查看輸出檔案。

    ![Azure 資料湖分析工作輸出檔案預覽](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-output-preview.png)

##另請參閱

- 若要查看更複雜的查詢，請參閱 [使用 Azure 資料湖分析分析網站記錄檔](data-lake-analytics-analyze-weblogs.md)。
- 若要開始開發 U SQL 應用程式，請參閱 [使用資料湖 Tools for Visual Studio 開發 U SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要了解 U SQL，請參閱 [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)。
- 管理工作，請參閱 [管理 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-manage-use-portal.md)。
- 若要取得資料湖分析的概觀，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。
- 若要使用其他工具檢視同一個教學課程，請按一下頁面最上方的索引標籤選取器。


