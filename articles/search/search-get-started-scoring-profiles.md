<properties 
    pageTitle="如何在 Azure 搜尋服務中使用評分設定檔 | Microsoft Azure | 雲端託管搜尋服務" 
    description="在 Azure 搜尋服務 (Microsoft Azure 上之託管的雲端搜尋服務) 中透過評分設定檔調整搜尋排名。" 
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


# 如何使用 Azure Search 中的評分設定檔

評分設定檔是 Microsoft Azure 搜尋服務功能，可自訂搜尋分數的計算，進而影響項目在搜尋結果清單中的排序方式。 您可以提升符合預先定義之準則的項目，將評分設定檔視為產生相關性模型的方法。 例如，假設您的應用程式是線上旅館預訂網站。 藉由提升 `位置` 欄位中，搜尋包含在西雅圖一詞像西雅圖會導致更高的分數的項目 `位置` 欄位。 請注意，您可以有多個評分設定檔，而如果預設評分對您的應用程式而言就已足夠，亦可完全沒有。

為了協助您實驗評分設定檔，您可以下載範例應用程式，以使用評分設定檔來變更搜尋結果的排名順序。 此範例是學習工具仍是一個主控台應用程式-或許不是非常實際真實世界應用程式開發-但很有用。

範例應用程式來示範評分行為，使用虛構的資料，稱為 `musicstoreindex`。 簡單的範例應用程式便於修改評分設定檔和查詢，而接著便可查看執行程式後對於排名順序的立即效果。

<a id="sub-1"></a>
## 必要條件

範例應用程式是使用 Visual Studio 2013 以 C# 撰寫。 請試用免費 [Visual Studio 2013 Express 版](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) 如果您還沒有 Visual Studio 的複本。

您需要 Azure 訂用帳戶和 Azure 搜尋服務來完成教學課程。 請參閱 [入口網站中建立搜尋服務](search-create-service-portal.md) 有關設定此服務。

[AZURE.INCLUDE [You need an Azure account to complete this tutorial:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## 下載範例應用程式

移至 [Azure Search 評分設定檔示範](https://azuresearchscoringprofiles.codeplex.com/) codeplex 下載本教學課程中所述的範例應用程式。

在 [原始程式碼] 索引標籤上，按一下 [**下載**] 以取得解決方案的壓縮檔。

 ![][12]

<a id="sub-3"></a>
## 編輯 app.config

1. 將檔案解壓縮之後，請在 Visual Studio 中開啟解決方案以編輯組態檔。
1. 在 [方案總管] 中，按兩下 **app.config**。 此檔案會指定服務端點和 `api 金鑰` 可用來驗證要求。 您可以從傳統入口網站取得這些值。
1. 登入 [Azure 傳統入口網站](https://portal.azure.com)。
1. 移至 Azure Search 的服務儀表板。
1. 按一下 [**屬性**] 磚來複製服務 URL
1. 按一下 [ **金鑰** ] 磚來複製 `api 金鑰`。

當您完成新增 URL 和 `api 金鑰` 為 app.config，應用程式設定應該看起來像這樣:

   ![][11]


<a id="sub-4"></a>
## 探索應用程式

您已經快要可以建置並執行應用程式，但在這麼做之前，請先查看用來建立及擴展索引的 JSON 檔案。

**Schema.json** 會定義索引，包括此示範中強調的評分設定檔。 請注意，結構描述定義的所有欄位中的索引，包括不可搜尋的欄位，例如使用 `邊界`, ，可用於評分設定檔。 評分設定檔語法記載於 [評分設定檔新增至 Azure Search 索引](http://msdn.microsoft.com/library/azure/dn798928.aspx)。

**Data1-3.json** 會提供資料 (跨多種內容類型的 246 張專輯)。 資料是實際專輯和藝術工作者資訊，其中等虛構欄位的組合 `價格` 和 `邊界` 用來說明搜尋作業。 資料檔案符合索引並且會上傳到 Azure Search 服務。 資料上傳並編成索引之後，您即可對其發出查詢。

**Program.cs** 會執行下列作業：

- 開啟主控台視窗。

- 使用連接到 Azure Search 服務 URL 和 `api 金鑰`。

- 刪除 `musicstoreindex` 如果存在的話。

- 建立新 `musicstoreindex` 使用 schema.json 檔案。

- 使用資料檔案擴展索引。

- 使用四個查詢來查詢索引。 請注意，評分設定檔會被指定為查詢參數。 所有查詢都會搜尋相同的字詞 'best'。 第一個查詢示範預設評分。 其餘的三個查詢則使用評分設定檔。

<a id="sub-5"></a>
## 建置並執行應用程式

若要排除連接或組件參考問題，請建置並執行應用程式，以確保沒有需要先處理的問題。 您應該會看到在背景中開啟的主控台應用程式。 所有四個查詢會依序執行，而不會暫停。 在許多系統上，整個程式會在 15 秒內執行。 如果主控台應用程式包含以下訊息：「完成。 按 Enter 鍵繼續」，則表示程式順利完成。

若要比較查詢執行，您可以從主控台標示和複製查詢結果並貼到 Excel 檔案中。

下圖並排顯示前三個查詢的結果。 所有查詢都使用相同的搜尋字詞 'best'，該字詞會出現在許多專輯標題中。

   ![][10]

第一個查詢使用預設評分。 因為搜尋字詞只會出現在專輯標題中，且未指定任何其他準則，所以會依找到的順序傳回專輯標題中有 'best' 的項目。

第二個查詢使用評分設定檔，但請注意，設定檔沒有任何作用。 結果與第一個查詢的結果相同。 這是因為評分設定檔提升與查詢密無關的欄位 ('genre')。 搜尋字詞 'best' 不存在於任何文件的任何 'gene' 欄位中。 當評分設定檔沒有任何作用時，結果會與預設評分相同。

第三個查詢是評分設定檔影響的第一個證據。 搜尋字詞仍是 'best'，所以我們目前是處理同一組專輯，但因為評分設定檔提供了可提升 'rating' 和 'last-updated' 的額外準則，所以清單中有些項目會向上推。

下圖顯示第四個 (也是最後一個) 查詢 (由 'margin' 提升)。 'margin' 欄位不可搜尋且不能在搜尋結果中傳回。 'margin' 值會以手動方式加入試算表，協助說明具有較高邊際收益的項目會在搜尋結果清單中顯示於較前面。

   ![][9]

您現在已實驗評分設定檔，請嘗試變更程式，以使用不同的查詢語法、評分設定檔或更豐富的資料。 下一節中的連結提供詳細資訊。

<a id="next-steps"></a>
## 後續步驟

進一步了解評分設定檔。 請參閱 [評分設定檔新增至 Azure Search 索引](http://msdn.microsoft.com/library/azure/dn798928.aspx) 如需詳細資訊。

深入了解搜尋語法和查詢參數。 請參閱 [搜尋文件 (Azure 搜尋 REST API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) 如需詳細資訊。

需要回過頭來深入了解如何建立索引嗎？ [觀看這段影片](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) 基本知識。




[prerequisites]: #sub-1 
[download the sample application]: #sub-2 
[edit app.config]: #sub-3 
[explore the application]: #sub-4 
[build and run the application]: #sub-5 
[next steps]: #next-steps 
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG 
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG 
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG 
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG 

