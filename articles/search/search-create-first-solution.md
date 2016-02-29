<properties 
    pageTitle="使用 Azure 搜尋服務建立您的第一個搜尋解決方案 | Microsoft Azure | 雲端託管搜尋服務" 
    description="使用 Azure 搜尋服務 (Microsoft Azure 上託管的雲端搜尋服務) 建立您的第一個搜尋解決方案。" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="11/04/2015" 
    ms.author="heidist"/>

# 使用 Azure 搜尋建立您的第一個搜尋解決方案

此範例將說明 Adventure Works 資源回收公司的搜尋應用程式。 完成本教學課程後，您將取得附有豐富搜尋使用性的線上產品目錄，其中包括 Facet 導覽、多個可用於搜尋結果的排序選項，以及預先輸入查詢建議。

   ![][7]

此示範將引導您完成下列練習，以開始使用 Azure Search：

+   建立 Azure Search 索引。
+   從 SQL Server Database 將文件 (資料) 載入 Azure Search 索引中。
+   建置使用 Azure Search 的 ASP.NET MVC4 應用程式，以：
    +   搜尋及顯示 Azure 搜尋服務索引的結果
    +   在輸入搜尋詞彙時，在 [搜尋] 方塊中顯示預先輸入建議
    +   使用 Facet 篩選搜尋結果


<a id="sub-1"></a>
## 先決條件

+    [Azure 訂用帳戶](../includes/free-trial-note.md)。 如果您不準備要註冊試用訂閱，您可以略過本教學課程中，選擇 [Try Azure App Service](https://tryappservice.azure.com/) 改。 這個替代選項免費提供您 Azure 搜尋服務與 ASP.NET Web 應用程式 - 每個工作階段一小時 - 不需有訂用帳戶。
+   Visual Studio 2012 或更高版本，並安裝 ASP.NET MVC 4 與 SQL Server。 如果您還沒有安裝的軟體，您可以下載免費的 Express 版: [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) 和 [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx)。
+   Azure 搜尋服務。 您需要搜尋服務名稱，以及系統管理金鑰。 請參閱 [入口網站中建立 Azure 搜尋服務](search-create-service-portal.md) 如需詳細資訊。
+   [CodePlex 上的 adventure Works Azure Search 示範專案](http://go.microsoft.com/fwlink/p/?LinkID=510972)。 在 [來源] 索引標籤中，按一下 [ **下載** 以取得解決方案的壓縮檔。 

    ![][12]


此解決方案內含兩個專案：

+   **CatalogIndex** 建立 Azure 搜尋索引並從專案包含 SQL Server 資料庫載入資料。
+   **AdventureWorksWeb** 是查詢 Azure 搜尋索引的 mvc4 應用程式。 本教學課程假設您已具備 ASP.NET MVC 的使用知識。

<a id="sub-2"></a>
## 建立 Azure Search 索引

在此步驟中，您將使用 **CatalogIndex** 來建立新的 Azure 搜尋名為"catalog"的索引會根據 AdventureWorks SQL Server 資料庫中的資料。

1.  在 Visual Studio 中，開啟名為 Azure 搜尋示範解決方案 **AdventureWorksCatalog.sln**。  
2.  以滑鼠右鍵按一下 **CatalogIndexer** 方案總管] 中，選取 **設定為啟始專案** ，讓此應用程式執行，而不是 **AdventureWorksWeb** 專案中，當您按下 **F5**。
3.  開啟 **App.config** 在此專案，並為您 Azure 搜尋服務更新的值"SearchServiceName"和"SearchServiceApiKey"。 在搜尋服務名稱上，如果您的服務是 "mysearch.search.windows.net"，則您需要輸入 "mysearch"。
4.  App.config 會選擇性地包含採用 SQL Server 2014 Express LocalDB (Server=(LocalDB)\v11.0) 的 "SourceSqlConnectionString" 項目。 如果您使用不同版本的 SQL Server，請據以更新伺服器名稱。 例如，如果您具有本機預設執行個體，您可以使用 local 或 localhost。
5.  儲存 **App.config**。
6.  按下 **F5** 啟動專案。

命令提示字元應會開啟並顯示下列文字：「正在建立索引...」

一段時間後，作業應會完成並顯示文字：「完成。  按 <enter> 若要繼續: 」

   ![][8]

按下 **Enter** 關閉應用程式。 至此，您已成功建立 Azure Search 索引。 

> [AZURE.NOTE] 如果出現包含 「 金鑰 'attachdbfilename' 的值無效 」 錯誤，或是其他資料庫附加錯誤，您可能會執行 UAC 衝突發生。 為進行此示範，請執行下列動作以解決這些錯誤： 
> 將方案複製到可供已驗證的使用者存取的新資料夾或現有資料夾 (如 Temp)。 
> 使用 **系統管理員身分執行** 啟動 Visual Studio。 
> 開啟方案，建置它，然後按下 **F5** 來建立索引。

若要確認建立索引和文件上傳，請移至搜尋服務儀表板中 [Azure 傳統入口網站](https://portal.azure.com)。 在 [Usage] 中，索引計數應該會增加一個，而您應有 294 個文件，資料庫中的每項產品各一個。

按一下 [ **索引** 磚以顯示索引清單。 索引清單會列出新索引和文件計數。 請注意，您最多可在免費定價層具有三個索引。 如果已有三個索引，您必須刪除一個，以釋出空間給任何新的索引。

   ![][9]


<a id="sub-3"></a>
## 探索 CatalogIndexer

讓我們仔細看看 **CatalogIndexer** 專案，以了解它的運作方式。

1.  開啟 **Program.cs** 從方案總管] 中，移至 [ `Main(string[] args)` 函式。

    請注意，此函數如何根據您的 Azure 搜尋服務建置名為 `_serviceURI` 的 URI，然後建立名為 `_httpClient` 的 HttpClient，而使用您的 API 金鑰向此搜尋服務進行驗證。

2.  `ChangeEnumeratorSql` 和 `ChangeSet` 可用來從您的 SQL Server AdventureWorks Database 中的 Products 資料表列舉資料。 

3.  接著將會根據從這個資料表收集到的資料呼叫 `ApplyChanges`，以將這項資料套用至您的 Azure Search 索引。

4.  移至相同檔案中的 `ApplyChanges`。 請注意，此函數如何刪除已存在的索引 (`DeleteCatalogIndex`)，然後建立名為 "catalog" 的新索引 (`CreateCatalogIndex`)。  

5.  移至 `CreateCatalogIndex` 函數，然後觀察如何使用與 SQL Server 中的 Products 資料表資料行相符的結構描述建立索引。 每個欄位都有一個類型 (即 `Edm.String` 或 `Edm.Double`，以及定義這些欄位之用途的屬性。 請參閱 [Azure 搜尋服務 REST API 文件](http://msdn.microsoft.com/library/azure/dn798935.aspx) 如需有關這些屬性。

6.  返回 `ApplyChanges` 函數。 請留意此函數如何對列舉的變更 `ChangeSet` 中的所有資料進行迴圈。 變更不會逐一套用，而會分成以 1000 個為單位的群組，再套用至搜尋服務。 這會比逐一套用文件有效率得多。

您已了解如何使用 SQL Server 中的關聯式資料來建立及填入索引，現在我們將討論如何建置使用搜尋資料的產品目錄。

<a id="sub-4"></a>
## 使用 Azure Search 建置 MVC4 應用程式

在此步驟中，您將在網頁瀏覽器中建置及執行搜尋應用程式。

1.  在 Visual Studio 中，開啟名為 Azure 搜尋示範解決方案 **AdventureWorksCatalog.sln**。  

2.  以滑鼠右鍵按一下 **AdventureWorksWeb** 在方案總管] 中，選取 **設定為啟始專案**。

3.  開啟 **Web.config** 在此專案，並為您 Azure 搜尋服務更新的值"SearchServiceName"和"SearchServiceApiKey"。 在搜尋服務名稱上，如果您的服務是 "mysearch.search.windows.net"，則您需要輸入 "mysearch"。

4.  儲存 **Web.config**。

5.  按下 **F5** 啟動專案。 請遵循下列 [疑難排解](#err-mvc) 步驟，如果您收到建置錯誤。 

    ![][10]

6.  將 [搜尋] 方塊保留空白，然後按一下 [ **搜尋** 以全數傳回 294 產品。

7.  請留意位於左側的 Facet 清單。 嘗試按一下其中一個 Facet。 搜尋會重新執行，但這次會新增您所選擇的 Facet 以篩選結果。 您可以在第一行加入中斷點 **HomeController.cs** `Search` 函式可逐步執行 (F11) 每一行。

7.  輸入搜尋詞彙，例如 "mountain bikes"。 當您輸入時，請留意到建議查詢的下拉式清單。

    ![][11]

此示範剛開始時的螢幕擷取畫面會在下方再次出現，以提醒您找到了哪些項目。 在前幾節中，我們討論了 Facet 導覽 (位於左側)，位於頁面頂端的文件計數、建議，以及依關聯性、清單或價格排序的排序選項。

   ![][7]


<a id="sub-5"></a>
## 探索 AdventureWorksWeb

專案 AdventureWorksWeb 將說明如何使用 ASP.NET MVC 4 與 Web 應用程式中的 Azure Search 互動。 在本節裡，我們將檢視個別的應用程式碼部分，了解它們執行了哪些作業。

1.  在 [方案總管] 中，展開 **AdventureWorksWeb** | **控制器** 並開啟 **HomeController.cs**

    這是 MVC 控制器，可管理從 [索引] 檢視進行的互動。  在此控制器的頂端，您會看見 `CatalogSearch _catalogSearch` 的參考，此參考可建立對 Azure Search 服務的 HttpClient 連線物件。 這個程式碼 `CatalogSearch` 位於 **CatalogSeach.cs**

2. 內 **HomeController.cs**, ，有兩個主要函數:

    **搜尋**: 當使用者按一下 [搜尋] 按鈕或選擇 facet 時，會呼叫此函數來擷取結果，並將其傳回至要顯示的 [索引] 檢視。

    **建議**: 當使用者在搜尋方塊中輸入時，呼叫此函式會傳回 Azure 搜尋索引中的內容為基礎的建議清單。

我們將深入詳細探索這兩個函數。  

3.  在 **HomeController.cs** `Search` 函式，您會發現呼叫 `_catalogSearch.Search`, ，其中包含 Azure 搜尋服務的連線物件。 當您呼叫 Search 函數位於 **CatalogSearch.cs**, ，您可以看到它會利用這些參數建置 Azure Search 查詢。 查詢的結果會儲存在名為 `result` 的 JSON 物件中，然後傳回至會在網頁中剖析及顯示結果的 `Index` 檢視。

4.  開啟 **Index.cshtml** 檢視底下 |首頁，您會發現用來剖析這些結果的程式碼。

5.  停止應用程式是否仍在執行，並開啟 **Index.cshtml** 下檢視檔案 |首頁。  在此檔案的結尾處，您會看見使用 `JQuery $(function ())` 的 JavaScript 函數。 在頁面載入時，將會呼叫此函數。 它會使用 JQuery 自動完成函數，並將此函數連結作為來自文字方塊的回呼 (識別為 "q")。 每當有人在文字方塊中輸入時，就會呼叫這個自動建議函數，該函數會接著呼叫 /home/suggest (視輸入的值而定)。  `/home/suggest` 中的函式的參考 **HomeController.cs** 呼叫 `Suggest`。

6.  開啟 **HomeController.cs** 然後移至建議函數。 此程式碼是與使用搜尋函數非常類似 `_catalogSearch` 呼叫的函式物件 **CatalogSearch.cs** 呼叫 `Suggest`。 而不是執行搜尋查詢， `Suggest` 函式會呼叫 [建議 API](http://msdn.microsoft.com/library/azure/dn798936.aspx)。 此 API 會使用在文字方塊中輸入的詞彙，並建置可能建議的清單。 若要傳回的值 **Index.cshtml** 檔案，並自動列在搜尋方塊中作為預先輸入選項。

至此，您可能會好奇 Azure Search 如何得知應為哪些欄位建置建議。 此問題的答案就在您先前建立索引的過程中。 在 `CreateCatalogIndex`  Program.cs 檔案內的函式的 **CatalogIndexer** 專案中，屬性稱為 `Suggestions`。  只要此屬性設為 `True`，即表示 Azure Search 可使用此屬性作為擷取建議的欄位。

我們就來試試看。  

7.  同樣地，建置應用程式，然後按 **F5** 執行應用程式。

8.  在搜尋方塊中，輸入 "Road" 這個字。  您應可隨即看見項目清單顯示在文字方塊下方，並附有可供使用者選擇的建議。  

要加入中斷點至 `Suggest` 函式內 **HomeController.cs** 逐步執行 (F11) 為了建置建議清單而發出的每個呼叫。

示範就到此為止。 現在您已經逐一檢視過所有需要知道的主要作業流程，而可以開始建置使用 Azure Search 的 ASP.NET MVC4 應用程式。


<a id="err-mvc"></a>
## 如何解決「無法載入檔案或是組件 'System.Web.Mvc」

在打造 AdventureWorksWeb 時，如果出現「無法載入檔案或是組件 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 或其中一項相依性」的訊息時，請嘗試以下步驟來解決這個錯誤。

1. 開啟 [封裝管理員主控台: **工具** | **NuGet 封裝管理員** | **封裝管理員主控台**
2. 在 PM> 提示字元底下，輸入「Update-package -reinstall Microsoft.AspNet.Mvc」
3. 當系統要求您重新載入檔案，選擇 [ **全部**。
4. 重建方案，然後再試 **F5** 一次。


<a id="next-steps"></a>
## 後續步驟

如需其他自我學習，請考慮新增會在使用按一下其中一項搜尋結果時開啟的 [詳細資料] 頁面。 您可以執行下列動作加以準備：

+   閱讀有關 [查閱 API](http://msdn.microsoft.com/library/azure/dn798929.aspx) ，可讓您對 Azure 搜尋服務會傳回特定文件 (例如您可以傳送 productID) 進行查詢。
+   請嘗試在加入新的函式中 **HomeController.cs** 呼叫詳細資料的檔案。 新增對應 **Details.cshtml** 接收此查詢的結果，並顯示結果的檢視。
+   請查看下列額外的程式碼範例和視訊有關地理空間搜尋: [第 9 頻道-Azure Search 和地理空間資料](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) 和 [CodePlex: Azure 搜尋 GeoSearch 範例](http://azuresearchgeospatial.codeplex.com)

您也可以檢閱 [Azure 搜尋服務 REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx) MSDN 上。


<!--Anchors-->
[Prerequisites]: #sub-1
[Create an Azure Search index]: #sub-2
[Explore CatalogIndexer]: #sub-3
[Build an MVC4 Application using Azure Search]: #sub-4
[Explore AdventureWorksWeb]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-first-solution/AzureSearch_Create1_WebApp.PNG
[8]: ./media/search-create-first-solution/AzureSearch_Create1_ConsoleMsg.PNG
[9]: ./media/search-create-first-solution/AzureSearch_Create1_DashboardIndexes.PNG
[10]: ./media/search-create-first-solution/AzureSearch_Create1_WebAppEmpty.PNG
[11]: ./media/search-create-first-solution/AzureSearch_Create1_Suggestions.PNG
[12]: ./media/search-create-first-solution/AzureSearch_Create1_CodeplexDownload.PNG
