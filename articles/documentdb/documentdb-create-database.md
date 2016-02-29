<properties 
    pageTitle="如何在 DocumentDB 中建立資料庫 |Microsoft Azure" 
    description="了解如何使用 Azure DocumentDB 的線上服務入口網站 (其為一種 JSON 的 NoSQL 文件資料庫)，來建立受管理的資料庫。 立即取得免費試用版。" 
    keywords="how to create a database" 
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/18/2015" 
    ms.author="mimig"/>

# 如何建立 DocumentDB 資料庫

若要使用 Microsoft Azure DocumentDB，您必須擁有 [DocumentDB 帳戶](documentdb-create-account.md), 、 資料庫、 集合和文件。  本主題說明如何在 Microsoft Azure 入口網站中建立 DocumentDB 資料庫。 

![螢幕擷取畫面示範建立資料庫的方法，並反白顯示 [瀏覽] 按鈕、[瀏覽] 刀鋒視窗上的 DocumentDB 帳戶，及 [DocumentDB 帳戶] 刀鋒視窗上的 DocumentDB 帳戶等](./media/documentdb-create-database/docdb-database-creation-1-3.png)

1.  在 [Azure 入口網站](https://portal.azure.com/), ，在 Jumpbar 中按一下 [ **DocumentDB 帳戶**。 

2.  在 **DocumentDB 帳戶** 分頁中，選取要在其中新增 DocumentDB NoSQL 帳戶資料庫。 如果您沒有任何列出的帳戶，您將需要 [建立 DocumentDB 帳戶](documentdb-create-account.md)。

3. 在 **DocumentDB 帳戶** 刀鋒視窗中，按一下 [ **將資料庫加入**。

4. 在 **將資料庫加入** 刀鋒視窗中，輸入您的新資料庫的識別碼。 驗證名稱時，綠色的核取記號會出現在 **識別碼** 方塊。

5. 按一下 [ **確定** 以建立新資料庫螢幕底部。 

7. 新的資料庫現在會出現在 **資料庫** ] 透鏡上 **DocumentDB 帳戶** 刀鋒視窗。
 
    ![[DocumentDB 帳戶] 刀鋒視窗中的新資料庫螢幕擷取畫面](./media/documentdb-create-database/docdb-database-creation-7.png)

## 建立 DocumentDB 資料庫的其他方法

不需要使用入口網站來建立資料庫，您也可以建立這些使用 [DocumentDB Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 如需 C# 程式碼範例示範如何使用 DocumentDB.NET SDK 建立資料庫，請參閱 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) DatabaseManagement 專案中可用檔案中的 [azure azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 上的儲存機制 [GitHub.com](https://github.com)。 

## 後續步驟

既然您知道如何建立 documentdb 資料庫下, 一個步驟是 [建立集合](documentdb-create-collection.md)。

您的集合建立後，您可以 [新增 JSON 文件](../documentdb-view-json-document-explorer.md) 在網站中，使用 [文件總管] 中 [匯入文件](documentdb-import-data.md) 集合使用 DocumentDB 資料移轉工具，或使用其中一種 [DocumentDB Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx) 執行 CRUD 作業。 DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。 .NET 程式碼範例示範如何建立、 移除、 更新和刪除文件，請參閱 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) GitHub.com 上的 azure azure-documentdb-net 儲存機制中 DocumentManagement 專案中。  

集合中有文件之後，您可以使用 [DocumentDB SQL](documentdb-sql-query.md) 到 [執行查詢](documentdb-sql-query.md#executing-queries) 針對文件使用 [查詢總管](documentdb-query-collections-query-explorer.md) 入口網站中，在 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx), ，或其中一個 [Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 

