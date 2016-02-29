<properties
    pageTitle="將 Excel 連接到 SQL Database | Microsoft Azure"
    description="了解如何將 Microsoft Excel 連接到雲端的 Azure SQL Database。 將資料匯入 Excel 中進行報告和資料探索。"
    services="sql-database"
    keywords="connect excel to sql, import data to excel"
    documentationCenter=""
    authors="joseidz"
    manager="jeffreyg"
    editor="jeffreyg"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/09/2015"
    ms.author="joseidz"/>


# 將 Excel 連接到 Azure SQL Database 並建立報告 

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

了解如何將 Excel 連接到 SQL Database 以便將資料匯入 Excel。 然後透過資料建立報告。

您需要先有 SQL Database。 如果您沒有帳戶，請參閱 [建立您的第一個 SQL database](sql-database-get-started.md) 取得範例資料的資料庫啟動並執行幾分鐘的時間。 在本文中，您會將該文章中的範例資料匯入 Excel 中，但是您可以依照類似的步驟並使用您自己的資料來執行。

您也會需要 Excel。 本文使用 [Microsoft Excel 2016](https://products.office.com/en-US/)。

## 將 Excel 連接到 SQL Database 並建立報告

1.  若要將 Excel 連接到 SQL Database，請開啟 Excel，然後建立新的活頁簿。
        或者，開啟您想要連接到 SQL database 的現有 Excel 活頁簿。

2.  在頁面頂端的功能表列中按一下 **資料**, ，按一下 [ **從其他來源**, ，然後按一下 [ **從 SQL Server**。

    ![選取資料來源：將 Excel 連接到 SQL Database。](./media/sql-database-connect-excel/excel_data_source.png)

    資料連線精靈隨即開啟。

3.  在 **連接到資料庫伺服器** ] 對話方塊中，輸入 **伺服器名稱** 裝載您想要在表單中連接到邏輯伺服器 **<*servername*>。 database.windows.net**。 例如， **adventureserver.database.windows.net**。

4.  在 **登入認證** 區段中，按一下 **使用下列的使用者名稱和密碼**, ，型別 **使用者名稱** 和 **密碼** 您設定 SQL Database 伺服器時所建立，然後再按一下 **下一步**。

    > [AZURE.TIP] 同時 [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) 和 [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) Excel 的增益集有相似的經驗。

5. 在 **選取資料庫及資料表** 對話方塊中，選取 **AdventureWorks** 資料庫從下拉功能表，然後選取 **vGetAllCategories** 從清單中的資料表和檢視表，然後按一下 [ **下一步**。

    ![選取資料庫和資料表]。[] 5

6. 在 **儲存資料連線檔案和完成** ] 對話方塊中，按一下 [ **完成**。

7. 在 **匯入資料** 對話方塊中，選取 **樞紐分析圖** 按一下 **確定**。

    ![資料匯入 Excel: 在匯入資料] 對話方塊選取樞紐分析圖。][] 2

8. 在 **樞紐分析圖欄位** ] 對話方塊中，選取下列組態來建立的每個分類的產品計數報告。

    ![設定資料庫的報表]。[] 3

    成功案例看起來像這樣：

    ![成功: Excel 連接到 SQL 資料庫。][] 4

## 後續步驟

如果您是軟體即服務 (SaaS) 開發人員，了解 [彈性資料庫集區](sql-database-elastic-pool.md)。 您可以輕鬆地管理大型資料庫使用集合 [彈性資料庫工作](sql-database-elastic-jobs-overview.md)。

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

