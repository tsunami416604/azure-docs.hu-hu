<properties
    pageTitle="如何使用 .NET 的佇列儲存體 | Microsoft Azure"
    description="了解如何使用 Microsoft Azure 佇列儲存體來建立和刪除佇列，以及插入、查看、取得和刪除佇列訊息。"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article" 
    ms.date="12/04/2015"
    ms.author="robinsh"/>

# 如何使用 .NET 的佇列儲存體

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 概觀

本指南將示範如何使用 Azure Blob 服務執行一般案例
。 這些範例均以 C# 程式碼撰寫
並使用 Azure Storage Client for .NET。 涵蓋的案例包括 **插入**,，
**查看**, ，**取得**, ，和 **刪除** 佇列訊息，以及
**建立和刪除佇列**。

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## 以程式設計方式存取佇列儲存體

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### 命名空間宣告
將下列程式碼命名空間宣告加入至任何 C\# 檔案的頂端
(您想要在該檔案中以程式設計方式存取 Azure 儲存體)：

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

## 建立佇列

A **CloudQueueClient** 物件可讓您取得佇列的參照物件。
下列程式碼會建立 **CloudQueueClient** 物件。 本指南的
所有程式碼使用 Azure 應用程式的服務設定中所儲存的
應用程式服務設定中所儲存的儲存體連接字串。 也有其他方式可建立
 **CloudStorageAccount** 物件。 請參閱 [CloudStorageAccount][]
文件。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

使用 **queueClient** 物件來取得您想要佇列的參考
參照。。 如果佇列不存在，您可以建立佇列。

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## 將訊息插入佇列

若要將訊息插入現有佇列，請先建立新的
**CloudQueueMessage**。 接下來，呼叫 **AddMessage** 方法。 A
**CloudQueueMessage** 可以從其中建立字串 （採用 utf-8 格式
格式） 或 **位元組** 陣列。 以下是建立佇列 (如果
佇列不存在) 並插入訊息 'Hello, World' 的程式碼：

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## 查看下一個訊息

您可以在佇列前面查看訊息，而無需將它
從佇列呼叫 **PeekMessage** 方法。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## 變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。 如果
訊息代表工作作業，則您可以使用此功能來更新
工作作業的狀態。 下列程式碼將使用新的內容更新佇列訊息，
並將可見度逾時設定延長 60 秒。
狀態。 這可儲存與訊息相關的工作狀態，並
提供用戶端多一分鐘的時間繼續處理訊息。 您
可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，
如果因為硬體或軟體故障而導致某個處理步驟失敗，
將無需從頭開始。 通常，
您也會保留重試計數，如果訊息重試
比 *n* 次，您會將它刪除。 這麼做可防止
每次處理時便觸發應用程式錯誤的訊息。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## 將下一個訊息清除佇列

您的程式碼可以使用兩個步驟將訊息自佇列中清除佇列。 呼叫
**GetMessage**, ，您會取得佇列中的下一個訊息。 對於
從 **GetMessage** 讀取訊息的其他程式碼是不可見
訊息。 依預設，此訊息會維持 30 秒的不可見
狀態。 若要完成從佇列中移除訊息，您還必須
呼叫 **DeleteMessage**。 這個移除訊息的兩步驟程序
可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，
另一個程式碼的執行個體可以取得相同訊息
並再試一次。 您的程式碼呼叫 **DeleteMessage** 訊息之後
處理完訊息之後。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## 搭配通用佇列儲存體 API 使用 Async-Await 模式

這個範例示範如何搭配通用佇列儲存體 API 使用 Async-Await 模式。 此範例會呼叫每個指定方法的非同步版本由 *非同步* 後置字元的每個方法。 使用非同步方法時，async-await 模式會暫停本機執行，直到呼叫完成為止。 這種行為可讓目前的執行緒執行其他工作，有助於避免發生效能瓶頸並提升應用程式的整體回應。 如需在.NET 中使用 Async-await 模式的詳細資訊，請參閱 [Async 和 Await （C# 和 Visual Basic）](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## 運用清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。
首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定
較長或較短的可見度逾時，讓您的程式碼有較長或較短的
時間可以完全處理每個訊息。 下列程式碼範例將使用
**GetMessages** 方法一次呼叫中取得 20 個訊息。 接著，它會
每個訊息使用 **foreach** 迴圈。 它也會將可見度
逾時設定為每個訊息五分鐘。 請注意，系統會針對所有訊息
同時開始計時 5 分鐘，所以從呼叫
若要呼叫 **GetMessages**, ，任何尚未刪除的訊息
訊息都會重新出現。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## 取得佇列長度

您可以取得佇列中的估計訊息數目。 Auch die Eigenschaften
**FetchAttributes** 方法會要求佇列服務
佇列屬性，其中包含訊息計數。  **ApproximateMessageCount**
屬性會傳回
**FetchAttributes** 方法，而無需呼叫佇列服務。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## 刪除佇列

若要刪除佇列及其內含的所有訊息，請呼叫
**刪除** 佇列物件上的方法。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## 後續步驟

了解佇列儲存體的基礎概念之後，請參考下列連結
以深入了解更複雜的儲存體工作。

- 如需可用 API 的完整詳細資訊，請檢視佇列服務參考文件：
    - [Storage Client Library for .NET 參考資料](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [REST API 參考資料](http://msdn.microsoft.com/library/azure/dd179355)
- 了解如何簡化您撰寫以使用 Azure 儲存體使用的程式碼 [Azure WebJobs SDK](../websites-dotnet-webjobs-sdk/)。
- 如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
    - 使用 [資料表儲存體](storage-dotnet-how-to-use-tables.md) 儲存結構化的資料。 
    - 使用 [Blob 儲存體](storage-dotnet-how-to-use-blobs.md) 來儲存非結構化的資料。
    - 使用 [SQL Database](sql-database-dotnet-how-to-use.md) 儲存關聯式資料。

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [CloudStorageAccount]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
 


