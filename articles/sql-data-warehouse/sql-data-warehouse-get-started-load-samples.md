<properties
   pageTitle="將範例資料載入 SQL 資料倉儲 | Microsoft Azure"
   description="了解如何將範例資料載入 SQL 資料倉儲"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="lodipalm;barbkess"/>

# 將範例資料載入 SQL 資料倉儲

當您建立 SQL 資料倉儲執行個體時，可以輕鬆地將某些範例資料載入其中。 如果您錯過這個步驟中佈建期間，您也可以 [手動載入範例資料] []。 

以下將提供如何在資料庫中載入 AdventureWorksDW 的簡單檢視。 此資料集是以虛構公司 (稱為 AdventureWorks) 的範例資料倉儲結構為其模型，其中包含代表該公司之銷售與客戶的資料。 

## 在建立期間加入範例資料
您可以使用下列步驟，確保可在部署期間，將範例資料載入 SQL 資料倉儲：   

1. 尋找 SQL 資料倉儲中的 [Azure 傳統入口網站] []，依序按一下 [+ 新增] 以啟動建立程序然後 '和資料儲存體' 或在 Marketplace 中搜尋 「 SQL 資料倉儲 」。 
 
2. 一旦啟動程序之後，請務必按一下 [選取來源] 選項並將它設定為「範例」。 如果您正在建立的並非新的伺服器，系統也會要求您針對正用來建立的伺服器提供登入。  


> [AZURE.NOTE] 以範例資料載入您的執行個體，您必須啟用 Azure 服務存取的伺服器 (這應該是在預設情況下建立新的伺服器時)。  如果不這麼做，則載入將會失敗，但您仍然可以 [手動載入範例資料] []。


## 使用 Power BI 分析 Adventureworks

使用範例資料集是開始使用 Power BI 的好方法。 在載入之後的範例資料，您可以開啟的連接到 SQL 資料倉儲，無論是按一下 'Power BI 中的開啟' 按鈕 Azure 傳統入口網站或移至 [Power BI] [] 和 [連接到 SQL 資料倉儲] []。 連接之後，應該會使用與您的資料倉儲相同的名稱來建立新的資料集。  為了讓分析更容易，我們建立了名為 'AggregateSales' 的檢視，以及數個可用來分析公司銷售的關鍵衡量標準。 您可以按一下此檢視的名稱來展開檢視，並查看其中包含的資料行，而且可遵循下列步驟來建立一些快速的視覺效果：

1. 一開始，我們可以按一下 [PostalCode] 和 [SalesAmount] 資料行，輕鬆地建立所有銷售的地圖。 Power BI 甚至會自動將此辨識為地理資料，並將它放入地圖中。 

2. 現在，如果您想要建立銷售橫條圖，只要按一下 [SalesAmount] 資料行，Power BI 將會為您自動建立。 您可藉由將 [CustomerIncome] 圖表拖曳到 [AggregateSales] 左邊的 [軸] 欄位來增加額外的深度，以便依客戶營收等級顯示銷售業績。

3. 最後，如果您想要建立銷售時間軸，只需按一下 [SalesAmount]、[OrderDate] 和 [折線圖] ([視覺效果] 下第二行中的第一個圖示) 即可。

您隨時都可以按一下左下角的 [儲存] 按鈕並將您的視覺效果儲存為報告，藉以儲存您的進度。 

## 連接並查詢您的範例

您也可以利用傳統的方式來分析範例資料。 中所述 [連接和查詢] [] 文件，您可以連接到此 SQL Server Data Tools 使用 Visual Studio 中的資料庫。 既然您已經將某些範例資料載入您的 SQL 資料倉儲中，就可以快速地執行幾個查詢以開始。 

我們可以執行簡單的 select 陳述式，來取得員工的所有資訊：

    SELECT * FROM DimEmployee;

我們也可以使用建構 (例如 GROUP BY) 執行更複雜的查詢， 來查看每一天所有銷售的總金額：

    SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
    FROM FactInternetSales
    GROUP BY OrderDateKey
    ORDER BY OrderDateKey;

我們甚至可以使用 WHERE 子句，來篩選出特定日期之前的訂單：

    SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
    FROM FactInternetSales
    WHERE OrderDateKey > '20020801'
    GROUP BY OrderDateKey
    ORDER BY OrderDateKey;

事實上，SQL 資料倉儲支援幾乎所有 T-SQL 建構都與 SQL Server，而且您可以找到一些差異我們 [移轉程式碼] [] 文件。  



## 後續步驟
既然我們已經提供您一些時間來準備與範例資料看看如何 [開發] []，[載入] [] 或者 [移轉] []。

<!--Image references-->

<!--Article references-->
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-overview-load.md
[connect and query]: ./sql-data-warehouse-get-started-connect.md
[migrate code]: ./sql-data-warehouse-migrate-code.md
[load sample data manually]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Azure Classic Portal]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: ./sql-data-warehouse-integrate-power-bi.md

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/download/details.aspx?id=36433/

<!--Other Web references-->
[Sample Data Scripts]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip/ 

