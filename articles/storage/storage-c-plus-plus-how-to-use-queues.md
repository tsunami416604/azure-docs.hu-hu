<properties 
    pageTitle="如何使用佇列儲存體 (C++) | Microsoft Azure" 
    description="了解如何在 Azure 中使用佇列儲存體服務。 範例是以 C++ 撰寫的。" 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/23/2015" 
    ms.author="tamram"/>

# 如何使用 C++ 的佇列儲存體  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 概觀
本指南將示範如何使用 Azure 佇列儲存服務執行一般案例。 在 c + + 中並使用這些範例 [Azure Storage Client Library for c + +](https://github.com/Azure/azure-storage-cpp/blob/v1.0.0/README.md)。  涵蓋的案例包括 **插入**, ，**查看**, ，**取得**, ，和 **刪除** 佇列訊息，以及 **建立和刪除佇列**。  

>[AZURE.NOTE] 本指南以 Azure Storage Client Library for c + + 1.0.0 版和更新。 建議的版本是 Storage Client Library 1.0.0，可透過 [NuGet](http://www.nuget.org/packages/wastorage) 或 [GitHub](https://github.com/)。 

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 C++ 應用程式  
在本指南中，您將使用可以在 C++ 應用程式內執行的儲存體功能。  

若要這樣做，您需要安裝 Azure Storage Client Library for C++，並在 Azure 訂用帳戶中建立 Azure 儲存體帳戶。  

若要安裝 Azure Storage Client Library for C++，您可以使用下列方法：

-   **Linux:** 遵循提供的指示 [Azure Storage Client Library for c + + 讀我檔案](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) 頁面。  
-   **Windows:** 在 Visual Studio 中，按一下 [ **工具 > NuGet 封裝管理員 > Package Manager Console**。 輸入下列命令到 [NuGet Package Manager console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) 按 **ENTER**。  

        Install-Package wastorage 
 
## 設定您的應用程式以存取佇列儲存體
在您要使用 Azure 儲存體 API 來存取佇列的 C++ 檔案頂端，加入下列 include 陳述式：  

    #include "was/storage_account.h"
    #include "was/queue.h"

## 設定 Azure 儲存體連接字串

Azure 儲存體用戶端會使用儲存體連接字串來儲存存取資料管理服務時所用的端點與認證。 用戶端應用程式中執行時，您必須提供下列的格式，儲存體連接字串中所列的儲存體帳戶使用的儲存體帳戶和儲存體存取金鑰名稱 [Azure 入口網站](portal.azure.com) 的 *AccountName* 和 *AccountKey* 值。 如需儲存體帳戶和存取金鑰資訊，請參閱 [關於 Azure 儲存體帳戶](storage-create-storage-account.md)。 本範例將示範如何宣告靜態欄位來存放連接字串：  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

若要測試您的應用程式在本機的 Windows 電腦中，您可以使用 Microsoft Azure [儲存體模擬器](https://msdn.microsoft.com/library/azure/hh403989.aspx)  一起安裝 [Azure SDK](http://azure.microsoft.com/downloads/)。 儲存體模擬器是一個公用程式，可在本機開發電腦上模擬 Azure 提供的 Blob、佇列和表格服務。 下列範例示範如何宣告靜態欄位以便將連接字串存放到本機儲存體模擬器中：  

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

若要啟動 Azure 儲存體模擬器，請選取 **啟動** 按鈕或按下 **Windows** 索引鍵。 開始輸入 **Azure 儲存體模擬器**, ，然後選取 **Microsoft Azure 儲存體模擬器** 從應用程式清單。  

下列範例假設您已經使用這兩個方法之一來取得儲存體連接字串。  

## 擷取連接字串
您可以使用 **cloud_storage_account** 類別來代表儲存體帳戶資訊。 若要從儲存體連接字串擷取儲存體帳戶資訊，您可以使用 **剖析** 方法。 

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## 作法：建立佇列
A **cloud_queue_client** 物件可讓您取得佇列的參照物件。 下列程式碼會建立 **cloud_queue_client** 物件。  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create a queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

使用 **cloud_queue_client** 物件來取得您想要使用佇列的參考。 如果佇列不存在，您可以建立佇列。

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();  

## 作法：將訊息插入佇列中
若要將訊息插入現有佇列，先建立新 **cloud_queue_message**。 接下來，呼叫 **add_message** 方法。 A **cloud_queue_message** 可以從其中建立字串或 **位元組** 陣列。 以下是建立佇列 (如果佇列不存在) 並插入訊息 'Hello, World' 的程式碼：

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();

    // Create a message and add it to the queue.
    azure::storage::cloud_queue_message message1(U("Hello, World"));
    queue.add_message(message1);  

## 作法：查看下一個訊息
您可以查看在前面的佇列訊息，而它從佇列中移除藉由呼叫 **peek_message** 方法。  

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Peek at the next message.
    azure::storage::cloud_queue_message peeked_message = queue.peek_message();

    // Output the message content.
    std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## 作法：變更佇列訊息的內容
您可以在佇列中就地變更訊息內容。 如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。 下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。 這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。 您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。 通常，您也會保留重試計數，如果訊息重試超過 n 次，您會將它刪除。 這麼做可防止每次處理時便觸發應用程式錯誤的訊息。  

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));
        
    // Get the message from the queue and update the message contents.
    // The visibility timeout "0" means make it visible immediately.
    // The visibility timeout "60" means the client can get another minute to continue 
    // working on the message.
    azure::storage::cloud_queue_message changed_message = queue.get_message();

    changed_message.set_content(U("Changed message"));
    queue.update_message(changed_message, std::chrono::seconds(60), true);

    // Output the message content.
    std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## 作法：在下一個訊息清除佇列
您的程式碼可以使用兩個步驟將訊息自佇列中清除佇列。 當您呼叫 **get_message**, ，您會取得佇列中的下一個訊息。 從傳回的訊息 **get_message** 會從此佇列讀取訊息的任何其他程式碼是不可見。 若要完成從佇列移除訊息，您還必須呼叫 **delete_message**。 這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 您的程式碼呼叫 **delete_message** 處理完訊息之後。

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the next message.
    azure::storage::cloud_queue_message dequeued_message = queue.get_message();
    std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

    // Delete the message.
    queue.delete_message(dequeued_message); 

## 作法：運用清除佇列訊息的其他選項
自訂從佇列中擷取訊息的方法有兩種。 首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。 下列程式碼範例使用 **get_messages** 方法一次呼叫中取得 20 個訊息。 然後它會處理每個訊息使用 **的** 迴圈。 它也會將可見度逾時設定為每個訊息五分鐘。 請注意，在 5 分鐘會針對所有訊息，在相同的時間，因此 5 分鐘後自從呼叫 **get_messages**, ，任何尚未刪除的訊息都會重新出現。  

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to 
    // 5 minutes (300 seconds).
    azure::storage::queue_request_options options;
    azure::storage::operation_context context;

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);
        
    for (auto it = messages.cbegin(); it != messages.cend(); ++it)
    {
        // Display the contents of the message.
        std::wcout << U("Get: ") << it->content_as_string() << std::endl;
    }

## 作法：取得佇列長度
您可以取得佇列中的估計訊息數目。  **Download_attributes** 方法會要求佇列服務擷取佇列屬性，其中包含訊息計數。  **Approximate_message_count** 方法會取得佇列中大約的訊息數目。  

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Fetch the queue attributes.
    queue.download_attributes();

    // Retrieve the cached approximate message count.
    int cachedMessageCount = queue.approximate_message_count();

    // Display number of messages.
    std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## 作法：刪除佇列
若要刪除佇列及其內含的所有訊息，請呼叫 **delete_queue_if_exists** 佇列物件上的方法。

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // If the queue exists and delete it.
    queue.delete_queue_if_exists();  

## 後續步驟
了解佇列儲存體的基礎概念之後，請依照下列連結深入了解 Azure 儲存體。  

-   [如何使用 C++ 的 Blob 儲存體](storage-c-plus-plus-how-to-use-blobs.md)
-   [如何使用 C++ 的資料表儲存體](storage-c-plus-plus-how-to-use-tables.md)
-   [使用 C++ 列出 Azure 儲存體資源](storage-c-plus-plus-enumeration.md)
-   [Storage Client Library for C++ 參考資料](http://azure.github.io/azure-storage-cpp)
-   [Azure 儲存體文件](http://azure.microsoft.com/documentation/services/storage/)

 


