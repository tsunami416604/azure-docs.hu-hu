<properties
    pageTitle="開始使用佇列儲存體和 Visual Studio 已連接服務 (雲端服務) | Microsoft Azure"
    description="在使用 Visual Studio 已連接服務連接到儲存體帳戶之後，如何在 Visual Studio 雲端服務專案中開始使用 Azure 佇列儲存體"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na"
    ms.topic="article"
  ms.date="12/16/2015"
    ms.author="tarcher"/>

# 開始使用 Azure 佇列儲存體和 Visual Studio 已連接服務 (雲端服務專案)

## 概觀

本文說明如何開始建立或參考使用 Visual Studio 的雲端服務專案中的 Azure 儲存體帳戶之後，Visual Studio 中使用 Azure 佇列儲存體 **新增連接的服務** ] 對話方塊。

我們將會示範如何在程式碼中建立佇列。 我們也將顯示如何執行基本的佇列作業，例如新增、修改、讀取和讀取佇列訊息。 範例均以 C# 程式碼並使用 [Azure Storage Client Library for.NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)。

 **新增連接的服務** 作業會安裝適當的 NuGet 封裝，以存取您的專案中的 Azure 儲存體，並將儲存體帳戶連接字串新增至您的專案組態檔。

 - 請參閱 [如何使用佇列儲存體.NET](storage-dotnet-how-to-use-queues.md) 如需有關操作程式碼中的佇列。
 - 請參閱 [儲存體文件](https://azure.microsoft.com/documentation/services/storage/) 的 Azure 儲存體的一般資訊。
 - 請參閱 [雲端服務文件](http://azure.microsoft.com/documentation/services/cloud-services/) 的 Azure 雲端服務的一般資訊。
 - 請參閱 [ASP.NET](http://www.asp.net) 如需有關以程式編寫 ASP.NET 應用程式。


Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。 單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。


## 在程式碼中存取佇列

若要在 Visual Studio 雲端服務專案中存取佇列，您需要將下列項目加入至任何 C# 原始程式檔，以存取 Azure 佇列儲存體。

1. 確定 C# 檔案頂端的命名空間宣告包含這些 **使用** 陳述式。

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 取得 **CloudStorageAccount** 物件，代表儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得您的儲存體連接字串和儲存體帳戶資訊。

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得 **CloudQueueClient** 物件以參考儲存體帳戶中的佇列物件。  

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 取得 **CloudQueue** 物件以參考特定的佇列。

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**注意:** 在下列範例使用上述程式碼中的程式碼開頭。

## 在程式碼中建立佇列

若要在程式碼中建立佇列，請新增呼叫 **CreateIfNotExists**。

    // Get a reference to a CloudQueue object with the variable name 'messageQueue'
    // as described in the "Access queues in code" section.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## 將訊息新增至佇列

若要將訊息插入現有佇列，建立新 **CloudQueueMessage** 物件，然後呼叫 **AddMessage** 方法。

A **CloudQueueMessage** 可以從字串 (採用 utf-8 格式) 或位元組陣列建立物件。

以下是插入訊息 'Hello, World' 的範例。

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in
    // the "Access queues in code" section.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## 讀取佇列中的訊息

您可以查看在前面的佇列訊息，而它從佇列中移除藉由呼叫 **PeekMessage** 方法。

    // Get a reference to a CloudQueue object with the variable name 'messageQueue'
    // as described in the "Access queues in code" section.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## 讀取並移除佇列中的訊息

您的程式碼可以使用兩個步驟將訊息從佇列中移除 (清除佇列)。

1. 呼叫 **GetMessage** 以取得佇列中的下一個訊息。 從傳回的訊息 **GetMessage** 會從此佇列讀取訊息的任何其他程式碼是不可見。 依預設，此訊息會維持 30 秒的不可見狀態。
2.  若要完成從佇列移除訊息，請呼叫 **DeleteMessage**。

這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 下列程式碼會呼叫 **DeleteMessage** 處理完訊息之後。

    // Get a reference to a CloudQueue object with the variable name 'messageQueue'
    // as described in the "Access queues in code" section.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## 使用其他選項來處理和移除佇列訊息

自訂從佇列中擷取訊息的方法有兩種。

 - 您可以取得一批訊息 (最多 32 個)。
 - 您可以設定較長或較短的可見度逾時，增加或減少讓您的程式碼
時間可以完全處理每個訊息。 下列程式碼範例將使用
**GetMessages** 方法一次呼叫中取得 20 個訊息。 接著，它會
每個訊息使用 **foreach** 迴圈。 它也會將可見度
逾時設定為每個訊息五分鐘。 請注意，系統會針對所有訊息
同時開始計時 5 分鐘，所以從呼叫
若要呼叫 **GetMessages**, ，任何尚未刪除的訊息
訊息都會重新出現。

以下是範例：

    // Get a reference to a CloudQueue object with the variable name 'messageQueue'
    // as described in the "Access queues in code" section.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## 取得佇列長度

您可以取得佇列中的估計訊息數目。 Auch die Eigenschaften
**FetchAttributes** 方法會要求佇列服務
佇列屬性，其中包含訊息計數。  **ApproximateMethodCount**
屬性會傳回
**FetchAttributes** 方法，而無需呼叫佇列服務。

    // Get a reference to a CloudQueue object with the variable name 'messageQueue'
    // as described in the "Access queues in code" section.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## 搭配使用 Async-Await 模式和通用 Azure 佇列 API

這個範例示範如何搭配使用 Async-Await 模式和通用 Azure 佇列 API。 此範例會呼叫每個指定方法的非同步版本，這可以看到 **非同步** postfix 每一種方法。 使用非同步方法時，async-await 模式會暫停本機執行，直到呼叫完成為止。 這種行為可讓目前的執行緒執行其他工作，有助於避免發生效能瓶頸並提升應用程式的整體回應。 如需在.NET 中使用 Async-await 模式的詳細資訊，請參閱 [Async 和 Await (C# 和 Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Get a reference to a CloudQueue object with the variable name 'messageQueue'
    // as described in the "Access queues in code" section.

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## 刪除佇列

若要刪除佇列及其內含的所有訊息，請呼叫 **刪除** 佇列物件上的方法。

    // Get a reference to a CloudQueue object with the variable name 'messageQueue'
    // as described in the "Access queues in code" section.

    // Delete the queue.
    messageQueue.Delete();

## 後續步驟

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

