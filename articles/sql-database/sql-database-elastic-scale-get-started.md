<properties 
    pageTitle="開始使用彈性資料庫工具" 
    description="Azure SQL Database 彈性資料庫工具功能的基本解說，包括易於執行的範例應用程式。" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="ddove" 
    editor="sidneyh"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="ddove;sidneyh"/>


# 開始使用彈性資料庫工具

本文件將執行範例應用程式介紹開發人員使用經驗。 範例會建立簡易的分區化應用程式，並探討彈性資料庫工具的主要功能。 此範例會示範函式的 [彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)

## 必要條件

1. 需要具有 C# 的 Visual Studio 2012 或更新版本。 下載免費版本在 [Visual Studio 下載](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)。
2. Nuget 2.7 或更新版本。 若要取得最新版本，請參閱 [安裝 NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## 下載及執行範例應用程式

[**Azure SQL 與彈性資料庫 - 開始使用**] 範例應用程式會解說使用 Azure SQL 彈性資料庫工具開發分區化應用程式時最重要的開發經驗層面。 將重點放在主要使用案例 [分區對應管理](sql-database-elastic-scale-shard-map-management.md), ，[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md) 和 [多分區查詢](sql-database-elastic-scale-multishard-querying.md)。 若要下載及執行範例，請遵循下列步驟：

1. 開啟 Visual Studio，然後選取 [檔案] -> [新增] -> [專案]****。
2. 在對話方塊中，按一下 [線上]****。

    ![New Project>Online][2]
3. 然後，按一下 [範例]**** 下的 [Visual C#]****。

    ![Click Visual C#][3]
4. 在搜尋方塊中，輸入 **elastic db** 尋找範例。此時會出現標題 [Azure SQL 的彈性資料庫工具 - 開始使用]****。

    ![Search Box][1]

5. 選取範例、選擇新專案的名稱和位置，然後按 [確定]**** 以建立專案。
6. 在範例專案的解決方案中開啟 **app.config** 檔案，然後依照檔案中的指示新增您的 Azure SQL 資料庫伺服器名稱和您的登入資訊 (使用者名稱和密碼)。
7. 建置並執行應用程式。 當系統要求時，請允許 Visual Studio 還原解決方案的 NuGet 套件。 這將會從 NuGet 下載最新版的彈性資料庫用戶端程式庫。
8. 以不同的選項執行，以深入了解用戶端程式庫功能。 請記下應用程式在主控台輸出中採用的步驟，並盡情探索其後的程式碼。

    ![progress][4]

恭喜您 – 您已在 Azure SQL Database 上成功建置並執行第一個使用彈性資料庫工具的分區化應用程式。 請使用 Visual Studio 或 SQL Server Management Studio 連接到您的 Azure DB Server，以快速瀏覽範例所建立的分區。 您會看見範例所建立的新範例分區資料庫和分區對應管理員資料庫。

**注意**   如果您沒有 SQL Server Management Studio，請參閱 [管理 Azure SQL Database 使用 SQL Server Management Studio](sql-database-manage-azure-ssms.md), ，其中包含取得此工具的指示。

### 程式碼範例的主要部分

1. **管理分區和分區對應**：此程式碼會解說如何使用 **ShardMapManagerSample.cs** 檔案中的分區、範圍和對應。 您可以找到此說明主題的詳細資訊: [分區對應管理](http://go.microsoft.com/?linkid=9862595)。
2. **資料相依路由**：**DataDependentRoutingSample.cs** 中說明如何將交易路由至正確的分區。 如需詳細資訊，請參閱 [資料相依路由](http://go.microsoft.com/?linkid=9862596)。
3. **查詢多個分區**：**MultiShardQuerySample.cs** 檔案中說明如何在各個分區間進行查詢。 如需詳細資訊，請參閱 [多分區查詢](http://go.microsoft.com/?linkid=9862597)。
4. **新增空分區**: 反覆新增空分區中的程式碼執行
檔案 **AddNewShardsSample.cs**。 本主題的詳細資料請見: [分區對應管理](http://go.microsoft.com/?linkid=9862595)。

### 其他 Elastic Scale 作業

1. **分割現有的分區**：分割分區的功能是透過 [分割合併工具]**** 來提供。 您可以找到更多有關此工具: [分割合併工具概觀](sql-database-elastic-scale-overview-split-and-merge.md)。
2. **合併現有的分區**：分區合併也可使用 [分割合併工具]**** 來執行。 如需詳細資訊，請參閱: [分割合併工具概觀](sql-database-elastic-scale-overview-split-and-merge)。


## 成本

彈性資料庫工具是免費的。 彈性資料庫工具不會在您的 Azure 使用成本以外收取其他費用。

例如，範例應用程式會建立新資料庫。 其費用將取決於您所選擇的 Azure SQL DB 資料庫版本，以及您的應用程式的 Azure 使用量。

如需定價資訊，請參閱 [SQL Database 定價詳細資料](http://azure.microsoft.com/pricing/details/sql-database/)。

## 後續步驟

如需有關彈性資料庫工具的詳細資訊，請參閱：

* [彈性資料庫工具文件地圖](sql-database-elastic-scale-documentation-map.md)
-    Code Samples: 
    -    [Elastic DB with Azure SQL - Getting Started](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Elastic DB with Azure SQL - Integrating with Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Shard Elasticity on Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)

-    Blog: [Elastic Scale Announcement](http://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)

-    Channel 9: [Elastic Scale Overview Video](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)

-    Discussion Forum: [Azure SQL Database forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)






[the elastic scale sample application]: #The-Elastic-Scale-Sample-Application 
[download and run the sample app]: #Download-and-Run-the-Sample-App 
[cost]: #Cost 
[next steps]: #next-steps 
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png 
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png 
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png 
[4]: ./media/sql-database-elastic-scale-get-started/output2.png 

