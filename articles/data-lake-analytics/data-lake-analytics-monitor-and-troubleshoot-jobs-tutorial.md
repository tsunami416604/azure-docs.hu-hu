<properties 
   pageTitle="使用 Azure 入口網站疑難排解 Azure 資料湖分析作業 | Azure" 
   description="了解如何使用 Azure 入口網站疑難排解資料湖分析作業。 " 
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
   ms.date="10/27/2015"
   ms.author="jgao"/>

# 使用 Azure 入口網站疑難排解 Azure 資料湖分析作業

了解如何使用 Azure 入口網站疑難排解資料湖分析作業。

在本教學課程中，將會建立一個來源檔案遺失的問題，並使用 Azure 入口網站疑難排解問題。

**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **資料湖分析工作處理序的基本知識**。 請參閱 [開始使用 Azure 入口網站的 Azure 資料湖分析](data-lake-analytics-get-started-use-portal.md)。
- **資料湖分析帳戶**。 請參閱 [開始使用 Azure 入口網站的 Azure 資料湖分析](data-lake-analytics-get-started-use-portal.md#create-adl-analytics-account)。
- **將範例資料複製到預設資料湖存放區帳戶**。  請參閱 [準備來源資料](data-lake-analytics-get-started-use-portal.md.md#prepare-source-data)

##提交資料湖分析作業

現在您將使用一個不正確的來源檔案名稱建立 U-SQL 工作。  

**提交工作**

1. 從 Azure 入口網站中，按一下 [ **Microsoft Azure** 左上角。
2. 按一下具有您資料湖分析帳戶名稱的磚。  該建立帳戶時它便已釘選在此處。
如果帳戶不固定的發生，請參閱 
[從入口網站中開啟分析帳戶](data-lake-analytics-manage-use-portal.md#access-adla-account)。
3. 按一下 [ **新工作** 從上方的功能表。
4. 輸入工作名稱與下列 U SQL 指令碼：

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv1"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/output/SearchLog-from-adls.csv"
        USING Outputters.Csv();

    指令碼中定義的來源檔案是 **/Samples/Data/SearchLog.tsv1**, ，應該是 **/Samples/Data/SearchLog.tsv**。
     
5. 按一下 [ **提交工作** 頂端。 新的 [工作詳細資料] 窗格隨即開啟。 窗格的標題列會顯示工作狀態。 需花費數分鐘的時間完成。 您可以按一下 **重新整理** 取得最新狀態。
6. 等候作業的狀態變更為 **失敗**。  如果作業是 **成功**, ，這是因為您未移除的 /Samples 資料夾中。 請參閱 **必要條件** 區段開頭的教學課程。

您可能會懷疑，這麼小的工作為什麼要花費如此長的時間。  請記住資料湖分析是為了處理巨量資料所設計。  使用其分散式系統處理大量資料時則會有出色的表現。

讓我們假設您已提交工作並關閉入口網站。  在下一節中，您將學習如何疑難排解工作。


## 疑難排解作業

在上一節中，您已經提交工作，但工作失敗。  

**查看所有工作**

1. 從 Azure 入口網站中，按一下 [ **Microsoft Azure** 左上角。
2. 按一下具有您資料湖分析帳戶名稱的磚。  工作摘要會顯示在 **作業管理** 並排顯示。

    ![Azure 資料湖分析作業管理](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)
    
    [工作管理可] 讓您對工作的狀態一目瞭然。 請注意失敗的工作。
   
3. 按一下 [ **作業管理** ] 磚來查看工作。 工作會依分類 **執行**, ，**已排入佇列**, ，和 **結束**。 您應該會看到您失敗的作業中 **結束** 一節。 該工作應該列在清單中的首位。 當您有很多工作時，您可以按一下 **篩選** 可協助您尋找的工作。

    ![Azure 資料湖分析篩選作業](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. 按一下清單中的失敗工作，會以新的刀鋒視窗開啟工作詳細資料：

    ![Azure 資料湖分析失敗作業](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)
    
    請注意 **重新提交** ] 按鈕。 修正問題之後，您可以重新提交工作。

5. 按一下前一個螢幕擷取畫面中反白顯示的部分，開啟錯誤詳細資料。  您會看到類似下面的畫面：

    ![Azure 資料湖分析失敗作業詳細資料](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    它會告訴您找不到來源資料夾。
    
6. 按一下 [ **複製指令碼**。
7. 更新 **FROM** 路徑如下 ︰

    "/Samples/Data/SearchLog.tsv"

8. 按一下 [ **提交工作**。


##另請參閱

- [Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure PowerShell 開始使用 Azure 資料湖分析](data-lake-analytics-get-started-powershell.md)
- [透過 Visual Studio 開始使用 Azure 資料湖分析與 U-SQL](data-lake-analytics-get-started-u-sql-studio.md)
- [使用 Azure 入口網站管理 Azure 資料湖分析](data-lake-analytics-manage-use-portal.md)







