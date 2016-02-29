<properties 
    pageTitle="建立 DocumentDB 資料庫集合 | Microsoft Azure" 
    description="了解如何使用 Azure DocumentDB 的線上服務入口網站、JSON 的受管理 NoSQL 文件資料庫，來建立集合。 立即取得免費試用版。" 
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
    ms.date="09/28/2015" 
    ms.author="mimig"/>

# 建立 DocumentDB 集合

若要使用 Microsoft Azure DocumentDB，您必須擁有 [DocumentDB 帳戶](documentdb-create-account.md), 、 [資料庫](documentdb-create-database.md), 、 集合和文件。 本主題說明如何在 Azure 入口網站中建立 DocumentDB 集合。 

![螢幕擷取畫面，其中在 [資料庫] 透鏡裡反白顯示動態工具列的 [DocumentDB 帳戶]、[DocumentDB 帳戶] 刀鋒視窗中的帳戶和 [DocumentDB 帳戶] 刀鋒視窗中的資料庫](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  在 [Azure 入口網站](https://portal.azure.com/), ，在 Jumpbar 中按一下 [ **DocumentDB 帳戶**。 

2.  在 **DocumentDB 帳戶** 刀鋒視窗中，選取要在其中新增集合的帳戶。 如果您沒有任何列出的帳戶，您將需要 [建立 DocumentDB 帳戶](documentdb-create-account.md)。

3. 在 **DocumentDB 帳戶** 刀鋒視窗中選取帳戶，向下捲動至 **資料庫** 鏡頭，然後選取要在其中新增集合的資料庫。
    
4. 在 **資料庫** 刀鋒視窗中，按一下 [ **將集合加入**。

    ![螢幕擷取畫面，其中反白顯示 [資料庫] 刀鋒視窗上的 [新增集合] 按鈕、[新增集合] 刀鋒視窗上的設定，及 [確定] 按鈕](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. 在 **新增集合** 刀鋒視窗中，輸入您的新集合的識別碼。 驗證名稱時，[識別碼] 方塊中會出現綠色的核取記號。

6. 選取新集合的定價層。 您所建立的每個集合會是可計費的實體。 如需可用效能層級的詳細資訊，請參閱 [DocumentDB 中的效能層級](documentdb-performance-levels.md)。

7. 選取下列其中一種 **索引編製原則**。 

    - **預設**。 當您使用 ORDER BY、範圍和數字的相等查詢針對字串進行相等查詢時，這是最佳的原則。  此原則不會有較低的索引儲存空間負擔比 **範圍**。
    - **雜湊**。 當您針對數字和字串執行相等查詢時，這是最佳的原則。  這個原則的索引儲存空間負擔最低。
    - **範圍**。 當您使用 ORDER BY、範圍以及數字和字串的相等查詢時，這是最佳的原則。  此原則不會有較高的索引儲存空間負擔比 **預設** 或 **雜湊**。

    如需索引編製原則的詳細資訊，請參閱 [DocumentDB 索引編製原則](documentdb-indexing-policies.md)。

8. 按一下 [ **確定** 來建立新的集合在螢幕的底部。 


9. 新的集合現在會出現在 **集合** ] 透鏡上 **資料庫** 刀鋒視窗。
 
    ![[資料庫] 刀鋒視窗中新集合的螢幕擷取畫面](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## 建立 DocumentDB 集合的其他方法

不需要使用入口網站來建立集合，您也可以建立這些使用 [DocumentDB Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 如需 C# 程式碼範例示範如何使用 DocumentDB.NET SDK 建立集合，請參閱 [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) CollectionManagement 專案中可用檔案中的 [azure azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 上的儲存機制 [GitHub.com](https://github.com)。

## 後續步驟

既然您已有了集合，下一個步驟是將文件加入或滙入集合。 談到將文件加入集合時，您會有幾個選擇：

- 您可以 [新增文件](../documentdb-view-json-document-explorer.md) 入口網站中使用 Document Explorer。
- 您可以 [匯入文件和資料](documentdb-import-data.md) 使用 DocumentDB 資料移轉工具，可讓您從 SQL Server、 MongoDB、 Azure 資料表儲存體和其他 DocumentDB 集合匯入 JSON 和 CSV 檔案，以及資料。 
- 您可以使用其中一種加入文件或 [DocumentDB Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。  [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) 中 DocumentManagement 專案中可用的檔案 [azure azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) 上的儲存機制 [GitHub.com](https://github.com), ，示範如何使用 DocumentDB.NET SDK 的文件上的 CRUD 作業。

集合中有文件之後，您可以使用 [DocumentDB SQL](documentdb-sql-query.md) 到 [執行查詢](documentdb-sql-query.md#executing-queries) 針對文件使用 [查詢總管](documentdb-query-collections-query-explorer.md) 入口網站中，在 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx), ，或其中一個 [Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 

