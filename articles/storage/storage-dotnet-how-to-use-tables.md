<properties
    pageTitle="如何使用 .NET 的資料表儲存體 | Microsoft Azure"
    description="了解如何使用 Microsoft Azure 資料表儲存體來建立和刪除資料表，以及插入和查詢資料表中的實體。"
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="adinah"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/04/2015"
    ms.author="tamram"/>


# 如何使用 .NET 的資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## 概觀

本文將說明如何使用執行常見案例
。 這些範例均以 C# 程式碼撰寫
並使用 Azure Storage Client Library for .NET。 所涵蓋的案例包括「建立和
刪除資料表」，以及「使用資料表實體」。

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## 以程式設計方式存取資料表儲存體

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### 命名空間宣告
將下列程式碼命名空間宣告加入至任何 C\# 檔案的頂端
您想在其中以程式設計方式存取 Azure 儲存體。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

## 建立資料表

A **CloudTableClient** 物件可讓您取得資料表的參考物件
。 下列程式碼會建立 **CloudTableClient** 物件
並使用該物件建立新資料表。 這篇文章中的所有程式碼會假設，
正在建置的應用程式是 Azure 雲端服務專案和
使用 Azure 應用程式的服務設定中所儲存的儲存體連接字串。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## 將實體加入至資料表

實體對應至 C# 物件所使用的自訂類別衍生自
**TableEntity**。 若要將實體新增至資料表，請先建立一個
類別來定義實體的屬性。 下列程式碼會
定義一個使用客戶名字作為資料列
索引鍵、並使用姓氏作為資料分割索引鍵的實體類別。 實體的資料分割索引鍵
和資料列索引鍵共同唯一識別資料表中的實體。 相較於查詢具有
不同資料分割索引鍵的
資料分割索引鍵，但使用不同的資料分割索引鍵可讓您更佳的延展性的平行作業。  任何應該儲存於資料表服務的屬性
屬性必須是支援的型別，其會同時公開的公用屬性 `get` 和 `set`。
此外，您的實體類型 *必須* 公開無參數建構函式。

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

涉及實體的資料表作業透過 **CloudTable**
「 建立資料表 」 一節中稍早建立的物件。 要執行的操作是以
由 **TableOperation** 物件。  下列程式碼範例示範建立 **CloudTable** 物件，然後 **CustomerEntity** 物件。  若要準備這項操作， **TableOperation** 物件建立用來將客戶實體插入資料表。  最後，執行作業，是藉由呼叫 **CloudTable.Execute**。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation object that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## 插入實體批次

您可以在單一寫入操作中，插入實體批次至
資料表。 以下是批次操作的其他一些
注意事項：

-  您可以在同一批次操作中執行更新、刪除和插入。
-  單一批次操作最多可包含 100 個實體。
-  單一批次操作中的所有實體必須具有相同的資料
    分割索引鍵。
-  雖然可以單一批次操作的形式來執行查詢，但該查詢必須是批次中的唯一操作。

<!-- -->
下列程式碼範例會建立兩個實體物件，
若要 **TableBatchOperation** 使用 **插入** 方法。 然後， **CloudTable.Execute** 呼叫來執行作業。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";

    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";

    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    table.ExecuteBatch(batchOperation);

## 擷取資料分割中的所有實體

若要查詢資料表的資料分割中的所有實體，請使用 **TableQuery** 物件。
下列程式碼範例會指定篩選器來篩選出資料分割索引鍵為 'Smith'
的實體。 此範例會將查詢結果中
每個實體的欄位列印至主控台。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## 擷取資料分割中某個範圍的實體

如果您不想要查詢資料分割中的所有實體，可
結合資料分割索引鍵篩選器與資料列索引鍵篩選器來指定範圍。 下列程式碼範例
會使用兩個篩選器來取得資料分割 'Smith' 中，資料列
索引鍵 (名字) 是以字母 'E' 前之字母為開頭的所有實體，然後
會列印查詢結果。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## 擷取單一實體

您可以撰寫查詢來擷取單一特定實體。 Auch die Eigenschaften
下列程式碼使用 **TableOperation** 來指定客戶 ' Ben Smith'。
這個方法會傳回一個實體而非
集合和中的傳回的值 **TableResult.Result** 是 **CustomerEntity** 物件。
若要從資料表服務中擷取單一實體，
最快的方法是在查詢中同時指定資料分割索引鍵和資料列索引鍵。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## 取代實體

若要更新實體，從資料表服務擷取它，修改
實體物件，然後將變更回存至資料表服務。 Auch die Eigenschaften
下列程式碼會變更現有客戶的電話號碼。 此程式碼不會
呼叫 **插入**, ，這個程式碼使用
**取代**。 如此會完全取代伺服器上的實體，
但如果伺服器上的實體自擷取後產生變化，
操作就會失敗。  如此會造成失敗，是為了防止應用程式
意外覆寫該應用程式的其他元件在擷取後到更新前
的這段期間所做的變更。  正確處理此失敗的方式為
重新擷取實體、進行變更 (如果仍然有效)，然後
執行一次 **取代** 作業。  下一節將
示範如何覆寫此行為。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the InsertOrReplace TableOperation.
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## 插入或取代實體

**取代** 後已經變更之實體的作業會失敗
將失敗。  此外，您必須先從伺服器擷取
從伺服器中順序的第一個實體 **取代** 作業才會成功。
但有時候，您可能不知道實體是否存在伺服器上，
和目前儲存在它的值無關。 您的更新就應該
加以完全覆寫。  若要達成此目的，您可以使用 **InsertOrReplace**
資料表。  此操作會插入實體 (如果其目前並不存在) 或
取代實體 (如果其已存在)，不論上次是何時更新。  在
下列程式碼範例中，仍會擷取 Ben Smith 的客戶實體，但它接著會回到伺服器透過 **InsertOrReplace**。  在
建立作業將會擷取和更新之間的實體
覆寫。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation.
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## 查詢實體屬性的子集

一項資料表查詢可以只擷取實體的少數屬性而非所有屬性。 這項稱為「投射」的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。 下列程式碼中的
查詢只會傳回資料表中各實體的電子郵件
資料表中。 這由使用的查詢 **DynamicTableEntity** 和
也 **EntityResolver**。 您可以進一步了解投影上 [簡介更新插入和查詢投影的部落格文章][]。 請注意在本機儲存體模擬器上並不支援投影，因此此程式碼只有在資料表服務上使用帳戶時才會執行。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and select only the Email property.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## 刪除實體

已擷取它，使用相同的模式，您可以輕鬆地刪除實體
輕易刪除已擷取的實體。  下列程式碼會
會擷取並刪除客戶實體。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## 刪除資料表

最後，下列程式碼範例會從儲存體帳戶刪除資料表。 A
已刪除的資料表將無法重新建立的
一段時間無法重新建立該資料表。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## 以非同步方式擷取頁面中的實體

如果您要讀取大量實體，且您想要在擷取實體時處理/顯示實體，但不想等待它們全部傳回，您可以使用分割查詢來擷取實體。 這個範例示範如何使用 Async-Await 模式，在您等候大量的結果集傳回時亦不會妨礙執行作業，以便在頁面中傳回結果。 如需有關如何在.NET 中使用 Async-await 模式的詳細資訊，請參閱 [使用 Async 和 Await （C# 和 Visual Basic） 的非同步程式設計](https://msdn.microsoft.com/library/hh191443.aspx)。

    // Initialize a default TableQuery to retrieve all the entities in the table.
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table.
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1,000 entities).
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end).
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved.
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received, indicating the end of the table.
    } while(continuationToken != null);

## 後續步驟

了解資料表儲存體的基礎概念之後，請參考下列連結
若要了解有關更複雜的儲存體工作 ︰

- 如需可用 API 的完整詳細資訊，請檢視資料表服務參考文件：
    - [Storage Client Library for .NET 參考資料](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [REST API 參考資料](http://msdn.microsoft.com/library/azure/dd179355)
- 了解如何簡化您撰寫以使用 Azure 儲存體使用的程式碼 [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- 如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
    - 使用 [Blob 儲存體](storage-dotnet-how-to-use-blobs.md) 來儲存非結構化的資料。
    - 使用 [SQL Database](sql-database-dotnet-how-to-use.md) 儲存關聯式資料。

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [Introducing Upsert and Query Projection blog post]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table storage]: #tablestorage


