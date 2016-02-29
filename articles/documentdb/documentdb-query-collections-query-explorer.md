<properties 
    pageTitle="使用 [查詢總管]，針對 DocumentDB 集合建立、編輯和執行 SQL 查詢 | Microsoft Azure" 
    description="深入了解 DocumentDB 查詢總管，它是一種 Azure 入口網站的工具，可用來針對 DocumentDB 集合建立、編輯和執行 SQL 查詢。" 
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="12/01/2015" 
    ms.author="anhoh"/>

# 使用 [查詢總管]，針對 DocumentDB 集合建立、編輯和執行 SQL 查詢 #

這篇文章概述 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) 查詢總管] 中，Microsoft Azure 入口網站工具，可讓您建立、 編輯和執行查詢，針對 DocumentDB 集合。 

完成本教學課程後，您將能夠回答下列問題：  

-   如何透過網頁瀏覽器輕鬆地針對 DocumentDB 集合建立、編輯和執行查詢？
-   如何透過網頁瀏覽器輕鬆地瀏覽 DocumentDB 查詢結果頁面？
-   如何疑難排解 DocumentDB 查詢的語法錯誤？ 

##<a id="Launch"></a>啟動並瀏覽 查詢總管##

您可以從任何 DocumentDB 帳戶、資料庫和集合分頁啟動 [查詢總管] 。
  
1. 在 DocumentDB 帳戶或資料庫刀鋒視窗頂端，只要按一下 **查詢總管** 命令。

    ![[查詢總管] 命令的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)

2. 此外，每個刀鋒視窗底部附近看到是 **開發人員工具** 透鏡，其中包含 **查詢總管** 並排顯示。
    
    ![[查詢總管] 部件的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorerpart.png) 

2. 您只需按一下磚便可啟動 [查詢總管]。

     **資料庫** 和 **集合** 下拉式清單方塊會根據您要啟動 [查詢總管] 的內容預先填入。  例如，如果您要從資料庫刀鋒視窗啟動，則系統會預先填入目前資料庫。 如果您要從集合刀鋒視窗啟動，則系統會預先填入目前集合。

    ![[查詢總管] 的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)

##<a id="Create"></a>使用查詢總管來建立、編輯和執行查詢##

[查詢總管] 可讓您輕鬆地針對 DocumentDB 集合建立、編輯和執行查詢，並包含醒目提示的基本關鍵字和值以改善查詢撰寫經驗。  

- 初次開啟 [查詢總管] 時，系統會提供 SELECT * FROM 的預設查詢。  您可以接受預設查詢或建構自己，然後按一下 **執行查詢** ] 按鈕以檢視結果。 查詢總管] 支援 DocumentDB SQL 查詢語言中所述 [查詢 DocumentDB](documentdb-sql-query.md)。

    ![[查詢總管] 查詢結果的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryresults1.png) 

- 您也可以輸入多個查詢、 反白一個想要執行，並再按 **執行查詢** ] 按鈕以檢視結果。

    ![[查詢總管] 反白與執行的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png) 

- 您可以載入現有的檔案使用的內容 **載入檔案** 命令。

    ![[查詢總管] 載入檔案的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/loadqueryfile.png) 

- 根據預設，[查詢總管] 會傳回結果，以 100 為一組。  如果您的查詢產生超過 100 個結果，只需使用 **下一頁** 和 **上一頁** 命令即可瀏覽結果集。

    ![[查詢總管] 分頁支援的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

- 成功的查詢提供的資訊，例如顯示要求費用、 目前結果集，以及是否有更多結果，然後可以透過存取 **下一頁** 命令，如先前所述。

    ![[查詢總管] 查詢資訊的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryinformation.png)

- 同樣地，如果查詢完成但發生錯誤，則 [查詢總管] 會顯示可協助疑難排解工作的錯誤清單。

    ![[查詢總管] 查詢錯誤的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryerror.png)

##<a name="NextSteps"></a>後續步驟

- 若要深入了解 DocumentDB，請按一下 [這裡](http://azure.com/docdb)。
- 若要深入了解 DocumentDB SQL 文法支援查詢總管] 中，按一下 [ [這裡](documentdb-sql-query.md)。
 

