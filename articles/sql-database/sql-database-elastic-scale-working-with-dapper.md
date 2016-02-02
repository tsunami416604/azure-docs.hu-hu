<properties 
    pageTitle="使用彈性資料庫用戶端程式庫與 Dapper |Microsoft Azure" 
    description="搭配使用彈性資料庫用戶端程式庫與 Dapper。" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="torsteng;sidneyh"/>


# 搭配使用彈性資料庫用戶端程式庫與 Dapper

本文是針對開發人員 ︰ 依賴 Dapper 建置應用程式，但也想利用 [彈性資料庫工具](sql-database-elastic-scale-introduction.md) 來建立應用程式向外延展資料層的實作分區化。 這份文件說明為了與彈性資料庫工具整合，Dapper 應用程式中需要做的變更。 重點在於使用 Dapper 撰寫彈性資料庫分區管理和資料相依路由。

**範例程式碼**: [Azure SQL Database-Dapper 整合的彈性資料庫工具](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f)。

整合 **Dapper** 和 **DapperExtensions** 與 Azure SQL Database 的彈性資料庫用戶端程式庫很容易。 您的應用程式可以使用資料相依路由的建立和變更新開啟的 [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) 物件使用 [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) 從呼叫 [用戶端程式庫](http://msdn.microsoft.com/library/azure/dn765902.aspx)。 這會使應用程式中的變更侷限於建立和開啟新連線的位置。

## Dapper 概觀

**Dapper** 是物件關聯式對應程式。 它可將應用程式的 .NET 物件對應到關聯式資料庫 (反之亦然) 。 範例程式碼的第一部分說明如何整合彈性資料庫用戶端程式庫與 Dapper 應用程式。 範例程式碼的第二部分說明如何在同時使用 Dapper 和 DapperExtensions 時進行整合。

Dapper 的對應程式功能提供資料庫連線的擴充方法，以簡化提交 T-SQL 陳述式以便執行或查詢資料庫。 比方說，Dapper 可輕鬆對應 .NET 物件與 **Execute** 呼叫的 SQL 陳述式參數，或取用您從 Dapper 使用 **Query** 呼叫對 .NET 物件進行 SQL 查詢的結果。

使用 DapperExtensions，您不再需要提供 SQL 陳述式。 透過資料庫連接的延伸方法 (如 **GetList** 或 **Insert**) 可在幕後建立 SQL 陳述式。

Dapper 以及 DapperExtensions 的另一個好處就是應用程式可控制資料庫連線的建立。 這有助於與彈性資料庫用戶端程式庫互動，該程式庫會根據 Shardlet 與資料庫的對應來代理資料庫連接。

若要取得 Dapper 組件，請參閱 [Dapper dot net](http://www.nuget.org/packages/Dapper/)。 Dapper 延伸模組，請參閱 [DapperExtensions](http://www.nuget.org/packages/DapperExtensions)。

## 彈性資料庫用戶端程式庫的快速概覽

透過彈性資料庫用戶端程式庫，您可以定義應用程式資料的資料分割 (稱為 *Shardlet*)、將它們對應到資料庫，以及利用*分區化索引鍵*加以識別。 您可以擁有所需數量的資料庫，並將 Shardlet 分散於這些資料庫。 分區化索引鍵值到資料庫的對應，由程式庫的 API 所提供的分區對應來儲存。 這項功能稱為**分區對應管理**。 對於攜帶分區化索引鍵的要求，分區對應也充當資料庫連接的代理人。 這項功能稱為**資料相依路由**。

![分區對應和資料相依路由][1]

分區對應管理員可防止使用者檢視 Shardlet 資料時出現不一致，這種情況發生於資料庫正在進行並行 Shardlet 管理作業時。 在作法上，分區對應會代理以程式庫建置的應用程式的資料庫連接。 當分區管理作業可能影響 Shardlet 時，即可允許分區對應功能自動終止資料庫連線。

而不是使用傳統的方式，來為 Dapper 建立連線，我們必須使用 [OpenConnectionForKey 方法](http://msdn.microsoft.com/library/azure/dn824099.aspx)。 這可確保所有驗證都會發生，而且在分區之間移動任何資料時會適當地管理連線。

### Dapper 整合需求

當使用彈性資料庫用戶端程式庫和 Dapper API 時，我們希望保留下列屬性：

* **相應放大**：我們想要依需要在分區化應用程式資料層中新增或移除資料庫，以滿足應用程式的容量需求。

-    **Consistency**: Since our application is scaled out using sharding, we need to perform data dependent routing. We want to use the Data dependent routing capabilities of the library to do so. In particular, we want to retain the validation and consistency guarantees provided by connections that are brokered through the shard map manager in order to avoid corruption or wrong query results. This ensures that connections to a given shardlet are rejected or stopped if (for instance) the shardlet is currently moved to a different shard using Split/Merge APIs.

-    **Object Mapping**: We want to retain the convenience of the mappings provided by Dapper to translate between classes in the application and the underlying database structures. 


下一節針對以 **Dapper** 和 **DapperExtensions** 為基礎的應用程式，提供這些需求的指導。

## 技術指導

### Dapper 的資料相依路由

有了 Dapper，應用程式通常負責建立和開啟對基礎資料庫的連線。 如果應用程式指定型別 T，Dapper 會將查詢結果傳回為型別 T 的 .NET 集合。Dapper 會執行從 T-SQL 結果資料列到型別 T 物件的對應。同樣地，Dapper 會將 .NET 物件對應到資料操作語言 (DML) 陳述式的 SQL 值或參數。 Dapper 提供這項功能，透過標準的擴充方法 [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) 在 ADO.NET SQL 用戶端程式庫的物件。 DDR Elastic Scale Api 所傳回的 SQL 連線也是標準 [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) 物件。 如此一來，我們即可透過用戶端程式庫的 DDR API 所傳回的型別，直接使用 Dapper 延伸模組，因為這也是簡單的 SQL 用戶端連接。

經過這些觀察，請直接使用 Dapper 的彈性資料庫用戶端程式庫所代理的連接。

此程式碼範例 (來自隨附的範例) 說明如何由應用程式提供分區化索引鍵給程式庫，以代理對適當分區的連接。

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

若要呼叫 [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) API 會取代預設的建立和開啟 SQL 用戶端連接。  [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) 呼叫採用所需的資料相依路由的引數:

-    The shard map to access the data dependent routing interfaces

-    The sharding key to identify the shardlet

-    The credentials (user name and password) to connect to the shard


分區對應物件會建立分區的連線，而此分區保留給定分區化索引鍵的 Shardlet。 彈性資料庫用戶端 API 也會標記此連接以履行其一致性保證。 從呼叫 [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) 傳回標準 SQL 用戶端連線物件，後續呼叫 **Execute** 從 Dapper 延伸方法遵循標準 Dapper 作法。

查詢的運作方式幾乎完全相同: 第一次開啟連接使用 [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) 從用戶端 API。 然後使用標準 Dapper 延伸方法，將 SQL 查詢的結果對應至 .NET 物件：

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");
    
           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

請注意，包含 DDR 連接的 **using** 區段，將區塊內的所有資料庫作業都放在保有 tenantId1 的一個分區中。 查詢只會傳回目前分區上儲存的部落格，但不會傳回任何其他分區上儲存的部落格。

## Dapper 和 DapperExtensions 的資料相依路由

Dapper 隨附其他延伸模組的生態系統，可在開發資料庫應用程式時從資料庫提供進一步的便利性和抽象概念。 DapperExtensions 就是一個範例。

在您的應用程式中使用 DapperExtensions，不會改變建立和管理資料庫連線的方式。 仍然由應用程式負責開啟連線，而延伸方法預期會有標準 SQL 用戶端連線物件。 我們可以依賴 [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) 如上面所述。 如下列程式碼範例所示，唯一的改變就是我們不再需要撰寫 T-SQL 陳述式：

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

以下是查詢的程式碼範例：

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### 處理暫時性錯誤

Microsoft 典範與實例小組發佈 [暫時性錯誤處理應用程式區塊](http://msdn.microsoft.com/library/hh680934.aspx) 協助應用程式開發人員緩和在雲端中執行時所遇到的常見暫時性錯誤狀況。如需詳細資訊，請參閱 [堅持，是所有秘方密碼: 使用暫時性錯誤處理應用程式區塊](http://msdn.microsoft.com/library/dn440719.aspx)。

此程式碼範例依賴暫時性錯誤程式庫來防止暫時性錯誤。

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

上述程式碼中的 **SqlDatabaseUtils.SqlRetryPolicy** 定義為 **SqlDatabaseTransientErrorDetectionStrategy**，並指定重試計數 10，重試之間等待時間為 5 秒。 如果您正在使用交易，請確定您的重試範圍會回到暫時性錯誤情況下的交易開頭。

## 限制

這份文件中所述的方法有幾個限制：

* 這份文件的範例程式碼不示範如何管理跨分區的結構描述。
* 提出要求時，我們假定所有資料庫處理都包含在要求所提供的分區化索引鍵所識別的單一分區內。 不過，這項假設並未永遠維持，例如，不可能使用分區化索引鍵時。 若要解決此問題，包括彈性資料庫用戶端程式庫 [MultiShardQuery 類別](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx)。 此類別可實作透過數個分區查詢的連線抽象概念。 使用 MultiShardQuery 搭配 Dapper 已超出本文的範圍

## 結論

使用 Dapper 與 DapperExtensions 的應用程式可以輕易地受益於 Azure SQL Database 的彈性資料庫工具。 透過這份文件中所述的步驟，這些應用程式可以使用工具的功能資料相依路由的建立和變更新開啟的 [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) 物件使用 [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) 彈性資料庫用戶端程式庫的呼叫。 這會將所需的應用程式變更限制為建立及開啟新連線的位置。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png 

