<properties
    pageTitle="在入口網站中建立 Azure 搜尋服務索引 | Microsoft Azure | 雲端託管搜尋服務"
    description="在 Azure 傳統入口網站中填入欄位定義，即可為 Azure 搜尋服務 (雲端託管搜尋服務) 加入索引。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="11/04/2015"
    ms.author="heidist"/>

# 在 Azure 傳統入口網站中建立 Azure 搜尋服務索引
> [AZURE.SELECTOR]
- [概觀](search-what-is-an-index.md)
- [入口網站](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST API](search-create-index-rest-api.md)

只需在 Azure 傳統入口網站中建立一個索引，即可快速地在 Azure 搜尋服務中建立一個索引原型。 入口網站十分適合用來進行概念證明測試，對於已部署到您服務中的任何索引，您也可以透過入口網站檢視任一索引的結構描述定義和資源使用狀況。

若要完成此工作，請先確定您已擁有可供使用的 Azure 搜尋服務。 請參閱 [入口網站中建立 Azure 搜尋服務](search-create-service-portal.md) 如果您需要設定的說明。

1. 登入 [Azure 傳統入口網站](https://portal.azure.com)。

2. 開啟 Azure 搜尋服務的服務儀表板。 以下提供一些尋找儀表板的方法。
    - 在 Jumpbar 中按一下 [ **首頁**。 首頁上有您訂用帳戶中每個服務的磚。 按一下圖格即可開啟服務儀表板。
    - 在 Jumpbar 中按一下 [ **全部瀏覽** > **篩選** > **搜尋服務** 在清單中尋找您的搜尋服務。

3. 在服務儀表板中，您會看到命令列在頂端，其中包括 **新增索引**。

    查看定價層。 如果您使用免費版，您最多可以有 3 個索引。 您可能需要刪除一個索引以釋放空間。

     ![][1]

4. 若要刪除索引，請按一下索引即可滑動開啟刀鋒視窗。 按一下 [ **刪除**。

     ![][2]

5. 若要在入口網站中建立新的索引，請按一下 [ **新增索引** 並指定其名稱，例如 *旅館*。

    建立索引可能需要一些時間，一旦可供使用時，就會出現在索引清單上。

6. 按一下 [ **旅館** 開啟索引定義刀鋒視窗。

    當您在入口網站中建立索引時，將會為您建立所需欄位 (id)。 此為索引鍵欄位，用於唯一識別各個文件。 每個索引鍵 (不包括複合索引鍵) 只有一個欄位，而且一律為字串。

    如果您想在建立索引期間重新命名索引鍵欄位，那麼務必立即執行此步驟。 索引建立之後，您就無法重新命名該欄位。

    ![][3]

7. 若要編輯欄位名稱，請按一下欄位清單中的向右箭號。

8. 取代 **識別碼** 與 **hotelId**。

9. 按一下 [ **確定** （欄位和索引） 來建立索引的每個分頁上。

## 新增欄位

在 Azure 搜尋服務中，預設會啟用索引屬性，像是可搜尋的、可建立 Facet 屬性的，以及可篩選的。 當您設定這些屬性時，通常會關閉不合理的搜尋行為 (例如對描述排序或建立 Facet 屬性)。

入口網站則不同。 在入口網站中，預設會關閉搜尋行為，以便您可以逐項為不同欄位選取所有適用的行為。

1. 按一下 [ **新增/編輯欄位** 新增更多欄位。 在此練習中，我們將重新建立 *旅館* 索引文件所述 [如何搭配 Azure 搜尋服務使用 Fiddler](search-fiddler.md)。

    ![][4]

2. 新增並設定欄位以完成結構描述。

    ![][5]

    檢閱 [命名規則](https://msdn.microsoft.com/library/azure/dn857353.aspx) 和 [支援的資料類型](https://msdn.microsoft.com/library/azure/dn798938.aspx) 如欄位名稱和類型的參考資訊。

    索引屬性是由下列項目組成：

    - **擷取** 指定欄位是否可以傳回搜尋結果中。
    - **可篩選** 允許欄位可用於 **$filter** 查詢。
    - **可排序** 允許欄位可用來做為排序選項。
    - **可多面向** 允許欄位用於自我引導篩選多面向導覽結構。 通常，欄位若包含您可以用來將多份文件群組在一起的重複值 (例如，落在單一產品或服務類別目錄下的多份文件)，最適合做為 Facet。
    - **索引鍵** 是用於查閱文件每份文件的唯一識別碼。 每個索引必須有一個索引鍵。 只有一個欄位可以是索引鍵，而且它必須設定為 Edm.String。
    - **可搜尋** 將欄位標示為全文檢索搜尋。

3. 若要移除您不想，以滑鼠右鍵按一下並選取任何欄位 **刪除**。

4. 按一下 [ **確定** 以儲存您剛的索引定義，然後按一下 **確定** 在新增索引] 頁面上，來建立索引。


## 後續步驟

雖然已定義索引，但仍需事先載入文件才能供人使用。 假設您要重新建立 Hotels 索引，用於測試目的，您就能輕鬆載入文件中，該索引的少量 [Fiddler](search-fiddler.md), ，使用載入文件一節中的指示 [如何搭配 Azure 搜尋服務使用 Fiddler](search-fiddler.md)。 接著您可以按照該篇文章中的剩餘步驟執行一些查詢。

一旦您熟悉基本的索引，請考慮新增語言分析器或建議工具，以新增多語言支援或預先輸入建議。 索引結構描述中提供這兩項功能的詳細說明。 請參閱 [語言支援](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) 和 [Create Index](https://msdn.microsoft.com/library/azure/dn798941.aspx) 如需詳細資訊。

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG


