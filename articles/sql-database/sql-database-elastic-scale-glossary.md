<properties 
    pageTitle="彈性資料庫工具字彙 | Microsoft Azure" 
    description="彈性資料庫工具所用詞彙的解釋" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="ddove;sidneyh"/>

# 彈性資料庫工具字彙
下列詞彙定義 [彈性資料庫工具](sql-database-elastic-scale-introduction.md), ，Azure SQL Database 功能之一。 這些工具用來管理 [分區對應](sql-database-elastic-scale-shard-map-management.md), ，並包含 [用戶端程式庫](sql-database-elastic-database-client-library.md), 、 [分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md), ，[彈性集區](sql-database-elastic-pool.md), ，和 [查詢](sql-database-elastic-query-overview.md)。 

中使用這些詞彙 [使用彈性資料庫工具加入分區](sql-database-elastic-scale-add-a-shard.md) 和 [使用 RecoveryManager 類別來修正分區對應問題](sql-database-elastic-database-recovery-manager.md)。

![彈性擴縮詞彙][1]

**資料庫**: Azure SQL 資料庫。 

**資料相依路由**︰ 可讓應用程式連接到分區的特定分區化索引鍵的功能。 若要比較 **多分區查詢**。

**全域分區對應**︰ 分區化索引鍵在其個別分區之間的對應 **分區集**。 全域分區對應會儲存在 **分區對應管理員**。 若要比較 **本機分區對應**。

**清單分區對應**︰ 分區化索引鍵個別對應的分區對應。 若要比較 **範圍分區對應**。   

**本機分區對應**︰ 儲存在分區上，本機分區對應包含位於分區上之 shardlet 的對應。

**多分區查詢**︰ 能夠對多個分區，發出查詢使用 UNION ALL 語意 （semantics） （也稱為 「 展開傳送查詢 」） 傳回結果集。 若要比較 **資料相依路由**。

**範圍分區對應**︰ 分區分佈策略根據多個連續的數值範圍分區對應。 

**參考資料表**︰ 不分區化，而跨分區複寫的資料表。 例如，郵遞區號可以儲存參考資料表中。 

**分區**︰ 儲存來自分區化的資料集之資料的 Azure SQL 資料庫。 

**分區彈性**︰ 能夠執行 **水平縮放** 和 **垂直縮放**。

**分區化資料表**︰ 分區化，亦即，其中的資料分佈至分區的分區化索引鍵值為基礎的資料表。 

**分區化索引鍵**︰ 決定資料如何分佈至分區的資料行值。 實值型別可以是下列其中之一 ︰ **int**, ，**bigint**, ，**varbinary**, ，或 **uniqueidentifier**。 

**分區集**︰ 屬於相同分區對應的分區對應管理員中的分區集合。  

**Shardlet**︰ 所有分區上的分區化索引鍵的單一值相關聯的資料。 在重新分佈分區化資料表時，Shardlet 是可能的資料移動最小單位。 

**分區對應**︰ 一組分區化索引鍵及其個別分區之間的對應。

**分區對應管理員**︰ 管理物件和資料存放區，其中包含分區對應、 分區位置，以及一或多個分區集的對應。

![對應][2]


##動詞

**水平縮放**︰ 的相應放大 （或縮小） 動作藉由新增或移除分區對應的分區，如下所示的分區集合。

![水平和垂直縮放][3]

**合併**︰ 兩個分區的 shardlet 移至一個分區並相應地更新分區對應的動作。

**Shardlet 移動**︰ 單一 shardlet 移至不同的分區的動作。 

**分區**︰ 跨多個分區化索引鍵為基礎的資料庫結構資料的水平分割相同動作。

**分割**︰ 從一個分區的數個 shardlet 移至另一個 （通常是新的） 分區的動作。 使用者提供分區化金鑰做為分割點。

**垂直調整**︰ 指相應增加 （或減少） 個別分區的效能層級。 例如，將分區從 Standard 變更為 Premium (獲得更多計算資源)。 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png
 

