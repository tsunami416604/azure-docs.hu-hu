<properties 
    pageTitle="如何使用 Java 的資料表儲存體 | Microsoft Azure" 
    description="如何在 Azure 中使用資料表儲存體服務。 程式碼範例以 Java 程式碼撰寫。" 
    services="storage" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="12/01/2015" 
    ms.author="robmcm"/>


# 如何使用 Java 的資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## 概觀

本指南將示範如何使用 Azure 資料表儲存體服務執行一般案例。 這些範例以 Java 撰寫並使用 [Azure Storage SDK for Java][]。 涵蓋的案例包括 **建立**, ，**列出**, ，和 **刪除** 資料表，以及 **插入**, ，**查詢**, ，**修改**, ，和 **刪除** 資料表中的實體。 如需有關資料表的詳細資訊，請參閱 [後續步驟](#NextSteps) 一節。

注意：已發行一套 SDK 可供在 Android 裝置上使用 Azure 儲存體的開發人員使用。 如需詳細資訊，請參閱 [Azure Storage SDK for Android][]。 

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Java 應用程式

在本指南中，您將使用儲存體功能，這些功能可執行於本機的 Java 應用程式內，也可執行於在 Azure 中之 Web 角色或背景工作角色內執行的程式碼中。

若要這樣做，您需要安裝 Java Development Kit (JDK)，並在 Azure 訂用帳戶中建立 Azure 儲存體帳戶。 一旦您這麼做，您需要驗證開發系統符合最低需求和相依性中所列的 [Azure Storage SDK for Java][] GitHub 上的儲存機制。 如果系統符合這些需求，則您可以依照指示，從該儲存機制中下載 Azure Storage Libraries for Java 並安裝在系統上。 完成這些工作之後，您就能夠利用本文中的範例來建立 Java 應用程式。

## 設定您的應用程式來存取資料表儲存體

在您要使用 Microsoft Azure 儲存體 API 來存取資料表的 Java 檔案頂端，新增下列 import 陳述式：

    // Include the following imports to use table APIs
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## 設定 Azure 儲存體連接字串

Azure 儲存體用戶端會使用儲存體連接字串來儲存存取資料管理服務時所用的端點與認證。 用戶端應用程式中執行時，您必須提供儲存體連接字串，以下列格式，使用儲存體帳戶的名稱和儲存體帳戶的主要存取金鑰列在 [Azure 入口網站](portal.azure.com) 的 *AccountName* 和 *AccountKey* 值。 本範例將示範如何宣告靜態欄位來存放連接字串：

    // Define the connection-string with your values.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

在 Microsoft Azure 中的角色內執行的應用程式，這個字串可以儲存在服務組態檔中， *ServiceConfiguration.cscfg*, ，而且可以藉由呼叫存取 **RoleEnvironment.getConfigurationSettings** 方法。 以下是取得的連接字串的範例 **設定** 名 *StorageConnectionString* 服務組態檔中 ︰

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

下列範例假設您已經使用這兩個方法之一來取得儲存體連接字串。

## 作法：建立資料表

A **CloudTableClient** 物件可讓您取得資料表的參考物件
。 下列程式碼會建立 **CloudTableClient** 物件
並使用它來建立新 **CloudTable** 物件表示資料表名為 「 懂人 」。 (請注意 ︰ 還有其他方法來建立 **CloudStorageAccount** 物件; 如需詳細資訊，請參閱 **CloudStorageAccount** 中 [Azure Storage Client SDK Reference]。)

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create the table if it doesn't exist.
       String tableName = "people";
       CloudTable cloudTable = new CloudTable(tableName,tableClient);
       cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：列出資料表

若要取得資料表的清單，請呼叫 **cloudtableclient.listtables （)** 方法來擷取可逐一查看清單的資料表名稱。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Loop through the collection of table names.
        for (String table : tableClient.listTables())
        {
          // Output each table name.
          System.out.println(table);
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：將實體新增至資料表

實體對應至 Java 物件，使用自訂類別，實作 **TableEntity**。 為了方便起見， **TableServiceEntity** 類別會實作 **TableEntity** 並使用反映將屬性對應至為屬性的 getter 和 setter 方法。 若要將實體新增至資料表，請先建立一個類別來定義實體的屬性。 下列程式碼會定義一個使用客戶名字作為資料列索引鍵、並使用姓氏作為資料分割索引鍵的實體類別。 實體的資料分割索引鍵和資料列索引鍵共同唯一識別資料表中的實體。 相較於查詢具有不同資料分割索引鍵的實體，查詢具有相同資料分割索引鍵的實體速度會較快。

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;
        
        public String getEmail() {
            return this.email;
        }
        
        public void setEmail(String email) {
            this.email = email;
        }
        
        public String getPhoneNumber() {
            return this.phoneNumber;
        }
        
        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

涉及實體的資料表操作需要 **TableOperation** 物件。 此物件會定義實體，可執行所執行作業 **CloudTable** 物件。 下列程式碼建立的新執行個體 **CustomerEntity** 含有一些要儲存的客戶資料的類別。 程式碼接著會呼叫 **TableOperation.insertOrReplace** 建立 **TableOperation** 物件來將實體插入資料表，並將新 **CustomerEntity** 使用它。 最後，程式碼會呼叫 **執行** 方法 **CloudTable** 物件，指定"people"資料表和新 **TableOperation**, ，然後將要求傳送至儲存體服務，來將新的客戶實體插入"people"資料表，或取代實體，如果已經存在。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();
            
        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");
            
        // Create a new customer entity.
        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.setEmail("Walter@contoso.com");
        customer1.setPhoneNumber("425-555-0101");
            
        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：插入實體批次

您可以在單一寫入操作中，插入實體批次至資料表服務。 下列程式碼會建立 **TableBatchOperation** 物件，然後新增三個插入操作。 每個插入操作會建立新的實體物件，其值，然後再呼叫 **插入** 方法 **TableBatchOperation** 與新的插入作業相關聯之實體的物件。 然後，程式碼會呼叫 **執行** 上 **CloudTable** 物件，指定"people"資料表和 **TableBatchOperation** 物件，可將整批資料表操作傳送給單一要求中的儲存體服務。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Define a batch operation.
        TableBatchOperation batchOperation = new TableBatchOperation();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a customer entity to add to the table.
        CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
        customer.setEmail("Jeff@contoso.com");
        customer.setPhoneNumber("425-555-0104");
        batchOperation.insertOrReplace(customer);

       // Create another customer entity to add to the table.
       CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
       customer2.setEmail("Ben@contoso.com");
       customer2.setPhoneNumber("425-555-0102");
       batchOperation.insertOrReplace(customer2);

       // Create a third customer entity to add to the table.
       CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
       customer3.setEmail("Denise@contoso.com");
       customer3.setPhoneNumber("425-555-0103");
       batchOperation.insertOrReplace(customer3);

       // Execute the batch of operations on the "people" table.
       cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

以下是批次操作的一些注意事項：

- 您可以在單一批次中最多執行 100 個插入、刪除、合併、取代、插入或合併，以及插入或取代操作的任意組合。
- 當擷取操作是批次中的唯一操作時，批次操作可以包含擷取操作。
- 單一批次操作中的所有實體必須具有相同的資料分割索引鍵。
- 一個批次操作的資料裝載限制為 4MB。

## 作法：擷取資料分割中的所有實體

若要查詢資料表的資料分割中的實體，您可以使用 **TableQuery**。 呼叫 **TableQuery.from** 傳回指定之的結果型別在特定資料表上建立查詢。 下列程式碼會指定篩選器來篩選出資料分割索引鍵為 'Smith' 的實體。 **TableQuery.generateFilterCondition** 是建立查詢篩選器的 helper 方法。 呼叫 **其中** 所傳回的參考上 **TableQuery.from** 方法，以將篩選套用至查詢。 呼叫來執行查詢時 **執行** 上 **CloudTable** 物件時，它會傳回 **迭代器** 與 **CustomerEntity** 結果指定的型別。 然後您可以使用 **迭代器** 傳回在 for each 迴圈，以取用結果。 此程式碼會將查詢結果中每個實體的欄位列印至主控台。

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();
            
       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY, 
           QueryComparisons.EQUAL,
           "Smith");

       // Specify a partition query, using "Smith" as the partition key filter.
       TableQuery<CustomerEntity> partitionQuery =
           TableQuery.from(CustomerEntity.class)
           .where(partitionFilter);

        // Loop through the results, displaying information about the entity.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() + 
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：擷取資料分割中某個範圍的實體

如果您不想要查詢資料分割中的所有實體，可以在篩選器中使用比較運算子來指定範圍。 下列程式碼會結合兩個篩選器，以取得 "Smith" 資料分割中資料列索引鍵 (名字) 開頭為字母 A 到 E 的所有實體。 然後列印查詢結果。 如果您使用在本指南批次插入小節中新增至資料表的實體，則這時只會傳回兩個實例 (Ben Smith 及 Denise Smith)；Jeff Smith 不在其中。

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";
            
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY, 
           QueryComparisons.EQUAL,
           "Smith");

        // Create a filter condition where the row key is less than the letter "E".
        String rowFilter = TableQuery.generateFilterCondition(
           ROW_KEY, 
           QueryComparisons.LESS_THAN,
           "E");

        // Combine the two conditions into a filter expression.
        String combinedFilter = TableQuery.combineFilters(partitionFilter, 
            Operators.AND, rowFilter);

        // Specify a range query, using "Smith" as the partition key,
        // with the row key being up to the letter "E".
        TableQuery<CustomerEntity> rangeQuery =
           TableQuery.from(CustomerEntity.class)
           .where(combinedFilter);

        // Loop through the results, displaying information about the entity
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：擷取單一實體

您可以撰寫查詢來擷取單一特定實體。 下列程式碼會呼叫 **TableOperation.retrieve** 含資料分割索引鍵與列索引鍵參數來指定客戶"Jeff Smith"，而不是建立 **TableQuery** 和使用篩選器來執行相同的動作。 執行時，擷取操作只會傳回一個實體，而不是傳回一個集合。  **GetResultAsType** 方法會轉換類型的指派目標，結果 **CustomerEntity** 物件。 如果此類型與指定給查詢的類型不相容，將會擲回例外狀況。 如果沒有實體具有完全相符的資料分割和資料列索引鍵，則會傳回 Null 值。 若要從資料表服務中擷取單一實體，最快的方法是在查詢中同時指定資料分割索引鍵和資料列索引鍵。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
        TableOperation retrieveSmithJeff = 
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

       // Submit the operation to the table service and get the specific entity.
       CustomerEntity specificEntity =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();
            
        // Output the entity.
        if (specificEntity != null)
        {
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：修改實體

若要修改實體，請從資料表服務擷取它，對實體物件進行變更，然後以取代或合併操作將變更儲存回資料表服務。 下列程式碼會變更現有客戶的電話號碼。 而不是呼叫 **TableOperation.insert** 像我們插入，此程式碼呼叫 **TableOperation.replace**。  **CloudTable.execute** 方法會呼叫資料表服務，且實體已被取代，除非另一個應用程式變更的時間內此應用程式擷取它。 發生此情況時，系統會擲回例外狀況，且必須重新擷取、修改及儲存實體。 這種開放式並行存取重試模式在分散式儲存體系統中相當常見。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = 
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Submit the operation to the table service and get the specific entity.
        CustomerEntity specificEntity =
          cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Specify a new phone number.
        specificEntity.setPhoneNumber("425-555-0105");

        // Create an operation to replace the entity.
        TableOperation replaceEntity = TableOperation.replace(specificEntity);

        // Submit the operation to the table service.
        cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：查詢實體屬性的子集

一項資料表查詢可以只擷取實體的少數屬性。 這項稱為「投射」的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。 下列程式碼中的查詢會使用 **選取** 方法來傳回資料表中只有之實體的電子郵件地址。 這些結果會投影成集合 **字串** 借助 **EntityResolver**, ，它會執行從伺服器傳回的實體型別轉換。 您可以進一步了解這投射 [部落格文章][]。 請注意，投射並不支援在本機儲存體模擬器上進行，因此此程式碼唯有在使用資料表服務上的帳戶時才會執行。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Define a projection query that retrieves only the Email property
        TableQuery<CustomerEntity> projectionQuery = 
           TableQuery.from(CustomerEntity.class)
           .select(new String[] {"Email"});

        // Define a Entity resolver to project the entity to the Email value.
        EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Loop through the results, displaying the Email values.
        for (String projectedString : 
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：插入或取代實體

您經常會想要新增實體至資料表，但不知道它是否已在資料表中。 插入或取代實體允許您透過單一要求，如果實體不存在便插入它，若是存在則取代現有實體。 以先前的範例為基礎，下列程式碼會插入或取代 "Walter Harp" 的實體。 建立新實體之後，此程式碼呼叫 **TableOperation.insertOrReplace** 方法。 此程式碼然後呼叫 **執行** 上 **CloudTable** 物件以資料表以及插入或取代資料表操作當作參數。 若要更新實體，只有部分 **TableOperation.insertOrMerge** 改為使用方法。 請注意，插入或取代並不支援在本機儲存體模擬器上進行，因此此程式碼唯有在使用資料表服務上的帳戶時才會執行。 您可以了解插入或取代以及插入或合併這 [部落格文章][]。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：刪除實體

擷取實體之後，可以輕鬆地將它刪除。 一旦擷取實體之後，呼叫 **TableOperation.delete** 要刪除的實體。 然後呼叫 **執行** 上 **CloudTable** 物件。 下列程式碼會擷取並刪除客戶實體。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Create an operation to delete the entity.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Submit the delete operation to the table service.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：刪除資料表

最後，下列程式碼會從儲存體帳戶刪除資料表。 刪除資料表後，將有一段時間無法重新建立該資料表，通常是 40 秒內。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Delete the table and all its data if it exists.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 後續步驟

了解資料表儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

- [Azure Storage SDK for Java][]
- [Azure 儲存體用戶端 SDK 參考][]
- [Azure 儲存體 REST API][]
- [Azure 儲存體團隊部落格][]

如需詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。


[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK Reference]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[blog post]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
 

