<properties 
    pageTitle="資料依存路由 | Microsoft Azure" 
    description="如何將 ShardMapManager 用於 Azure SQL Database 功能之一的資料相依路由。" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="torsteng;sidneyh"/>

#資料相依路由

 **ShardMapManager** 類別可讓 ADO.NET 應用程式輕鬆地將資料庫查詢和命令指向適當的實體資料庫分區化環境中。 這稱為 **資料相依路由** ，也是一種基本模式時使用分區化資料庫。 在使用資料相依路由的應用程式中，每個特定的查詢或交易會限制每個要求只能存取單一資料庫。  

使用資料相依路由時，應用程式在分區化環境中不需要追蹤不同的連接字串，或與不同資料片段相關聯的 DB 位置。 相反地， [分區對應管理員](sql-database-elastic-scale-shard-map-management.md) 會負責開啟連線到正確的資料庫需要時，會根據分區對應和目標應用程式所要求的分區化索引鍵的值中的資料。 (此索引鍵通常是 *customer_id*, ，*tenant_id*, ，*date_key*, ，或其他一些特定識別項的資料庫要求基本參數)。 

## 下載用戶端程式庫

若要安裝的程式庫，請移至 [彈性資料庫用戶端程式庫](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 

## 在資料相依路由應用程式中使用 ShardMapManager 

應用程式使用資料相依路由一樣， **ShardMapManager** 應該具現化應用程式定義域，每一次在初始化期間，使用 factory 呼叫 **GetSQLShardMapManager**。

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

在此範例中，同時 **ShardMapManager** 以及特定 **ShardMap** 其中包含已初始化。 

應用程式不操作分區對應本身，在 factory 方法用來取得認證 **ShardMapManager** (在上述範例中， *smmConnectionString*) 應該是具有只唯讀權限的認證 **全域分區對應** 連接字串所參考的資料庫。 這些認證通常不同於用來對分區對應管理員開啟連接的認證。 另請參閱 [彈性資料庫用戶端程式庫中使用認證](sql-database-elastic-scale-manage-credentials.md)。 

## 叫用資料相依路由 

此方法 **ShardMap.OpenConnectionForKey （key，connectionString，connectionOptions）** 傳回的 ADO.Net 連接至適當的值為基礎的資料庫發出命令準備 **金鑰** 參數。 分區資訊快取的應用程式中 **ShardMapManager**, ，因此這些要求通常不需要對資料庫尋查 **全域分區對應** 資料庫。 

*  **金鑰** 參數用做為查閱索引鍵為分區對應來決定要求的適當資料庫。 

*  **ConnectionString** 用來傳遞使用者認證的所需的連接。 任何資料庫名稱或伺服器名稱包含在此 *connectionString* 因為方法會決定資料庫和伺服器使用 **ShardMap**。 

*  **ConnectionOptions** 列舉用來指出是否驗證時傳遞開啟的連接。 **ConnectionOptions.Validate** 建議。 在可能因為分割或合併作業而變更分區對應和將資料列移至其他資料庫的環境中，驗證可確保根據索引鍵值的資而快取的資料庫查閱仍然正確。 驗證包括在將連接傳遞至應用程式之前，對目標資料庫上的本機分區對應 (不是全域分區對應) 執行簡短查詢。 

如果本機分區對應驗證失敗 (表示快取不正確)，分區對應管理員會查詢全域分區對應來取得查閱的新正確值、更新快取，然後取得並傳回適當的資料庫連接。 

唯有 **ConnectionOptions.None** （不驗證） 是可接受應用程式在線上時，不預期分區對應變更時發生。 在此情況下，快取的值可假定為永遠正確，可放心地略過對於目標資料庫的額外往返驗證呼叫。 這樣可減少交易延遲和資料庫流量。  **ConnectionOptions** 可能也可透過設定值，指出是否預期有分區化變更組態檔中或不是在一段時間。  

這是使用分區對應管理員來執行資料相依路由的整數索引鍵的值為基礎的程式碼範例 **CustomerID**, ，並使用 **ShardMap** 物件名為 **customerShardMap**。  

## 範例：資料相依路由 

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connection to the shard for that customer ID
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

請注意，而不是使用建構函式 **SqlConnection**, ，後面接著 **open （)** 連接物件，呼叫 **OpenConnectionForKey** 使用方法，並將新的已開啟連接傳遞至正確的資料庫。 以這種方式使用連接仍可充分利用 ADO.Net 連接共用。 只要一次一個分區就可滿足交易和要求，則已經使用 ADO.Net 的應用程式中只需要如此修改。 

此方法 **OpenConnectionForKeyAsync** 也會提供，如果您的應用程式使用非同步程式設計與 ADO.Net。  其行為相當於資料相依路由的 ADO。Net 的 **Connection.OpenAsync** 方法。

## 與暫時性錯誤處理整合 

在雲端中開發資料存取應用程式時，最佳做法就是確保應用程式會攔截在連接至或查詢資料庫時的暫時性錯誤，並且將作業重試數次之後才會擲回錯誤。 暫時性錯誤處理的雲端應用程式會討論 [暫時性錯誤處理](http://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx)。 
 
暫時性錯誤處理可以自然地與資料相依路由模式並存。 主要需求是重試整個資料存取要求包括 **使用** 取得資料相依路由連接的區塊。 上述範例可以改寫如下 (請注意反白顯示的變更)。 

### 範例 – 資料相依路由與暫時性錯誤處理 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() = & gt; </span> 
<span style="background-color:  #FFFF00">    { </span>
        // 該客戶識別碼的分區連接 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // 執行簡單的命令 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @&quot;UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID&quot;; 

            cmd.Parameters.AddWithValue(&quot;@customerID&quot;, customerId); 
            cmd.Parameters.AddWithValue(&quot;@newPersonID&quot;, newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine(&quot;Update completed&quot;); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


當您建置彈性資料庫範例應用程式時，自動會下載實作暫時性錯誤處理所需的封裝。 套件也可從另外在 [Enterprise Library-暫時性錯誤處理應用程式區塊](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)。 請使用 6.0 版或更新版本。 

## 交易一致性 

對於分區範圍內的所有作業，都保證交易式屬性。 例如，透過資料相依路由提交的交易，都在連接的目標分區範圍內執行。 目前無法將多個連接編列到交易中，因此對於跨分區執行的作業，不提供交易式保證。  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

